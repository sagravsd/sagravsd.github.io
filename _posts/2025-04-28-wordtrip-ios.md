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
- Personal Information (name, email address, IP, etc.)
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
- **Name**: Word Trip
- **Version**: 1.446.0 v.2024.11.14 (632295)

### Device Details

The device used for testing this app was an iPhone 8 (Model MQ6V2LL/A) with iOS version 15.4.1. A full file system image of this mobile device was acquired for the analysis. To achieve this, we used Autopsy to analyze the full system image. Multiple images were taken at different times to check the difference in data stored and app behavior.

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
   - **Procedure:** Check what information is collected and if it sent securely (using HTTPS or another secure method); check that the app has a clear privacy policy explaining how personal information is used and shared. Verify that users can control what personal information they want to share and can turn off data collection.
   - **Expected Outcome:** Personal information is collected only when necessary, securely stored, and users know how their data is used; Users can manage or delete their personal information easily.
   - **Artifacts:**  Although the privacy policy states that personal information is collected only when necessary, outlines the purpose of data collection, and transparently discloses data sharing practices, it does mention secure data transmission and describes general security practices. However, it does not explicitly reference the use of HTTPS or TLS protocols for data transmission.

<p style="margin-left: 30px;">The artifacts listed below further demonstrate whether this objective is being met.</p>
   
<p style="margin-left: 30px;">The x-bplist (binary property list) file contains the public IP address in the device used for this testing. This format is used to store structured data and is commonly employed by macOS and iOS applications to hold configuration settings (such as those for the app Word Trip), cached data, user preferences, and system information.</p>

<p style="margin-left: 30px;"><strong>Figure 1:</strong></p>
<img src="/img/P1.JPG" alt="IP" width="350" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;"><strong>Figure 2:</strong></p>
<img src="/img/P2.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;"><strong>Figure 3:</strong></p>
<img src="/img/P3.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">The image below shows data collected by Word Trip.</p>
                                                                                                      
<p style="margin-left: 30px;"><strong>Figure 4:</strong></p>
<img src="/img/P4.JPG" alt="IP" width="1500" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">Table 1 below provides information of important keys and the potential information it collects.</p>

<p style="margin-left: 30px;"><strong>Table 1:</strong></p>
   
| **Key**                                       | **Potential Data Collected**                                                                 |
|----------------------------------------------|-----------------------------------------------------------------------------------------------|
| **com.fireperf.fpr_rl_network_request_event_count_fg** | Count of network requests in foreground, may include method, timing, and response status     |
| **com.unity.ads.lastKnownUserAgent**         | Last known user agent (device info, OS, browser)                                             |
| **BIDDER_TOKEN_EXTRAS**                      | User IDs such as public ID, session info, or targeting parameters |
| **com.facebook.sdk:FBSDKSettingsInstallTimestamp** | Timestamp of Facebook initialization, used for attribution and analytics                          |
| **lastRequestedAttributionsDetailsTimeStamp** | Timestamp of last request for ad attribution data                                            |
| **com.fireperf.fpr_log_source**              | Log source ID (e.g., iOS) to help route performance logs                              |
| **com.fireperf.fpr_enabled**                 | Boolean flag indicating if performance monitoring is enabled                                 |
| **MTGImageCachedTransformed**                | Transformed image cache keys or metadata (e.g., timestamps or image variants)                |




<p style="margin-left: 30px;">As seen in Figure 5 below, the first row includes a key string value labeled “GDPR_Consent,” indicating that the app requires user consent in accordance with the General Data Protection Regulation (GDPR). This applies to citizens of the European Union (EU) and to all users, as the app is available worldwide. Prompting for this consent when the app is downloaded on an iPhone helps ensure the app complies with GDPR by notifying users appropriately.</p>

<p style="margin-left: 30px;"><strong>Figure 5:</strong></p>
<img src="/img/P5.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">In addition, the “CCPA_Privacy” key (referring to the California Consumer Privacy Act) has an empty string value. This suggests that no consent has been recorded or acknowledged from users who download the app. If this is the case, it may indicate non-compliance with CCPA requirements for users residing in California.</p>

<p style="margin-left: 30px;">This figure also shows that the iOS version at the time the app was last launched is collected.</p>

<p style="margin-left: 30px;"><strong>Figure 6:</strong></p>
<img src="/img/P6.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">Figure 7 and Table 2 below augment Figure 4 and Table 1, providing the public ID, session information timestamp, and the timestamp of the last request for ad attribution data.</p>

