# Implementation Plan: Hero Typing Animation & Entrance Effects

## Overview

Add a typing animation that cycles through professional titles and staggered entrance animations to the hero section of a vanilla HTML/CSS/JS portfolio site. All CSS goes into `static/css/resume.css` and all JS is inline in `index.html`.

## Tasks

- [x] 1. Add typing subtitle HTML and cursor CSS
  - [x] 1.1 Add the `#typing-subtitle` element to `index.html`
    - Insert `<p class="hero-subtitle" id="typing-subtitle" aria-label="Software Development Engineer" aria-live="off"><span class="typing-text"></span><span class="typing-cursor" aria-hidden="true"></span></p>` between `.hero-name` and `.hero-about` in the hero section
    - Remove the `style="margin-bottom: var(--space-2xl);"` from `.hero-name` since the subtitle now fills that gap
    - _Requirements: 1.1, 3.1, 5.3, 5.4_

  - [x] 1.2 Add cursor and subtitle CSS to `static/css/resume.css`
    - Add `.hero-subtitle` styles (font size, positioning, min-height to prevent layout shift)
    - Add `.typing-cursor` styles: `display: inline-block`, `width: 2px`, `height: 1.1em`, `background: var(--color-accent)`, `margin-left: 2px`, `vertical-align: text-bottom`, `animation: blink 1s step-end infinite`
    - Add `.typing-cursor.typing` rule to suppress blink (`animation: none; opacity: 1`)
    - Add `@keyframes blink` with `0%,100% { opacity: 1 }` and `50% { opacity: 0 }`
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [x] 2. Implement the typing animator JavaScript
  - [x] 2.1 Add the `TYPING_CONFIG` object and typing animator logic to the inline `<script>` in `index.html`
    - Define `TYPING_CONFIG` with `titles` array, `typingSpeed: 80`, `deletingSpeed: 40`, `pauseDuration: 1500`
    - Implement `typeStep()` function using `setTimeout` that appends/removes one character at a time, updates `.typing-text` `textContent`, and toggles `.typing` class on cursor
    - Implement `startTyping()` entry point called on `DOMContentLoaded`
    - Handle single-title edge case: type once and stop without deleting
    - Handle empty titles array: skip animation, leave subtitle empty
    - Guard `getElementById` calls: if elements are null, skip silently
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 2.1, 2.2, 2.3, 2.4, 2.5, 6.1, 6.2_

  - [ ]* 2.2 Write property test: Character-by-character typing correctness
    - **Property 1: Character-by-character typing correctness**
    - Extract `getVisibleText(title, charIndex)` as a pure function and test with fast-check: for any string and valid charIndex, result equals `title.slice(0, charIndex)`
    - **Validates: Requirements 1.1**

  - [ ]* 2.3 Write property test: Pause state after typing completes
    - **Property 2: Pause state after typing completes**
    - Extract `getNextState(currentState, config)` as a pure function and test: when charIndex equals title length, next state has `isDeleting=false` and delay equals `pauseDuration`
    - **Validates: Requirements 1.2**

  - [ ]* 2.4 Write property test: Title index cycling with modular wrap
    - **Property 3: Title index cycling with modular wrap**
    - Extract `getNextTitleIndex(currentIndex, titlesLength)` and test: for any index `i` and length `N > 1`, result equals `(i + 1) % N`
    - **Validates: Requirements 1.3, 1.4**

  - [ ]* 2.5 Write property test: Cursor blink suppression during active phases
    - **Property 4: Cursor blink suppression during active phases**
    - Test that `.typing` class is present when state is actively typing or deleting, and absent during pause
    - **Validates: Requirements 3.4**

- [x] 3. Implement IntersectionObserver viewport pause
  - [x] 3.1 Add IntersectionObserver to pause/resume the typing animator
    - Create observer on `#about` with `threshold: 0`
    - Implement `pauseTyping()` that clears `timerId` and sets `isPaused = true`
    - Implement `resumeTyping()` that restarts the `typeStep` loop from current state
    - Feature-detect `IntersectionObserver`; if unavailable, let animation run continuously
    - _Requirements: 6.3_

  - [ ]* 3.2 Write property test: Pause when hero is off-screen
    - **Property 6: Pause when hero is off-screen**
    - Test that when intersection is false, `isPaused === true` and `timerId === null`; when re-entering, animation resumes from previous state
    - **Validates: Requirements 6.3**

- [x] 4. Checkpoint - Verify typing animation works
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5. Add hero entrance animations
  - [x] 5.1 Add entrance animation CSS to `static/css/resume.css`
    - Add `.hero-animate-target` base state: `opacity: 0; transform: translateY(30px)`
    - Add `.hero-animate-target.hero-animate-in` active state: `opacity: 1; transform: translateY(0); transition: opacity 0.8s ease-out, transform 0.8s ease-out`
    - Add stagger `transition-delay` rules: `.hero-name` 0ms, `#typing-subtitle` 300ms, `.hero-about` 600ms, `.hero-cta` 900ms
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 6.4_

  - [x] 5.2 Add entrance animation JS trigger and update HTML in `index.html`
    - Add `hero-animate-target` class to `.hero-name`, `#typing-subtitle`, `.hero-about`, `.hero-cta` elements in the HTML
    - Remove `data-aos="fade-up"` and `data-aos-duration="1000"` from `.hero-content` since custom entrance animations replace AOS for the hero
    - On `DOMContentLoaded`, add `.hero-animate-in` class to all `.hero-animate-target` elements
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5_

- [x] 6. Add reduced motion and responsive handling
  - [x] 6.1 Add `prefers-reduced-motion` support in both CSS and JS
    - In CSS: add `@media (prefers-reduced-motion: reduce)` rule that sets `.hero-animate-target` to `opacity: 1; transform: none; transition: none` and `.typing-cursor` to `animation: none`
    - In JS: check `window.matchMedia('(prefers-reduced-motion: reduce)').matches` before starting animations
    - If reduced motion: set `.typing-text` textContent to `titles[0]` as static text, hide cursor, apply `.hero-animate-in` immediately with no transition
    - _Requirements: 5.1, 5.2_

  - [ ]* 6.2 Write property test: First title used for accessibility and reduced motion
    - **Property 5: First title used for accessibility and reduced motion**
    - Test that `aria-label` equals `titles[0]` and that reduced-motion mode displays `titles[0]` as static text
    - **Validates: Requirements 5.1, 5.3**

  - [x] 6.3 Verify responsive behavior across viewport widths
    - Ensure `.hero-subtitle` and `.typing-text` do not overflow or cause layout shifts on viewports from 320px to 2560px
    - Add responsive CSS rules if needed (e.g., font-size adjustments for small screens)
    - _Requirements: 5.5_

- [x] 7. Final checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.
  - Verify typing animation cycles through titles correctly
  - Verify entrance animations stagger properly
  - Verify reduced motion disables all animations
  - Verify IntersectionObserver pauses animation when scrolled past

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- This is a vanilla HTML/CSS/JS site — no build system, no framework
- All CSS additions go into `static/css/resume.css`
- All JS additions are inline in the `<script>` tag at the bottom of `index.html`
- Property tests use fast-check and require extracting pure functions from the animator logic
- Each task references specific requirements for traceability
