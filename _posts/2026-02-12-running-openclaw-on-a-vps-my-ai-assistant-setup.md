---
layout: post
title: "My AI Assistant Setup"
date: 2026-02-12
tags: [AI, OpenClaw, VPS, Productivity]
header-img: /img/ai-assist.png
---

I've been running an AI assistant called OpenClaw for the past few months. It's transformed how I work. Here's my setup and why I think this is the future.

## The Setup

I run OpenClaw on a VPS (Virtual Private Server). Here's why:

- **24/7 availability**: My laptop doesn't need to be awake for the assistant to work
- **No censorship**: Unlike commercial AI services that filter outputs, I control prompts and responses
- **Security**: Data stays on my server. No sharing with third-party AI companies
- **Cost-effective**: Use local or API models as needed. Pay for what I use

The VPS runs the OpenClaw Gateway, which handles:

- Message routing (WhatsApp, Telegram, Google Chat)
- Calendar integration (Google Calendar, Snapp)
- Cron jobs for scheduled tasks
- Skills and extensions

## What My Assistant Does

Here's a typical day:

**7:00 AM** - Daily news briefing arrives on WhatsApp. International news from 4 sources (Al Jazeera, BBC, NYTimes, The Hindu), Persian news translated from 3 sources, and latest from @clashreport. All in one message.

**7:30 AM** - AI & tech briefing. Top threads from X/Twitter (Andrew Ng, Karpathy, Y Combinator accounts) plus Hacker News front page. I get the top 10 most important tech stories of the day without opening any app.

**8:00 AM** - Schedule check. My assistant looks at both my Google Calendar and Snapp Calendar, then sends me a WhatsApp message with all my meetings for the day. Times in Tehran timezone.

**Throughout the day**:
- "Read it to me [URL]" → it fetches and converts to speech using gTTS (supports Urdu, Arabic, Persian, English)
- "Book a Snapp" → books cab rides via CLI
- "Take a note: [content]" → saves to weekly notes system
- "Check tweets" → fetches and summarizes X/Twitter threads
- "HN" → sends top 10 Hacker News stories
- Prayer times, Hijri date conversion, Persian audio transcription

Everything happens through WhatsApp or Telegram. No apps to install.

## What Impressed Me

A few capabilities surprised even me:

**Memory across time**

The assistant remembers context across days and weeks. References conversations from last week. Knows my preferences. There's no "reset" when I close the chat. It feels continuous.

**Create skills by messaging**

Want a new capability? I send a WhatsApp message: "Create a skill for [task]". The assistant scaffolds a skill folder, writes a SKILL.md file, and installs it. Next conversation, it's using the new skill.

I've added skills for:
- Farsi transcription (ElevenLabs Scribe API)
- Snapp taxi booking
- Weekly notes system
- Hacker News digest

Each took a single message to create.

**Voice commands**

Send a voice note on WhatsApp. The assistant transcribes it (Farsi or English) and executes the command. No typing required. "Book a cab to work" becomes a scheduled ride.

**Self-healing**

When Twitter CLI (bird) had architecture issues, the assistant diagnosed the problem: "Binary compiled for ARM64, system is x86_64". It fixed it by using the JavaScript npm version instead.

When cron jobs conflicted, the assistant rescheduled them to different times to avoid resource clashes.

It updates its own documentation. Maintains memory files. Fixes installation problems.

The system improves itself.

## What Worries Me

Two issues I've encountered:

**Memory consistency**

The assistant forgets things it did days earlier. It generated a birthday image for my daughter using DALL-E, then a week later refused to do the same task, insisting it didn't have image generation capabilities. Or it checks my calendar perfectly one day, then claims no calendar integration exists three days later.

**Lesson learned:** When the assistant does something impressive, ask it to explicitly remember how. "Save this to your memory files" or "Document this for future sessions."

The architecture for long-term memory exists (MEMORY.md, daily log files), but the assistant doesn't always use it consistently.

**Security surface area**

