
---
title: "Blog 2"
date: 2026-11-27
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# 🔐 UNDERSTANDING THE PHP2SHELL VULNERABILITIES: FROM EARLY DETECTION BY AMAZON TO MITIGATION WITH AWS WAF

PHP2Shell is a critical security issue involving two vulnerabilities in PHP that can allow unauthenticated remote code execution (RCE). What makes this incident particularly important is that Amazon Threat Intelligence observed exploitation attempts through its MadPot honeypot four days before the vulnerabilities were publicly disclosed.

This incident highlights how quickly attackers can exploit newly discovered vulnerabilities and why organizations must combine timely patching, continuous monitoring, and layered security controls.

Before examining the vulnerabilities, it is useful to understand several key terms:

- **Remote Code Execution (RCE):** A vulnerability that allows an attacker to execute commands or code on a remote system.
- **Honeypot:** A security mechanism designed to attract and observe suspicious activity in a controlled environment.
- **Embargo:** A period during which vulnerability information is restricted from public disclosure to allow affected parties to prepare security updates.
- **Proof of Concept (PoC):** A demonstration showing that a vulnerability can potentially be exploited.
- **Web Application Firewall (WAF):** A security control that monitors and filters HTTP/HTTPS traffic to help protect web applications.

<figure>
  <a href="/aws-workshop/images/3-BlogsPosted/3.2-Blog2/php2shell-aws-waf.png" target="_blank">
    <img src="/aws-workshop/images/3-BlogsPosted/3.2-Blog2/php2shell-aws-waf.png" alt="PHP2Shell vulnerabilities and AWS WAF mitigation overview" style="display:block;width:90%;max-width:1100px;height:auto;margin:0 auto;" />
  </a>
  <figcaption style="text-align:center;">Overview of PHP2Shell vulnerabilities and AWS WAF mitigation strategies.</figcaption>
</figure>


## 1. Overview of the PHP2Shell Vulnerabilities

PHP2Shell refers to two vulnerabilities affecting PHP's handling of multipart/form-data requests:

| Vulnerability | Description |
|---|---|
| CVE-2026-109618 | Memory corruption associated with PHP running out of memory while processing a request. |
| CVE-2026-109619 | Stale data or global state from a previous request may be reused by a later request handled by the same worker. |

Both vulnerabilities can lead to unauthenticated remote code execution under affected conditions. They are rated **Critical**, with a CVSS v4.0 score of **9.2**.

The vulnerabilities are particularly concerning because the affected request-processing behavior occurs before the application code executes. Consequently, an application-level check may not be sufficient to prevent exploitation.

## 2. How Amazon Detected Exploitation Before Public Disclosure

One of the most significant findings came from Amazon Threat Intelligence. Its MadPot honeypot infrastructure recorded suspicious activity before the vulnerabilities were publicly disclosed.

The following timeline summarizes the observations reported by AWS:

| Date and time (UTC) | Observed activity |
|---|---|
| October 17, 2026 | MadPot observed an exploitation attempt originating from a commercial anonymization network, four days before public disclosure. |
| October 21, around 02:00 | The first publicly available proof-of-concept material appeared. |
| October 21, 02:00–08:00 | Additional exploitation attempts were observed from anonymized infrastructure. Differences in payloads suggested the involvement of multiple actors. |
| October 21, 14:00 | The disclosure embargo was lifted and PHP security updates were released. |
| After public disclosure | Proof-of-concept material spread rapidly, increasing the likelihood of broader exploitation attempts. |

It is important to distinguish an observed exploitation attempt from a confirmed compromise. AWS stated that it was unclear how the early actor obtained the exploit information and whether the observed attempts would have succeeded against real-world systems.

This finding demonstrates that defenders should not assume that a vulnerability is safe from exploitation simply because its public disclosure date has not yet arrived.

## 3. Why These Vulnerabilities Matter

The PHP2Shell incident highlights several important security challenges.

### 3.1. Exploitation Can Begin Before Public Disclosure

The October 17 observation shows that exploitation attempts may occur while vulnerability information is still under embargo. Organizations should therefore avoid relying exclusively on public disclosure dates when determining when suspicious activity may have started.

### 3.2. The Time Between Patching and Exploitation Is Shrinking

Once technical details and proof-of-concept material become available, attackers can quickly adapt and develop their own exploitation attempts. AWS highlighted that AI-assisted analysis can further accelerate this process.

### 3.3. Critical Vulnerabilities Require Rapid Response

For internet-facing systems, waiting several days to assess or apply a critical security update may create unnecessary exposure. Vulnerability response should be treated as an urgent operational activity, especially when exploitation attempts have already been observed.

## 4. Applying AWS WAF Mitigations

AWS published guidance on using AWS WAF to apply additional mitigations for the PHP2Shell vulnerabilities.

The managed rule group updates include:

- **Version 2.3:** Introduced the `PHP2Shell_BODY` rule to help mitigate CVE-2026-109619.
- **Version 2.4:** Introduced the `PHP2Shell_BODY_Extended` rule to provide additional coverage for CVE-2026-109618. Version 2.4 became the default version on October 23, 2026.

These rules provide an additional layer of protection, but they should not be considered a replacement for upgrading PHP.

