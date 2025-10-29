# SPOOFWATCH: Investigating Phishing Attacks

## Overview

As a cybersecurity analyst collaborating with **Cyblack** Security Operations Center (SOC) team, I conducted an in-depth investigation into a suspicious email flagged by the company’s email gateway on **August 16, 2023**. The email, claiming to originate from the *"Microsoft account team"*, exhibited unusual language and phishing indicators, leading to its automatic quarantine. This project, **SPOOFWATCH**, documents the detailed analysis of the raw email to determine its legitimacy, identify spoofing indicators, and provide actionable recommendations to prevent future phishing attacks.

This repository contains the executive summary, methodology, findings, and mitigation strategies derived from the investigation. The analysis confirmed the email as a **spoofed phishing attempt**, and the recommendations aim to strengthen organizational defenses against similar threats.

## Project Objective

The goal of this investigation was to analyze the raw email file to determine whether it was **legitimate**, **spoofed**, or **spam** by addressing the following questions:

1. Identify the **originating IP address**, its inferred location, and list all IP addresses in the `Received` headers.
2. Evaluate **SPF**, **DKIM**, and **DMARC** results and their implications.
3. Examine the email’s server path for discrepancies or spoofing indicators.
4. Verify the stated sender and domain alignment with **Microsoft**.
5. Assess authentication headers (`X-MS-Exchange-Organization-AuthAs` and `AuthSource`).
6. Determine the legitimacy of the **subject** and `Reply-To` address.
7. Analyze **timestamps** for discrepancies.
8. Provide a **resolution** and **mitigation recommendations**.

## Methodology

The analysis was conducted using a structured approach:

1. **Environment and Tools**:
- **Platform**: Ubuntu-based Linux system.
- **Tools**:
- *Mozilla Thunderbird* (email client) for viewing raw email source.
- *Sublime Text* (text editor) for header and content analysis.
- **Process**: Headers and body were extracted and analyzed for phishing indicators, including tracking pixels, suspicious links, and sender discrepancies.
2. **External Validation**:
- GeoIP lookup using `geoiplookup`/`geoip-bin` to identify the originating IP’s location and ownership.

## Key Findings

1. **Originating IP and Geolocation**:
- **IP**: `89.144.44.41` (Ghostnet GmbH, Germany, AS58212).
- **Inference**: The IP is *not* part of Microsoft’s infrastructure, indicating an external, non-legitimate source.
- **Received Header IPs** (in transmission order):
1. `89.144.44.41` (external origin, `atujpdfghher.co.uk`)
2. `10.13.30.233` (Microsoft edge)
3. `2603:10b6:303:85:cafe::78` (Microsoft protection)
4. `2603:10b6:303:85::34` (Microsoft internal)
5. `2603:10b6:208:38b::5` (Microsoft internal)
6. `::1` (localhost, Microsoft internal)

2. **SPF, DKIM, and DMARC**:
- **SPF**: `None` (no valid SPF record for `atujpdfghher.co.uk`).
- **DKIM**: `None` (no signature; Microsoft emails are typically signed).
- **DMARC**: `permerror` (misconfigured or missing policy).
- **Implication**: Failed authentication checks confirm the email as unauthorized.

3. **Email Path**:
- The email originated from an external, non-Microsoft domain (`atujpdfghher.co.uk`) and passed through Microsoft’s protection layer without internal authentication.
- No forged headers were detected, but the external origin is a strong spoofing indicator.

4. **Sender and Domain**:
- **Display Name**: `Microsoft account team <no-reply@access-accsecurity.com>`.
- **Domain**: `access-accsecurity.com` (*not* a Microsoft domain).
- **Conclusion**: Clear impersonation of Microsoft.

5. **Authentication Headers**:
- **X-MS-Exchange-Organization-AuthAs**: `Anonymous` (sender not authenticated).
- **X-MS-Exchange-Organization-AuthSource**: Microsoft edge gateway (indicates acceptance, not validation).
- **Implication**: The email was not sent from a trusted Microsoft source.

6. **Subject and Reply-To**:
- **Subject**: *"Microsoft account unusual signin activity"* (mimics legitimate alerts).
- **Reply-To**: `solutionteamrecognizd03@gmail.com` (Gmail address, highly suspicious).
- **Body**: Contains a tracking pixel (`http://thebandalisty.com/track/...`) and attacker-controlled links.
- **Conclusion**: Social-engineering tactics designed to deceive users.

7. **Timestamps**:
- No significant discrepancies in `Received` header timestamps (clustered within seconds).
- No evidence of manipulation, though other indicators dominate.

8. **Spam Indicators**:
- **SCL**: 5 (moderate spam likelihood).
- **BCL**: 6 (bulk characteristics).
- Presence of tracking pixel and mismatched `Reply-To` confirm malicious intent.

## Resolution

The email is a **spoofed phishing attempt** impersonating a Microsoft security notification. Key evidence includes:

- Non-Microsoft originating IP (`89.144.44.41`, Ghostnet GmbH, Germany).
- Failed **SPF**, **DKIM**, and **DMARC** checks.
- Suspicious `Reply-To` (Gmail) and tracking pixel.
- Non-Microsoft domain (`access-accsecurity.com`).

The email gateway’s quarantine action was appropriate, preventing potential compromise.

## Recommendations

To mitigate future spoofed emails, the following measures are recommended:

1. **Gateway and Policy Enforcement**:
- Quarantine or reject emails with `SPF=none` or `DMARC=permerror`.
- Enforce strict DMARC policies (`p=quarantine` or `p=reject`).
- Enable link and attachment sandboxing.

2. **Authentication and Domain Hygiene**:
- Ensure corporate domains have valid **SPF** and **DKIM** records.
- Publish **DMARC** policies with reporting enabled.

3. **Blocking and Allowlisting**:
- Block malicious IPs (`89.144.44.41`) and domains (`atujpdfghher.co.uk`, `access-accsecurity.com`).
- Rely on authentication protocols over static allowlists.

4. **User Awareness**:
- Conduct phishing training on spotting domain mismatches and suspicious `Reply-To` addresses.
- Implement a *“report-to-SOC”* button in email clients.

5. **Monitoring and Detection**:
- Enable **DMARC** aggregate/forensic reporting.
- Review quarantine logs and SCL/BCL trends weekly.

6. **Account Security**:
- Enforce **multifactor authentication (MFA)**.
- Disable automatic link-clicking and remote image loading.

## Conclusion

The **SPOOFWATCH** investigation, conducted with *Cyblack*, confirmed the email as a phishing attempt using robust analysis with *Mozilla Thunderbird* and *Sublime Text*. The findings highlight the importance of email authentication protocols (**SPF**, **DKIM**, **DMARC**) and proactive gateway configurations in detecting and mitigating phishing threats. The recommended measures will enhance the organization’s resilience against future attacks.

## Acknowledgments

This project was a collaborative effort with the **Cyblack SOC team**. Special thanks to the team for their expertise and support in analyzing this phishing attempt.

## Contact

For questions or contributions, please contact me here via GitHub.

---

**Stay vigilant, stay secure!**
*Project created by [Neptune430] in collaboration with Cyblack.*
