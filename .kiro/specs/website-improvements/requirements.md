# Requirements Document

## Introduction

Enhance the hero section of the personal portfolio website with a typing animation effect and dynamic content to create a more engaging first impression. The current hero section displays a static name and about text. This feature adds a typewriter-style animation that cycles through professional titles/roles beneath the name, along with subtle entrance animations for hero content elements to improve visual appeal and visitor engagement.

## Glossary

- **Hero_Section**: The full-screen header area of the portfolio site (HTML `<header id="about">`) containing the name, about text, education column, and CTA buttons
- **Typing_Animator**: A JavaScript component that displays text character-by-character in a typewriter style, cycles through a configurable list of strings, and shows a blinking cursor
- **Title_List**: An ordered array of professional title strings that the Typing_Animator cycles through (e.g., "Software Development Engineer", "Full-Stack Developer", "Cloud Architect")
- **Cursor**: A blinking vertical bar character displayed at the end of the currently typed text to simulate a text editor cursor
- **Typing_Speed**: The delay in milliseconds between each character being appended during the typing phase
- **Deleting_Speed**: The delay in milliseconds between each character being removed during the deleting phase
- **Pause_Duration**: The delay in milliseconds the Typing_Animator waits after fully typing a title before beginning to delete the text
- **Hero_Entrance_Animation**: A set of staggered CSS/JS animations applied to hero content elements (name, subtitle, about box, CTA buttons) when the page first loads

## Requirements

### Requirement 1: Typing Animation Display

**User Story:** As a visitor, I want to see an animated typing effect cycling through professional titles below the name, so that I get an immediate sense of the site owner's expertise and the page feels dynamic.

#### Acceptance Criteria

1. WHEN the Hero_Section loads, THE Typing_Animator SHALL display the first title from the Title_List by appending one character at a time at the configured Typing_Speed
2. WHEN the Typing_Animator finishes typing a title, THE Typing_Animator SHALL pause for the configured Pause_Duration before beginning to delete the title
3. WHEN the Typing_Animator finishes deleting a title, THE Typing_Animator SHALL proceed to type the next title in the Title_List
4. WHEN the Typing_Animator reaches the end of the Title_List, THE Typing_Animator SHALL loop back to the first title and continue cycling indefinitely
5. THE Typing_Animator SHALL display a blinking Cursor at the end of the currently visible text at all times during typing, pausing, and deleting phases

### Requirement 2: Typing Animation Configuration

**User Story:** As the site owner, I want the typing animation speeds and title list to be easily configurable, so that I can adjust the feel and content without modifying core logic.

#### Acceptance Criteria

1. THE Typing_Animator SHALL read the Title_List from a JavaScript configuration object defined at the top of the script
2. THE Typing_Animator SHALL use a default Typing_Speed of 80 milliseconds per character
3. THE Typing_Animator SHALL use a default Deleting_Speed of 40 milliseconds per character
4. THE Typing_Animator SHALL use a default Pause_Duration of 1500 milliseconds
5. WHERE the Title_List contains only one entry, THE Typing_Animator SHALL type that title once and remain displayed without deleting or cycling

### Requirement 3: Cursor Styling

**User Story:** As a visitor, I want the blinking cursor to look like a real text editor cursor, so that the typing effect feels authentic.

#### Acceptance Criteria

1. THE Cursor SHALL be rendered as a vertical bar character using a CSS pseudo-element or inline span
2. THE Cursor SHALL blink with a smooth opacity transition at a rate of approximately 1 blink per second (500ms on, 500ms off)
3. THE Cursor SHALL use the accent color (gold/amber) defined in the site's CSS custom properties to remain consistent with the design theme
4. WHILE the Typing_Animator is actively typing or deleting characters, THE Cursor SHALL remain visible without blinking

### Requirement 4: Hero Entrance Animations

**User Story:** As a visitor, I want the hero content to animate in with staggered timing when the page loads, so that the first impression feels polished and intentional.

#### Acceptance Criteria

1. WHEN the page loads, THE Hero_Section SHALL animate the name element in first using a fade-up effect with a duration between 600ms and 1000ms
2. WHEN the name animation begins, THE Hero_Section SHALL animate the typing subtitle element in after a stagger delay of 200ms to 400ms
3. WHEN the subtitle animation begins, THE Hero_Section SHALL animate the about box in after an additional stagger delay of 200ms to 400ms
4. WHEN the about box animation begins, THE Hero_Section SHALL animate the CTA buttons in after an additional stagger delay of 200ms to 400ms
5. THE Hero_Entrance_Animation SHALL use CSS transforms and opacity transitions rather than JavaScript-driven layout changes to maintain smooth 60fps rendering

### Requirement 5: Responsive and Accessible Behavior

**User Story:** As a visitor on any device or using assistive technology, I want the typing animation to work correctly and not interfere with accessibility, so that the site remains usable for everyone.

#### Acceptance Criteria

1. WHEN the user has enabled the `prefers-reduced-motion` OS setting, THE Typing_Animator SHALL skip the animation and display the first title from the Title_List as static text immediately
2. WHEN the user has enabled the `prefers-reduced-motion` OS setting, THE Hero_Entrance_Animation SHALL skip all staggered animations and display all elements immediately
3. THE Typing_Animator container SHALL include an `aria-label` attribute containing the full first title text so that screen readers announce the complete text without waiting for the animation
4. THE Typing_Animator container SHALL use `aria-live="off"` to prevent screen readers from announcing each character change during the animation cycle
5. THE Typing_Animator SHALL render correctly on viewports from 320px width to 2560px width without text overflow or layout shifts

### Requirement 6: Performance

**User Story:** As a visitor, I want the typing animation to run smoothly without impacting page load or scroll performance, so that the site feels fast and responsive.

#### Acceptance Criteria

1. THE Typing_Animator SHALL use `setTimeout` or `requestAnimationFrame` for character timing rather than blocking the main thread
2. THE Typing_Animator SHALL add fewer than 1KB of minified JavaScript to the page payload
3. WHEN the Hero_Section is not visible in the viewport (user has scrolled past), THE Typing_Animator SHALL pause its animation cycle to avoid unnecessary CPU usage
4. THE Hero_Entrance_Animation SHALL use CSS `transform` and `opacity` properties exclusively to ensure animations are GPU-accelerated and do not trigger layout reflows