<p style="margin-left: 30px;"><strong>Figure 7:</strong></p>
<img src="/img/P7.JPG" alt="IP" width="850" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;"><strong>Table 2:</strong></p>

| **Key**                                       | **Potential Data Collected**                                                                 |
|----------------------------------------------|-----------------------------------------------------------------------------------------------|
| **BIDDER_TOKEN_EXTRAS** | 1743726856: Monday, 5 May 2025 17:47:36 UTC and Monday, 5 May 2025 12:47:36 EST     |
| **lastRequestedAttributionsDetailsTimeStamp**         | 1743726474660: Monday, 5 May 2025 17:47:54 UTC and Monday, 5 May 2025 12:47:54 EST   |


### 2. Device Information
   - **Objective:** Ensure device information is collected and used securely. 
   - **Procedure:** Check what device information is collected (i.e. device type, OS, etc.) and ensure that the app asks for permission; Check that device IDs or other identifiers are protected or anonymized.
   - **Expected Outcome:** Personal device information is collected only when needed, transmitted securely, and protected; Users can manage or delete their device information when they want.
   - **Artifacts:**
      - **Device identifiers are protected or anonymized:** The privacy policy mentions data protection practices but does not state whether device IDs are anonymized or hashed before storage or transfer. There is no evidence indicating how this is achieved. Evidence indicates that information is sent in clear text.
      - **Secure transmission of device information:**  As shown in Figure X, the ZACCOUNTPROPERTY table indicates whether a user has agreed to the terms of service for the Word Trip app. Furthermore, the application's privacy policy does not explicitly confirm the use of HTTPS or other secure data transfer protocols. Available evidence suggests that user information is transmitted in clear text, as demonstrated by the artifacts presented.

<p style="margin-left: 30px;">In addition, the Word Tripp application was found to log multiple device- and user-specific data points, including the iOS version, web user agent strings, IMSI/IMEI numbers, ICCID, phone number, model identifier, and time zone configuration. These artifacts were identified using 3uTools (for hardware and OS-level information) and iLEAPP (for SIM-related identifiers and cellular metadata). Application state transitions, crash event counts, session durations, and timestamps related to app suspension were recovered from plist and SQLite database files, as confirmed via 3uTools and DB Browser for SQLite.</p>

### 3. Location history and GPS Data
   - **Objective:** Analyze GPS and location history data to determine the movements of the device user during the timeframe of interest. 
   - **Procedure:** Extract and analyze location data from apps (e.g., Google Maps, Apple Maps), system logs, and geotagged media.
   - **Analysis Points:** 
      - Locations visited (via GPS data or app history)
      - Dates and times of location data
      - Patterns of movement
   - **Expected Outcome:** A map or list of locations visited, along with timestamps indicating when the user was at each location.
   - **Artifacts:**
      - The privacy policy intentionally limits location collection to only the minimum necessary (approximate via IP).
      - Location Access Evidence: The evidence in figures x through x shows that the Word Trip app has access to location services and may record data such as IP-based approximate location and possibly GPS data. As a result, location data is be accessible through app logs and system files.
      - Timestamps: The presence of timestamps (like ts: 1743726856) associated with app launches or IP addresses allows for some behavioral correlation with time.

<p style="margin-left: 30px;">Based on analysis of the x-bplist file, the Word Trip app collects metadata including the user's IP address, web browser data, and iOS version. This is evident in the BIDDER_TOKEN_EXTRAS key, which contains a JSON-like string such as {"ip": "96.255.xx.xxx", "ts": 1743726856}. The IP address allows the app to estimate the user’s general location via geolocation techniques, potentially down to the city or neighborhood level.</p>

<p style="margin-left: 30px;">The app can combine this IP address with the application launch timestamp (ts) to correlate user behavior with specific location data points.</p>

<p style="margin-left: 30px;">Using BackupViewer, additional evidence was found:</p>

<p style="margin-left: 30px;">The client.plist file located at System > root > Library > Caches > locationd includes a key named com.littleengine.wordtreat, indicating the internal name used by the Word Trip app. This entry has a SupportedAuthorizationMask value of 3, meaning the app can request both "When In Use" and "Always" location permissions, allowing for flexible access depending on user settings and app behavior.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P25.JPG" alt="IP" width="750" style="display: block; margin-left: 30px;">


