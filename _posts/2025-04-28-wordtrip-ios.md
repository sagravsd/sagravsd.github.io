---
layout: post
title: "Initial Findings: Security Testing of Word Trip Data on iOS"
date: 2025-04-28
categories: iOS forensics
---
*Release 1*

---
This blog aims to identify the recovered artifacts focusing on security risks involving privacy and data collection and retention, app permissions, and legal compliance. 

The type of data collected include:
- Personal Information (name, email address, IPs, etc.)
- Device Information
- Location history and GPS data
- System logs (app information and performance, device activity, app installs)
- Other data (timestamps, browser details, etc.)


## App Description

Word Trip is a word puzzle game app where you connect letters on a board to find hidden words. As you complete the puzzles, the puzzle games become more challenging. The user can earn points, use power-ups, and can compete with friends to see who can solve puzzles the fastest.

### App Privacy Policy

PlaySimple, creators of the Word Trip app, collect three main categories of data: data provided by users, data collected automatically, and data obtained from partners.

#### 1. Data Provided by the Player

This includes any personal information or content voluntarily submitted by the player while using the app:
- Player and Profile Information: Name (real or alias), contact details (e.g., email address, phone number), and profile photo.
- In-Game Communications: Messages and content submitted within the game, such as support tickets and chat logs.
- Account Recovery Information: Details provided by the player to help recover or verify ownership of a game account.
- Other Voluntary Submissions: Any additional personal information the player chooses to share during gameplay or through customer support interactions.

#### 2. Data Collected Automatically
This data is collected when the player interacts with app:
- Game Account Information: Account identifiers, game progress, and user preferences.
- Device and Network Information: Mobile device identifiers, IP address, device model, operating system, and language settings.
- Usage and Performance Data: Information related to gameplay behavior, in-app interactions, and application performance (e.g., crash reports).
- Location Data: Approximate geographic location inferred from the IP address.
- Cookies and Tracking Technologies: Data gathered through cookies and similar technologies used for analytics, personalization, fraud prevention, and advertising purposes.

#### 3. Data from Partners
The Word Trip app may receive data from external partners and service providers, depending on the player's interactions and privacy settings:
- Social Platform Integrations: Information shared via the player’s privacy settings on third-party platforms (e.g., Facebook, Google, Apple).
- Demographic and Location Data: Approximate demographic details or geographic location inferred through partner services.
- Advertising and Analytics Data: Information from advertising networks and analytics providers used to improve ad targeting and service quality.
- Payment and Purchase Verification: Transaction data from app stores or payment processors used to confirm purchases and prevent fraud.
- Fraud Detection and Prevention: Data used to identify suspicious behavior, reduce refund abuse, and improve overall security.

#### Sharing of Data:
Data may be shared with service providers, affiliates, advertisers, payment processors, other players (via social features), and authorities (if required).

## Testing Objectives and Analysis Procedures

### Application Details
- **Name**: Wordtrip
- **Version**: 1.446.0 v.2024.11.14 (632295)

### Device Details

The device used for testing this app was an **iPhone 8** (Model MQ6V2LL/A) with **iOS version 16.7.10**. A full file system image of this mobile was acquired for the analysis. To achieve this, we used **Autopsy** to analyze the full system image. Multiple images were taken at different times to check the difference in data stored and app behavior.

### Software Used

Due to the kind and amount of data in the iOS image, we used multiple software for our analysis. A link to all of these tools is mentioned in the reference section of the blog.

| Software                  | Version    | Purpose                          |
|---------------------------|------------|----------------------------------|
| **Autopsy**                | 4.21.0     | Analyzing image                 |
| **DB Browser for SQLite**  | 1.18.6     | Open SQLite database files|
| **iBackupViewer**          | 7.65.0.247 | View iOS device backups    |
| **iLeapp**| v1.5.1    | Analyzing image, Protobuf parsing                  |
| **3uTools**           | 1.9.7      | View iOS device backups               |

## Testing and Analysis

Testing of the app accounted for the fact that all requested permissions, including notification access, were granted. The app was used over a one-month period to allow sufficient time for data collection and domain interactions.

Analysis was conducted in accordance with the objectives specified in the test cases:

### 1. Personal Information
   - **Objective:** Ensure that personal information is collected, stored, and shared properly and securely.
   - **Procedure:** Check what information is collected and if it sent securely (using HTTPS or another secure method); check that the app has a clear privacy policy explaining how personal information is used and shared. Verify that uses can control what personal information they want to share and can turn off data collection.
   - **Expected Outcome:** Personal information is collected only when necessary, securely stored, and users know how their data is used; Users can manage or delete their personal information easily.
   - **Artifacts:**  Although secure transmission of data is mentioned and security practices are described, there is no explicit mention of HTTPS or TLS protocols for data transmission.

<p style="margin-left: 40px;">The x-bplist (binary property list) file contains the public IP address in the device used for this testing. This format is used to store structured data and is commonly employed by macOS and iOS applications to hold configuration settings (such as those for the app Word Trip), cached data, user preferences, and system information.</p>

