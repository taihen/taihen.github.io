---
title: "Test Page"
date: 2023-10-10T12:00:00Z
draft: false
---

# Test Page

This is a test page for the Hugo website hosted on GitHub.

Welcome to your first test page!

## Diagnostics

- **Page Title:** {{ .Title }}
- **Publish Date:** {{ .Date }}
- **Draft Status:** {{ .Draft }}
- **Base URL:** {{ .Site.BaseURL }}
- **Language:** {{ .Site.Language.Lang }}
- **Hugo Version:** {{ .Hugo.Version }}
- **Build Date:** {{ .Hugo.BuildDate }}
- **Environment:** {{ .Hugo.Environment }}
- **Number of Pages:** {{ len .Site.Pages }}
- **Number of Posts:** {{ len (where .Site.Pages "Section" "posts") }}
- **Theme:** {{ .Site.Theme }}
