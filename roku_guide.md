# Complete Guide to Building a Roku TV App - Full Instructions & Explanations

## Table of Contents
1. [Prerequisites & Setup](#1-prerequisites--setup)
2. [Programming Languages](#2-programming-languages---the-foundation)
3. [Development Tools](#3-development-tools)
4. [First Steps (Account & Device Setup)](#4-first-steps---getting-started)
5. [Building Your First App (Hello World)](#5-building-your-first-app-hello-world)
6. [SceneGraph Framework Explained](#6-scenegraph-framework---ui-architecture)
7. [App Structure & Components](#7-app-structure--components)
8. [Development Workflow](#8-development-workflow)
9. [Testing & Debugging](#9-testing--debugging)
10. [Publishing Your App](#10-learning-resources)

---

## 1. PREREQUISITES & SETUP

### What You Need Before Starting:
- **Computer:** Windows, Mac, or Linux machine for development
- **Roku Device:** A Roku TV or player device (purchase from Roku.com or retailers)
- **Internet Connection:** For account creation and app deployment
- **Web Browser:** For accessing the Roku Developer Dashboard
- **Text Editor/IDE:** Visual Studio Code recommended

---

## 2. PROGRAMMING LANGUAGES - The Foundation

Roku app development uses two complementary languages:

### A. SceneGraph (XML-based)
- **Purpose:** Designs the User Interface (UI)
- **Similar to:** HTML for web pages
- **Function:** Creates visual layouts, buttons, grids, screens
- **Example:** Creating a menu screen or video player interface

### B. BrightScript
- **Purpose:** Defines app behavior and logic
- **Similar to:** JavaScript for web pages
- **Function:** Handles user interactions, data processing, network calls
- **Example:** When user clicks a button, what should happen?

> **Key Concept:** Think of SceneGraph as *"what it looks like"* and BrightScript as *"how it works"*

---

## 3. DEVELOPMENT TOOLS

### Official Roku Tools:

| Tool | Purpose |
|------|---------|
| Roku Developer Dashboard | Central hub for creating, managing, and publishing apps |
| Development Application Installer | Installs apps on your test device |
| Layout Editor | Visual UI design tool |
| BrightScript Profiler | Analyzes app performance (CPU, memory usage) |
| Screenshot Utility | Takes HD screenshots for app store listings |
| Debug Console | Views runtime output and errors |

### Community Tools (Highly Recommended):

**BrightScript Extension for Visual Studio Code**

Features:
- Code completion and syntax highlighting
- Interactive debugging sessions
- Automatic code formatting
- In-editor log viewing
- Symbol navigation
- Direct client-side validation

---

## 4. FIRST STEPS - Getting Started

### Step 1: Create a Roku Customer Account
1. Go to [Roku.com](https://www.roku.com)
2. Click "Create Account"
3. Use an active, monitored email address (Roku sends important notifications)
4. Verify your email

### Step 2: Enroll in Roku Developer Program
1. Visit the Roku Developer enrollment page
2. Accept the Roku Application Distribution Agreement
3. Gain access to the Developer Dashboard

### Step 3: Obtain a Roku Device
Purchase options:
- Roku.com
- Local electronics retailers
- Second-hand marketplaces
- Roku TVs or standalone players work for development

### Step 4: Activate Developer Mode on Your Device

**On Roku Remote:**
1. Press **Home** button **3 times**
2. Press **Up arrow** **2 times**
3. Press **Right, Left, Right, Left, Right** (sequence important!)

**On Screen:**
1. Write down the IP address displayed
2. Click "Enable Development Application Installer"
3. Accept the Developer Tools License Agreement
4. Set a developer password (case-sensitive - write it down!)
5. Device will reboot automatically

> **After reboot:** Your device is ready for sideloading apps!

---

## 5. BUILDING YOUR FIRST APP (HELLO WORLD)

### Phase 1: Download & Extract
1. Download the Roku Hello World sample app from GitHub
2. Unzip the file
3. Navigate to: `/hello-world-master/dist/apps/hello-world`

### Phase 2: Understand the Directory Structure

```bash
hello-world/
├── components/          # SceneGraph XML files (UI layout)
├── source/             # BrightScript files (logic, main entry point)
├── images/             # Splash screens and artwork
├── manifest            # App metadata and versioning
└── makefile           # Optional automation tool
```

> **Important:** Maximum 100 files per directory to avoid performance issues

### Phase 3: Edit the App

Open: `components/helloworld.xml`

Default content (simplified):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<component name="helloworld" extends="Scene">
  <children>
    <Label id="label" text="Hello World" />
  </children>
  <script type="text/brightscript" uri="helloworld.brs" />
</component>
```

To customize:
- Change the `text` field (line with "Hello World")
- Example: `text="My First Roku App!"`
- Optional: Change color by setting `label.color = 0xFFFFFF` (white)

Color codes (hex format):
- White: `0xFFFFFF`
- Red: `0xFF0000`
- Blue: `0x0000FF`
- Green: `0x00FF00`

Save the file.

### Phase 4: Package Your App
1. Create a ZIP file of the `hello-world` directory contents
2. **Important:** Zip the *contents*, NOT the folder itself
3. Name it descriptively: `my-hello-world.zip`

### Phase 5: Sideload the App
1. Open your web browser
2. Enter your Roku device's IP address (the one you noted)
3. Log in:
   - **Username:** `rokudev`
   - **Password:** The one you set during developer mode activation
4. Development Application Installer appears
5. Click "Upload"
6. Select your `my-hello-world.zip` file
7. Choose compression:
   - `squashfs` = faster (recommended)
   - `zip` = standard

> **Result:** Your app launches on the TV!

### Phase 6: Using the Debug Console

**Why use it?**
- See runtime errors
- Debug variables
- View console output
- Check compilation errors

**To open debug console:**

On Windows (PuTTY):
```
telnet [roku-ip-address] 8085
```

On Mac/Linux (Terminal):
```
telnet roku-ip-address 8085
```

Replace `roku-ip-address` with your actual IP address (e.g., `192.168.1.140`)

**Steps:**
1. Open telnet connection
2. Back in web browser, click **Delete** then **Upload** again
3. Select the same ZIP file
4. Click **Replace**
5. In debug console, see your app's output

---

## 6. SCENEGRAPH FRAMEWORK - UI Architecture

### Core Concept
SceneGraph is an object-oriented XML-based UI framework that organizes your app's interface into reusable components.

### Key Components

#### A. Scene (Root Container)
- Top-level component
- Everything displays on a scene
- Like the "main stage" of your app

#### B. Common UI Components

| Component | Purpose | Example |
|-----------|---------|---------|
| Label | Display text | "Welcome to My App" |
| Button | Clickable button | "Play", "Settings" |
| Rectangle | Draw shapes/colors | Background boxes |
| Image | Display pictures | Thumbnails, logos |
| Group | Container for other components | Menu section |
| LayoutGroup | Auto-arrange child elements | Grid of items |
| GridScreen | Display content in grid | Video catalog |
| PosterGrid | Video poster grid | Netflix-style view |
| Video | Playback controls | Player component |

### Basic SceneGraph Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<component name="MyScene" extends="Scene">
  <children>
    <!-- Your UI elements go here -->
    <Rectangle id="background" width="1280" height="720" color="0x000000" />
    <Label id="title" text="My App" x="50" y="50" />
    <Button id="playBtn" text="Play" x="50" y="150" />
  </children>
  
  <script type="text/brightscript" uri="myscene.brs" />
</component>
```

### Understanding XML Attributes
- `id`: Unique identifier (used in BrightScript)
- `text`: Content text
- `x`, `y`: Position on screen (0,0 = top-left)
- `width`, `height`: Dimensions in pixels
- `color`: RGB in hex format (`0xRRGGBB`)
- `visible`: Show/hide element (`true`/`false`)
- `opacity`: Transparency (`0.0` = invisible, `1.0` = opaque)

---

## 7. APP STRUCTURE & COMPONENTS

### A. Manifest File (App Metadata)
- **Location:** Root directory
- **Purpose:** Defines app properties

Example:
```makefile
title=My Streaming App
major_version=1
minor_version=0
build_version=1
mm_icon_focus_hd=pkg:/images/icon.png
splash_screen_sd=pkg:/images/splash.png
```

**Key Fields:**
- `title`: Your app name
- `major_version`: Major release number
- `minor_version`: Minor updates
- `build_version`: Build number
- `mm_icon_focus_hd`: App icon (280x210px)
- `splash_screen_sd`: Loading screen

### B. Source Directory (BrightScript Logic)
- **Main entry point:** `main.brs`
- **Function:** Initializes the app and displays the main scene

Example:
```brightscript
Sub Main()
    ' Create and display the scene
    screen = CreateObject("roSGScreen")
    scene = screen.CreateScene("MyScene")
    screen.Show()
    
    ' Keep app running
    While True
        msg = wait(0)
    End While
End Sub
```

### C. Components Directory (UI Layouts)
**Naming convention:** `Name.xml` → `Name.brs`

Example File Structure:
```
components/
├── HomeScreen.xml      # UI layout
├── HomeScreen.brs      # Associated logic
├── VideoPlayer.xml
├── VideoPlayer.brs
├── GridScreen.xml
└── GridScreen.brs
```

---

## 8. DEVELOPMENT WORKFLOW

### Step-by-Step Development Process

```
1. Design → 2. Code → 3. Sideload → 4. Test → 5. Debug → 6. Repeat
```

### Detailed Workflow

**1. DESIGN Phase**
- Sketch your app screens
- Plan user interactions
- List required components

**2. CODE Phase**
- Create SceneGraph XML files (UI)
- Write BrightScript code (logic)
- Structure files in proper directories

**3. SIDELOAD Phase**
- Create ZIP file
- Upload via Development Application Installer
- App installs on your test device

**4. TEST Phase**
- Test all features
- Check navigation
- Verify user interactions

**5. DEBUG Phase**
- Use debug console
- View errors and logs
- Check variable values

**6. ITERATE Phase**
- Make improvements
- Re-sideload
- Repeat until satisfied

### Automated Deployment with Makefile

**Purpose:** Automate the sideloading process

**Setup Steps:**

Verify file structure:
- `makefile` in same directory as `manifest`
- `app.mk` file present

Set environment variables:

Windows (Command Prompt):
```batch
set ROKU_DEV_TARGET=192.168.1.140
set DEVPASSWORD=your_password
cd path\to\hello-world
make install
```

Mac/Linux (Terminal):
```bash
export ROKU_DEV_TARGET=192.168.1.140
export DEVPASSWORD=your_password
cd /path/to/hello-world
make install
```

**Variables:**
- `ROKU_DEV_TARGET`: Your Roku device IP address
- `DEVPASSWORD`: Your developer mode password

> **Result:** App automatically sideloads with one command!

---

## 9. TESTING & DEBUGGING

### Debug Console Features

What it shows:
- ✓ Runtime errors with line numbers
- ✓ Compilation errors
- ✓ Variable values at crash time
- ✓ Console output (print statements)
- ✓ Performance metrics

### Adding Debug Output

In BrightScript:
```brightscript
print "This message appears in debug console"
print "User clicked button: " + buttonId
```

### Common Error Types

| Error Type | Example | Fix |
|------------|---------|-----|
| Syntax Error | Missing quote | Check code syntax |
| Runtime Error | Undefined variable | Declare variables |
| Component Error | Wrong component name | Verify component exists |
| Type Error | Treating string as number | Check data types |

### Using the Roku Plug-in Utilities

**Screenshot Utility**
- **Purpose:** Generate app store images
- **Resolution:** 1280x720 (HD) or 1920x1080 (FHD)
- Steps:
  1. Open Utilities tab
  2. Click "Screenshot"
  3. Screenshot saves to computer
  4. Use for publishing

**Profiling Data Utility**
- **Purpose:** Analyze performance
- Metrics captured:
  - CPU usage
  - Memory usage
  - Wall-clock time
  - Function call counts
- Steps:
  1. Click "Profiling data"
  2. Run your app
  3. Analyze with BrightScript Profiler

**Package Inspector**
- **Purpose:** Verify package details
- Information shown:
  - Developer ID
  - Creation date
  - Package version

**Rekey Utility**
- **Purpose:** Replace signing key
- **Use case:** Developing multiple apps on same device

---

## 10. LEARNING RESOURCES

### Official Course: SceneGraph Developers: Build an App

This is a 13-video course that teaches everything:

**Topics Covered:**

*Introductory Materials*
- Getting started on Roku
- SceneGraph core concepts
- Creating content feeds

*Building Essential Screens*
- Grid Screen (content catalog view)
- Video Player (playback)
- Details Screen (content information)
- Episode Selection Screen

*Advanced Features*
- Monetization with video ads
- Subscription integration
- Deep linking
- Certification testing

### Sample Apps
Roku provides full working examples:
1. Download from GitHub
2. Review complete code
3. Sideload and test
4. Use as template for your app

### Community Resources
- **Roku Developer Forum:** Discussion threads, Q&A
- **Roku Developer Slack:** Real-time chat with community
- **GitHub Repository:** Sample code and tools
- **Visual Studio Code Extension:** Community-maintained development tool

---

## QUICK START CHECKLIST

**Week 1: Setup**
- [ ] Create Roku account
- [ ] Enroll in developer program
- [ ] Get Roku device
- [ ] Activate developer mode
- [ ] Download Hello World sample

**Week 2: First App**
- [ ] Edit Hello World app
- [ ] Create ZIP file
- [ ] Sideload on device
- [ ] View in debug console
- [ ] Customize colors and text

**Week 3: Learn Framework**
- [ ] Watch SceneGraph intro videos
- [ ] Understand XML structure
- [ ] Learn BrightScript basics
- [ ] Study sample components

**Week 4+: Build Real App**
- [ ] Design your app screens
- [ ] Create UI components
- [ ] Implement logic
- [ ] Test thoroughly
- [ ] Prepare for publishing

---

## KEY TERMS GLOSSARY

| Term | Definition |
|------|-----------|
| Sideload | Install an app on test device outside the Streaming Store |
| Manifest | File defining app metadata and configuration |
| SceneGraph | XML-based UI framework for app interfaces |
| BrightScript | Scripting language for app logic and behavior |
| Component | Reusable UI or functional element |
| Scene | Top-level container for app display |
| Debug Console | Tool for viewing app output and errors |
| Streaming Store | Roku's app marketplace |
| Roku OS | Operating system for Roku devices |
| Grid Screen | UI component showing items in grid layout |

---

## NEXT STEPS

1. Enroll in the official SceneGraph course
2. Build your Hello World app
3. Study the sample apps on GitHub
4. Join the developer community
5. Start designing your streaming app

---

# Complete Roku SceneGraph Components Guide

## FOUNDATIONAL / CONTAINER NODES

### 1. Group

**Purpose:** The base container node for all renderable nodes. Acts as a foundation for positioning, transforming, and controlling visibility of child nodes.

**Use Case:** Creating hierarchical node structures, grouping related elements, controlling transparency and visibility of multiple nodes at once.

**Attributes:**

| Attribute | Type | Default | Values / Description |
|-----------|------|---------|----------------------|
| translation | vector2d | [0.0, 0.0] | [x, y] — Offsets the node position |
| rotation | float | 0.0 | Radians — Rotation around scaleRotateCenter (positive = counterclockwise) |
| scale | vector2d | [1.0, 1.0] | [x, y] — Scaling factors |
| scaleRotateCenter | vector2d | [0.0, 0.0] | [x, y] — Center point for scale/rotation operations |
| visible | boolean | true | true/false — Shows or hides node and children |
| opacity | float | 1.0 | 0.0–1.0 — Transparency (0=transparent, 1=opaque) |
| inheritParentTransform | boolean | true | true/false — Inherits parent transformation |
| inheritParentOpacity | boolean | true | true/false — Inherits parent opacity |
| clippingRect | array of float | [0,0,0,0] | [x, y, width, height] — Clips rendering to rectangle |

**Example:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<component name="MyGroupExample" extends="Group">
  <children>
    <Group id="mainContainer" translation="[100, 50]" opacity="0.8">
      <Rectangle width="500" height="300" color="0xFF0000FF"/>
      <Label text="Content Area" translation="[10, 10]"/>
    </Group>
  </children>
</component>
```

---

### 2. Scene

**Purpose:** Abstract base class for the root node of a SceneGraph application. Every SceneGraph app must extend Scene.

**Use Case:** Creating the main application scene/screen.

**Attributes:** Inherits all Group attributes, plus:

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| backgroundColor | color | 0x000000FF | Background color of the scene |
| dialog | Dialog node | — | Shows a modal dialog |
| focusedChild | node | — | Currently focused child node |

**Example:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<component name="MyApplicationScene" extends="Scene">
  <script type="text/brightscript">
    <![CDATA[
      sub init()
        m.top.backgroundColor = "0x1A1A1AFF"
        m.top.setFocus(true)
      end sub
    ]]>
  </script>
  <children>
    <Label text="Welcome to My App" translation="[640, 100]"/>
  </children>
</component>
```

---

### 3. LayoutGroup

**Purpose:** Automatically arranges child nodes in a row or column with configurable spacing and alignment.

**Use Case:** Creating menus, button bars, arranged lists without complex positioning logic.

**Attributes:**

| Attribute | Type | Default | Values |
|-----------|------|---------|--------|
| layoutDirection | string | "vertical" | "vertical" / "horizontal" |
| itemSpacings | array | [] | Array of spacing values between items |
| horizAlignment | string | "center" | "left" / "center" / "right" |
| vertAlignment | string | "center" | "top" / "center" / "bottom" |
| numRows | integer | 0 | Number of rows (0 = auto) |
| numColumns | integer | 0 | Number of columns (0 = auto) |

**Example:**
```xml
<LayoutGroup layoutDirection="horizontal" itemSpacings="[20]"
             horizAlignment="center" vertAlignment="center">
  <Button text="Play" minWidth="100" height="60"/>
  <Button text="Pause" minWidth="100" height="60"/>
  <Button text="Stop" minWidth="100" height="60"/>
</LayoutGroup>
```

---

## BASIC RENDERABLE NODES

### 4. Rectangle

**Purpose:** Draws a solid colored rectangle to the screen.

**Use Case:** Creating backgrounds, borders, visual separators, boxes for grouping content.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| width | float | 0.0 | Width in pixels |
| height | float | 0.0 | Height in pixels |
| color | color (hex) | 0xFFFFFFFF | RGBA color (e.g., 0xFF0000FF = red) |
| blendingEnabled | boolean | true | Alpha blending with background |

**Example:**
```xml
<Rectangle id="background"
           width="1280" height="720"
           color="0x00000099"
           translation="[0, 0]"/>
<Rectangle id="accent"
           width="5" height="100"
           color="0xFF6600FF"
           translation="[10, 50]"/>
```

---

### 5. Label

**Purpose:** Displays formatted text with customizable fonts, colors, and wrapping.

**Use Case:** Text display, titles, descriptions, captions, any text-based content.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| text | string | "" | Text to display |
| font | font | system default | Font specification |
| color | color | 0xddddddff | Text color (RGBA) |
| width | float | -1.0 | Width for text wrapping |
| height | float | -1.0 | Height constraint |
| wrap | boolean | false | Enable text wrapping |
| horizAlign | string | "left" | "left" / "center" / "right" |
| vertAlign | string | "top" | "top" / "center" / "bottom" |
| lineSpacing | float | 0 | Space between lines in pixels |

**Example:**
```xml
<Label id="title"
       text="Movie Title"
       font="font:LargeBoldSystemFont"
       color="0xFFFFFFFF"
       translation="[100, 50]"/>

<Label id="description"
       text="A long description that wraps..."
       font="font:MediumSystemFont"
       width="800"
       wrap="true"
       translation="[100, 150]"/>
```

---

### 6. Poster

**Purpose:** Displays bitmap image files (JPEG, PNG, etc.) with optional scaling and effects.

**Use Case:** Image display, thumbnails, artwork, icons, album covers, movie posters.

**Attributes:**

| Attribute | Type | Default | Values |
|-----------|------|---------|--------|
| uri | uri | "" | Path to image file (`pkg:/images/file.png` or `http://...`) |
| width | float | 0.0 | Width in pixels (0 = use image size) |
| height | float | 0.0 | Height in pixels (0 = use image size) |
| loadingBitmapUri | uri | "" | Image to show while loading |
| failedBitmapUri | uri | "" | Image to show if load fails |
| loadingBitmapOpacity | float | 1.0 | Opacity of loading image (0.0–1.0) |
| failedBitmapOpacity | float | 1.0 | Opacity of failed image (0.0–1.0) |

**Example:**
```xml
<Poster id="thumbnail"
        uri="pkg:/images/poster.jpg"
        width="300"
        height="450"
        translation="[100, 100]"
        loadingBitmapUri="pkg:/images/loading.png"
        failedBitmapUri="pkg:/images/failed.png"/>
```

---

### 7. SimpleLabel

**Purpose:** Lightweight, memory-efficient single-line text display with simplified font control.

**Use Case:** Labels where memory efficiency matters, simple text display without complex formatting.

**Attributes:**

| Attribute | Type | Default | Values |
|-----------|------|---------|--------|
| text | string | "" | Text to display |
| fontUri | string | system default | "font:SystemFontName" or "path/to/font.ttf" |
| fontSize | integer | system default | Font size in points |
| color | color | 0xddddddff | Text color (RGBA) |
| horizOrigin | string | "left" | "left" / "center" / "right" |
| vertOrigin | string | "top" | "top" / "center" / "baseline" / "bottom" |

**Example:**
```xml
<SimpleLabel text="Status: Ready"
             fontUri="font:MediumSystemFont"
             color="0x00FF00FF"
             horizOrigin="center"
             vertOrigin="center"
             translation="[640, 360]"/>
```

---

## LIST & GRID NODES

### 8. RowList

**Purpose:** Vertically-scrollable list of horizontally-scrollable rows (hybrid list/grid).

**Use Case:** Content browsing (Netflix-style), category lists with multiple items per row.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| content | ContentNode | — | Data for rows and items |
| itemComponentName | string | — | Custom XML component for each item |
| itemSize | vector2d | [0,0] | [width, height] of row items |
| numRows | integer | 0 | Visible rows |
| rowHeights | array | [] | Individual row heights |
| rowFocusAnimationStyle | string | "floatingFocus" | "floatingFocus" / "fixedFocus" / "fixedFocusWrap" |
| vertFocusAnimationStyle | string | "fixedFocus" | "floatingFocus" / "fixedFocus" / "fixedFocusWrap" |
| rowItemFocused | array | [] | [rowIndex, itemIndex] of focused item |
| rowItemSelected | array | [] | [rowIndex, itemIndex] of selected item |

**Example:**
```xml
<RowList id="contentList"
         translation="[0, 100]"
         itemComponentName="MovieItem"
         itemSize="[300, 200]"
         numRows="4"
         rowHeights="[200, 200, 200, 200]"/>
```

---

### 9. PosterGrid

**Purpose:** 2D grid of poster images with optional captions.

**Use Case:** Movie/image galleries, photo collections, grid-based content browsers.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| content | ContentNode | — | Grid content database |
| basePosterSize | vector2d | [0,0] | Default [width, height] of posters |
| numColumns | integer | 0 | Number of columns |
| numRows | integer | 12 | Visible rows |
| itemSpacing | vector2d | [0,0] | [horizontal, vertical] spacing |
| caption1NumLines | integer | 0 | Lines for first caption (0=none) |
| caption2NumLines | integer | 0 | Lines for second caption (0=none) |
| posterDisplayMode | string | "noScale" | "noScale" / "scaleToFit" / "scaleToFill" / "scaleToZoom" |
| drawFocusFeedback | boolean | true | Show focus indicator |

**Example:**
```xml
<PosterGrid id="movieGrid"
            basePosterSize="[250, 375]"
            numColumns="4"
            numRows="3"
            itemSpacing="[20, 20]"
            caption1NumLines="1"
            translation="[50, 100]"/>
```

---

### 10. MarkupList

**Purpose:** List with custom XML component-based items, allowing complex item layouts.

**Use Case:** Custom-designed lists with mixed content (images, text, icons).

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| content | ContentNode | — | List content |
| itemComponentName | string | — | Custom component for items |
| itemSize | vector2d | [0,0] | Item dimensions |
| itemSpacing | vector2d | [0,0] | Space between items |
| numRows | integer | 12 | Visible items |
| drawFocusFeedback | boolean | true | Show focus indicator |
| itemFocused | integer | 0 | Index of focused item |
| itemSelected | integer | 0 | Index of selected item |

**Example:**
```xml
<MarkupList id="customList"
            itemComponentName="CustomListItem"
            itemSize="[1000, 100]"
            itemSpacing="[0, 10]"
            numRows="8"/>
```

---

### 11. MarkupGrid

**Purpose:** Grid version of MarkupList with custom XML component items.

**Use Case:** Custom-designed grids with complex item presentations.

**Attributes:** Similar to MarkupList plus:

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| numColumns | integer | 0 | Number of columns |
| columnWidths | array | [] | Individual column widths |
| fixedLayout | boolean | false | Use X,Y,W,H positioning |

---

### 12. LabelList

**Purpose:** Simple list of text labels.

**Use Case:** Simple menu lists, option selections, basic lists.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| content | ContentNode | — | List content |
| itemSize | vector2d | [0, 50] | Item dimensions |
| itemSpacing | vector2d | [0, 0] | Space between items |
| numRows | integer | 12 | Visible items |
| focusedColor | color | 0xFFFFFFFF | Focused item text color |
| unfocusedColor | color | 0xddddddff | Unfocused item text color |

---

## INPUT & DIALOG NODES

### 13. Button

**Purpose:** Selectable button with label and optional icon.

**Use Case:** UI navigation, action triggers, controls.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| text | string | "" | Button label |
| textColor | color | 0xddddddff | Label color when unfocused |
| focusedTextColor | color | 0x262626ff | Label color when focused |
| textFont | font | system | Font for unfocused state |
| focusedTextFont | font | system | Font for focused state |
| iconUri | uri | "" | Icon image URL |
| focusedIconUri | uri | "" | Focused state icon |
| focusBitmapUri | uri | "" | Background when focused |
| focusFootprintBitmapUri | uri | "" | Background when unfocused |
| minWidth | float | 0 | Minimum width |
| maxWidth | float | 32767 | Maximum width |
| height | float | 64 | Button height |
| showFocusFootprint | boolean | false | Show background when unfocused |
| buttonSelected | event | — | Fires when button selected |

**Example:**
```xml
<Button id="playBtn"
        text="Play"
        textFont="font:LargeSystemFont"
        focusedTextColor="0x000000FF"
        height="80"
        minWidth="200"
        showFocusFootprint="true"
        translation="[100, 400]"/>
```

---

### 14. Keyboard

**Purpose:** On-screen keyboard for text input.

**Use Case:** User text entry, search input, password fields.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| text | string | "" | Input text |
| keyColor | color | 0xffffffff | Key label color |
| focusedKeyColor | color | 0xffffffff | Focused key color |
| textEditBox | TextEditBox | — | Internal text box node |
| showTextEditBox | boolean | true | Show/hide text display |

**Example:**
```xml
<Keyboard id="searchKeyboard"
          translation="[100, 300]"/>
```

---

### 15. TextEditBox

**Purpose:** Single text input field (used within Keyboard, standalone, or in dialogs).

**Use Case:** Direct text input without full keyboard, password fields.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| text | string | "" | Current text |
| hintText | string | "" | Placeholder text |
| maxTextLength | integer | 15 | Max characters |
| cursorPosition | integer | 0 | Cursor location |
| textColor | color | 0xffffffff | Text color |
| hintTextColor | color | 0xffffffff | Hint color |
| secureMode | boolean | false | Hide text (password) |
| active | boolean | false | Show cursor |
| backgroundUri | uri | "" | Background image |
| width | float | -1.0 | Field width |

**Example:**
```xml
<TextEditBox id="nameInput"
             hintText="Enter your name"
             width="400"
             textColor="0x000000FF"
             translation="[100, 200]"/>
```

---

### 16. Dialog

**Purpose:** Modal popup dialog for messages and user choices.

**Use Case:** Confirmations, alerts, messages requiring user response.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| title | string | "" | Dialog title |
| message | string | "" | Main message text |
| bulletText | array | [] | Bullet points |
| buttons | array | [] | Button labels |
| graphicUri | uri | "" | Image in dialog |
| buttonSelected | integer | 0 | Selected button index |
| buttonFocused | integer | 0 | Focused button index |
| close | boolean | false | Close dialog |
| wasClosed | event | — | Fires when closed |

**Example:**
```xml
<Dialog id="confirmDialog"
        title="Confirm Action"
        message="Are you sure?"
        buttons="[&quot;Yes&quot;, &quot;No&quot;]"
        translation="[640, 360]"/>
```

---

### 17. KeyboardDialog

**Purpose:** Dialog with integrated keyboard for text input.

**Use Case:** Login, user registration, text search within a dialog.

**Attributes:** Inherits Dialog attributes plus:

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| text | string | "" | Input text |
| keyboard | Keyboard | — | Internal keyboard |

**Example:**
```xml
<KeyboardDialog id="loginDialog"
                title="Enter Username"
                message="Please login"
                buttons="[&quot;Login&quot;, &quot;Cancel&quot;]"/>
```

---

### 18. StandardDialog & StandardKeyboardDialog

**Purpose:** Modern dialog nodes with improved graphics and voice entry support.

**Use Case:** Updated app dialogs with consistent styling and voice input.

**Key Attributes:**
- `title`: Dialog title
- `message`: Dialog message (array of strings)
- `buttons`: Button labels (array)
- `palette`: Color palette for styling
- `keyboardDomain`: `"email"` / `"numeric"` / `"alphanumeric"` / `"generic"` / `"password"`

---

## MEDIA NODES

### 19. Video

**Purpose:** Video player supporting HLS, DASH, and MP4 streams.

**Use Case:** Video playback, streaming content, media player.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| content | ContentNode | — | Video content metadata |
| control | string | "none" | "play" / "pause" / "stop" / "prebuffer" / "resume" |
| state | string (RO) | — | "playing" / "paused" / "stopped" / "buffering" / "error" |
| position | time (RO) | — | Current playback position |
| duration | time (RO) | — | Total duration |
| loop | boolean | false | Loop playback |
| seek | time (WO) | — | Set playback position |
| enableUI | boolean | true | Show playback controls |
| enableTrickPlay | boolean | true | Allow rewind/fast-forward |
| width | float | 0.0 | Player width (0=fullscreen) |
| height | float | 0.0 | Player height (0=fullscreen) |

**Example:**
```xml
<?xml version="1.0" encoding="utf-8"?>
<component name="VideoPlayer" extends="Scene">
  <script type="text/brightscript">
    <![CDATA[
      sub init()
        m.video = m.top.findNode("videoNode")
        m.videoContent = createObject("roSGNode", "ContentNode")
        m.videoContent.url = "http://example.com/video.m3u8"
        m.videoContent.streamformat = "hls"
        m.videoContent.title = "My Video"
        m.video.content = m.videoContent
        m.video.control = "play"
      end sub
    ]]>
  </script>
  <children>
    <Video id="videoNode" width="1280" height="720"/>
  </children>
</component>
```

---

### 20. Audio

**Purpose:** Audio player for music, podcasts, sound effects.

**Use Case:** Audio playback, music apps, podcast players.

**Attributes:** Similar to Video:
- `content`: Audio content metadata
- `control`: "play" / "pause" / "stop"
- `state`: Playback state
- `position`: Current time
- `duration`: Total length

---

## ANIMATION & EFFECTS NODES

### 21. Animation

**Purpose:** Animates node properties over time using interpolators.

**Use Case:** Moving elements, fading, scaling, color transitions.

**Attributes:**

| Attribute | Type | Default | Values |
|-----------|------|---------|--------|
| duration | time | 0 | Animation length in seconds |
| easeFunction | string | "outCubic" | "linear" / "inQuad" / "outQuad" / "inOutQuad" / etc. |
| repeat | boolean | false | Loop animation |
| control | string | "none" | "start" / "stop" |

**Example:**
```xml
<Animation id="fadeIn" duration="1" repeat="false">
  <FloatFieldInterpolator key="[0, 1]"
                          keyValue="[0, 1]"
                          fieldToInterp="myPoster.opacity"/>
</Animation>
```

---

### 22. Timer

**Purpose:** Generates timed events at intervals.

**Use Case:** Auto-scrolling, periodic updates, delayed actions.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| duration | time | 1 | Seconds between fires |
| repeat | boolean | false | Repeat indefinitely |
| control | string | "none" | "start" / "stop" |
| fire | event | — | Fires at interval |

**Example:**
```xml
<Timer id="updateTimer"
       duration="5"
       repeat="true"
       control="start"/>
```

---

## SPECIALIZED NODES

### 23. ButtonGroup

**Purpose:** Container for buttons with automatic focus management.

**Use Case:** Button menus, grouped controls.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| buttons | array | [] | Button children |
| focusedButton | integer | 0 | Currently focused button |
| selectedButton | integer | 0 | Selected button index |

---

### 24. ProgressBar

**Purpose:** Progress indicator bar.

**Use Case:** Download progress, loading status, task completion.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| percentage | integer | 0 | Progress 0–100 |
| width | float | system | Bar width |
| height | float | system | Bar height |
| filledBarImageUri | uri | "" | Completed portion image |
| emptyBarImageUri | uri | "" | Remaining portion image |

---

### 25. ComponentLibrary

**Purpose:** Downloads and manages custom component libraries.

**Use Case:** Loading external component packages at runtime.

**Attributes:**

| Attribute | Type | Default | Description |
|-----------|------|---------|-------------|
| uri | uri | "" | Library URL |
| id | string | "" | Unique library ID |
| loadStatus | string (RO) | "none" | "none" / "loading" / "ready" / "failed" |

---

## COORDINATE & TRANSFORM ATTRIBUTES (All Nodes)

Every renderable node supports these common transformation attributes:

| Attribute | Type | Default | Example |
|-----------|------|---------|---------|
| translation | vector2d | [0, 0] | `translation="[100, 50]"` |
| rotation | float | 0.0 | `rotation="1.57"` (π/2 radians) |
| scale | vector2d | [1, 1] | `scale="[2, 1]"` (double width) |
| scaleRotateCenter | vector2d | [0, 0] | `scaleRotateCenter="[640, 360]"` |
| opacity | float | 1.0 | `opacity="0.5"` (50% transparent) |
| visible | boolean | true | `visible="false"` |
| id | string | — | `id="myElement"` |

---

## COMMON USAGE PATTERNS

### Creating a Simple Menu
```xml
<LayoutGroup layoutDirection="vertical" itemSpacings="[20]">
  <Button text="New Game"/>
  <Button text="Continue"/>
  <Button text="Settings"/>
  <Button text="Exit"/>
</LayoutGroup>
```

### Creating a Grid of Items
```xml
<PosterGrid basePosterSize="[300, 450]"
            numColumns="3"
            numRows="2"
            itemSpacing="[20, 20]"/>
```

### Playing a Video
```xml
<Video id="mainPlayer" width="1280" height="720"/>
```

---

## Complete List of Available SceneGraph Components

### SGDEX Views (High-Level Components)

| # | Component | Description |
|---|-----------|-------------|
| 1 | **GridView** | Grid display with lazy loading |
| 2 | **ParagraphView** | Text display with buttons |
| 3 | **EntitlementView** | Subscription/authentication handling |
| 4 | **SearchView** | Search functionality with keyboard (requires firmware 9.1+) |
| 5 | **DetailsView** | Details view with items and buttons |
| 6 | **TimeGridView** | Time-based grid for schedules/TV guides |
| 7 | **MediaView** | Video/audio playback |
| 8 | **SlideShowView** | Image slideshow |
| 9 | **CategoryListView** | Dual-list (categories + items) |

### SGDEX Base Components

| # | Component | Description |
|---|-----------|-------------|
| 1 | **SGDEXComponent** | Base component for all SGDEX views |
| 2 | **ButtonBar** | Button collection overlay |
| 3 | **BaseScene** | Base scene for SGDEX applications |

### SGDEX Handlers (Business Logic)

| # | Handler | Description |
|---|---------|-------------|
| 1 | **ContentHandler** | Content loading tasks |
| 2 | **RAFHandler** | Roku Advertising Framework integration |
| 3 | **EntitlementHandler** | Entitlement/subscription business logic |
| 4 | **ComponentController** | View management controller |

### Built-in SceneGraph Node Classes

Scene, Group, Rectangle, Label, Poster, Video, Keyboard, LayoutGroup, and all other built-in nodes covered in the Components Guide above.

> **Summary:** The list above represents the complete set of **9 major SGDEX Views**, **3 SGDEX Base Components**, **4 Handler types**, plus all built-in node classes. These components cover all major use cases for Roku SceneGraph development including content display, media playback, search, authentication, and more.

---

# BrightScript: Comprehensive Guide

## 1. What is BrightScript?

BrightScript is a dynamically-typed scripting language developed by Roku for creating applications on Roku devices (smart TVs, streaming players, etc.). It is specifically designed for building interactive streaming applications and user interfaces.

**Key Characteristics:**
- **Platform:** Roku OS
- **Type:** Dynamically typed, interpreted language
- **Purpose:** Develop apps for Roku streaming devices
- **Syntax:** Similar to BASIC with object-oriented features

---

## 2. Core Language Features

### A. Data Types

BrightScript supports these primitive data types:

```brightscript
' String
name = "John Doe"
message = "Hello, World!"

' Integer
age = 25
count = 100

' Float/Double
price = 19.99
percentage = 75.5

' Boolean
isActive = true
hasAccess = false

' Associative Array (Dictionary)
person = {
    name: "John",
    age: 30,
    city: "New York"
}

' Arrays
numbers = [1, 2, 3, 4, 5]
mixed = ["text", 42, true, 3.14]

' Invalid type (null equivalent)
unknown = invalid
```

### B. Variable Declaration

```brightscript
' Variables don't require explicit declaration
x = 10
y = 20

' Good practice: use dim for pre-declaration
dim count = 0

' Function-scoped variables
function doSomething()
    local value = 100
    return value
end function
```

---

## 3. Operators

### A. Arithmetic Operators

```brightscript
a = 10
b = 3

sum        = a + b      ' 13
difference = a - b      ' 7
product    = a * b      ' 30
quotient   = a / b      ' 3.33...
modulo     = a MOD b    ' 1
exponent   = 2 ^ 3      ' 8
```

### B. Comparison Operators

```brightscript
' Returns true or false
5 = 5        ' Equality
5 <> 3       ' Not equal
10 > 5       ' Greater than
10 < 15      ' Less than
10 >= 10     ' Greater than or equal
5 <= 10      ' Less than or equal
```

### C. Logical Operators

```brightscript
' AND operator
if (age > 18 AND hasLicense = true)
    print "Can drive"
end if

' OR operator
if (isMember = true OR hasGuestPass = true)
    print "Can enter"
end if

' NOT operator
if NOT isBlocked
    print "Access granted"
end if
```

### D. String Operators

```brightscript
first  = "Hello"
second = "World"

result  = first + " " + second   ' "Hello World"
message = "Count: " + 5          ' "Count: 5"
```

---

## 4. Control Flow Statements

### A. If/Else Statement

```brightscript
age = 20

if age < 13
    print "Child"
else if age < 18
    print "Teenager"
else
    print "Adult"
end if
```

### B. Select/Case Statement

```brightscript
day = 3

select day
    case 1
        print "Monday"
    case 2
        print "Tuesday"
    case 3
        print "Wednesday"
    case default
        print "Other day"
end select
```

### C. While Loop

```brightscript
count = 0
while count < 5
    print count
    count = count + 1
end while
' Output: 0, 1, 2, 3, 4
```

### D. For Loop

```brightscript
' Traditional for loop
for i = 1 to 5
    print i
end for

' For loop with step
for i = 0 to 10 step 2
    print i         ' 0, 2, 4, 6, 8, 10
end for

' For-each loop (arrays)
colors = ["red", "green", "blue"]
for each color in colors
    print color
end for

' For-each loop (associative arrays)
person = {name: "John", age: 30}
for each key in person
    print key + ": " + person[key]
end for
```

### E. Exit Loop

```brightscript
for i = 1 to 10
    if i = 5
        exit for        ' Exits the loop
    end if
    print i
end for
```

---

## 5. Functions

### A. Basic Function Declaration

```brightscript
' Function with no parameters
function sayHello()
    print "Hello!"
end function

sayHello()

' Function with parameters
function greet(name as string)
    print "Hello, " + name
end function

greet("Alice")           ' Output: Hello, Alice

' Function with return value
function add(a as integer, b as integer) as integer
    return a + b
end function

result = add(5, 3)       ' result = 8
```

### B. Multiple Return Values (using Associative Arrays)

```brightscript
function getCoordinates() as object
    return {x: 10, y: 20}
end function

coords = getCoordinates()
print coords.x           ' 10
print coords.y           ' 20
```

### C. Type Hints

```brightscript
function process(input as string) as boolean
    return true
end function

' Dynamic typing (no type hints)
function calculate(a, b)
    return a + b
end function
```

---

## 6. Objects and Classes

BrightScript doesn't have traditional classes — it uses Associative Arrays as objects.

### A. Creating Objects

```brightscript
person = {
    name: "John",
    age: 30,
    city: "New York",
    greet: function()
        return "Hello, my name is " + m.name
    end function
}

print person.name        ' John
print person.age         ' 30
print person.greet()     ' Hello, my name is John
```

### B. Constructor-like Functions

```brightscript
function createPerson(name as string, age as integer) as object
    person = {
        name: name,
        age: age,
        introduce: function()
            return m.name + " is " + stri(m.age) + " years old"
        end function
    }
    return person
end function

john = createPerson("John", 30)
print john.introduce()   ' John is 30 years old
```

---

## 7. Arrays and Collections

### A. Arrays

```brightscript
numbers = [10, 20, 30, 40, 50]

print numbers[0]         ' 10
print numbers.count()    ' 5

numbers.push(60)         ' Add to end
numbers.pop()            ' Remove from end

for each num in numbers
    print num
end for
```

### B. Associative Arrays (Objects / Dictionaries)

```brightscript
student = {
    id: 1,
    name: "Alice",
    grades: [85, 90, 92],
    enrolled: true
}

print student["name"]    ' Alice
print student.name       ' Alice (dot notation)

' Adding / modifying
student.gpa = 3.8
student["class"] = "Grade 10"

' Iterating
for each key in student
    print key + " = " + student[key]
end for
```

---

## 8. String Operations

```brightscript
str = "Hello"

' Length
len = len(str)                             ' 5

' Substring (1-indexed)
substr = mid(str, 1, 3)                    ' "Hel"

' Case conversion
upper = ucase(str)                         ' "HELLO"
lower = lcase(str)                         ' "hello"

' Trimming
trimmed = trim("  Hello  ")               ' "Hello"

' Search
pos = instr(1, str, "l")                   ' Position of first "l"

' Replace
result = substitute("Hello World", "World", "There")  ' "Hello There"

' Type conversion
num = val("42")                            ' 42  (string → number)
str = stri(42)                             ' "42" (number → string)
```

---

## 9. Built-in Functions

### A. Math Functions

```brightscript
print abs(-10)           ' 10
print int(3.7)           ' 3
print cdbl(5)            ' 5.0
print sqr(16)            ' 4
print rnd(1)             ' Random between 0 and 1

sine    = sin(0.5)
cosine  = cos(0.5)
tangent = tan(0.5)
```

### B. Type Checking Functions

```brightscript
x = 10

if type(x) = "roInteger"
    print "It's an integer"
end if

if isString(x)
    print "It's a string"
end if

if isArray(x)
    print "It's an array"
end if
```

### C. Other Useful Functions

```brightscript
' Create a dynamic array
words = CreateObject("roArray", 10)

' Sleep (milliseconds)
sleep(1000)              ' Sleep for 1 second

' Get current time
now = CreateObject("roDateTime")
```

---

## 10. Roku-Specific Components

### A. HTTP Requests

```brightscript
http = CreateObject("roUrlTransfer")
http.SetUrl("https://api.example.com/data")

response = http.GetToString()
print response
```

### B. JSON (Very Common in Roku Apps)

```brightscript
' Parse JSON
json_str = "{""name"": ""John"", ""age"": 30}"
json = ParseJson(json_str)
print json.name          ' John

' Create JSON
obj = {name: "Alice", age: 25}
json_output = FormatJson(obj)
print json_output
```

---

## 11. Comments

```brightscript
' Single line comment
print "Hello"  ' Inline comment

REM Another way to comment
REM This is a comment
```

---

## 12. Error Handling

```brightscript
try
    result = 10 / 0
catch error
    print "Error: " + error
end try

' Null check pattern
if invalid <> x
    print "x is valid"
end if
```

---

## 13. Complete Example: Simple Calculator App

```brightscript
function main()
    print "Welcome to BrightScript Calculator"

    result = calculate(10, 5, "add")
    print "10 + 5 = " + result

    result = calculate(10, 5, "subtract")
    print "10 - 5 = " + result

    result = calculate(10, 5, "multiply")
    print "10 * 5 = " + result
end function

function calculate(a as integer, b as integer, operation as string) as integer
    select operation
        case "add"
            return a + b
        case "subtract"
            return a - b
        case "multiply"
            return a * b
        case "divide"
            if b <> 0
                return a / b
            end if
        case default
            return 0
    end select
end function

main()
```

---

## 14. Key Best Practices

**DO:**
- Use meaningful variable names
- Add comments for complex logic
- Check for `invalid` values before using them
- Use type hints when possible
- Organize code into focused functions
- Make HTTP calls in background tasks (not the main thread)

**DON'T:**
- Use global variables excessively
- Ignore memory management (Roku devices have limited resources)
- Make HTTP calls on the main thread
- Forget to handle errors and invalid states

