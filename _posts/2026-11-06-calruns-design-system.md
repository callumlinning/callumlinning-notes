---
layout: post
title: "building my app's design system"
description: "the colours, type and components behind my vibe-coded running app"
date: 2026-11-06
social_image: "/assets/images/calruns-design-system.jpg"
---
calruns is a running app I built for myself with Claude. It uses my running and recovery data from Garmin to assess my fitness and build training plans around upcoming races.

having complete freedom over the design meant I spent probably too much time refining how it looked and felt. I wanted it to reflect my minimalist taste while still feeling like a polished, premium product.

to get there, I started defining a consistent set of colours, components, layouts and visual rules that could be reused across the app. Eventually, those decisions started to resemble a design system.

<style>
.crds{--ground:#f5f3ee;--surface:#ffffff;--raised:#f4f1eb;--border:#dbd6cc;--text:#1c1b19;--text-2:#46423c;--muted:#66615a;--gold:#b8924f;--gold-bg:#f8f1e1;--gold-hover:#6f4f19;--gold-btn:#8a6a2f;--on-gold-btn:#fdfbf7;--steel:#4c7ca5;--steel-deep:#c9d9e8;--steel-soft:#8fb0cc;--rose:#ad5757;--danger:#b24545;--sage:#34784f;--good-text:#2e7350;--good-bg:#e6f2eb;--copper:#9f5820;--copper-bg:#f7ebe0;--danger-bg:#f6e4e4;--ink:#3b3936;--on-ink:#faf8f4;--switch-off:#d6d0c5;--ai-line:#d3bf95;font-family:Inter,system-ui,sans-serif;font-variant-numeric:tabular-nums;color:var(--text);background:var(--ground);border:1px solid var(--border);border-radius:20px;padding:28px;margin:24px 0;line-height:1.5}
.crds *{box-sizing:border-box}
.crds .serif{font-family:Fraunces,Georgia,serif;font-weight:400}
.crds .card{background:var(--surface);border-radius:20px;padding:24px}
.crds .label{font-size:12px;color:var(--muted)}
.crds .row{display:flex;flex-wrap:wrap;gap:12px;align-items:center}
.crds .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:12px}
.crds .sw{border-radius:12px;overflow:hidden;border:1px solid var(--border);background:var(--surface)}
.crds .sw .chip span+span{border-left:1px solid var(--border)}
.crds .sw .chip{display:flex;height:56px}
.crds .sw .chip span{flex:1}
.crds .sw .meta{padding:8px 10px;font-size:12px;color:var(--muted)}
.crds .sw .meta b{display:block;color:var(--text);font-weight:500;font-size:13px}
.crds .btn{display:inline-flex;align-items:center;gap:6px;height:32px;padding:0 12px;border-radius:8px;font:500 12px Inter,system-ui,sans-serif;border:1px solid var(--border);background:transparent;color:var(--text);cursor:default}
.crds .btn.gold{background:var(--gold-btn);border-color:var(--gold-btn);color:var(--on-gold-btn);font-weight:600}
.crds .btn.ai{border-color:var(--ai-line)}
.crds .btn.quiet{border-color:transparent;color:var(--text-2)}
.crds .btn.danger{border-color:var(--danger);color:var(--danger)}
.crds .pill{display:inline-block;font-size:11px;padding:2px 9px;border-radius:999px}
.crds .pill.green{background:var(--good-bg);color:var(--good-text)}
.crds .pill.grey{background:var(--raised);color:var(--text-2)}
.crds .pill.copper{background:var(--copper-bg);color:var(--copper)}
.crds .pill.red{background:var(--danger-bg);color:var(--danger)}
.crds .switch{width:36px;height:20px;border-radius:999px;background:var(--switch-off);position:relative;display:inline-block}
.crds .switch.on{background:var(--gold)}
.crds .switch i{position:absolute;top:3px;left:3px;width:14px;height:14px;border-radius:50%;background:#fff;box-shadow:0 1px 2px rgba(0,0,0,.25)}
.crds .switch.on i{left:19px}
.crds .seg{display:inline-flex;background:var(--raised);border-radius:8px;padding:3px}
.crds .seg span{font-size:12px;padding:5px 12px;border-radius:6px;color:var(--text-2)}
.crds .seg span.sel{background:var(--ink);color:var(--on-ink)}
.crds .tile{background:var(--raised);border-radius:12px;padding:14px 16px}
.crds .zones{display:grid;grid-template-columns:repeat(5,1fr);gap:6px}
.crds .zones div{background:var(--raised);border-radius:0 0 8px 8px;padding:10px;font-size:12px;color:var(--text-2)}
.crds .key{display:grid;gap:6px;font-size:13px;color:var(--text-2)}
.crds .key span{display:inline-block;width:8px;height:8px;border-radius:50%;margin-right:8px}
.crds .split{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:16px}
.crds .tok{font:12px ui-monospace,Menlo,monospace;color:var(--text-2)}
</style>

## the character

given that I’m the only person who’ll use the app, I wanted the design to reflect my own taste. My aim was to capture some of the polish of premium fitness brands while keeping everything minimal and understated.

I started by giving Claude a rough brief for the look and feel, then refined it from there, starting with the colours.

## colour

dark graphite is the default. I also added a light mode and an option to follow the theme set in Windows.

the examples here are from light mode. Each swatch shows its light value on the left and dark value on the right. Every token has both, and small coloured text always uses a readable variant rather than relying on opacity, so it passes contrast checks in either theme.

<div class="crds">
  <div class="label" style="margin-bottom:10px">Surfaces and text</div>
  <div class="grid">
    <div class="sw"><div class="chip"><span style="background:#f5f3ee"></span><span style="background:#0b0b0c"></span></div><div class="meta"><b>Ground</b>#f5f3ee · #0b0b0c</div></div>
    <div class="sw"><div class="chip"><span style="background:#ffffff"></span><span style="background:#17171a"></span></div><div class="meta"><b>Surface</b>#ffffff · #17171a</div></div>
    <div class="sw"><div class="chip"><span style="background:#f4f1eb"></span><span style="background:#1f1f23"></span></div><div class="meta"><b>Raised</b>#f4f1eb · #1f1f23</div></div>
    <div class="sw"><div class="chip"><span style="background:#dbd6cc"></span><span style="background:#2a2a2e"></span></div><div class="meta"><b>Border</b>#dbd6cc · #2a2a2e</div></div>
    <div class="sw"><div class="chip"><span style="background:#1c1b19"></span><span style="background:#f2efea"></span></div><div class="meta"><b>Text</b>#1c1b19 · #f2efea</div></div>
    <div class="sw"><div class="chip"><span style="background:#66615a"></span><span style="background:#a09c94"></span></div><div class="meta"><b>Muted</b>#66615a · #a09c94</div></div>
  </div>
  <div class="label" style="margin:20px 0 10px">Accents</div>
  <div class="grid">
    <div class="sw"><div class="chip"><span style="background:#b8924f"></span><span style="background:#c9a96e"></span></div><div class="meta"><b>Gold</b>Accent, today, target</div></div>
    <div class="sw"><div class="chip"><span style="background:#4c7ca5"></span><span style="background:#7fa7c9"></span></div><div class="meta"><b>Steel</b>Pace, fitness, estimates</div></div>
    <div class="sw"><div class="chip"><span style="background:#ad5757"></span><span style="background:#c98a8a"></span></div><div class="meta"><b>Rose</b>Heart rate, off target</div></div>
    <div class="sw"><div class="chip"><span style="background:#34784f"></span><span style="background:#8fb3a0"></span></div><div class="meta"><b>Sage</b>Good, done</div></div>
    <div class="sw"><div class="chip"><span style="background:#9f5820"></span><span style="background:#d4925e"></span></div><div class="meta"><b>Copper</b>Warnings, outside normal</div></div>
  </div>
  <div class="label" style="margin:20px 0 10px">Heart-rate zones and training phases</div>
  <div class="row">
    <div style="display:flex;border-radius:8px;overflow:hidden;height:28px;flex:1;min-width:220px"><span style="flex:1;background:#7FA7C9"></span><span style="flex:1;background:#8FB3A0"></span><span style="flex:1;background:#D6B36E"></span><span style="flex:1;background:#C98A6A"></span><span style="flex:1;background:#C96A6A"></span></div>
    <div style="display:flex;border-radius:8px;overflow:hidden;height:28px;flex:1;min-width:220px"><span style="flex:4;background:#7FA7C9"></span><span style="flex:6;background:#4D6F8C"></span><span style="flex:3;background:#C98A8A"></span><span style="flex:3;background:#8FB3A0"></span></div>
  </div>
  <div style="display:flex; gap:12px; margin-top:6px;">
  
  <div style="flex:1;">
    <div class="zone-bar">
      <!-- Z1–Z5 bar -->
    </div>
    <div class="label" style="margin-top:6px; text-align:left;">
      Z1 to Z5
    </div>
  </div>

  <div style="flex:1;">
    <div class="phase-bar">
      <!-- Base / Build / Peak / Taper bar -->
    </div>
    <div class="label" style="margin-top:6px; text-align:left;">
      Classic periodisation base, build, peak & taper
    </div>
  </div>

</div>
</div>

AI features deliberately get no colour of their own. No violet backgrounds, gradients or glow that AI-generated websites and apps have become infamous for. The AI cards are just a normal card with a 1px champagne hairline as a subtle differenciator. 

## typefaces

two typefaces are used throughout the app: **Fraunces** for page titles and large numbers, and **Inter** for everything else. Numbers use tabular figures so they line up properly. I only use weights 400 and 500, with 600 reserved for buttons and actions.

<div class="crds">
  <div class="split">
    <div class="card">
      <div class="label">Estimated finish</div>
      <div class="serif" style="font-size:44px;line-height:1.1;margin-top:6px">1:42:18</div>
      <div style="font-size:13px;color:var(--muted);margin-top:8px">Fraunces 44px, supporting line 13px muted</div>
    </div>
    <div class="card">
      <div class="serif" style="font-size:28px;line-height:1.2">Easy run</div>
      <div style="font-size:14px;color:var(--text-2);margin-top:6px">About 35 minutes</div>
      <div class="label" style="margin-top:14px">Labels are 12px, sentence case, muted. No spaced capitals and no colons, anywhere.</div>
    </div>
  </div>
</div>

## components

card actions all share the same dimensions of 32px high, 12px text and an 8px radius. Colour marks the role rather than changing the shape or size. There’s only ever one outlined button per row. Everything else stays quiet until you hover over it.

<div class="crds">
  <div class="row">
    <span class="btn gold">Build plan</span>
    <span class="btn">See all</span>
    <span class="btn ai"><svg width="13" height="13" viewBox="0 0 16 16" fill="none" stroke="#b8924f" stroke-width="1.4"><path d="M8 1.5v3M8 11.5v3M1.5 8h3M11.5 8h3M3.6 3.6l2 2M10.4 10.4l2 2M3.6 12.4l2-2M10.4 5.6l2-2"/></svg>Write review</span>
    <span class="btn quiet">Undo</span>
    <span class="btn danger">Delete everything</span>
  </div>
  <div class="row" style="margin-top:18px">
    <span class="pill green">Inside your normal</span>
    <span class="pill grey">Planned</span>
    <span class="pill copper">Short</span>
    <span class="pill red">Missed</span>
    <span style="width:12px"></span>
    <span class="switch on"><i></i></span>
    <span class="switch"><i></i></span>
    <span style="width:12px"></span>
    <span class="seg"><span class="sel">Week</span><span>Month</span></span>
  </div>
</div>

every on/off setting is a toggle rather than a checkbox because settings save automatically. Selected segments use a soft ink colour, which in light mode is dark grey rather than black.

### the target tick

this is the race card. It shows a countdown to my next race alongside my current target and estimated finish time. The estimated range is a solid bar, with the target shown as a 2px gold tick. Times use arrows the way a runner reads them: lower is better, so ▼ means faster.

<div class="crds">
  <div class="card">
    <div class="row" style="justify-content:space-between"><span style="font-size:12px;color:var(--text)">Next race <span style="color:var(--muted)">· In 100 days</span></span><span style="font-size:12px;color:var(--good-text)">▼ 0:20</span></div>
    <div class="serif" style="font-size:22px;margin:6px 0 22px">City Half Marathon</div>
    <div style="position:relative;height:28px">
      <div style="position:absolute;top:12px;left:0;right:0;height:2px;background:var(--border)"></div>
      <div style="position:absolute;top:8px;left:34%;width:30%;height:10px;border-radius:3px;background:var(--steel-deep);border:1px solid var(--steel-soft)"></div>
      <div style="position:absolute;top:2px;left:70%;width:2px;height:22px;background:var(--gold);border-radius:1px"></div>
    </div>
    <div class="row label" style="justify-content:space-between"><span>1:38</span><span>Estimate 1:41–1:44 · target 1:45</span><span>1:50</span></div>
  </div>
</div>

### rings with straight ends

the sleep ring follows a rule I was oddly pedantic about: solid segments have straight ends with slightly softened corners, never rounded caps. The gaps have parallel sides so they're the same width from the inside of the ring to the outside. Nothing sits in the middle.

<div class="crds">
  <div class="row" style="gap:28px">
    <svg width="120" height="120" viewBox="0 0 120 120" role="img" aria-label="Sleep stages: deep 1h 20m, light 4h 22m, REM 1h 40m, awake 10m">
      <path d="M61.50 6.02 A54 54 0 0 1 107.37 34.08 L91.34 42.28 A36 36 0 0 0 61.50 24.03 Z" fill="#4d6f8c" stroke="#4d6f8c" stroke-width="2.5" stroke-linejoin="round"/>
      <path d="M108.74 36.75 A54 54 0 1 1 6.06 62.51 L24.07 62.17 A36 36 0 1 0 92.70 44.95 Z" fill="#7fa7c9" stroke="#7fa7c9" stroke-width="2.5" stroke-linejoin="round"/>
      <path d="M6.00 59.51 A54 54 0 0 1 46.91 7.61 L50.79 25.20 A36 36 0 0 0 24.01 59.17 Z" fill="#9a86c4" stroke="#9a86c4" stroke-width="2.5" stroke-linejoin="round"/>
      <path d="M49.84 6.97 A54 54 0 0 1 58.50 6.02 L58.50 24.03 A36 36 0 0 0 53.72 24.55 Z" fill="#b9805c" stroke="#b9805c" stroke-width="2.5" stroke-linejoin="round"/>
    </svg>
    <div class="key">
      <div><span style="background:#4d6f8c"></span>Deep 1h 20m</div>
      <div><span style="background:#7fa7c9"></span>Light 4h 22m</div>
      <div><span style="background:#9a86c4"></span>REM 1h 40m</div>
      <div><span style="background:#b9805c"></span>Awake 10m</div>
    </div>
    <div class="tile">
      <div class="label">Total</div>
      <div class="serif" style="font-size:38px;line-height:1.1">7h 32m</div>
      <div style="font-size:13px;color:var(--good-text);margin-top:8px">12m over</div>
    </div>
  </div>
</div>

the same flatness applies to zone strips: a square-ended 3px line across the top of each tile, with only the bottom corners rounded.

<div class="crds">
  <div class="zones">
    <div style="border-top:3px solid #7FA7C9">Z1 · 12%</div>
    <div style="border-top:3px solid #8FB3A0">Z2 · 64%</div>
    <div style="border-top:3px solid #D6B36E">Z3 · 18%</div>
    <div style="border-top:3px solid #C98A6A">Z4 · 5%</div>
    <div style="border-top:3px solid #C96A6A">Z5 · 1%</div>
  </div>
</div>

## removing ai slop design

a surprising amount of the design system is actually a list of things the app shouldn't do. No glows on status dots. No pulsing. No spaced capitals. No colons in labels. No full stops on short headlines. Basically, the telltale signs that something has been vibe-coded without design thought.

<div class="callout">
  <strong>skills to help</strong>
  <p>
    I used a few skills in Claude to help strip out some of these AI design clichés. I found 
    Emil Kowalski's <a href="https://github.com/emilkowalski/skills/">skills for designers and engineers</a> 
    and <a href="https://github.com/heydamir/unslop">unslop</a> from heydamir particularly effective.
  </p>
</div>

there are functional rules too. A missing night's data never breaks a trend line. The line joins the readings either side, but nothing is invented to fill the gap.

## motion

motion is another way I've tried to give the app the polished feel I wanted. Charts draw in once when they're first shown. The only thing that loops indefinitely is the sync icon, and only while it's actually syncing. Every animation has a reduced-motion fallback, usually a simple fade.

## design & dev workflow

not all of this was designed upfront. A lot of the refinements came from more than eighty rounds of testing, each with a handful of increasingly specific notes like *“make this match the other buttons”* or *“those lines should be flat, not rounded.”*. As a result, the design system feels less like a traditional style guide and more like a record of my taste as the project evolved.

to keep it consistent, I have a Markdown file called `calruns05-design-system` in Claude's context. Whenever I'm working on a new feature, the development workflow reads those rules first so anything new or changed follows the decisions I've already made.