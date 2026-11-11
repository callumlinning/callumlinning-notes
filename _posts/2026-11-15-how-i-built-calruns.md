---
layout: post
title: "how i built my running app with claude"
description: "the decisions, the parts and the process behind a running app built with claude"
date: 2026-11-15
tags: [ai, vibe-coding]
social_image: "/assets/images/design-system/calruns-homepage.png"
---
In my [last post](/calruns-design-system/) I wrote about the design of calruns, the running app I've been building for myself with claude. This one is about how I built it, the decisions behind it, how it fits together, and the process that [Claude](https://claude.com) and I settled into to keep it working.

## the brief

I started with a short list of things I wanted the app to do:

- show whether my running fitness is improving, flat or declining
- explain why one run felt different from a similar one
- track how I'm preparing for a race against a target time
- build a training plan from my fitness, history and availability, and adapt it as I go
- answer questions about my training using only my own data
- keep all of my health, activity and location data local

## the architecture

calruns runs entirely on my device. It's a single [Python](https://www.python.org) process that serves the app in the browser, runs a few background jobs and stores everything in a local [SQLite](https://sqlite.org) database.

<style>
.fig{margin:32px 0;padding:20px 22px;border:1px solid #eeeeec;border-radius:6px;font-size:.95rem;line-height:1.5;font-variant-numeric:tabular-nums}
.fig .row{display:grid;grid-template-columns:2rem minmax(0,1fr) auto;gap:2px 16px;padding:10px 0;border-bottom:1px solid var(--border)}
.fig .row:first-child{border-top:1px solid var(--border)}
.fig .n,.fig .side,.fig small,.fig .cap{color:var(--muted)}
.fig .side{text-align:right;font-size:.9rem}
.fig small{display:block;font-size:.85rem}
.fig .cap{font-size:.85rem;margin-top:12px}
.fig.snap .row{grid-template-columns:10rem minmax(0,1fr)}
.fig.snap .side{text-align:left}
.fig.snap .row:first-child{border-top:0}
.fig.snap .row:last-child{border-bottom:0}
.fig.loop .row:first-child{border-top:0}
.fig.loop .row:last-child{border-bottom:0}
.fig.files .row:first-child{border-top:0}
.fig.files .row:last-child{border-bottom:0}
.fig.files .row{grid-template-columns:2rem minmax(0,1fr) minmax(0,1fr)}
.fig.files .side{text-align:left}
.fig.bars .row{grid-template-columns:minmax(0,1fr) auto}
.fig.bars .row{border-bottom:0;padding:12px 0}
.fig.bars .row:first-child{border-top:0}
.fig.bars .cap{padding-top:12px;border-top:1px solid #eeeeec}
.fig.bars .bar{grid-column:1 / -1;height:4px;margin-top:8px;background:#ececea;border-radius:999px}
.fig.bars .bar i{display:block;height:100%;background:#a9a9a4;border-radius:999px;min-width:4px}
.fig.tree .sub{margin-left:1.1rem;padding-left:1.1rem;border-left:1px solid var(--border)}
.fig.tree .sub .sub{margin-left:0}
.fig.tree .pill{grid-column:2;grid-row:1 / span 2;justify-self:end;align-self:center;font-size:.72rem;line-height:1.4;padding:1px 9px;border-radius:999px;background:#efefed;color:var(--muted)}
.fig.tree .line{display:grid;grid-template-columns:minmax(0,1fr) 7rem;gap:2px 16px;padding:10px 0;border-bottom:1px solid var(--border)}
.fig.tree .line b{font-weight:400;color:var(--text)}
.fig.tree .line span.d{color:var(--muted);font-size:.85rem;grid-column:1}
@media (max-width:520px){.fig.snap .row{grid-template-columns:minmax(0,1fr)}.fig.tree .line span.d{grid-column:1 / -1}.fig.tree .pill{grid-row:1}.fig.tree .line{grid-template-columns:minmax(0,1fr) auto}.fig.files .row{grid-template-columns:2rem minmax(0,1fr)}.fig.files .side{grid-column:2}}
</style>

<div class="fig tree" role="figure" aria-label="The parts of calruns, nested: the browser talks to the app on localhost; the app contains sync, metrics, the plan engine, the coach, backups and storage; sync reads from Garmin and the coach uses Ollama on the laptop">
  <div class="line"><b>browser</b><span class="d">localhost only; React and TypeScript, in the browser on the laptop</span></div>
  <div class="sub">
    <div class="line"><b>app</b><span class="d">one Python process; FastAPI, serving the app and its background jobs</span></div>
    <div class="sub">
      <div class="line"><b>sync</b><span class="pill">Python</span><span class="d">Garmin pulls in runs, sleep and HRV data every 3 hours</span></div>
      <div class="line"><b>metrics</b><span class="pill">Python</span><span class="d">fitness, load and race estimates</span></div>
      <div class="line"><b>plan engine</b><span class="pill">Python</span><span class="d">builds, adapts, moves, skips and pauses plans</span></div>
      <div class="line"><b>ai coach</b><span class="pill">Ollama</span><span class="d">ai chat and summaries locally on device</span></div>
      <div class="line"><b>backups</b><span class="pill">Python</span><span class="d">weekly, and before every upgrade or restore</span></div>
      <div class="line"><b>storage</b><span class="pill">SQLite</span><span class="d">raw Garmin files and backups</span></div>
    </div>
  </div>
</div>

The stack is Python and [FastAPI](https://fastapi.tiangolo.com) on the backend, [React](https://react.dev) and [TypeScript](https://www.typescriptlang.org) on the frontend, and SQLite for storage. It only listens on localhost, so nothing else on my network can reach it. It also refuses any change that doesn't come from its own page, so another website open in the same browser can't trigger a sync or delete anything accidentally

The only things it talks to are Garmin, a [GOV.UK](https://www.gov.uk/bank-holidays) download of bank holidays (so they can be added as rest days), and the ai model running locally through Ollama.

Route maps were tricky. I downloaded an offline map of an area once with a script, and the run page draws the route onto the map. The map provider only sees the rectangle I asked for. Outside a downloaded area, the route is just drawn on its own, with an option in the preview to download the map for that area.

## a local ai coach

The ai features run on [Ollama](https://ollama.com/), which allows you to run open models on your own device. The model used is [gemma4:e4b](https://ollama.com/library/gemma4), which runs fine on my laptop that doesn't have a dedicated graphics card.

When I ask the ai coach something, calruns puts a snapshot of my numbers into the prompt:

<div class="fig snap" role="figure" aria-label="What calruns puts into the ai coach's prompt: today's date, load, next race, this week and next, how the plan changed, the last 10 runs, and the rules the model must follow">
  <div class="row"><span>today's date</span><span class="side">so "this week" means something</span></div>
  <div class="row"><span>load</span><span class="side">fitness, recent load, freshness and ramp rate</span></div>
  <div class="row"><span>next race</span><span class="side">date, distance, target time, estimated range and readiness</span></div>
  <div class="row"><span>this week and next</span><span class="side">each planned session, and what I actually ran</span></div>
  <div class="row"><span>how the plan changed</span><span class="side">newest first, with the reason for each change</span></div>
  <div class="row"><span>last 10 runs</span><span class="side">distance, pace and average heart rate</span></div>
  <div class="row"><span>the rules</span><span class="side">use only these numbers and quote them, say when the data doesn't answer, never invent runs or medical facts, under 150 words</span></div>
</div>

There are also ai summaries built into the screens: a weekly review, a review of each run and a review of how the plan is progressing. They're generated when I ask for them and saved, rather than being regenerated every time I open a page.

A small local model is nowhere near as capable as frontier models, but for answering questions about my own training from my own numbers, it's been good enough. The ai sits behind a simple gateway, so I could swap in a different provider later if I ever wanted to use something more capable.

## keeping my data safe

Because everything is local, I'm responsible for not losing it. calruns takes a backup every week and keeps the last ten, plus one before every upgrade and every restore. I can export everything as CSVs, or delete everything with confirmation.

There's a demo mode that swaps the whole app over to a made-up runner, which is how I take screenshots without showing my own personal information.

## giving claude context

Every new chat with claude starts from nothing. It doesn't remember the decisions we made last week, why a button looks the way it does, or which rules must never be broken. To get around that, calruns has a set of context notes, a handful of `.md` files kept in a claude project.

Every chat starts with the same prompt, asking claude to read `calruns-00-start-here.md` first. That file is the index. It says what calruns is, where things stand, the rules that always apply (like never asking for my Garmin key), and which of the other files to read for each kind of task. A design tweak only needs the design system, while a change to the plan engine needs the requirements and the architecture. claude reads what's relevant rather than everything, every time.

<div class="fig files" role="figure" aria-label="The handover files and when each one is read">
  <div class="row"><span class="n">00</span><span>start here</span><span class="side">the index, always read first</span></div>
  <div class="row"><span class="n">01</span><span>requirements and decisions</span><span class="side">scope, privacy, Garmin or ai</span></div>
  <div class="row"><span class="n">02</span><span>architecture</span><span class="side">any code change</span></div>
  <div class="row"><span class="n">03</span><span>roadmap and backlog</span><span class="side">planning what's next</span></div>
  <div class="row"><span class="n">04</span><span>development workflow</span><span class="side">before building anything</span></div>
  <div class="row"><span class="n">05</span><span>design system</span><span class="side">any change to how it looks</span></div>
  </div>

The writing back is what makes it work. The files aren't a spec I wrote once at the start. They're updated as part of every round, so they become a running record of what we've built and decided. The design system contains updated rules that reverse an earlier choice, with a note to always standardise components. The workflow file has a list of gotchas learned the hard way, so the same mistake doesn't get made again in a different chat session.

Everything claude reads uses up tokens, and I was hitting limits pretty quickly. As the project grew, the structure above ended up being about reading less and more about efficiency. I learnt that to optimise token use, the context still had to be important, but leaner and more dynamic, depending on what each prompt was about.

<div class="fig bars" role="figure" aria-label="Approximate tokens to read: a typical chat 4,600; all seven handover files 18,000; the testing log 31,000; all of the code 233,000">
  <div class="row"><span>a typical chat</span><span class="side">4,600</span><div class="bar"><i style="width:2%"></i></div></div>
  <div class="row"><span>all seven handover files</span><span class="side">18,000</span><div class="bar"><i style="width:7.8%"></i></div></div>
  <div class="row"><span>the testing log</span><span class="side">31,000</span><div class="bar"><i style="width:13.4%"></i></div></div>
  <div class="row"><span>all of the code</span><span class="side">233,000</span><div class="bar"><i style="width:100%"></i></div></div>
  <div class="cap">Roughly how many tokens claude would read at the start of a chat, at about four characters per token.</div>
</div>

The *start here* file is about 1,500 tokens on its own, its job is to point claude at one or two others rather than all seven.

The *architecture* file lists every module and what it does, so claude can search for the right file and read only the part it's changing. Reading the whole codebase up front would cost around **fifty times** as much as a typical chat.

The *requirements* file says that where it and the original proposal disagree, it wins, so claude never needs to read the proposal. The roadmap file is a short snapshot of a longer live document.

The testing log is the biggest file, and it grows every round. It's kept newest first, so when it is needed, the latest rounds are right at the top.

The files are mostly short bullets and tables, with no padding. The workflow tells claude to reply briefly with what changed and not to recap the steps it took, which saves on its output too.

## how it's built

Every change to calruns goes through the same loop. Claude reads the handover notes and the code, makes the change, then runs the typecheck, the build, the unit tests and a set of browser tests that click through the main user journeys. It then takes screenshots of the screens it changed and looks at them before telling me it's done.

<div class="fig loop" role="figure" aria-label="The development loop for each round of feedback">
  <div class="row"><span class="n">1</span><span>use it and provide feedback <small>a handful of notes, sometimes with a screenshot</small></span></div>
  <div class="row"><span class="n">2</span><span>read the notes and the code <small>read the handover files first, then the code around the changes</small></span></div>
  <div class="row"><span class="n">3</span><span>make the change <small>backend, frontend and tests for any new logic</small></span></div>
  <div class="row"><span class="n">4</span><span>check it <small>typecheck, build, unit tests, browser tests and accessibility in light and dark</small></span></div>
  <div class="row"><span class="n">5</span><span>look at it <small>screenshots of every changed screen, read before saying it's done</small></span></div>
  <div class="row"><span class="n">6</span><span>version and log it <small>bump the version number and add a round in the testing log</small></span></div>
  <div class="row"><span class="n">7</span><span>commit and deliver <small>the changed files are copied onto the laptop</small></span></div>
  <div class="row"><span class="n">8</span><span>restart calruns <small>the update banner shows what's new</small></span></div>
</div>

Each round of feedback gets a version number and an entry in a testing log, which also powers the "what's new" panel in the app. I'm now more than 90 rounds in, and the last thing on my list before I'd call it finished is two weeks of daily use without a serious bug.

## from vibe coding to something more structured

calruns started out as pure vibe coding, just playing around with claude. I'd describe what I wanted, look at what came back, and ask for changes until it felt right. As the app grew, so did the risk of claude forgetting a decision, breaking something that used to work, or undoing a design choice from a few rounds earlier.

The handover files, the checks on every change, the version numbers and the testing log all came from needing each round to build on the last rather than start again. I still judge calruns by how it functions and works, not by reading the code line by line. The difference is that I'm no longer just engineering the app, I'm engineering the process claude works within, which feels closer to agentic engineering than just vibe coding.
