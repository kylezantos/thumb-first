<overview>
Touch interactions on mobile differ significantly from mouse interactions on desktop. This reference covers eliminating the 300ms tap delay, proper touch target sizing, gesture handling in React, and scroll behavior customization.
</overview>

<tap_delay>
## 300ms Tap Delay

<problem>
Historically, mobile browsers waited 300ms after a tap to see if the user would double-tap to zoom. This made apps feel sluggish.
</problem>

<solution name="CSS touch-action (Recommended)">
Modern solution - no JavaScript required:

```css
/* Apply to interactive elements */
a, button, input, select, textarea, label, summary, [role="button"] {
  touch-action: manipulation;
}
```

`touch-action: manipulation` tells the browser:
- Allow panning and pinching
- Don't wait for double-tap zoom
- Fire click events immediately

**Browser support:** iOS Safari 9.3+, all modern browsers.
</solution>

<solution name="Viewport Meta Tag">
Browsers also remove the delay when:

```html
<meta name="viewport" content="width=device-width">
```

This signals the page is mobile-optimized and doesn't need double-tap zoom.
</solution>

<anti_pattern name="FastClick Library">
**Don't use FastClick** - it's obsolete since iOS 9.3 (2016).

```javascript
// DON'T DO THIS
import FastClick from 'fastclick';
FastClick.attach(document.body);
```

FastClick can introduce bugs and is no longer needed.
</anti_pattern>

<edge_case name="PWA Standalone Mode">
There's an edge case where the delay persists in standalone PWAs with:
```html
<meta name="apple-mobile-web-app-capable" content="yes">
```

Fix by explicitly adding touch-action:
```css
* {
  touch-action: manipulation;
}
```
</edge_case>
</tap_delay>

<touch_targets>
## Touch Target Sizing

<requirements>
**Minimum sizes:**
- **Apple HIG:** 44×44pt (points)
- **Material Design:** 48×48dp (density-independent pixels)
- **WCAG 2.1 AAA:** 44×44 CSS pixels

**Recommendation:** Use 44px minimum, 48px preferred.
</requirements>

<implementation>
```css
/* Minimum touch target */
.touch-target {
  min-height: 44px;
  min-width: 44px;
  padding: 12px;
}

/* Icon button with proper touch area */
.icon-button {
  /* Visual size */
  width: 24px;
  height: 24px;
  /* But clickable area is larger */
  padding: 10px;
  margin: -10px; /* Negative margin to maintain layout */
}

/* Or use pseudo-element for larger hit area */
.small-button {
  position: relative;
}
.small-button::before {
  content: '';
  position: absolute;
  top: -10px;
  right: -10px;
  bottom: -10px;
  left: -10px;
}
```
</implementation>

<spacing>
**Spacing between touch targets:**
- Minimum 8px between adjacent targets
- Prevents accidental taps on wrong element
</spacing>
</touch_targets>

<gesture_libraries>
## Gesture Handling in React

<library name="@use-gesture (Web)">
Best for React web apps - works with react-spring:

```bash
pnpm add @use-gesture/react
```

```tsx
import { useDrag, useGesture } from '@use-gesture/react';
import { useSpring, animated } from '@react-spring/web';

function DraggableCard() {
  const [{ x, y }, api] = useSpring(() => ({ x: 0, y: 0 }));

  const bind = useDrag(({ offset: [ox, oy] }) => {
    api.start({ x: ox, y: oy });
  });

  return (
    <animated.div
      {...bind()}
      style={{
        x, y,
        touchAction: 'none', // IMPORTANT: prevent scroll interference
      }}
    >
      Drag me
    </animated.div>
  );
}
```

**Important:** Always set `touchAction: 'none'` on draggable elements to prevent browser scroll interference.
</library>

<library name="React Native Gesture Handler">
For React Native apps (not web):

```tsx
import { GestureDetector, Gesture } from 'react-native-gesture-handler';

function SwipeableItem() {
  const pan = Gesture.Pan()
    .onUpdate((e) => {
      // Handle pan
    })
    .onEnd((e) => {
      // Handle end
    });

  return (
    <GestureDetector gesture={pan}>
      <View>Swipe me</View>
    </GestureDetector>
  );
}
```
</library>
</gesture_libraries>

<touch_events>
## Touch Event Handling

