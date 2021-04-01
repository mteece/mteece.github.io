---
layout: post
title: App Store Connect API + Fastlane Match
subtitle : No generic Apple ID's for you!
tags: [mobile, ios, apple, app-store-connect, api, fastlane]
author: Matthew Teece
comments : False
---

At [Tribute](https://tribute.co) we're expanding our brand and branching out into mobile on iOS and Android. As the project kicked off and active development began in the fall, the topic of continuous integration and deployment had come up. For all of our web projects, we already use [CircleCI](https://circleci.com/) so it was obviously our first choice to use that platform. Given my background in mobile development and mobile devops the task was delegated to me.

Over the course of a sprint I scaffoled the configurations around CircleCI, Fastlane, Firebase App Distribution, and TestFlight. One key aspect that is always a challenge is setting up the authorization between continuous integration platforms and Apple's stack. Apple Developer and App Store Connect respectively. [Fastlane Match](https://docs.fastlane.tools/actions/match/) is a tool designed to keep iOS developers and mobile teams productive; not bashing keyboards with code-signing issues.

The typical glue between Fastlane Match, Apple Developer, and App Store Connect is a generic account tied to your organizations domain. An email alias or group like **devops@example.com** or **ci@example.com**. This is even noted in the [CircleCI App Store Connect documentation](https://circleci.com/docs/2.0/deploying-ios/#app-store-connect). However over the last 12 months Apple has frowned upon using such a pattern. You may recall that Apple Developer and App Store Connect users were recently merged together. The result being that generic accounts need to be actual email accounts. No generic email address, alias. How I found out about this firsthand was actually quite frustrating.

I created a generic email account akin to **devops@example.com** or **ci@example.com**. Set the alias up, and was able to start sending and receiving email. Then I created an Apple ID and used an old iPhone for the 2FA. Next up was inviting the generic account to App Store Connect. Straightforward, right up until the invite was received. Once the invite URL was clicked, I constantly saw a *500 Error*. I went in made a new generic account, encountered the same result.

I opened a ticket with App Store Connect support. I fell silent, and in the meantime I thought I would get clever. This is where [Maurice Moss](https://theitcrowd.fandom.com/wiki/Maurice_Moss) comes into play. So rather than a generic account, I just made the fictional **Maurice Moss** a legitimate account (he's an employee now) with a corresponding Apple ID. I went through the whole invitations process once more and encountered the same blocking result. So I opened another ticket with App Store Connect support.

At this stage I am talking on the phone with App Store Connect support, and masquerading trying to help my fictional employee Maurice Moss get unblocked and obtain access to App Store Connect. I tried my best to pretend that Maurice and I were in Slack troubleshooting. In fact I even had two computers to keep the smoke and mirrors up. One of the WiFi and the other on my iPhone's hotspot in case they were logging ip-address's. Sure enough they were onto my ruse. Once I had my collegue in South America help out. It got kind of obvious and I relented.

So what is the point of all the shenanigans and tomfoolery? At this stage if you have an App Store Connect generic account already setup (prior to 2020) then you're likely all set. Otherwise generic account are not an option. I recommend using the [App Store Connect API](https://appstoreconnect.apple.com/access/api) and generating an API key that allows you to configure, authenticate, and use one or more Apple services for that key. It has various levels of access and you can create more than one.

When you generate an API key, there will be a JSON file associated with it (commonly *api_key_info*). You can then tie that together with [Fastlane Pilot](https://docs.fastlane.tools/actions/pilot/#usage) as an optional parameter to the `upload_to_testflight( ... )` command. For example:

    upload_to_testflight(
      api_key_path: "./fastlane/../../entitlements/api_key_info.json",
      skip_waiting_for_build_processing: true,
      skip_submission: true,
      ipa: IPA_OUTPUT_PATH
    )

Once everything was implemented, we were then on our way to automating all of our iOS builds and distribution. Thanks for your help Maurice.


### Read More ###
- https://docs.fastlane.tools/app-store-connect-api/
- https://appstoreconnect.apple.com/access/api