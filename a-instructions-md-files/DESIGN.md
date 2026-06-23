# DESIGN.md - UI/UX Design Standards

## Visual Design Principles

### Color Palette
- **Primary**: Use project's brand color
- **Secondary**: Complementary color
- **Accent**: Highlight important elements
- **Neutral**: Gray scale for text and backgrounds
- **Status**: Green (success), Red (error), Yellow (warning), Blue (info)

### Typography
- **Font Stack**: Use system fonts or Google Fonts
- **Hierarchy**: 
  - H1: 2.5rem, bold
  - H2: 2rem, bold
  - H3: 1.5rem, semibold
  - Body: 1rem, regular
  - Small: 0.875rem, regular
- **Line Height**: 1.5 for body text, 1.2 for headings
- **Letter Spacing**: -0.02em for headings, 0 for body

### Spacing System (8px grid)
- **4px**: Extreme tight
- **8px**: Tight
- **16px**: Default
- **24px**: Comfortable
- **32px**: Spacious
- **48px**: Large
- **64px**: Huge
- **80px+**: Massive

### Responsive Breakpoints
- **Mobile**: 0 - 640px
- **Tablet**: 641px - 1024px
- **Desktop**: 1025px - 1440px
- **Wide**: 1441px+

## Component Design Standards

### Buttons
- **Primary**: Filled with primary color, white text
- **Secondary**: Outlined, primary color border
- **Tertiary**: Text-only, primary color
- **Sizes**: Small (32px), Medium (40px), Large (48px)
- **States**: Default, Hover, Active, Disabled, Loading

### Forms
- **Labels**: Always visible, above input
- **Placeholders**: Only for example values
- **Validation**: Inline, with clear error messages
- **Submit**: Button type="submit" with loading state
- **Accessibility**: Proper label-for relationships

### Navigation
- **Primary**: Top or left sidebar
- **Secondary**: Within content sections
- **Breadcrumbs**: Show path, if depth > 2
- **Active states**: Clearly highlight current location

### Cards
- **Elevation**: Use box-shadow for depth
- **Padding**: 16-24px inside card
- **Border radius**: 8px default, 12px for cards
- **Header/Footer**: Optional, with divider

### Modals
- **Overlay**: 50% opacity black background
- **Position**: Center of screen
- **Size**: Small (400px), Medium (600px), Large (800px)
- **Close**: X in top-right, click outside to close
- **Actions**: Buttons at bottom (Cancel, Confirm)

## Accessibility Standards
- **Color Contrast**: WCAG AA minimum (4.5:1)
- **Focus States**: Visible outline on all interactive elements
- **Alt Text**: All images need descriptive alt text
- **ARIA Labels**: For non-semantic elements
- **Keyboard Navigation**: Full tab support
- **Screen Readers**: Test with VoiceOver/NVDA

## Animation Principles
- **Purpose**: Animate to communicate, not distract
- **Duration**: 200-400ms for most animations
- **Easing**: Ease-in-out for entering, ease-out for exiting
- **Performance**: Use transform and opacity only (GPU-accelerated)
- **Motion Reduction**: Support prefers-reduced-motion

## Mobile-First Approach
1. **Design for mobile first**
2. **Enhance for tablet**
3. **Optimize for desktop**
4. **Test on real devices**
5. **Touch targets: minimum 44px**
6. **Use responsive images (srcset/picture)**

## Brand Consistency
- **Logo**: Always use official logo files
- **Favicon**: Properly sized (16x16, 32x32)
- **Open Graph**: og:image, og:title, og:description
- **Social Cards**: Twitter card, Facebook card
- **Favicons**: Multiple sizes for all devices

## Design Deliverables
- **Wireframes**: Low-fidelity layout sketches
- **Mockups**: High-fidelity visual designs (Figma/Sketch)
- **Prototypes**: Interactive click-throughs
- **Design System**: Document all components
- **Style Guide**: Typography, colors, spacing

## Review Checklist
- [ ] Visual hierarchy is clear
- [ ] Colors are consistent with brand
- [ ] Typography is readable at all sizes
- [ ] All interactive elements have feedback
- [ ] Responsive at all breakpoints
- [ ] Accessibility requirements met
- [ ] Performance is optimized (images, fonts)
- [ ] Content is complete and accurate