<lifecycle>
**Touch event sequence:**
1. `touchstart` - Finger touches screen
2. `touchmove` - Finger moves (may fire many times)
3. `touchend` - Finger lifts
4. `click` - Fires after touchend (with delay if touch-action not set)
</lifecycle>

<basic_handling>
```tsx
function TouchableElement() {
  const handleTouchStart = (e: React.TouchEvent) => {
    // Get touch position
    const touch = e.touches[0];
    console.log(`Touch at ${touch.clientX}, ${touch.clientY}`);
  };

  const handleTouchMove = (e: React.TouchEvent) => {
    // Track touch movement
    const touch = e.touches[0];
    // Handle movement...
  };

  const handleTouchEnd = (e: React.TouchEvent) => {
    // Touch ended
  };

  return (
    <div
      onTouchStart={handleTouchStart}
      onTouchMove={handleTouchMove}
      onTouchEnd={handleTouchEnd}
    >
      Touch me
    </div>
  );
}
```
</basic_handling>

<pointer_events>
**Consider Pointer Events** for unified mouse/touch handling:

```tsx
function UnifiedElement() {
  const handlePointerDown = (e: React.PointerEvent) => {
    console.log(`Pointer type: ${e.pointerType}`); // 'mouse', 'touch', 'pen'
  };

  return (
    <div
      onPointerDown={handlePointerDown}
      onPointerMove={handlePointerMove}
      onPointerUp={handlePointerUp}
    >
      Works with mouse and touch
    </div>
  );
}
```
</pointer_events>
</touch_events>

<scroll_behavior>
## Scroll Behavior

<momentum_scrolling>
**Enable smooth momentum scrolling (iOS):**
```css
.scroll-container {
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
```
</momentum_scrolling>

<prevent_scroll>
**Prevent scroll while dragging:**
```tsx
const bind = useDrag(({ event }) => {
  event.preventDefault(); // Prevent scroll during drag
}, {
  filterTaps: true,
});
```

Or with CSS:
```css
.dragging {
  touch-action: none;
}
```
</prevent_scroll>

<overscroll>
**Control overscroll (rubber band) behavior:**
```css
/* Prevent rubber band entirely */
html {
  overscroll-behavior: none;
}

/* Contain to element (prevent scroll chaining) */
.modal {
  overscroll-behavior: contain;
}

/* Only on y-axis */
.list {
  overscroll-behavior-y: contain;
}
```
</overscroll>

<pull_to_refresh>
**Disable browser pull-to-refresh:**
```css
body {
  overscroll-behavior-y: contain;
}
```

**Note:** This also disables the pull-to-refresh gesture. Only use if implementing custom pull-to-refresh.
</pull_to_refresh>
</scroll_behavior>

<common_issues>
## Common Touch Issues

<issue name="Ghost clicks">
When touch handler removes/moves element, click may fire on element now at that position.

**Fix:** Use `e.preventDefault()` in touch handler or delay removal.
</issue>

<issue name="Scroll jank during touch">
Heavy JavaScript in touch handlers causes scroll jank.

**Fix:** Use passive event listeners:
```javascript
element.addEventListener('touchmove', handler, { passive: true });
```

React handles this automatically for scroll events.
</issue>

<issue name="Touch not working on iOS">
iOS Safari has quirks with touch events on non-clickable elements.

**Fix:** Add `cursor: pointer` to make element "clickable":
```css
.touchable {
  cursor: pointer;
}
```
</issue>

<issue name="Multi-touch tracking">
Touches can be added/removed in any order. Don't rely on array order.

**Fix:** Track touches by `identifier`:
```javascript
const touches = new Map();

function handleTouchStart(e) {
  for (const touch of e.changedTouches) {
    touches.set(touch.identifier, {
      x: touch.clientX,
      y: touch.clientY,
    });
  }
}
```
</issue>
</common_issues>

<accessibility>
## Accessibility Considerations

1. **Don't disable zoom** - Users with vision impairments need to zoom
2. **Support keyboard** - All touch interactions should have keyboard equivalents
3. **Provide alternatives** - Some users can't perform complex gestures
4. **Use semantic elements** - `button` is more accessible than `div` with touch handler
5. **Test with assistive tech** - VoiceOver, TalkBack handle touch differently
</accessibility>
