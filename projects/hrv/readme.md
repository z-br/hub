# HRV / Sleep-Health

> **Status:** 🟡 paused · **Updated:** 2026-06-14
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** HRV analysis & VPC/APC detection from Polar H10 ECG data. Home for sleep/heart health work — see Related for CPAP reference repos.

## Links
- **Code:** `~/dev/hrv`
- **Live:** _tbd_

## Stack
Express · TypeScript

## Overview
An Express/TypeScript app that ingests **Polar H10 ECG** data (CSV) and analyzes heart-rate variability, detecting **VPCs/APCs** (premature ventricular/atrial contractions). Local-only (no remote), 5 commits, last touched Nov 2025 — commit messages flag the zoom UI and the detection algorithm as the rough edges ("algo questionable"). Anchors the personal sleep/heart-health work; the CPAP repos below are reference tooling.

## Related — CPAP / sleep reference repos
These live in `~/dev` and back the sleep-health work (mostly third-party tools, not your own projects):

| Repo | What it is | Source |
|---|---|---|
| `~/dev/OSCAR-code` | OSCAR — open-source CPAP analysis/reporting app (C++/Qt) | gitlab.com/CrimsonNape/OSCAR-code |
| `~/dev/CPAP-Exporter` | Exports CPAP data (.NET) | github.com/CascadePass/CPAP-Exporter |
| `~/dev/cpap-lib` | Library for reading CPAP machine data (.NET) | github.com/EEGKit/cpap-lib |
