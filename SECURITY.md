# Security Policy

## Supported Versions



| Version  | Supported |
| -------- | --------- |
| 1.0.x    | ✅        |
| <= 1.0.x | ❌        |

## Reporting a Vulnerability

Please post your vulnerability report from the following page:
https://github.com/jfrog/log-analytics-splunk/security/advisories

## Fixed issues

#### March 22, 2024

Upgraded docker images to use fluentd:1.16.3
This resolves the following CVEs

* [CVE-2023-4911](https://github.com/advisories/GHSA-m77w-6vjw-wh2f "CVE-2023-4911")
* [CVE-2019-8457](https://github.com/advisories/GHSA-p4jx-5p2x-4pq7)

Note: When scanning our fluentd docker image `releases-pts-observability-fluentd.jfrog.io/fluentd:4.1` for volunerabilities, [CVE-2023-45853](https://www.cve.org/CVERecord?id=CVE-2023-45853) still comes up (from fluentd:1.16.3). However, fluentd claims it's a [non-issue](https://github.com/fluent/fluentd-kubernetes-daemonset/issues/1464#:~:text=zlib%3A%20CVE,not%20built%2Din.) since affected code (MiniZip) is not built-in
