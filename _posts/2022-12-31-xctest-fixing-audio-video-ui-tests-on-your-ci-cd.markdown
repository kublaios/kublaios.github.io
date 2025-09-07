---
id: 238
title: 'Fixing XCTest audio/video issues on CI/CD'
date: '2022-12-31T20:56:46+00:00'
author: 'Kubilay Erdogan'
layout: post
guid: 'https://www.kubilayerdogan.net/?p=238'
permalink: /xctest-fixing-audio-video-ui-tests-on-your-ci-cd/
categories:
    - 'Blog Posts'
tags:
    - CI/CD
    - iOS
    - Swift
    - 'UI Testing'
    - XCTest
---

You can use UI tests to verify the behavior of audio or video players in your app, but they may fail on a CI/CD environment. You can find the fix in this post.

Before going any further, let's quickly get to the bottom of the issue. CI providers use virtual machines without sound because they are primarily used for running automated tests and building software projects, rather than for tasks that require audio output. When test runner opens the media player on your app, the app will do nothing. Interestingly enough, it won't even crash -simply will remain silent on the player screen (pun intended).

One fix I found on the internet is attaching a null audio driver before running UI tests. Here's how I do it in my CircleCI config:

```
run-ui-tests:
  steps:
    - run:
        name: Attach null audio driver
        command: |
          git clone --depth 1 --branch v0.1.1 https://github.com/kyleneideck/BackgroundMusic.git
          cd BackgroundMusic
          sudo xcodebuild -project BGMApp/BGMAppTests/NullAudio/AudioDriverExamples.xcodeproj -target NullAudio DSTROOT="/" install
          sudo launchctl kickstart -kp system/com.apple.audio.coreaudiod || sudo killall coreaudiod
          cd .. && sudo rm -rf BackgroundMusic
```

What we are doing here is quite straightforward:

- We clone a repository named [BackgroundMusic](https://github.com/kyleneideck/BackgroundMusic). We use a specific version as they seem to have removed the code we need in the following versions.
- We build the NullAudio project and attach it to the system
- We delete the code that's no longer needed.

With this fix, your UI tests should run as they do locally. One important thing to note here is that we are using an old version of the repository from 5 years ago and you might encounter warnings on your CI when building the audio driver project (might even crash on the future versions of MacOS). I'll be sure to update here if I'll have to come up with a fix for that.

I created a small project which demonstrates this scenario and has the fix. [Here's the GitHub link](https://github.com/kublaios/UITestsWithSoundOutput).

Credits

- [The original support ticket on CircleCI forums](https://discuss.circleci.com/t/sound-output-on-device/13579)
- [TravisCI example (1)](https://github.com/travis-ci/travis-yml/blob/f25fae51bc8aaab4d0792c6f14d5e26435f2a9ec/spec/fixtures/configs/la/LABSN:expyfun.yml)
- [TravisCI example (2)](https://github.com/xristoforaki/xplan/blob/da74e5ea1dde9988f6e4ccc90e319dbee6b08fd4/.travis.yml)