<p style="margin-left: 40px;"><strong>Figure 1:</strong></p>
<img src="/img/P1.JPG" alt="IP" width="350" style="display: block; margin-left: 40px;">

<p style="margin-left: 40px;"><strong>Figure 2:</strong></p>
<img src="/img/P2.JPG" alt="IP" width="650" style="display: block; margin-left: 40px;">

<p style="margin-left: 40px;"><strong>Figure 3:</strong></p>
<img src="/img/P3.JPG" alt="IP" width="650" style="display: block; margin-left: 40px;">
  
### 2. Device Information
   - **Objective:** Ensure device information is collected and used securely. 
   - **Procedure:** Check what device information is collected (i.e. device type, OS, etc.) and ensure that the app asks for permission; Check that device IDs or other identifies are protected or anonymized.
   - **Expected Outcome:** Personal device information is collected only when needed, transmitted securely, and protected; Users can manage or delete their device information when they want.
   - **Artifacts:**
      - **Device identifiers are protected or anonymized:** The policy mentions data protection practices but does not state whether device IDs are anonymized or hashed before storage or transfer. There is no evidence indicating how this is achieved. 
      - **Secure transmission of device information:**  There is no clear mention of requesting user permission for collecting device information and the policy does not explicitly confirm the use of HTTPS or other secure transfer protocols. Evidence indicates that information is sent in clear text.  

### 3. Location history and GPS Data
   - **Objective:** Analyze GPS and location history data to determine the movements of the device user during the timeframe of interest. 
   - **Procedure:** Extract and analyze location data from apps (e.g., Google Maps, Apple Maps), system logs, and geotagged media.
   - **Analysis Points:** 
      - Locations visited (via GPS data or app history)
      - Dates and times of location data
      - Patterns of movement
   - **Expected Outcome:** A map or list of locations visited, along with timestamps indicating when the user was at each location.
   - **Artifacts:**
      - The privacy policy intentionally limits location collection to only the minimum necessary (approximate via IP), however, evidence suggests.

### 4. System Logs 
   - **Objective:** Extract and analyze system logs, including device activity logs and app installs and usage history. 
   - **Procedure:** Retrieve and analyze logs from the device (e.g., app installs, device settings changes).
   - **Analysis Points:** 
      - Date and time of app installations/uninstallations
      - System logs for device behavior
      - Any errors or abnormal activity
   - **Expected Outcome:** A record of app installations, deletions, and system activity related to the time of interest.
   - **Artifacts:**
      - **App installation/uninstallation timestamps:** The policy does not mention collecting logs about when apps are installed or uninstalled. However, evidence indicates that there is a record of this.
      - **System logs or device behavior history:** There is no reference collecting system-level logs or OS activity like settings changes, crashes outside the app, or device usage history.  
      - **Errors or abnormal activity (in-app):** The policy references collecting "application performance and debugging information" — likely limited to app-level crash reports or in-game errors.  
      - **General device log retrieval or system diagnostics:** No reference to full system diagnostics or general device logs beyond the scope of the app.  
     
### 5. Other Data
   - **Objective:** Review app data to identify any relevant evidence from timestamps, browser details, etc. 
   - **Procedure:** As appropriate, extract app-specific data, such as timestamps, browser details, account information, and usage patterns.
   - **Analysis Points:** 
      - App activity logs
      - Account data 
   - **Expected Outcome:** A list of relevant data not otherwise defined that can demonstrate insecure endpoints or risks associated with using the app.
   - **Artifacts:**
      - **App activity logs (e.g., timestamps, usage):** The policy mentions collecting gameplay data, interactions with others, and performance/debugging information. However, it does not clarify whether detailed timestamps or event-level logs are captured.
      - **Browser details:** There is no reference collecting system-level logs or OS activity like settings changes, crashes outside the app, or device usage history.  
      - **Account data:** The policy references that player name, profile, contact info, game account details, and purchase history are collected.  
      - **Usage patterns:** The policy mentions using analytics to monitor gameplay and interactions, but does not specify granularity (e.g., session logs, clickstreams).  
      - **Evidence of insecure endpoints or risks:** There's no mention of analyzing or disclosing potentially insecure endpoints, nor any user-facing tools to review such risks. Also, there is no evidence to suggest otherwise.

### Locations of Data Storage
The following locations had the most useful data stored in it:

- **/private/var/mobile/Containers/Data/Application/<App ID>/Library**
- **/private/var/containers/Bundle/Application/<App ID>/Meetup.app**

These folders contained app data, including caches, user data, and app metadata.

## References

Below is a list of software tools utilized for analysis and the collection of relevant artifacts.

| Software                 | Link    |
|--------------------------|------------|
| **Autopsy**              | https://www.autopsy.com/download/ |
| **DB Browser for SQLite**| https://sqlitebrowser.org/dl/ |
| **iBackupViewer**        | https://ibackupviewer.com/           |
| **iLeapp**               | https://github.com/abrignoni/iLEAPP |
| **3uTools**              | https://www.3u.com/|

