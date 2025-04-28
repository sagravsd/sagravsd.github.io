---
layout: post
title: "Initial Findings: Security Testing of Word Trip Data on iOS"
date: 2025-04-28
categories: iOS forensics
---
*Release 1*

---
This blog aims to identify the recovered relevant artifacts focusing on security risks involving privacy and data collection and retention, app permissions, and legal compliance. 

The type of data collected include:
- Personal Information (name, email address, IPs, etc.)
- Device Information
- Location history and GPS data
- System logs (app information and performance, device activity, app installs)
- Other data (timestamps, browser details, etc.)


## App Description

Word Trip is a word puzzle game app where you connect letters on a board to find hidden words. As you complete the puzzles, the puzzle games become more challenging. The user can earn points, use power-ups, and can compete with friends to see who can solve puzzles the fastest.

## Testing Objectives and Analysis Procedures

### Application Details
- **Name**: Wordtrip
- **Version**: 1.446.0 v.2024.11.14 (632295)

### Device Details

The device used for testing this app was an **iPhone 6s** (Model MN1M2LL/A) with **iOS version 15.8.3**. A full file system image of this mobile was acquired for the analysis. To achieve this, we used **Cellebrite**, which ran the **checkra1n jailbreak** to gain privileged access to the phone to get all possible files. Multiple images were taken at different times to check the difference in data stored in cache.

### Software Used

Due to the kind and amount of data in the iOS image, we used multiple software for our analysis. A link to all of these tools is mentioned in the reference section of the blog.

| Software                  | Version    | Purpose                          |
|---------------------------|------------|----------------------------------|
| **Autopsy**                | 4.21.0     | Analyzing image                 |
| **DB Browser for SQLite**  | 1.18.6     | Open SQLite database files|
| **iBackupViewer**          | 7.65.0.247 | View iOS device backups    |
| **iLeapp**| v1.5.1    | Analyzing image, Protobuf parsing                  |
| **3uTools**           | 1.9.7      | View iOS device backups               |

## Analysis

The analysis focused on these objectives:

### 1. Personal Information
   - **Objective:** Ensure that personal information is collected, stored, and shared properly and securely. 
   - **Expected Outcome:** Personal info is collected only when necessary, securely stored, and users know how their data is used; Users can manage or delete their personal information easily.
   - **Actual Outcome:**


### Locations of Data Storage
The following locations had the most useful data stored in it:

- **/private/var/mobile/Containers/Data/Application/<App ID>/Library**
- **/private/var/containers/Bundle/Application/<App ID>/Meetup.app**

These folders contained app data, including caches, user data, and app metadata.

## References

Below is a list of software tools utilized for analysis and the collection of relevant artifacts.

| Software                 | Link    |
|--------------------------|------------|
| **Autopsy**                | https://www.autopsy.com/download/ |
| **DB Browser for SQLite**| https://sqlitebrowser.org/dl/ |
| **iBackupViewer**        | https://ibackupviewer.com/           |
| **iLeapp**               | https://github.com/abrignoni/iLEAPP |
| **3uTools**              | https://www.3u.com/|

