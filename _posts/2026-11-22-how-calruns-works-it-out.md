---
layout: post
title: "how calruns works it out"
description: "the formulas and standard rules behind the numbers in my running app and why i chose them"
date: 2026-11-22
tags: [ai, vibe-coding]
---
My [first post](/calruns-design-system/) was about how calruns looks. My [second](/how-i-built-calruns/) was about how it's built. This one is about the numbers it shows, how each one is calculated, and why I picked those methods.

## my own numbers, not someone else's

Garmin, Strava and the coaching apps all have their own branded metrics, with private formulas you can't check. calruns doesn't copy any of them. It uses published, well-known methods that are simple enough to explain in a sentence. It had to anyway. The [Garmin Personal API](https://developer.garmin.com/gc-developer-program/) gives me my runs, sleep and HRV, but not Garmin's readiness, recovery time or VO2 max. Everything below runs locally, from the data in calruns' own database.

## training load

Almost every number starts with training load, a single figure for how much work a run was. calruns uses [Banister's TRIMP](https://www.polar.com/img/static/whitepapers/pdf/polar-training-load-pro-white-paper.pdf), from 1991. It gives hard minutes more weight than easy ones, on an exponential curve.

```text
heart-rate reserve = (average HR − resting HR) ÷ (max HR − resting HR)
load = minutes × reserve × 0.64 × e^(1.92 × reserve)
```

It only needs a run's length and average heart rate, which nearly every run has. A run with no heart rate counts as 1 point a minute, so a flat watch battery doesn't make a run disappear. Maximum heart rate is the second-highest max in all my runs, so one spike from a loose strap doesn't skew everything.

## fitness, recent load and freshness

From daily load, calruns works out Coggan's standard fitness and fatigue numbers, [CTL, ATL and TSB](https://www.trainingpeaks.com/learn/articles/what-is-the-performance-management-chart/), under plainer names.

<style>
.fig{margin:32px 0;padding:20px 22px;border:1px solid var(--hairline);border-radius:6px;font-size:.95rem;line-height:1.5;font-variant-numeric:tabular-nums}
.fig .row{display:grid;grid-template-columns:2rem minmax(0,1fr) auto;gap:2px 16px;padding:10px 0;border-bottom:1px solid var(--border)}
.fig .side,.fig small{color:var(--muted)}
.fig .side{text-align:right;font-size:.9rem}
.fig small{display:block;font-size:.85rem}
.fig.snap .row{grid-template-columns:10rem minmax(0,1fr)}
.fig.snap .side{text-align:left}
.fig.snap .row:last-child{border-bottom:0}
.pcal{--ground:#f5f3ee;--surface:#fff;--raised:#f4f1eb;--border:#dbd6cc;--hover-line:#c4bdb1;--text:#1c1b19;--text-2:#46423c;--muted:#66615a;--gold-hover:#6f4f19;margin:32px 0;font-family:Inter,system-ui,sans-serif;font-variant-numeric:tabular-nums;color:var(--text);line-height:1.4}
.pcal .frame{padding:24px;background:var(--ground);border:1px solid var(--border);border-radius:20px}
.pcal .card{background:var(--surface);border-radius:20px;padding:28px}
.pcal .head{display:flex;justify-content:space-between;align-items:center;gap:12px;padding-bottom:16px;border-bottom:1px solid var(--border)}
.pcal .h2{font-size:12px;font-weight:500;color:var(--muted)}
.pcal .row{display:grid;column-gap:6px}
.pcal .bars{align-items:end;height:120px;margin-top:20px}
.pcal .wk{display:flex;flex-direction:column;align-items:center;justify-content:flex-end;height:100%}
.pcal .b{display:block;width:100%;border-radius:4px}
.pcal .nums{margin-top:10px;padding-top:10px;border-top:1px solid var(--border)}
.pcal .no{text-align:center;font-size:12px;color:var(--muted)}
.pcal .no.on{color:var(--gold-hover);font-weight:600}
.pcal .grps{margin-top:14px}
.pcal .grp{display:flex;flex-direction:column;gap:6px;font-size:12px;color:var(--muted)}
.pcal .grp span{height:3px;border-radius:2px}
@media (max-width:640px){.pcal .frame{padding:12px}.pcal .card{padding:20px 16px}.pcal .row{column-gap:3px}.pcal .no{font-size:10px}.pcal .grp{font-size:11px}}
.pchg .head{border-bottom:1px solid var(--border)}
.pchg .log{list-style:none;margin:20px 0 0;padding:0}
.pchg .entry{position:relative;padding:0 0 16px 28px;margin:0}
.pchg .entry.last{padding-bottom:0}
.pchg .line{position:absolute;left:5px;top:32px;bottom:0;width:1px;background:var(--border)}
.pchg .dot{position:absolute;left:0;top:21px;width:11px;height:11px;box-sizing:border-box;border-radius:50%;border:2px solid #bdb6aa;background:var(--surface)}
.pchg .dot.latest{border:0;background:#b8924f;box-shadow:0 0 0 4px rgba(201,169,110,.18)}
.pchg .chg{padding:16px 18px;border-radius:12px;border:1px solid var(--border)}
.pchg .chg-head{display:flex;flex-wrap:wrap;justify-content:space-between;align-items:center;gap:10px 16px}
.pchg .body{min-width:0;flex:1 1 280px}
.pchg .what{font-size:14px;font-weight:600}
.pchg .sum{margin-top:2px;font-size:13px;line-height:1.5;color:var(--text-2)}
.pchg .why{display:flex;flex-wrap:wrap;align-items:center;gap:4px 8px;margin-top:8px;font-size:12px;line-height:1.5;color:var(--text-2)}
.pchg .why-label{display:inline-flex;align-items:center;height:18px;padding:0 7px;border:1px solid var(--border);border-radius:6px;font-weight:500;color:var(--muted)}
.pchg .link{color:#8a6a2f;font-weight:600}
.pchg .meta{flex:none;margin-left:auto;display:flex;align-items:center;gap:8px;font-size:12px;color:var(--muted);white-space:nowrap}
.pchg .wait{display:inline-flex;align-items:center;font-size:11px;font-weight:500;border-radius:999px;padding:2px 8px;color:var(--gold-hover);background:#f8f1e1;border:1px solid #e0c99c}
.pchg .undo{display:inline-flex;align-items:center;min-height:32px;padding:0 12px;border-radius:8px;border:1px solid var(--border);font-size:12px;font-weight:500;color:var(--text)}
.pchg .items{list-style:none;margin:12px 0 0;padding:0;display:flex;flex-direction:column;gap:8px}
.pchg .it{margin:0;padding:14px;border-radius:10px;background:var(--raised);font-size:13px;font-weight:500}
.pchg .old{color:#ad5757}.pchg .ar{color:var(--muted)}.pchg .new{color:#34784f;font-weight:600}
@media (max-width:640px){.pchg .chg{padding:14px}.pchg .meta{margin-left:0}}
.psig .tiles{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:16px}
.psig .tile{background:var(--raised);border-radius:16px;padding:20px 22px;display:flex;flex-direction:column;gap:12px;min-width:0}
.psig .top{display:flex;justify-content:space-between;align-items:center;gap:8px}
.psig .name{font-size:12px;color:var(--muted);white-space:nowrap}
.psig .pill{display:inline-flex;align-items:center;font-size:11px;font-weight:500;border-radius:999px;padding:4px 10px}
.psig .pill.in{color:#2e7350;background:#e6f2eb}
.psig .pill.out{color:#9f5820;background:#fbf0e4}
.psig .val{font-family:Fraunces,Georgia,serif;font-size:34px;line-height:1;white-space:nowrap}
.psig .unit{font-family:Inter,system-ui,sans-serif;font-size:15px;color:var(--muted);margin-left:6px}
.psig .track{position:relative;height:8px;border-radius:4px;background:var(--border)}
.psig .band{position:absolute;top:-2px;bottom:-2px;border-radius:5px;background:#e6f2eb;border:1px solid #b7dbc6}
.psig .dot{position:absolute;top:50%;width:14px;height:14px;margin:-7px 0 0 -7px;border-radius:50%;border:2px solid var(--raised);box-sizing:border-box}
.psig .ticks{position:relative;height:16px;margin-top:8px;font-size:11px;color:var(--muted)}
.psig .ticks span{position:absolute;transform:translateX(-50%);white-space:nowrap}
.psig .lines{margin-top:auto;font-size:12px;line-height:1.5;color:var(--muted)}
.psig .under{color:#9f5820}
@media (max-width:640px){.psig .tiles{grid-template-columns:minmax(0,1fr)}}
.psim .verdict{display:block;margin-top:20px;font-family:Fraunces,Georgia,serif;font-size:24px;font-weight:400;line-height:1.2}
.psim .tbl{display:flex;flex-direction:column;margin-top:20px}
.psim .tr{display:grid;grid-template-columns:1fr 1fr 1fr 1.3fr;align-items:baseline;gap:12px;padding:12px 0;border-top:1px solid var(--border);font-size:14px;color:var(--text-2)}
.psim .tr>:first-child{color:var(--muted);font-size:13px}
.psim .tr strong{color:var(--text);font-weight:500;font-size:16px}
.psim .tr small{color:var(--muted);font-size:11px;font-weight:400}
.psim .th{border-top:0;padding-top:0;font-size:12px;color:var(--muted)}
.psim .diff{font-size:13px}.psim .good{color:#34784f}.psim .bad{color:#ad5757}
@media (max-width:640px){.psim .tr{gap:8px}.psim .tr strong{font-size:14px}}
.pfit .chart{display:flex;gap:8px;margin-top:20px}
.pfit .yl{position:relative;width:28px;height:220px;flex:none;font-size:11px;color:var(--muted)}
.pfit .yl span{position:absolute;right:0;transform:translateY(-50%)}
.pfit .plot{flex:1;min-width:0}
.pfit svg{display:block;width:100%;height:220px;overflow:visible}
.pfit .grid line{stroke:var(--border);stroke-width:1;vector-effect:non-scaling-stroke}
.pfit polyline{fill:none;vector-effect:non-scaling-stroke;stroke-linejoin:round;stroke-linecap:round}
.pfit .fit{stroke:#4c7ca5;stroke-width:2}
.pfit .recent{stroke:rgba(102,97,90,.5);stroke-width:1.25}
.pfit .xl{position:relative;height:16px;margin-top:8px;font-size:11px;color:var(--muted)}
.pfit .xl span{position:absolute;transform:translateX(-50%);white-space:nowrap}
.pfit .xl span:first-child{transform:none}.pfit .xl span:last-child{transform:translateX(-100%)}
.pfit .legend{display:flex;gap:18px;flex-wrap:wrap;margin-top:16px;font-size:12px;color:var(--muted)}
.pfit .legend span{display:inline-flex;align-items:center;gap:8px}
.pfit .legend i{display:block;width:18px;height:2.5px;border-radius:2px}
.pfit .lf{background:#4c7ca5}.pfit .lr{background:rgba(102,97,90,.5)}
@media (max-width:640px){.pfit .yl,.pfit svg{height:160px}.pfit .xl span:nth-child(even){display:none}}
@media (max-width:520px){.fig.snap .row{grid-template-columns:minmax(0,1fr)}}
</style>

<div class="fig snap" role="figure" aria-label="The four load numbers in calruns: fitness is a 42-day weighted average of daily load; recent load is a 7-day weighted average; freshness is fitness minus recent load; ramp rate is the last 7 days' load divided by the weekly average of the last 28 days">
  <div class="row"><span>fitness</span><span class="side">42-day weighted average of daily load. Slow to rise and slow to fall</span></div>
  <div class="row"><span>recent load</span><span class="side">7-day weighted average of daily load. Reacts within days</span></div>
  <div class="row"><span>freshness</span><span class="side">fitness minus recent load. Negative means you've been working harder than you're used to</span></div>
  <div class="row"><span>ramp rate</span><span class="side">last 7 days' load divided by the weekly average of the last 28 days</span></div>
</div>

Freshness gets a label, from fresh to very tired, so I don't have to remember what the number means. For ramp rate, 0.8 to 1.3 is a sensible rate of building and over 1.5 is too fast. calruns gives the ai coach the same thresholds, so it explains them the same way the screen does.

<figure class="pcal pfit" role="figure" aria-label="Fitness and recent load over the last 28 days, from calruns demo data. Fitness stays between 53 and 59, while recent load moves between 36 and 69. On 19 November fitness is 54 and recent load 44, so freshness is +9, labelled balanced">
  <div class="frame" aria-hidden="true"><div class="card">
    <div class="head"><span class="h2">Fitness and recent load</span></div>
    <div class="chart">
      <div class="yl"><span style="top:100.0%">0</span><span style="top:75.0%">20</span><span style="top:50.0%">40</span><span style="top:25.0%">60</span><span style="top:0.0%">80</span></div>
      <div class="plot">
        <svg viewBox="0 0 600 100" preserveAspectRatio="none"><g class="grid"><line x1="0" x2="600" y1="100.0" y2="100.0"/><line x1="0" x2="600" y1="75.0" y2="75.0"/><line x1="0" x2="600" y1="50.0" y2="50.0"/><line x1="0" x2="600" y1="25.0" y2="25.0"/><line x1="0" x2="600" y1="0.0" y2="0.0"/></g><polyline class="recent" points="0.0,54.4 22.2,53.0 44.4,23.6 66.7,33.8 88.9,31.2 111.1,40.4 133.3,29.4 155.6,28.2 177.8,27.5 200.0,13.9 222.2,25.4 244.4,27.5 266.7,37.1 288.9,27.4 311.1,29.4 333.3,38.7 355.6,23.2 377.8,33.4 400.0,42.2 422.2,50.0 444.4,35.5 466.7,34.5 488.9,35.1 511.1,18.2 533.3,29.1 555.6,26.1 577.8,36.0 600.0,44.5"/><polyline class="fit" points="0.0,32.8 22.2,33.0 44.4,28.2 66.7,29.9 88.9,29.6 111.1,31.2 133.3,29.5 155.6,29.2 177.8,29.1 200.0,26.8 222.2,28.4 244.4,28.8 266.7,30.4 288.9,28.9 311.1,29.1 333.3,30.9 355.6,28.2 377.8,30.0 400.0,31.6 422.2,33.2 444.4,31.0 466.7,31.0 488.9,31.1 511.1,28.2 533.3,30.0 555.6,29.4 577.8,31.1 600.0,32.8"/></svg>
        <div class="xl"><span style="left:0.00%">23 Oct</span><span style="left:16.67%">27 Oct</span><span style="left:33.33%">1 Nov</span><span style="left:50.00%">6 Nov</span><span style="left:66.67%">10 Nov</span><span style="left:83.33%">14 Nov</span><span style="left:100.00%">19 Nov</span></div>
      </div>
    </div>
    <div class="legend"><span><i class="lf"></i>Fitness</span><span><i class="lr"></i>Recent load</span></div>
  </div></div>
  <figcaption>Fitness and recent load over the last 28 days</figcaption>
</figure>

## heart rate zones

Zones use the [Karvonen](https://en.wikipedia.org/wiki/Heart_rate#Karvonen_method) method, a share of heart-rate reserve, so they move as my resting heart rate changes. Time in zone comes from the second-by-second heart rate, so a hard finish shows as a hard finish. Each week, calruns compares my time in zones 1 and 2 with my time in zones 3 to 5, against [Stephen Seiler's](https://doi.org/10.1123/ijspp.5.3.276) 80/20 split. It's the check I needed most, because I run my easy runs too fast.

## race estimates and personal bests

Race estimates use [Riegel's formula](https://en.wikipedia.org/wiki/Peter_Riegel), from 1977:

```text
predicted time = known time × (target distance ÷ known distance) ^ 1.06
```

The hard part is choosing which runs to use, because easy runs predict slow races. calruns takes runs of 5 km or more from the last 6 weeks, keeps the **three fastest** predictions and shows them as a range. A single time to the second would suggest more confidence than the formula has.

Personal bests are found **inside** runs, so a fast 5K in the middle of a 12 km run still counts.

## comparing a run with similar runs

To explain why a run felt different, calruns compares it with runs within 20 % of the distance from the previous 90 days. It also works out Joe Friel's [aerobic decoupling](https://www.trainingpeaks.com/blog/aerobic-endurance-and-decoupling/). If heart rate rises in the second half while pace stays the same, decoupling goes up. Under 5 % means heart rate held steady.

<figure class="pcal psim" role="figure" aria-label="Compared with similar runs: about your usual. This run's pace was 6:02 per km against a usual 6:04, 2 seconds faster. Heart rate was 151 bpm against a usual 146, 5 bpm higher">
  <div class="frame" aria-hidden="true"><div class="card">
    <div class="head"><span class="h2">Compared with similar runs</span></div>
    <strong class="verdict">About your usual</strong>
    <div class="tbl">
      <div class="tr th"><span></span><span>This run</span><span>Usual</span><span>Difference</span></div>
      <div class="tr"><span>Pace</span><strong>6:02<small> /km</small></strong><span>6:04<small> /km</small></span><span class="diff good">0:02 faster</span></div>
      <div class="tr"><span>Heart rate</span><strong>151<small> bpm</small></strong><span>146<small> bpm</small></span><span class="diff bad">5 bpm higher</span></div>
    </div>
  </div></div>
  <figcaption>A run compared with similar runs from the previous 90 days</figcaption>
</figure>

## building a plan

A plan runs from now to race week, split into base, build, peak and taper. Weekly distance starts from the distance I **ran** over the last 4 weeks, not a target. It grows by no more than 10 % a week, drops back every 4th week, and tapers in the last two weeks before the race.

<figure class="pcal" role="figure" aria-label="Weekly distance for a 16-week half marathon plan starting at 25 km. Base, weeks 1 to 4: 25, 27, 28 and 22 km. Build, weeks 5 to 10: 30, 32, 34, 27, 36 and 39 km. Peak, weeks 11 to 13: 41, 35 and 44 km. Taper, weeks 14 to 16: 33 and 26 km, then race week">
  <div class="frame" aria-hidden="true"><div class="card">
    <div class="head"><span class="h2">Plan</span></div>
    <div class="row bars" style="grid-template-columns:minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr)">
      <div class="wk" style="grid-column:1"><span class="b" style="height:68.2px;background:#7FA7C9"></span></div>
      <div class="wk" style="grid-column:2"><span class="b" style="height:72.5px;background:#7FA7C9"></span></div>
      <div class="wk" style="grid-column:3"><span class="b" style="height:77.2px;background:#7FA7C9"></span></div>
      <div class="wk" style="grid-column:4"><span class="b" style="height:60.3px;background:#7FA7C9"></span></div>
      <div class="wk" style="grid-column:6"><span class="b" style="height:82.4px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:7"><span class="b" style="height:87.5px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:8"><span class="b" style="height:93.3px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:9"><span class="b" style="height:72.8px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:10"><span class="b" style="height:99.3px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:11"><span class="b" style="height:105.8px;background:#4D6F8C"></span></div>
      <div class="wk" style="grid-column:13"><span class="b" style="height:112.6px;background:#C98A8A"></span></div>
      <div class="wk" style="grid-column:14"><span class="b" style="height:95.7px;background:#C98A8A"></span></div>
      <div class="wk on" style="grid-column:15"><span class="b" style="height:120.0px;background:#F2A3A3"></span></div>
      <div class="wk" style="grid-column:17"><span class="b" style="height:90.0px;background:#8FB3A0"></span></div>
      <div class="wk" style="grid-column:18"><span class="b" style="height:69.5px;background:#8FB3A0"></span></div>
      <div class="wk" style="grid-column:19"><span class="b" style="height:82.1px;background:#8FB3A0"></span></div>
    </div>
    <div class="row nums" style="grid-template-columns:minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr)">
      <span class="no" style="grid-column:1">1</span>
      <span class="no" style="grid-column:2">2</span>
      <span class="no" style="grid-column:3">3</span>
      <span class="no" style="grid-column:4">4</span>
      <span class="no" style="grid-column:6">5</span>
      <span class="no" style="grid-column:7">6</span>
      <span class="no" style="grid-column:8">7</span>
      <span class="no" style="grid-column:9">8</span>
      <span class="no" style="grid-column:10">9</span>
      <span class="no" style="grid-column:11">10</span>
      <span class="no" style="grid-column:13">11</span>
      <span class="no" style="grid-column:14">12</span>
      <span class="no on" style="grid-column:15">13</span>
      <span class="no" style="grid-column:17">14</span>
      <span class="no" style="grid-column:18">15</span>
      <span class="no" style="grid-column:19">16</span>
    </div>
    <div class="row grps" style="grid-template-columns:minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr) 12px minmax(0,1fr) minmax(0,1fr) minmax(0,1fr)">
      <div class="grp" style="grid-column:1 / 5"><span style="background:#7FA7C9"></span>Base</div>
      <div class="grp" style="grid-column:6 / 12"><span style="background:#4D6F8C"></span>Build</div>
      <div class="grp" style="grid-column:13 / 16"><span style="background:#C98A8A"></span>Peak</div>
      <div class="grp" style="grid-column:17 / 20"><span style="background:#8FB3A0"></span>Taper</div>
    </div>
  </div></div>
  <figcaption>Weekly distance for a 16-week half marathon plan</figcaption>
</figure>

Every pace comes from race pace, which is set by my target time or the current race estimate. Each kind of run is a fixed share of race pace. A 1:56 half marathon is 5:30 /km. For that race, easy runs are 6:36 to 7:09 /km, tempo is 5:20 and intervals are 5:04.

The rules that keep a plan sensible include the 10 % weekly increase, a long run of no more than 45 % of the week, and at most 2 hard sessions. I can edit them, but only within limits, so I can't talk myself into something silly.

## adapting the plan

After every sync, calruns matches my runs to the plan and adjusts. If I ran clearly more than planned, the next sessions are trimmed. If I keep running over, it suggests a bit more distance. If I miss a long run, it suggests repeating it rather than making the next one longer.

The hardest part was deciding **which changes should happen without asking me**. calruns applies small changes, like a reduction or a swap to an easy run, and tells me. Anything bigger waits for my OK, and I can undo every change.

<figure class="pcal pchg" role="figure" aria-label="How your plan has adapted, newest first. Waiting for approval: repeat the 16 km long run on Sunday 31 January instead of 17 km, because the long run on Sunday 24 January was missed. Applied automatically, with an Undo all button: two sessions trimmed after a tempo run 2.9 km longer than planned, intervals from 6.5 to 5.5 km and an easy run from 7 to 5 km">
  <div class="frame" aria-hidden="true"><div class="card">
    <div class="head"><span class="h2">How your plan has adapted</span></div>
    <ol class="log">
      <li class="entry"><span class="line"></span><span class="dot latest"></span><div class="chg">
        <div class="chg-head"><div class="body">
          <div class="what">Repeat 16 km long run on Sun 31 Jan?</div>
          <div class="why"><span class="why-label">Why</span><span>You missed the long run on Sun 24 Jan.</span></div>
        </div><div class="meta"><span>Mon 25 Jan · 06:30</span><span class="wait">Waiting for you</span></div></div>
        <ul class="items"><li class="it">31 Jan · Long run: <span class="old">17 km</span> <span class="ar">→</span> <span class="new">16 km</span></li></ul>
      </div></li>
      <li class="entry last"><span class="dot"></span><div class="chg">
        <div class="chg-head"><div class="body">
          <div class="what">2 sessions adjusted after your tempo run on 19 Jan</div>
          <div class="why"><span class="why-label">Why</span><span>Your tempo run on Tue 19 Jan was 9.4 km, 2.9 km more than planned.</span></div>
        </div><div class="meta"><span>Tue 19 Jan · 19:40</span><svg width="16" height="16" viewBox="0 0 16 16" fill="none" stroke="#34784f" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="8" cy="8" r="6.5"/><path d="M5.2 8.2l2 2 3.7-4"/></svg><span class="undo">Undo all</span></div></div>
        <ul class="items"><li class="it">22 Jan · Intervals: <span class="old">6.5 km</span> <span class="ar">→</span> <span class="new">5.5 km</span></li><li class="it">23 Jan · Easy: <span class="old">7 km</span> <span class="ar">→</span> <span class="new">5 km</span></li></ul>
      </div></li>
    </ol>
  </div></div>
  <figcaption>Plan changes, each with a one-line reason</figcaption>
</figure>


If I'm ill or injured, I can pause the plan, and it eases me back in with up to three easier weeks depending on how long I was out.

## recovery

The recovery page shows whether my numbers this morning are inside my normal range. It checks HRV, sleep and resting heart rate, each against my own history rather than a population average.

<figure class="pcal psig" role="figure" aria-label="The three recovery signals for one night. HRV 52 ms, inside Garmin's balanced range of 45 to 61 ms. Sleep 6 hours 41 minutes, short: 1 hour 24 minutes under an 8 hour 5 minute need. Resting heart rate 49 bpm, inside: 1 bpm above a normal of 48">
  <div class="frame" aria-hidden="true"><div class="card"><div class="tiles">
    <div class="tile">
      <div class="top"><span class="name">HRV</span><span class="pill in">Inside</span></div>
      <div class="val">52<span class="unit">ms</span></div>
      <div><div class="track"><span class="band" style="left:30%;width:53.33%"></span><span class="dot" style="left:53.33%;background:#34784f"></span></div><div class="ticks"><span style="left:30%">45</span><span style="left:83.33%">61</span></div></div>
      <div class="lines">Balanced range 45&#8288;–&#8288;61&nbsp;ms</div>
    </div>
    <div class="tile">
      <div class="top"><span class="name">Sleep</span><span class="pill out">Short</span></div>
      <div class="val">6h 41m</div>
      <div><div class="track"><span class="band" style="left:50%;width:42.86%"></span><span class="dot" style="left:47.14%;background:#9f5820"></span></div></div>
      <div class="lines"><span class="under">−1h 24m</span> on your <span style="white-space:nowrap">8h 05m need</span></div>
    </div>
    <div class="tile">
      <div class="top"><span class="name">Resting HR</span><span class="pill in">Inside</span></div>
      <div class="val">49<span class="unit">bpm</span></div>
      <div><div class="track"><span class="band" style="left:18.75%;width:40.63%"></span><span class="dot" style="left:50%;background:#34784f"></span></div></div>
      <div class="lines">+1 bpm on your normal of 48</div>
    </div>
  </div></div></div>
  <figcaption>The three recovery signals, each against my own normal</figcaption>
</figure>

I decided **not** to build a single readiness score. Three clear signals are easier to trust than one number made from them. The wording is deliberate too: "inside", "outside", "short" and "raised", never "healthy", "risk" or "recovered", and no red anywhere. calruns isn't a medical tool, and I didn't want it to sound like one. For the same reason, recovery never changes the plan by itself.