<p style="margin-left: 30px;">The ZProcess table in System > WirelessDomain > Library > Databases shows entries such as ZBUNDLENAME = "com.apple.datausage" and ZPROCNAME values containing "locationd", referring to the iOS daemon responsible for handling location services. This contextualizes how location access is managed at the system level.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P26.JPG" alt="IP" width="850" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">The csidata file at System > WirelessDomain > Library > Preferences contains flags like "VoWiFiLocationEvaluated", "WifiCallingLocationAuthorization", "SimInfo", and "GsmSettingsModel", suggesting the device and its apps—such as Word Trip—are configured to interface with location services.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P27.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">In conclusion, coordinate logs extracted from Apple’s location caches confirm that Word Trip has accessed or at least been granted permissions to request GPS location data under both "When In Use" and "Always" settings.</p>
    

### 4. System Logs 
   - **Objective:** Extract and analyze system logs, including device activity logs and app installs and usage history. 
   - **Procedure:** Retrieve and analyze logs from the device (e.g., app installs, device settings changes).
   - **Analysis Points:** 
      - Date and time of app installations/uninstallations
      - System logs for device behavior
      - Any errors or abnormal activity
   - **Expected Outcome:** A record of app installations, deletions, and system activity related to the time of interest.
   - **Artifacts:**
      - **App installation/uninstallation timestamps:** The privacy policy does not mention collecting logs about when apps are installed or uninstalled. However, evidence indicates that there is a record of this.
      - **System logs or device behavior history:** There is no reference collecting system-level logs or OS activity like settings changes, crashes outside the app, or device usage history.  
      - **Errors or abnormal activity (in-app):** The privacy policy references collecting "application performance and debugging information" — likely limited to app-level crash reports or in-game errors.  
      - **General device log retrieval or system diagnostics:** No reference to full system diagnostics or general device logs beyond the scope of the app.  

<p style="margin-left: 30px;">The figure below shows the Word Trip database imported from Autopsy into DB Browser, including the tables and indices it contains.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P9.JPG" alt="IP" width="1500" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">Figure x and Table 3 below shows the keys related to social account integration settings used in the Accounts or Social framework on Apple platforms to configure access to social media services. Apps like Word Trip use these keys when requesting access to social accounts configured on the user's device, allowing access to services such as Facebook, LinkedIn, Tencent Weibo, and others.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P10.JPG" alt="IP" width="450" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;"><strong>Table 3:</strong></p>

| **ZNAME (Key Name)**                       | **What It Is**                                 | **Purpose / Use**                                                                 |
|------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------|
| **ACFacebookAppIdKey**       | Facebook App ID                                  | Specifies the Facebook App ID registered for the app, used to access Facebook API |
| **ACFacebookPermissionsKey** | Facebook Permissions                             | Array of permissions the app is requesting (e.g., email, friends list)            |
| **ACLiverpoolContainerId**   | Possibly internal or enterprise-specific         | Could be a container or identifier for a service named "Liverpool" (not standard) |
| **ACFacebookAudienceKey**    | Facebook audience type                           | Defines the audience for posts (e.g., `OnlyMe`, `Friends`, `Everyone`)            |
| **ACLinkedInPermissionsKey** | LinkedIn Permissions                             | Array of permissions requested for LinkedIn integration                           |
| **ACLinkedInAppIdKey**       | LinkedIn App ID                                  | LinkedIn App ID used for authenticating with LinkedIn API                         |
| **ACTencentWeiboAppIdKey**   | Tencent Weibo App ID                             | App ID for Tencent Weibo integration (Chinese social media platform)              |

<p style="margin-left: 30px;">Furthermore, as shown in Figure X below, analysis of the ZACCOUNT table reveals that email addresses are being collected. Although the privacy policy acknowledges this collection, the data is stored in clear text. Please refer to section '1. Personal Information' above. </p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P11.JPG" alt="IP" width="650" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">Figure X below reveals that the ZACCOUNTPROPERTY table shows whether the user has agreed to the terms of service for using the Word Trip app.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P12.JPG" alt="IP" width="750" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">Also, as shown in Figures x and x below, analysis of the ObservedDomains table reveals that the Word Trip app collects and accesses Internet domains, as recorded in the figure. Several registrable domains were identified, illustrating the endpoints the app communicates with. The figure also indicates whether data transmissions are secure and highlights any potential risks associated with insecure communication channels. I manually verified each domain by visiting the listed websites and confirmed that all were secure and did not pose a risk of insecure communication.</p>

