<overview>
Responsive design patterns ensure web apps work well across all screen sizes. This reference covers common mistakes, anti-patterns, and best practices for mobile-first responsive design with React and Tailwind CSS.
</overview>

<mobile_first>
## Mobile-First Approach

<principle>
**Write mobile styles first, then add breakpoints for larger screens:**

```css
/* Mobile first (default) */
.container {
  padding: 1rem;
  display: flex;
  flex-direction: column;
}

/* Tablet and up */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
    flex-direction: row;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
    margin: 0 auto;
  }
}
```
</principle>

<tailwind_approach>
**Tailwind CSS mobile-first:**
```tsx
<div className="
  p-4 flex flex-col          {/* Mobile */}
  md:p-8 md:flex-row         {/* Tablet */}
  lg:max-w-6xl lg:mx-auto    {/* Desktop */}
">
```

Tailwind breakpoints:
- `sm`: 640px
- `md`: 768px
- `lg`: 1024px
- `xl`: 1280px
- `2xl`: 1536px
</tailwind_approach>
</mobile_first>

<common_mistakes>
## Common Mistakes

<mistake name="Device-Specific Breakpoints">
**Bad:** Targeting specific devices:
```css
/* Don't do this */
@media (width: 375px) { } /* iPhone SE */
@media (width: 390px) { } /* iPhone 14 */
```

**Good:** Use content-based breakpoints:
```css
/* Content-driven breakpoints */
@media (min-width: 768px) { }
```

Device widths change constantly. Design for content, not devices.
</mistake>

<mistake name="Fixed Widths on Mobile">
**Bad:**
```css
.card {
  width: 400px; /* Will overflow on small screens */
}
```

**Good:**
```css
.card {
  width: 100%;
  max-width: 400px;
}
```
</mistake>

<mistake name="Horizontal Overflow">
**Bad:** Content overflows horizontally, causing scroll.

**Fix:** Check for:
- Fixed-width elements
- Images without max-width
- Tables without horizontal scroll wrapper
- Long unbreakable strings

```css
/* Prevent horizontal overflow */
body {
  overflow-x: hidden;
}

img {
  max-width: 100%;
  height: auto;
}

/* Handle long words */
.text {
  word-wrap: break-word;
  overflow-wrap: break-word;
}
```
</mistake>

<mistake name="Small Touch Targets">
**Bad:**
```css
.icon-button {
  width: 24px;
  height: 24px;
}
```

**Good:**
```css
.icon-button {
  width: 44px;  /* Minimum touch target */
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
}
```
</mistake>

<mistake name="Small Font Sizes">
**Bad:**
```css
.body-text {
  font-size: 12px;
}
```

**Good:**
```css
.body-text {
  font-size: 16px; /* Minimum for comfortable reading */
}
```

iOS Safari zooms inputs with font-size < 16px.
</mistake>

<mistake name="Using 100vh">
See viewport-layout.md for the full explanation.

**Bad:**
```css
.hero { height: 100vh; }
```

**Good:**
```css
.hero {
  height: 100vh;
  height: 100svh;
}
```
</mistake>

<mistake name="Ignoring Landscape Orientation">
**Remember:** Users rotate phones. Test:
- Navigation in landscape
- Forms with keyboard in landscape
- Media player layouts
- Safe areas shift in landscape (notch on side)
</mistake>

<mistake name="Desktop-Only Hover States">
**Bad:** Relying on hover for critical functionality:
```css
.dropdown-menu {
  display: none;
}
.dropdown:hover .dropdown-menu {
  display: block;
}
```

**Good:** Make it work with tap/click:
```tsx
const [isOpen, setIsOpen] = useState(false);

<button onClick={() => setIsOpen(!isOpen)}>Menu</button>
{isOpen && <DropdownMenu />}
```
</mistake>
</common_mistakes>

<patterns>
## Responsive Patterns

<pattern name="Fluid Typography">
Scale font size based on viewport:

```css
/* clamp(min, preferred, max) */
.heading {
  font-size: clamp(1.5rem, 4vw, 3rem);
}

.body {
  font-size: clamp(1rem, 2.5vw, 1.25rem);
}
```

