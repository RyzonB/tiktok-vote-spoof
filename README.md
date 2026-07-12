# TikTok Android App - Poll Vote Badge Injection

[Jump to example](#example)

## Overview

This repository documents a Client-Side UI Spoofing vulnerability discovered in the TikTok Android application. The flaw allows an attacker to inject arbitrary text into the "Voted" badge displayed on comments, bypassing the intended poll functionality.

**Platform:** TikTok (Android)

**CVSS v3.1 Score:** 7.7 (High)

**Vulnerability Type:** Improper Validation of Client-Side Parameters / UI Spoofing

**Status:** Disclosed & Validated (Duplicate)

## Vulnerability Details

When a user submits his/her vote within the poll embedded within a TikTok video, the client makes a request to the server to post the comment/vote. The client sends a specific parameter (for example, `vote_text`) in its request, which is used for determining the text of the "voted" badge.

The problem here is that the server does not verify this `vote_text` parameter based on the server-side options available for the poll within the video. Since the server takes the parameter value from the client request unconditionally, it can be changed by an attacker.

### Impact

However, despite not being capable of gaining unauthorized access to user accounts or database information, it is still quite risky since:

* **UI Spoofing**: It is possible to make users believe that attackers actually voted for whatever choice they wanted to.

* **Social Engineering**: It is worth noting that those badges are styled just like native ones from the TikTok UI, which means that it is possible to use them in order to mislead people or distribute some malicious information.

* **Context Irrelevance**: In spite of the lack of context, the injected information was rendered successfully.

## Proof of Concept

1. **Set Up the Environment:** Install and launch an Android emulator (such as Bluestacks, LD Player) on your machine.

2. **Bypass App Security:** Use a dynamic instrumentation toolkit like Frida to inject into the TikTok application upon startup. This bypasses the app's SSL pinning/security walls and exposes the network traffic.

3. **Intercept Network Traffic:** Open an HTTP proxy tool (like Charles) to capture and read the requests sent between the app and TikTok's servers.

4. **Identify the Comment Payload:** Post a standard comment on any video. In your proxy, observe the outgoing request. You will see standard fields such as the video ID and the comment text.

5. **Inject the Vote Parameter:** On a comment that has a genuine poll vote, TikTok includes a field called `user_vote_info`. Using your proxy tool or a custom Frida script, intercept your comment request and manually inject the `user_vote_info` field into the payload with your own custom text, even if the video has no poll.

6. **Forward the Request:** Send the modified request to the server. Because the backend does not validate whether the video actually contains a poll or if the text is a valid option, it accepts the manipulated payload.

7. **Observe the Results:** View the comment section from another device. The injected text will successfully render as an official "Voted" badge beneath your comment.

## Example

![Example ](comment.png)

## Responsible Disclosure Timeline

* **July 2026:** Vulnerability discovered and Proof of Concept developed.

* **July 2026:** Report submitted to TikTok  

* **July 2026:** Report triaged and validated by the TikTok Security Team.

* **July 2026:** Public release of this educational repository.

## Mitigation & Remediation

To resolve this issue, the backend server must strictly validate the `vote_text` parameter against the predefined, server-side list of valid poll options for the specific video ID. Any input that does not exactly match the expected options should be rejected, or the badge rendering should be suppressed entirely.

## Disclaimer

**This repository is intended for educational purposes, defensive security research, and documentation of a responsible disclosure.** The information provided here should not be used for any malicious activities, harassment, or to disrupt services. Always follow responsible disclosure guidelines and obtain proper authorization when testing applications. 