<p style="margin-left: 30px;">The “hadUserInteraction” field, when set to “1,” indicates that the iPhone user directly interacted with a site—for example, by clicking a link or advertisement within the app that triggered a request to that domain. A value of “0” suggests that the Word Trip application initiated contact with the domain without user interaction, likely as part of background processes or core application functionality.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P13.JPG" alt="IP" width="750" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P14.JPG" alt="IP" width="750" style="display: block; margin-left: 30px;">

<p style="margin-left: 30px;">As shown in Figure x, the Word Trip app collects the dates it was launched, as recorded in the 'OperatingDates' table.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>
<img src="/img/P14b.JPG" alt="IP" width="450" style="display: block; margin-left: 30px;">

### 5. Other Data
   - **Objective:** Review app data to identify any relevant evidence from timestamps, browser details, etc. 
   - **Procedure:** As appropriate, extract app-specific data, such as timestamps, browser details, account information, and usage patterns.
   - **Analysis Points:** 
      - App activity logs
      - Account data 
   - **Expected Outcome:** A list of relevant data not otherwise defined that can demonstrate insecure endpoints or risks associated with using the app.
   - **Artifacts:**
      - **App activity logs (e.g., timestamps, usage):** The privacy policy mentions collecting gameplay data, interactions with others, and performance/debugging information. However, it does not clarify whether detailed timestamps or event-level logs are captured.
      - **Browser details:** The privacy policy does not mention the collection of browser-specific data (e.g., browser information, user agent strings, or visited URLs). However, Figures X, X, and X show that this type of data is being collected.  
      - **Account data:** The privacy policy references that player name, profile, contact info, game account details, and purchase history are collected.  
      - **Usage patterns:** The privacy policy mentions using analytics to monitor gameplay and interactions, but does not specify granularity (e.g., session logs, clickstreams).  
      - **Evidence of insecure endpoints or risks:** There is no mention of analyzing or disclosing potentially insecure endpoints, nor any user-facing tools to review such risks. Also, there is no evidence to suggest otherwise.

<p style="margin-left: 30px;">In addition to the data being logged as listed in the Device Information, further metadata—including application launch timestamps, epoch-based activity markers, and advertising telemetry such as ad unit identifiers, skip timing parameters, and mediation flags—was primarily extracted through Autopsy’s plist analysis. These records indicate persistent behavioral tracking and highlight the app’s capacity for device fingerprinting through embedded SDKs such as Unity Ads and AppLovin </p>

<p style="margin-left: 30px;">This figure also shows that the web browser version used to access the app-related sites is being collected.</p>

<p style="margin-left: 30px;"><strong>Figure x:</strong></p>

<img src="/img/P8.JPG" alt="IP" width="750" style="display: block; margin-left: 30px;">

## Summary
### Device and User Metadata
   Artifacts collected from Word Tripp include:
- iOS version
- Web user agent strings
- IMSI and IMEI numbers
- ICCID (Integrated Circuit Card Identifier)
- User phone number
- Device model identifier
- Time zone settings

These were identified via 3uTools and iLEAPP, confirming the app’s access to and retention of sensitive device-level and SIM-related identifiers.

### Application Behavior Logging
Through plist and database inspection, the following behavioral data was identified:

   - App state transitions (e.g., foreground/background changes)
   - Crash event counts
   - Session durations
   - Timestamps for app suspensions
   - These data points were found in both property list files and SQLite databases, as revealed by analysis with 3uTools and DB Browser for SQLite.

### Advertising and Analytics Telemetry
Additional metadata related to in-app advertising and analytics includes:

   - Launch timestamps
   - Epoch-based activity markers
   - Ad configuration values, including:
   - Ad unit IDs
   - Skip timers
   - Mediation flags

Autopsy's plist analysis revealed that these elements are linked to advertising SDKs, notably Unity Ads and AppLovin MAX, indicating a capacity for detailed user behavior tracking and potential device fingerprinting.

## References

Below is a list of software tools utilized for analysis and the collection of relevant artifacts.

| Software                 | Link    |
|--------------------------|------------|
| **Autopsy**              | https://www.autopsy.com/download/ |
| **DB Browser for SQLite**| https://sqlitebrowser.org/dl/ |
| **iBackupViewer**        | https://ibackupviewer.com/           |
| **iLeapp**               | https://github.com/abrignoni/iLEAPP |
| **3uTools**              | https://www.3u.com/|

