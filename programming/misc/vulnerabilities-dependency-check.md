# Vulnerabilities Dependency Check

This demostrate how to use [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) to scan publicly disclosed vulnerabilities contained within a project’s dependencies. It does this by determining if there is a Common Platform Enumeration (CPE) identifier for a given dependency. If found, it will generate a report linking to the associated CVE entries.

## Setup

- Go to [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) website
- Scroll to the bottom and look for **Command Line**. Click on the link to download OWASP Dependency Check. It will download a ZIP file to the Downloads folder.
- Unzip the downloaded zip file to your downloads location. e.g. `C:\temp\dependency-check-6.1.5-release\`

## Usage On Windows

- Launch the cmd command line window 
- From cmd, navigate to the bin file path. example:
  ```
  CD C:\temp\dependency-check-6.1.5-release\bin
  ```
- Execute the scan command with the target file path and report output path. Assuming your project file path is `C:\projects\hello-world`
  ```
  dependency-check.bat -s "C:\projects\hello-world" -o "C:\temp\hello-world-dependency-check-report.html"
  ```
- For Example
  ```
  C:\temp\dependency-check-6.1.5-release\bin>dependency-check.bat -s "C:\projects\hello-world" -o "C:\temp\hello-world-dependency-check-report.html"
  [INFO] Checking for updates
  [INFO] Skipping NVD check since last check was within 4 hours.
  [INFO] Skipping RetireJS update since last update was within 24 hours.
  [INFO] Check for updates complete (1066 ms)
  [INFO] Analysis Started
  [INFO] Finished File Name Analyzer (0 seconds)
  [INFO] Finished MSBuild Project Analyzer (0 seconds)
  [INFO] Finished Dependency Merging Analyzer (0 seconds)
  [INFO] Finished Version Filter Analyzer (0 seconds)
  [INFO] Finished Hint Analyzer (0 seconds)
  [INFO] Created CPE Index (2 seconds)
  [INFO] Finished CPE Analyzer (3 seconds)
  [INFO] Finished False Positive Analyzer (0 seconds)
  [INFO] Finished NVD CVE Analyzer (0 seconds)
  [INFO] Finished Sonatype OSS Index Analyzer (3 seconds)
  [INFO] Finished Vulnerability Suppression Analyzer (0 seconds)
  [INFO] Finished Dependency Bundling Analyzer (0 seconds)
  [INFO] Analysis Complete (7 seconds)
  [INFO] Writing report to: C:\temp\hello-world-dependency-check-report.html
  ```
- Open generated html report to analysing the vulnerabilities.
  
## Reference

- [OWASP Dependency Check Documentation](https://jeremylong.github.io/DependencyCheck/)
- [Sample Report](https://jeremylong.github.io/DependencyCheck/general/SampleReport.html)
- [Github](https://github.com/jeremylong/DependencyCheck)