Tailwind approach:
```tsx
<h1 className="text-2xl md:text-4xl lg:text-5xl">
  Responsive Heading
</h1>
```
</pattern>

<pattern name="Responsive Grid">
```tsx
<div className="
  grid
  grid-cols-1
  sm:grid-cols-2
  lg:grid-cols-3
  xl:grid-cols-4
  gap-4
">
  {items.map(item => <Card key={item.id} {...item} />)}
</div>
```
</pattern>

<pattern name="Stack to Inline">
```tsx
<div className="
  flex flex-col space-y-4
  md:flex-row md:space-y-0 md:space-x-4
">
  <Input />
  <Button>Submit</Button>
</div>
```
</pattern>

<pattern name="Show/Hide Elements">
```tsx
{/* Show on mobile only */}
<nav className="md:hidden">
  <MobileNav />
</nav>

{/* Show on desktop only */}
<nav className="hidden md:block">
  <DesktopNav />
</nav>
```
</pattern>

<pattern name="Responsive Images">
```tsx
<Image
  src="/photo.jpg"
  alt="Photo"
  width={1200}
  height={800}
  className="w-full h-auto"
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
/>
```
</pattern>

<pattern name="Container Queries">
Modern CSS for component-based responsive design:

```css
.card-container {
  container-type: inline-size;
}

.card {
  padding: 1rem;
}

@container (min-width: 400px) {
  .card {
    padding: 2rem;
    display: flex;
  }
}
```

Tailwind support via `@tailwindcss/container-queries` plugin.
</pattern>
</patterns>

<navigation>
## Mobile Navigation Patterns

<pattern name="Bottom Navigation">
Best for apps with 3-5 main sections:

```tsx
<nav className="
  fixed bottom-0 left-0 right-0
  flex justify-around
  bg-background border-t
  pb-safe  {/* Safe area for home indicator */}
  md:hidden  {/* Hide on desktop */}
">
  <NavItem icon={Home} label="Home" />
  <NavItem icon={Search} label="Search" />
  <NavItem icon={User} label="Profile" />
</nav>
```
</pattern>

<pattern name="Hamburger Menu">
For complex navigation:

```tsx
const [isOpen, setIsOpen] = useState(false);

<button
  className="md:hidden p-2"
  onClick={() => setIsOpen(!isOpen)}
  aria-label="Toggle menu"
>
  <Menu className="w-6 h-6" />
</button>

{isOpen && (
  <div className="
    fixed inset-0 z-50
    bg-background
    md:hidden
  ">
    <MobileMenu onClose={() => setIsOpen(false)} />
  </div>
)}
```
</pattern>

<pattern name="Sliding Drawer">
```tsx
<Sheet>
  <SheetTrigger asChild>
    <Button variant="ghost" size="icon" className="md:hidden">
      <Menu />
    </Button>
  </SheetTrigger>
  <SheetContent side="left" className="w-80">
    <nav>...</nav>
  </SheetContent>
</Sheet>
```
</pattern>
</navigation>

<forms>
## Responsive Forms

<pattern name="Stacked to Inline">
```tsx
<form className="space-y-4">
  <div className="flex flex-col md:flex-row md:gap-4">
    <div className="flex-1">
      <Label htmlFor="firstName">First Name</Label>
      <Input id="firstName" />
    </div>
    <div className="flex-1">
      <Label htmlFor="lastName">Last Name</Label>
      <Input id="lastName" />
    </div>
  </div>
  <div>
    <Label htmlFor="email">Email</Label>
    <Input id="email" type="email" className="text-base" /> {/* 16px prevents iOS zoom */}
  </div>
</form>
```
</pattern>

<pattern name="Full-Width Buttons on Mobile">
```tsx
<Button className="w-full md:w-auto">
  Submit
</Button>
```
</pattern>
</forms>

<testing>
## Testing Responsive Design

1. **Use real devices** - Emulators miss nuances
2. **Test orientations** - Portrait and landscape
3. **Test with keyboard** - Layout changes when keyboard opens
4. **Check content reflow** - Text should reflow, not truncate unexpectedly
5. **Verify touch targets** - 44px minimum
6. **Test safe areas** - Notches and home indicators
7. **Check font rendering** - Fonts render differently per OS
</testing>
