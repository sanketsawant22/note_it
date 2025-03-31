Here's the enhanced Framer Motion notes in Markdown format with additional important concepts:

```markdown
# Framer Motion Notes

## Introduction
Framer Motion is a powerful animation library for React that allows you to create smooth and complex animations with ease. It provides an intuitive API to handle animations, gestures, and transitions effortlessly.

## Installation
```sh
npm install framer-motion
# or
yarn add framer-motion
```

## Basic Usage
```jsx
import { motion } from "framer-motion";

function MyComponent() {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 1 }}
    >
      Hello, Framer Motion!
    </motion.div>
  );
}
```

## Core Concepts

### Animation Props
| Prop        | Description                                                                 |
|-------------|-----------------------------------------------------------------------------|
| `initial`   | Starting state (e.g., `{ opacity: 0 }`)                                     |
| `animate`   | Target animation state (e.g., `{ opacity: 1 }`)                             |
| `exit`      | Animation when component unmounts (for AnimatePresence)                     |
| `transition`| Controls animation timing (duration, delay, ease, etc.)                     |
| `variants`  | Pre-defined animation states for reusable animations                        |

### Transition Properties
```js
transition={{
  duration: 0.5,       // Animation duration in seconds
  delay: 0.2,          // Delay before animation starts
  ease: "easeInOut",   // Easing function
  type: "spring",      // Animation type (spring/tween/inertia)
  stiffness: 100,      // For spring animations
  damping: 10          // For spring animations
}}
```

## Advanced Features

### Variants
```jsx
const itemVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: { 
    opacity: 1, 
    y: 0,
    transition: { duration: 0.5 }
  }
};

<motion.div variants={itemVariants} initial="hidden" animate="visible" />
```

### Gestures
```jsx
<motion.button
  whileHover={{ scale: 1.1, backgroundColor: "#f00" }}
  whileTap={{ scale: 0.9 }}
  whileFocus={{ boxShadow: "0 0 0 2px #555" }}
>
  Interactive
</motion.button>
```

### Drag
```jsx
<motion.div
  drag
  dragConstraints={{ left: 0, right: 0, top: 0, bottom: 0 }}
  dragElastic={0.5}
  whileDrag={{ scale: 1.1 }}
/>
```

### Layout Animations
```jsx
<motion.div layout>
  {items.map(item => (
    <motion.div key={item.id} layout>
      {item.content}
    </motion.div>
  ))}
</motion.div>
```

### Scroll Animations
```jsx
import { useScroll, useTransform } from "framer-motion";

const { scrollYProgress } = useScroll();
const scale = useTransform(scrollYProgress, [0, 1], [1, 1.5]);

<motion.div style={{ scale }} />
```

### AnimatePresence (Exit Animations)
```jsx
import { AnimatePresence } from "framer-motion";

<AnimatePresence>
  {isVisible && (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    >
      Content
    </motion.div>
  )}
</AnimatePresence>
```

### Keyframes
```jsx
<motion.div
  animate={{
    scale: [1, 1.5, 1.5, 1, 1],
    rotate: [0, 0, 270, 270, 0],
  }}
  transition={{
    duration: 2,
    repeat: Infinity,
  }}
/>
```

### SVG Animations
```jsx
<motion.svg
  initial={{ rotate: 0 }}
  animate={{ rotate: 360 }}
  transition={{ duration: 2, repeat: Infinity }}
>
  <motion.circle
    cx="50"
    cy="50"
    r="40"
    fill="none"
    stroke="red"
    strokeWidth="2"
    strokeDasharray="100"
    strokeDashoffset="0"
    animate={{
      strokeDashoffset: [0, 100],
    }}
    transition={{
      duration: 2,
      repeat: Infinity,
    }}
  />
</motion.svg>
```

## Performance Tips
1. Use `will-change: transform` for animated elements
2. Prefer `opacity` and `transform` properties for smoother animations
3. Use `layout` prop wisely (can be performance intensive)
4. For complex animations, consider using `useAnimation` hooks

## Common Patterns

### Staggered Animations
```jsx
const container = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
};

const item = {
  hidden: { y: 20, opacity: 0 },
  visible: { y: 0, opacity: 1 }
};

<motion.ul variants={container} initial="hidden" animate="visible">
  {items.map((item) => (
    <motion.li key={item.id} variants={item} />
  ))}
</motion.ul>
```

### Shared Layout Animations
```jsx
<motion.div layoutId="unique-id" />
// Later in another component:
<motion.div layoutId="unique-id" />
```

## Useful Hooks
- `useAnimation()` - Control animations programmatically
- `useCycle()` - Cycle between animations
- `useReducedMotion()` - Detect user's motion preference
- `useElementScroll()` - Get scroll position of an element
- `useViewportScroll()` - Get viewport scroll position

## Resources
- [Official Documentation](https://www.framer.com/motion/)
- [Framer Motion Examples](https://framer-motion.vercel.app/)
- [Animation Cheat Sheet](https://www.framer.com/api/motion/examples/)
```

This Markdown file now includes:
1. Proper formatting for code blocks
2. Additional important concepts like layout animations and SVG animations
3. Performance tips
4. Common patterns
5. Useful hooks
6. Resource links
7. More detailed transition properties
8. Better organization with tables and sections

You can save this as `framer-motion-notes.md` for easy reference.
