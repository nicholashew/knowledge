# AWS SDK - SSM

Example usage
```java
SsmClient ssmClient = AwsUtils.GetSsmClient(Region.AP_SOUTHEAST_1);

try {	
  String value1 = AwsUtils.getSsmParamValue(ssmClient, "normal_string_without_encryption");
  String value2 = AwsUtils.getSsmParamValueWithDecryption(ssmClient, "secure_string_encrypted_with_kms");
} catch (SsmException e) {
  LOG.error("failed to get ssm param value", e);
  throw e;
} finally {
  ssmClient.close();
}
```

Maven dependencies
```
//pom.xml
  <dependencies>
    <!-- https://mvnrepository.com/artifact/software.amazon.awssdk/ssm -->
    <dependency>
      <groupId>software.amazon.awssdk</groupId>
      <artifactId>ssm</artifactId>
      <version>2.17.229</version>
    </dependency>
```

AWS Utilities
```java
//AwsUtils.java
package com.example.awsssm;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.ssm.SsmClient;
import software.amazon.awssdk.services.ssm.model.GetParameterRequest;
import software.amazon.awssdk.services.ssm.model.GetParameterResponse;
import software.amazon.awssdk.services.ssm.model.SsmException;

/**
 * <h1>AWS utilities.</h1>
 * Reference:
 * <ul>
 *   <li>https://docs.aws.amazon.com/code-samples/latest/catalog/javav2-ssm-src-main-java-com-example-ssm-GetParameter.java.html</li>
 * </ul>
 */
public class AwsUtils {
  public static SsmClient GetSsmClient() {		
    return GetSsmClient(Region.AP_SOUTHEAST_1);
  }

  public static SsmClient GetSsmClient(Region region) {		
    SsmClient ssmClient = SsmClient.builder()
      .region(region)
      .build();

    return ssmClient;
  }

  public static String getSsmParamValueWithDecryption(SsmClient client, String paramName) throws SsmException {
    GetParameterRequest request = GetParameterRequest.builder()
      .name(paramName)
      .withDecryption(Boolean.TRUE)
      .build();

    GetParameterResponse response = client.getParameter(request);

    return response.parameter().value();
  }

  public static String getSsmParamValue(SsmClient client, String paramName) throws SsmException {
    GetParameterRequest request = GetParameterRequest.builder()
      .name(paramName)
      .build();

    GetParameterResponse response = client.getParameter(request);

    return response.parameter().value();
  }
}

```

## Reference

- [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)
- [Amazon SSM - GetParameter.java](https://docs.aws.amazon.com/code-samples/latest/catalog/javav2-ssm-src-main-java-com-example-ssm-GetParameter.java.html)
