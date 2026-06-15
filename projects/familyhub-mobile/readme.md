# FamilyHub Mobile

> **Status:** 🟢 active · **Updated:** 2026-06-14
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Mobile app for [[familyhub]] — Expo/React Native client (iOS-focused, Android-capable) talking to the FamilyHub API.

## Links
- **Code:** `~/dev/FamilyHubMobile` — ⚠️ local only, no git remote yet
- **Live:** _tbd_

## Stack
React Native · Expo (expo-router) · TypeScript

## Overview
The mobile front-end for FamilyHub's task management. Auth + data go through the FamilyHub API via `lib/api-client.ts` (`EXPO_PUBLIC_API_URL`, defaults to `http://localhost:3001`). Expo means it can build for iOS and Android; primary target is iOS.

## Related
- Backend / web app: [[familyhub]]
