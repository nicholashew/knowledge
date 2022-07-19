# mTLS HTTP Connection

This demostrates how to make a mutual TLS (2 Way SSL) http connection using the popular library [Bouncy Castle](https://www.bouncycastle.org/java.html) in Java Maven Project.

## Prerequisite

Install the following maven dependencies

```xml
//pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  ...

  <dependencies>
    <!-- https://mvnrepository.com/artifact/org.bouncycastle/bcprov-jdk15on -->
    <dependency>
      <groupId>org.bouncycastle</groupId>
      <artifactId>bcprov-jdk15on</artifactId>
      <version>1.70</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.bouncycastle/bcpkix-jdk15on -->
    <dependency>
      <groupId>org.bouncycastle</groupId>
      <artifactId>bcpkix-jdk15on</artifactId>
      <version>1.70</version>
    </dependency>
  </dependencies>
  ...
```

## Example With EC Private Key

In this example, we are using ca cert, public cert, and elliptic curve key-pair (private key) 

The ca cert and public cert should in below format
```
-----BEGIN CERTIFICATE-----
base64 encoded string
-----END CERTIFICATE-----
```

The elliptic curve key-pair (private key) should in below format
```
-----BEGIN EC PARAMETERS-----
base64 encoded string
-----END EC PARAMETERS-----
-----BEGIN EC PRIVATE KEY-----
base64 encoded string
-----END EC PRIVATE KEY-----
```

Example
```java
package com.example.mtls;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.security.PrivateKey;
import java.security.cert.Certificate;
import java.util.Base64;
import java.util.Collections;
import java.util.Map;

import javax.net.ssl.HttpsURLConnection;
import javax.net.ssl.KeyManager;
import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;

public class Main {
  public static void main(String[] args)
  {
    final String tempPassword = "test";
    String publicCaCert = getResourceAsString("ca.crt");
    String publicCert = getResourceAsString("client_public.crt");
    String privateKey = getResourceAsString("client_private_key_ec.pem");
    
    Certificate caCert = PemUtils.loadCertificate(publicCaCert);
    Certificate clientPublicKey = PemUtils.loadCertificate(publicCert);
    PrivateKey clientPrivateKey = PemUtils.loadEcPrivateKey(privateKey);
    
    KeyManager[] keyManagers = PemUtils.getKeyManagers(clientPublicKey, clientPrivateKey, tempPassword);
    TrustManager[] trustManagers = PemUtils.getTrustManagers(caCert);
    
    SSLContext sslContext = SSLContext.getInstance("TLS");
    sslContext.init(keyManagers, trustManagers, new java.security.SecureRandom());
    
    String invokeUrl = "https://127.0.0.1/some/secure/url/with/mtls";
    HttpsURLConnection conn = (HttpsURLConnection) (new URL(invokeUrl)).openConnection();
    conn.setSSLSocketFactory(sslContext.getSocketFactory());
    conn.setHostnameVerifier(PemUtils.allHostsValid);
    conn.setAllowUserInteraction(true);
    //conn.setDoInput(true);
    conn.setDoOutput(true);
    conn.setUseCaches(false);
    conn.setConnectTimeout(30000);
    conn.setReadTimeout(180000);
    conn.setRequestMethod("GET");
    conn.setRequestProperty("Content-Type", "application/json");
    
    int responseCode = conn.getResponseCode();
    
    StringBuffer response = new StringBuffer();
    BufferedReader in;
    
    if (responseCode == HttpURLConnection.HTTP_OK) { // 200 OK Success
      in = new BufferedReader(new InputStreamReader(conn.getInputStream()));      
    } else {
      in = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
    }
    
    String inputLine;
    while ((inputLine = in.readLine()) != null) {
      response.append(inputLine);
    }
    in.close();
    
    System.out.println(response.toString());
  }
  
  public static String getResourceAsString(String fileName) throws IOException {
    InputStream inputStream = getResourceAsStream(fileName);

    String text = new BufferedReader(
      new InputStreamReader(inputStream, StandardCharsets.UTF_8))
        .lines()
        .collect(Collectors.joining("\n"));

    return text;
  }

  public static InputStream getResourceAsStream(String fileName) {
    // The class loader that loaded the class
    ClassLoader classLoader = Main.class.getClassLoader();
    InputStream inputStream = classLoader.getResourceAsStream(fileName);

    // the stream holding the file content
    if (inputStream == null) {
      throw new IllegalArgumentException("file not found! " + fileName);
    } else {
      return inputStream;
    }
  }
}
```

## Example With PKCS#8 Private Key

The ca cert and public cert should in below format
```
-----BEGIN CERTIFICATE-----
base64 encoded string
-----END CERTIFICATE-----
```

The PKCS#8 unencrypted private key should in below format
```
-----BEGIN PRIVATE KEY-----
base64 encoded string
-----END PRIVATE KEY-----
```

Example
```java
package com.example.mtls;

...

public class Example {
  public static void main(String[] args)
  {
    final String tempPassword = "test";
    String publicCaCert = getResourceAsString("ca.crt");
    String publicCert = getResourceAsString("client_public.crt");
    String privateKey = getResourceAsString("client_private_key_pkcs8.pem");
    
    Certificate caCert = PemUtils.loadCertificate(publicCaCert);
    Certificate clientPublicKey = PemUtils.loadCertificate(publicCert);
    PrivateKey clientPrivateKey = PemUtils.loadPkcs8PrivateKey(privateKey);
    
    // same as Example With EC Private Key
    ...
}
```

## Pem Utilities

```java
//PemUtils.java
package com.example.mtls;

import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.Reader;
import java.io.StringReader;
import java.security.GeneralSecurityException;
import java.security.KeyFactory;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.PrivateKey;
import java.security.Security;
import java.security.UnrecoverableKeyException;
import java.security.cert.Certificate;
import java.security.cert.CertificateException;
import java.security.cert.CertificateFactory;
import java.security.cert.X509Certificate;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.PKCS8EncodedKeySpec;
import java.util.Arrays;
import java.util.Base64;
import java.util.HashSet;
import java.util.Set;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.KeyManager;
import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.SSLSession;
import javax.net.ssl.TrustManager;
import javax.net.ssl.TrustManagerFactory;
import javax.net.ssl.X509TrustManager;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.bouncycastle.asn1.pkcs.PrivateKeyInfo;
import org.bouncycastle.jce.provider.BouncyCastleProvider;
import org.bouncycastle.openssl.PEMDecryptorProvider;
import org.bouncycastle.openssl.PEMEncryptedKeyPair;
import org.bouncycastle.openssl.PEMKeyPair;
import org.bouncycastle.openssl.PEMParser;
import org.bouncycastle.openssl.jcajce.JcaPEMKeyConverter;
import org.bouncycastle.openssl.jcajce.JcePEMDecryptorProviderBuilder;
import org.bouncycastle.util.io.pem.PemObject;
import org.bouncycastle.util.io.pem.PemReader;

/**
 * <h1>PEM utilities.</h1>
 * Reference:
 * <ul>
 *   <li>https://milosgarunovic.com/posts/java-mtls-http-client/</li>
 *   <li>https://egkatzioura.com/2021/10/20/execute-mtls-calls-using-java/</li>
 *   <li>https://stackoverflow.com/a/41947163</li>
 *   <li>https://github.com/spotify/docker-client/blob/master/src/main/java/com/spotify/docker/client/DockerCertificates.java#L130</li>
 *   <li>https://github.com/Hakky54/sslcontext-kickstart</li>
 *   <li>http://www.javawenti.com/?post=35759</li>
 *   <li>https://github.com/aws-samples/serverless-mutual-tls/blob/main/software/3-lambda-using-parameter-store/src/main/java/com/amazon/aws/example/AppClient.java#L61</li>
 * </ul>
 * 
 * <h2>Dependencies:</h2>
 * <ul>
 *   <li>
 *     org.bouncycastle » bcprov-jdk15on » 1.70 <br/>
 *     https://mvnrepository.com/artifact/org.bouncycastle/bcprov-jdk15on
 *   </li>
 *   <li>
 *     org.bouncycastle » bcpkix-jdk15on » 1.70 <br/>
 *     https://mvnrepository.com/artifact/org.bouncycastle/bcpkix-jdk15on
 *   </li>
 * </ul>
 */
public class PemUtils {	
  
  private static final Set<String> PRIVATE_KEY_ALGS = new HashSet<String>(Arrays.asList("RSA", "EC"));
  private static final Logger LOG = LogManager.getLogger(PemUtils.class);

  /**
   * Get Key Managers by public certificate and private key
   * @param clientPublicCert - The public certificate
   * @param clientPrivateKey - The private key
   * @param keyStorePassword - The keystore password to be set
   * @return
   * @throws IOException
   * @throws NoSuchAlgorithmException
   * @throws CertificateException
   * @throws KeyStoreException
   * @throws UnrecoverableKeyException
   */
  public static KeyManager[] getKeyManagers(Certificate clientPublicCert, PrivateKey clientPrivateKey,
    String keyStorePassword) throws IOException, NoSuchAlgorithmException, CertificateException,
    KeyStoreException, UnrecoverableKeyException {
    // Create a KeyStore containing public cert and private key
    KeyStore keyStore = KeyStore.getInstance("JKS");
    keyStore.load(null, null);
    char[] password = keyStorePassword.toCharArray();
    Certificate[] chain = new Certificate[] { clientPublicCert };
    // keyStore.setCertificateEntry("client-cert", clientPublicKey);
    keyStore.setKeyEntry("client-key", clientPrivateKey, password, chain);

    // Create a KeyManager that trusts the keystore
    String kmfAlgorithm = KeyManagerFactory.getDefaultAlgorithm();
    KeyManagerFactory kmf = KeyManagerFactory.getInstance(kmfAlgorithm);
    kmf.init(keyStore, password);

    return kmf.getKeyManagers();
  }
	
  /**
   * Get Trust Managers by CA Certificate
   * @param caCert
   * @return
   * @throws KeyStoreException
   * @throws NoSuchAlgorithmException
   * @throws CertificateException
   * @throws IOException
   */
  public static TrustManager[] getTrustManagers(Certificate caCert)
    throws KeyStoreException, NoSuchAlgorithmException, CertificateException, IOException {
    // Create a KeyStore containing our trusted CAs
    KeyStore trustKeyStore = KeyStore.getInstance("JKS");
    trustKeyStore.load(null, null);
    trustKeyStore.setCertificateEntry("ca-cert", caCert);

    // Create a TrustManager that trusts the CAs in our KeyStore
    String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
    TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
    tmf.init(trustKeyStore);

    return tmf.getTrustManagers();
  }
	
  /**
   * Read certificate from PEM content
   */
  public static Certificate loadCertificate(String certificatePem) throws IOException, GeneralSecurityException {
    final byte[] content = readPemContent(certificatePem);
    return loadCertificate(content);
  }
	
  /**
   * Read certificate from bytes
   */
  public static Certificate loadCertificate(byte[] certBytes) throws CertificateException {
    CertificateFactory certificateFactory = CertificateFactory.getInstance("X509");
    return certificateFactory.generateCertificate(new ByteArrayInputStream(certBytes));
  }
	
  /**
   * Read PEM byte content from string
   * @param pem
   * @return
   * @throws IOException
   */
  public static byte[] readPemContent(String pem) throws IOException {
    final byte[] content;		
    PemReader pemReader = new PemReader(new StringReader(pem));
    final PemObject pemObject = pemReader.readPemObject();
    content = pemObject.getContent();
    pemReader.close();		
    return content;
  }
	
  /**
   * Read PrivateKey from elliptic curve private key PEM content
   * <p>Example usage</p>
   * <pre>{@code
   * String ecPrivateKeyPem = "-----BEGIN EC PRIVATE KEY-----\n"
   *      + "MHQCAQEEIDzESrZFmTaOozu2NyiS8LMZGqkHfpSOoI/qA9Lw+d4NoAcGBSuBBAAK\n"
   *      + "oUQDQgAE7kIqoSQzC/UUXdFdQ9Xvu1Lri7pFfd7xDbQWhSqHaDtj+XY36Z1Cznun\n"
   *      + "GDxlA0AavdVDuoGXxNQPIed3FxPE3Q==\n"
   *      + "-----END EC PRIVATE KEY-----\n");
   * PrivateKey privateKey = loadEcPrivateKey(ecPrivateKeyPem);
   * System.out.println(privateKey);
   * }</pre>
   * 
   * Refer: <a>https://github.com/spotify/docker-client/blob/9a7c3b4994be9a55988f821f7177029201ff6aef/src/main/java/com/spotify/docker/client/DockerCertificates.java#L130</a>
   */
  public static PrivateKey loadEcPrivateKey(String ecPrivateKeyPem) throws IOException {
    // removes the EC PARAMETERS footer and header, if any
    int beginIndex = ecPrivateKeyPem.indexOf("-----BEGIN EC PRIVATE KEY-----");
    String privateKey = ecPrivateKeyPem.substring(beginIndex);

    final Reader reader = new StringReader(privateKey);	
    final PEMParser pemParser = new PEMParser(reader);

    try {
      JcaPEMKeyConverter converter = new JcaPEMKeyConverter();
      PEMDecryptorProvider decryptionProv = new JcePEMDecryptorProviderBuilder().build(null);
      Object keyPair = pemParser.readObject();
      PrivateKeyInfo keyInfo;
      if (keyPair instanceof PEMEncryptedKeyPair) {
        PEMKeyPair decryptedKeyPair = ((PEMEncryptedKeyPair) keyPair).decryptKeyPair(decryptionProv);
        keyInfo = decryptedKeyPair.getPrivateKeyInfo();
        LOG.info("load EC Private Key from PEMEncryptedKeyPair");
      } else {
        keyInfo = ((PEMKeyPair) keyPair).getPrivateKeyInfo();
        LOG.info("load EC Private Key from PEMKeyPair");
      }

      return converter.getPrivateKey(keyInfo);			
    } catch (IOException e) {
     throw e;
    } finally {
      reader.close();
      pemParser.close();
    }
  }
		
  /**
   * Read PrivateKey from PKCS#8 private key PEM content using "RSA" algorithm,
   * retry with "EC" algorithm if failed to read from "RSA" algorithm
   */
  public static PrivateKey loadPkcs8PrivateKey(String privateKeyPem)
   throws NoSuchAlgorithmException, InvalidKeySpecException {
    // removes PKCS#8 footer, header, carriage return, line breaks
    String privateString = privateKeyPem
    .replace("-----BEGIN PRIVATE KEY-----", "")
    .replace("-----END PRIVATE KEY-----", "")
    .replaceAll("\r", "")
    .replaceAll("\n", "");

    // adds BouncyCastle Provider 
    Security.addProvider(new BouncyCastleProvider());	

    // decode the content using Base64 decoder
    byte[] encoded = Base64.getDecoder().decode(privateString);		    

    // Creates a new PKCS8EncodedKeySpec with the given encoded key.
    PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(encoded);	    

    // Try generate PrivateKey from RSA and EC algorithm
    return tryGeneratePrivateKey(keySpec, PRIVATE_KEY_ALGS);
  }
	
  private static PrivateKey tryGeneratePrivateKey(final PKCS8EncodedKeySpec spec, final Set<String> algorithms)
    throws InvalidKeySpecException {
    KeyFactory kf;
    PrivateKey key;

    for (final String algorithm : algorithms) {
      try {
        kf = KeyFactory.getInstance(algorithm);
        key = kf.generatePrivate(spec);
        LOG.info("Generated private key from spec using the '{}' algorithm", algorithm);
        return key;
      } catch (InvalidKeySpecException | NoSuchAlgorithmException e) {
        LOG.info("Tried generating private key from spec using the '{}' algorithm", algorithm, e);
      }
    }

    final String error = String.format("Could not generate private key from spec. Tried using %s",
    String.join(",", algorithms));

    throw new InvalidKeySpecException(error);
  }
	
  /**
   * Gets a HostVerifier that accepts all connections.
   * You should implement your own HostVerifier with custom verify condition if required. 
   */
  public static HostnameVerifier allHostsValid = new HostnameVerifier() {
    public boolean verify(String hostname, SSLSession session) {
      return true;
    }
  };
  
  /**
   * Gets a TrustManager that will accept all certificates presented from the server.
   * Usually when using self signed certificates we need to use a TrustManager that will accept them.
   * You should generate the TrustManager by the CA certificate or keystore if required.
   */
  public static TrustManager[] acceptAllTrustManager = {
    new X509TrustManager() {
      public X509Certificate[] getAcceptedIssuers() {
          return new X509Certificate[0];
      }
  
      public void checkClientTrusted(X509Certificate[] certs, String authType) throws CertificateException {
      }
  
      public void checkServerTrusted(X509Certificate[] certs, String authType) throws CertificateException {
      }
    }
  };
}
```

## OpenSSL Commands

Check a certificate
```bat
openssl x509 -in certificate.crt -text -noout
openssl x509 -in certificate.cer -text -noout
```

Convert elliptic curve key-pair (private key) to PKCS#8 pem format
```bat
openssl pkcs8 -topk8 -nocrypt -in client_private_key_ec.pem -out client_private_key_pkcs8.pem
```

## Others

If you having the ".jks" file, it would be more straigh forward to generate the KeyManagers and TrustManager compare to using PEM files in this example.

## Reference
- [Execute mTLS calls using Java](https://egkatzioura.com/2021/10/20/execute-mtls-calls-using-java/)
- [Generate Certificates](https://egkatzioura.com/2021/10/01/add-mtls-to-nginx/)