### 4.1. Upgrade PHP to a Patched Version

The primary remediation is to upgrade affected PHP installations to a fixed release.

AWS identifies the following patched versions:

| PHP branch | Fixed version |
|---|---|
| PHP 8.5 | 8.5.12 |
| PHP 8.4 | 8.4.27 |
| PHP 8.3 | 8.3.36 |
| PHP 8.2 | 8.2.35 |

For older PHP branches, organizations should review the applicable vendor guidance and available backported security fixes. Systems running end-of-life versions require particular attention.

### 4.2. Use AWS WAF Managed Rules

Organizations can use the AWS Managed Rules rule group and its PHP2Shell-related protections to inspect incoming requests.

A cautious deployment approach is:

1. Enable the relevant managed rule group.
2. Begin with **Count** mode to observe which requests would match.
3. Review logs and investigate potential false positives.
4. Move to **Block** mode after validating the impact on legitimate application traffic.

This staged approach helps reduce the risk of disrupting normal application functionality.

### 4.3. Limit Request Body Size

Request body size restrictions can reduce exposure to certain memory-exhaustion scenarios associated with CVE-2026-109618.

However, this is only a partial mitigation. AWS notes that request-header amplification and other factors can limit the effectiveness of a simple body-size restriction.

Therefore, body-size limits should be combined with PHP updates and appropriate server-side protections.

### 4.4. Restrict Unnecessary Multipart Uploads

If an application does not require file uploads or multipart/form-data requests, administrators can consider blocking this content type through AWS WAF.

This reduces exposure to the affected request-processing path. However, it must be tested carefully because some applications legitimately depend on multipart uploads.

### 4.5. Apply Rate-Based Rules and Monitor Requests

Rate-based rules can help reduce repeated suspicious POST requests. They are useful as an additional control, but they cannot guarantee protection against a single successful exploitation attempt.

Organizations should also enable AWS WAF logging and review suspicious POST requests, particularly those containing the `PHP_SESSION_UPLOAD_PROGRESS` parameter.

**Important limitation:** AWS WAF cannot fully inspect PHP worker memory or replace fixes to PHP's internal request-processing behavior. WAF protections are defense-in-depth measures, not a complete solution.

## 5. Recommended Security Response

Based on the AWS findings, organizations operating PHP-based applications should consider the following actions:

1. **Patch affected systems:** Upgrade to an applicable fixed PHP release as soon as possible.
2. **Review historical logs:** Investigate suspicious activity starting from October 17, 2026, rather than only from the public disclosure date.
3. **Inspect suspicious POST requests:** Look for unusual multipart/form-data requests and the `PHP_SESSION_UPLOAD_PROGRESS` parameter.
4. **Investigate unexpected activity:** Review unexpected process execution and file modifications on affected servers.
5. **Evaluate suspicious IP addresses carefully:** Commercial anonymization infrastructure may be shared by legitimate users. Investigate the associated activity instead of automatically treating every observed address as malicious.
6. **Deploy WAF protections:** Use managed rules, logging, and appropriate request restrictions as additional defensive layers.

## 6. Lessons Learned

The PHP2Shell incident provides several important lessons for security practitioners:

- **Embargo does not guarantee the absence of exploitation:** Attackers may obtain or discover vulnerability information before public disclosure.
- **Patch management must be responsive:** Critical vulnerabilities affecting internet-facing services require rapid assessment and remediation.
- **Monitoring must cover historical activity:** Security investigations should consider the possibility that suspicious activity began before a vulnerability was publicly announced.
- **Defense-in-depth remains essential:** Patching, WAF rules, server-side protections, and continuous monitoring should work together.
- **Security teams must adapt to faster exploitation cycles:** Automated analysis and AI-assisted techniques can shorten the time between vulnerability discovery and exploitation.

## 7. Personal Reflection and Conclusion

What I find particularly important about PHP2Shell is not only the severity of the vulnerabilities, but also the timeline of the observed exploitation attempts.

The fact that Amazon MadPot detected suspicious activity four days before public disclosure demonstrates why security monitoring should be continuous rather than limited to responding after a vulnerability becomes widely known.

I also learned that AWS WAF can provide useful additional protection, but it cannot replace patching the underlying application. Security controls must be selected according to the actual limitations of the technology and the needs of the application.

For me, this incident reinforces an important principle in cybersecurity: effective defense requires early detection, timely remediation, and multiple layers of protection working together.

## References

1. Amazon Web Services. (2026, October 21). *Amazon Threat Intelligence identifies PHP2Shell exploitation prior to public disclosure in MadPot.*  
   https://aws.amazon.com/blogs/security/amazon-threat-intelligence-identifies-php2shell-exploitation-prior-to-public-disclosure-in-madpot/

2. Amazon Web Services. (2026, October 21; updated October 29, 2026). *Using AWS WAF to apply mitigations for PHP2Shell RCE vulnerabilities CVE-2026-109618 and CVE-2026-109619.*  
   https://aws.amazon.com/blogs/security/using-aws-waf-to-apply-mitigations-for-php2shell-rce-vulnerabilities-cve-2026-109618-and-cve-2026-109619/
