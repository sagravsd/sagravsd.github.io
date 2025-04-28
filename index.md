---
layout: home
title: Mobile Forensics
categories: forensics, mobile
tags: ios, forensics
---
This blog is for informational purposes only, based on initial analysis and security testing of the data stored in the Word Trip app installed and used on an iOS mobile device.

---
# Initial Findings: Security Testing of Wordtrip Data on iOS
*Release 1*

---
This blog is for informational purposes only, based on initial analysis and security testing of the data stored in the Word Trip app installed and used on an iOS mobile device.

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
| **iLeapp**                 | 1.18.6     | Analyzing image, Protobuf parsing|
| **Cellebrite**             | 7.65.0.247 | Image collection                |
| **MacForensic Deserializer**| v1.5.1    | Analyze plists                  |
| **PList Editor**           | 1.9.7      | Analyze plists                  |
| **DB Browser for SQLite**  | 3.13.1    | Open SQLite database files      |
| **jsonformatter.org**      | -          | Format JSON data                |

## Analysis

As per our analysis, we can see that the majority of the user-related data is stored only in **2 database files** – cache and Apollo. Mainly, the portion accessed multiple times is retained in the phone’s storage in the form of cache for quick access. This section will go into detail on where Meetup stores its data on the device.

### Locations of Data Storage
The following locations had the most useful data stored in it:

- **/private/var/mobile/Containers/Data/Application/<App ID>/Library**
- **/private/var/containers/Bundle/Application/<App ID>/Meetup.app**

These folders contained app data, including caches, user data, and app metadata.

## References

Below is a list of software tools utilized for analysis and the collection of relevant artifacts.

| Software                  | Link    |
|---------------------------|------------|
| **Autopsy**                | https://www.autopsy.com/download/ |
| **DB Browser for SQLite**  | https://sqlitebrowser.org/dl/ |
| **iBackupViewer**          | https://ibackupviewer.com/           |
| **iLeapp**                 | https://github.com/abrignoni/iLEAPP |
| **3uTools**                | https://www.3u.com/|

