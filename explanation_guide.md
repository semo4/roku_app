# Roku App — Explanation Guide

A step-by-step walkthrough of every file in this three-page Roku channel
(`Home`, `Blogs`, `Live Video`), how they connect, and the design decisions made.

---

## Table of Contents

1. [Project Structure](#1-project-structure)
2. [App Entry Point — `Main.brs`](#2-app-entry-point--mainbrs)
3. [App Manifest — `manifest`](#3-app-manifest--manifest)
4. [Main Scene — `main.xml`](#4-main-scene--mainxml)
5. [Home Page — `HomePage.xml`](#5-home-page--homepagexml)
6. [Blogs Page — `BlogsPage.xml`](#6-blogs-page--blogspagexml)
7. [Blog Card Component — `BlogCard.xml`](#7-blog-card-component--blogcardxml)
8. [API Background Task — `BlogsApiTask.xml`](#8-api-background-task--blogsapitaskxml)
9. [Live Video Page — `LiveVideoPage.xml`](#9-live-video-page--livevideopagexml)
10. [Navigation Model](#10-navigation-model)
11. [Color Scheme](#11-color-scheme)
12. [Remote-Control Key Map](#12-remote-control-key-map)
13. [Data Flow Diagrams](#13-data-flow-diagrams)

---

## 1. Project Structure

```
roku_app/
├── manifest                   App metadata (title, icons, splash screen)
├── source/
│   └── Main.brs               Entry point — creates the Scene
└── components/
    ├── main.xml               Root Scene — nav bar + page management
    ├── HomePage.xml           Page 0: welcome hero + feature cards
    ├── BlogsPage.xml          Page 1: scrollable grid fetched from API
    ├── BlogCard.xml           Reusable card rendered inside BlogsPage
    ├── BlogsApiTask.xml       Background Task that calls the REST API
    └── LiveVideoPage.xml      Page 2: HLS live-stream video player
```

> Legacy files (`blogs.xml`, `CardItemBlogs.xml`, `CardItemMain.xml`) are kept
> for reference but are no longer wired into the app.

---

## 2. App Entry Point — `Main.brs`

```brightscript
sub Main()
    screen = CreateObject("roSGScreen")
    m.port = CreateObject("roMessagePort")
    screen.SetMessagePort(m.port)
    scene = screen.CreateScene("MainPage")   ' ← loads main.xml
    screen.Show()
    while(true)
        msg = wait(0, m.port)
        if type(msg) = "roSGScreenEvent"
            if msg.isScreenClosed() then return
        end if
    end while
end sub
```

**What it does:**

| Step | Action |
|------|--------|
| 1 | Creates an `roSGScreen` — Roku's SceneGraph display surface |
| 2 | Creates an `roMessagePort` for receiving system messages |
| 3 | Instantiates the `MainPage` Scene (defined in `main.xml`) |
| 4 | Enters an event loop that keeps the channel alive until the screen is closed |

Nothing else needs to change here — all app logic lives in the SceneGraph
components.

---

## 3. App Manifest — `manifest`

The manifest registers the channel with the Roku OS:

```
title=OptimumPartners
major_version=1 / minor_version=0 / build_version=00002
mm_icon_focus_*   → pkg:/images/op.webp   (menu icon)
splash_screen_*   → pkg:/images/op.webp   (launch splash)
splash_color=#000000
splash_min_time=5
```

No changes were needed. Roku automatically discovers every `*.xml` file
in `components/` and registers each `<component>` tag as a node type.

---

## 4. Main Scene — `main.xml`

**Role:** Top-level container. Owns the nav bar and manages which page is
visible. Holds focus except when the Live Video page is active.

### 4.1 Layout (XML children)

```
┌─────────────────────────────────────────────────────────┐  y=0
│  navBg  (0x0B1E33FF)                            h=68    │
│  LayoutGroup: [ HOME ] [ BLOGS ] [ LIVE VIDEO ]         │
├─────────────────────────────────────────────────────────┤  y=68
│  navLine (0x00c996FF accent)                    h=3     │
├─────────────────────────────────────────────────────────┤  y=71
│  Page content area  (649 px tall)                       │
│  — HomePage      (visible initially)                    │
│  — BlogsPage     (hidden)                               │
│  — LiveVideoPage (hidden)                               │
└─────────────────────────────────────────────────────────┘  y=720
```

### 4.2 BrightScript logic

**`init()`**
- Sets `backgroundColor = "0x07111EFF"` (dark navy fills the whole scene).
- Collects `navHome`, `navBlogs`, `navLive` labels and sets `font.size = 26`.
- Calls `createChild()` to instantiate all three pages — they are created
  immediately so the Blogs API starts loading in the background before the
  user ever navigates to that page.
- Registers `observeField("requestClose", "onLiveClose")` on the live page.

**`updateNav()`**  
Colors the active label **green** (`0x00c996FF`) and inactive labels
**blue** (`0x4A90E2FF`).

**`showPage(index)`**  
- Guards against re-entering the same page.
- If leaving page 2 (Live), sets `m.livePage.active = false` (stops video).
- Loops through `m.pages[]` and sets `visible` based on `index`.
- If entering page 2, sets `active = true` then calls `setFocus(true)` on
  the live page so it handles key events.

**`onLiveClose()`**  
Observed whenever `requestClose` changes on `LiveVideoPage`.  
Only acts when `requestClose = true AND navIndex = 2`, then calls
`showPage(0)` to return to Home.

**`onKeyEvent(key, press)`**

| Key | NavIndex | Action |
|-----|----------|--------|
| RIGHT | 0 or 1 | `showPage(navIndex + 1)` |
| LEFT | 1 or 2 | `showPage(navIndex - 1)` |
| DOWN | 1 (Blogs) | decrements `blogsScrollY` by 130 px, writes to `m.blogsPage.scrollY` |
| UP | 1 (Blogs) | increments `blogsScrollY`, clamps to 0 |

> Key events reach `onKeyEvent` in `MainPage` either because `MainPage`
> itself is focused (Home/Blogs pages) or because `LiveVideoPage` returned
> `false` for LEFT/RIGHT (key propagation up the focus chain).

---

## 5. Home Page — `HomePage.xml`

**Extends:** `Group`  
**Visible by default** — shown when the app launches.

### Layout

```
┌──────────────────────────────────────────────────────────┐  y=0
│  Hero section  (0x0D2440FF)                    h=300     │
│  ▌ Green accent bar (5 px wide)                          │
│  "Welcome to OptimumPartners"   (white, 40 px)           │
│  Subtitle line                  (green, 24 px)           │
│  Description                    (muted blue, 20 px)      │
├──────────────────────────────────────────────────────────┤  y=300
│  (gap)                                                   │
├──────────────────────────────────────────────────────────┤  y=330
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  feature cards│
│  │ Blogs    │  │Live Video│  │Community │  h=220 each    │
│  │ (green)  │  │ (blue)   │  │ (green)  │               │
│  └──────────┘  └──────────┘  └──────────┘               │
├──────────────────────────────────────────────────────────┤  y=615
│  Navigation hint                                         │
└──────────────────────────────────────────────────────────┘  y=649
```

Each feature card has a 4 px top accent bar in the page's accent color
(`0x00c996FF` green or `0x1A6BFFFF` blue) and a dark background
(`0x0D2440FF`).

**No dynamic data** — purely static markup; `init()` only sets font sizes.

---

## 6. Blogs Page — `BlogsPage.xml`

**Extends:** `Group`  
**Data source:** `https://opencollective.com/babel/members/all.json?TierId=1906&limit=10&offset=0`

### Layout

```
y=0  ┌── Header bar (0x0B1E33FF, h=64) ─────────────────────┐
     │  ▌ "Blogs"  /  "Community Contributors"              │
y=64 ├── cardsScroll (clippingRect=[0,0,1200,565]) ──────────┤
     │  ┌ cardsGrid (translated by scrollY) ──────────────┐ │
     │  │  [BlogCard][BlogCard][BlogCard]  ← row 0        │ │
     │  │  [BlogCard][BlogCard][BlogCard]  ← row 1        │ │
     │  │  [BlogCard][BlogCard][BlogCard]  ← row 2        │ │
     │  │  [BlogCard]  …                  ← row 3        │ │
     │  └─────────────────────────────────────────────────┘ │
y=635└── Scroll hint (hidden until data loads) ──────────────┘
```

### Key design decisions

**`clippingRect`** on `cardsScroll` ensures `BlogCard` nodes that have
scrolled above or below the viewport are not rendered.

**`scrollY` interface field** — `MainPage` writes to this field and Roku
fires the `onChange` callback (`onScrollChange`) synchronously on the
render thread, which updates `cardsGrid.translation`.

**API task starts in `init()`** — the data is pre-loaded so it is ready
when the user navigates to the Blogs page.

### Card grid math

```
cardW = 370,  cardH = 260
hGap  = 25,   vGap  = 30
cols  = 3

x = (i mod 3) * (370 + 25)   = col × 395
y = 10 + (i \ 3) * (260 + 30) = 10 + row × 290

Total height for 10 items (4 rows) ≈ 10 + 4 × 290 = 1170 px
Visible window = 565 px  →  scroll range ≈ 605 px
```

`MainPage` clamps `blogsScrollY` to `[-620, 0]`.

---

## 7. Blog Card Component — `BlogCard.xml`

**Extends:** `Group`  
**Used by:** `BlogsPage` (creates one per API item)

### Interface fields

| Field | Type | onChange handler |
|-------|------|-----------------|
| `memberName` | string | `onNameChange` — sets label text; derives initial letter for avatar |
| `memberRole` | string | `onRoleChange` — replaces `_` with space (e.g. `HOST_ORGANIZATION` → `HOST ORGANIZATION`) |
| `memberDesc` | string | `onDescChange` — truncates to 110 chars |
| `imgUrl` | string | `onImgChange` — shows `Poster` if URL provided, else shows lettered placeholder |

### Visual structure (370 × 260 px)

```
┌────────────────────────────────────────────────────────┐
│████ green accent (4 px)                                │
│█ │  ┌──────────┐  Member Name  (white, 22 px)          │
│B │  │  Poster  │  MEMBER ROLE  (green, 16 px)          │
│l │  │  or      │                                       │
│u │  │  avatar  │               ──────────────────────  │
│e │  └──────────┘  Description text (muted, 16 px)      │
│  │                limited to ~110 characters            │
└────────────────────────────────────────────────────────┘
```

**Avatar placeholder:** `imgPlaceholder` (dark rectangle) + `imgInitial`
(first letter of the member name, in green) — shown whenever `imgUrl` is
empty or not yet provided.

---

## 8. API Background Task — `BlogsApiTask.xml`

**Extends:** `Task`  
**Thread:** Runs on a background thread so the UI never blocks.

### Interface

| Field | Direction | Description |
|-------|-----------|-------------|
| `apiUrl` | input | Full URL written by `BlogsPage` before starting |
| `result` | output | `roArray` of `roAssociativeArray` objects; `BlogsPage` observes this |

### How it works

```
init()  →  functionName = "GetBlogs"   (Roku calls this function on the Task thread)

GetBlogs():
  1. Create roUrlTransfer
  2. SetCertificatesFile("common:/certs/ca-bundle.crt")  ← handles HTTPS
  3. GET request → raw JSON string
  4. ParseJson() → roArray
  5. Write to m.top.result  → triggers observer in BlogsPage
```

`InitClientCertificates()` + `SetCertificatesFile()` are required for
outbound HTTPS on Roku devices.

---

## 9. Live Video Page — `LiveVideoPage.xml`

**Extends:** `Group`  
**Stream:** `https://maitv-vod.lab.eyevinn.technology/VINN.mp4/master.m3u8` (HLS)

### Layout

```
y=0   ┌── Video node (1280 × 558) ─────────────────────────────┐
      │                                                        │
      │   Video fills this area; Roku maintains aspect ratio   │
      │                                                        │
y=558 ├── Info bar (0x0B1E33FF, h=91) ──────────────────────── │
      │  ●LIVE  "OptimumPartners Live Stream"                  │
      │  [status label]          [controls hint]              │
y=649 └────────────────────────────────────────────────────────┘
```

### active / requestClose lifecycle

```
MainPage sets active=true
  └─► onActiveChange()
        ├─ resets requestClose = false
        └─ startVideo()
               ├─ builds ContentNode (url + streamFormat="hls")
               └─ videoPlayer.control = "play"

User presses BACK
  └─► onKeyEvent(back)
        ├─ stopVideo()           (control = "stop")
        ├─ requestClose = true
        └─ return true           (event consumed)

MainPage's onLiveClose observer fires
  └─► showPage(0)               (back to Home)
```

### Key handling

| Key | Action | Returns |
|-----|--------|---------|
| BACK | Stop video, set `requestClose = true` | `true` (consumed) |
| OK | Toggle play / pause | `true` (consumed) |
| LEFT / RIGHT | Not handled | `false` → propagates to MainPage for nav switching |

---

## 10. Navigation Model

```
App start
    │
    ▼
[MainPage] ── has focus ──► shows HomePage
    │
    ├── RIGHT ──────────────► shows BlogsPage  (MainPage keeps focus)
    │       UP/DOWN ────────► MainPage scrolls BlogsPage via scrollY field
    │       LEFT ───────────► shows HomePage
    │
    └── RIGHT (×2) ──────────► shows LiveVideoPage, gives focus to it
            LiveVideoPage.back ──► requestClose=true ──► showPage(0)
            LiveVideoPage.left/right ──► propagates to MainPage ──► nav switch
```

**Why MainPage keeps focus for the Blogs page:**  
Scrolling is handled by `MainPage.onKeyEvent` writing to `BlogsPage.scrollY`.
This avoids having to set/return focus between two nodes for a simple
scroll operation.

**Why LiveVideoPage gets focus:**  
The Video node needs a focused ancestor to deliver buffering/error state
events correctly, and the live page needs to intercept BACK/OK for
playback control.

---

## 11. Color Scheme

| Token | Hex | Usage |
|-------|-----|-------|
| **Primary Green** | `0x00c996FF` | Active nav label, card accents, role text, avatar initial |
| **Blue** | `0x4A90E2FF` | Inactive nav labels, hints, status labels |
| **Deep Blue** | `0x1A6BFFFF` | Card left accent bar, Live Video feature card accent |
| **Scene Background** | `0x07111EFF` | Root background color |
| **Nav / Header Bar** | `0x0B1E33FF` | Nav bar, page headers, info bars |
| **Card Background** | `0x0D2440FF` | Individual card fill |
| **Image Placeholder** | `0x1A3050FF` | Avatar background when no image |
| **Primary Text** | `0xFFFFFFFF` | Titles, member names |
| **Secondary Text** | `0x88AABBFF` | Descriptions, body copy |
| **Live Indicator** | `0xFF3344FF` | Red dot + "LIVE" label |

All alpha channels are `FF` (fully opaque) unless otherwise noted.

---

## 12. Remote-Control Key Map

| Key | Context | Result |
|-----|---------|--------|
| **RIGHT** | Any page | Advance to next nav item |
| **LEFT** | Any page | Go back to previous nav item |
| **DOWN** | Blogs page | Scroll cards down (−130 px per press) |
| **UP** | Blogs page | Scroll cards up (+130 px per press) |
| **OK** | Live Video | Toggle play / pause |
| **BACK** | Live Video | Stop stream, return to Home |
| **BACK** | Home / Blogs | Default Roku behaviour (exits app or goes up) |

---

## 13. Data Flow Diagrams

### Blogs page data flow

```
BlogsPage.init()
  └─► Creates BlogsApiTask
  └─► Sets apiUrl field
  └─► Sets control = "RUN"
        │
        │  (background thread)
        ▼
  BlogsApiTask.GetBlogs()
  └─► roUrlTransfer.GetToString()
  └─► ParseJson()
  └─► Sets result field
        │
        │  (onChange observer, render thread)
        ▼
  BlogsPage.onApiResult()
  └─► Iterates data array
  └─► createChild("BlogCard") for each item
  └─► Sets memberName / memberRole / imgUrl / memberDesc
        │
        │  (field onChange handlers, synchronous)
        ▼
  BlogCard updates labels and Poster URI
```

### Live video activation flow

```
User navigates RIGHT to Live Video
  └─► MainPage.showPage(2)
        └─► m.livePage.active = true
              └─► LiveVideoPage.onActiveChange()
                    └─► startVideo()
                          └─► ContentNode.url  = HLS URL
                          └─► ContentNode.streamFormat = "hls"
                          └─► videoPlayer.content = content
                          └─► videoPlayer.control = "play"
                                │
                                ▼
                          videoPlayer.state changes
                          └─► onVideoStateChange()
                                └─► updates statusLabel text
```