The system is designed to be easy to extend. Skills drop into folders with a SKILL.md file and become available. Message handlers plug into channels. This flexibility has a downside: it's easy to compromise.

MoltBot. Clawdbot. OpenClaw. The names changed as the project evolved. Multiple installation paths. Skills can execute shell commands. API keys stored in config files.

If someone sends a malicious message to my WhatsApp, what's the blast radius?

**What I do:**

- Run on isolated VPS (not my laptop)
- Review skill code before installing
- Limit which channels can send commands
- Monitor logs for suspicious activity
- Keep sensitive operations (email, tweets) require explicit confirmation

The architecture makes security my responsibility. Not automated, but manageable.

## Why This Is the Future

AI assistants are getting powerful. But most people access them through:

- ChatGPT/Claude web UIs (blocked at work, requires browser)
- Mobile apps (one more app to juggle)
- API calls (requires programming knowledge)

OpenClaw is different. It meets you where you already are:

**Messaging apps as the interface**

You don't need to learn a new UI. If you use WhatsApp, Telegram, or Google Chat, you already know how to use it. The assistant becomes a contact.

**Continuous operation**

Scheduled tasks run whether you think about them or not. News briefings, calendar checks, reminders. No manual triggering required.

**Multi-modal by default**

Text, voice, images. My assistant can read web pages aloud, transcribe Persian audio from Bale.ir links, generate mood images from Hacker News topics.

**Extensible through skills**

Want to add a new capability? Drop a folder in the skills directory with a SKILL.md file. The assistant learns when to use it. No complex plugin development required.

I've added skills for:
- News aggregation (international and Persian RSS feeds)
- Prayer times calculator
- Hijri date conversion
- Snapp taxi booking
- Weekly notes system
- Hacker News digest

Each took minutes to set up.

**Privacy and control**

My data isn't training someone else's model. Calendar stays on my server. Messages route through my gateway. I choose which AI model to use (GLM-4.7, GPT-4, Claude — I can switch per-task).

## The Semi-Technical Pitch

If you're comfortable:

- Running a Linux server
- Setting up cron jobs
- Basic API configuration

You can run this yourself.

OpenClaw Gateway is a Node.js service. Skills are JavaScript, Python, or bash scripts. Configuration is JSON. The agent uses whatever LLM you configure (local models or APIs).

For less technical users: this is what AI products should look like. No account signups. No UI to learn. Just add a contact on WhatsApp.

The interface becomes invisible.

## What's Next

I'm experimenting with:

- **Agent workflows**: One request spawns multiple sub-agents working in parallel
- **Context sharing**: Main agent briefs sub-agents on recent conversations
- **Cross-channel**: Start a task on WhatsApp, continue on Telegram

The goal: an assistant that feels like a knowledgeable colleague. Not a chatbot.

---

**Hardware**: RackNerd VPS, 2GB RAM
**Software**: OpenClaw 2026.2.1, GLM-4.7 model
**Channels**: WhatsApp (primary), Telegram, Google Chat
**Runtime**: 3 months uptime, zero manual intervention required

**Links**: [OpenClaw GitHub](https://github.com/openclaw/openclaw) | [Skills Directory](https://clawhub.com)

## Cost Breakdown

People ask if running an AI assistant is expensive. Here's my actual setup:

**Infrastructure:**
- RackNerd VPS: $30/year ($2.50/month)

**AI Model:**
- z.ai GLM-4.7 Lite plan: $10 for 3 months ($3.33/month, promotional)

**Total monthly cost:** ~$6-7

For comparison, ChatGPT Plus is $20/month.

This setup costs one-third of that and gives me:
- 24/7 availability
- Multi-channel messaging (WhatsApp, Telegram, Google Chat)
- Scheduled briefings without API calls
- Privacy and data control
- Extensible skills

**Note:** I use free tiers for:
- Brave Search API (1 request/second)
- Google Calendar (service account)
- News RSS feeds (public sources)

Most tasks consume zero API credits. The AI model handles reasoning; scripts handle data fetching and formatting.
