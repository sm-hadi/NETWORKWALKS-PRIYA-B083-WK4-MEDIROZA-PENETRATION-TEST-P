# M1 – Initial Access

## Goal

Assess the Mediroza patient portal and determine whether unauthorized access could be obtained to the confidential patient laboratory reports.

## Approach

1. Reviewed `robots.txt` and identified several disallowed paths, including `/patient/`, `/staff/`, and `/old/`. These paths were accessible despite being listed as disallowed in `robots.txt`.
2. Discovered the patient portal login endpoint at `/patient/login.php`, which was not present in the site's public sitemap.
3. Tested the login functionality with multiple username and password combinations. No distinguishable differences in the application's error responses were observed, so username enumeration through error-message differences was not identified.
4. Tested the login form for SQL injection vulnerabilities.

## Exploitation

**Vulnerability:** SQL Injection – Authentication Bypass
**Affected Field:** Username
**Payload:** `admin' -- `
**Password:** `test123`

### Explanation

The application appeared to construct the authentication SQL query by directly incorporating user-supplied input.

The single quote (`'`) in the payload terminated the original username string, while the `-- ` SQL comment syntax caused the remainder of the query to be ignored. As a result, the portion of the authentication query responsible for validating the password was bypassed.

This allowed authentication to succeed without providing the legitimate password for the `admin` account.

### Result

Authentication was successfully bypassed, providing unauthorized access to the patient portal.

The portal exposed **three confidential patient laboratory reports** that were available for download.

### Security Impact

Successful exploitation resulted in unauthorized access to sensitive patient information. In a real-world environment, exposure of medical laboratory reports could constitute a serious **confidentiality and privacy breach** and potentially expose the organization to regulatory and legal consequences.

**Suggested Severity:** High/Critical, depending on the lab's severity-rating methodology.

## Evidence

### 1. Reconnaissance – `robots.txt`

`robots.txt` revealed the following disallowed paths:

* `/patient/`
* `/staff/`
* `/old/`



### 2. SQL Injection Payload

The username field was tested with:

`admin' -- `


### 3. Successful Portal Access

Following successful authentication bypass, the patient portal displayed three laboratory reports available for download.
