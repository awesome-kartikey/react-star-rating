# Frequently Asked Questions (FAQ)

Here are answers to some common questions about the Awesome Kartikey React Star Rating component.

**1. How can I change the maximum number of stars displayed?**

You can set the maximum number of stars by passing the `maxRating` prop to the `StarRating` component.

```jsx
import StarRating from "./star-rating.component";

function MyComponent() {
  // Display 10 stars instead of the default 5
  return (
    <StarRating maxRating={10} onChange={(rating) => console.log(rating)} />
  );
}
```

If the `maxRating` prop is not provided, it defaults to `5`.

**2. How do I get the rating value selected by the user?**

Use the `onChange` prop. It's a callback function that receives the newly selected rating (a number) as its argument whenever the user clicks on a star.

```jsx
import React, { useState } from "react";
import StarRating from "./star-rating.component";

function RatingCollector() {
  const [productRating, setProductRating] = useState(0);

  const handleRatingUpdate = (newRating) => {
    console.log("User selected:", newRating);
    setProductRating(newRating);
  };

  return (
    <div>
      <h2>Rate our product:</h2>
      <StarRating onChange={handleRatingUpdate} />
      <p>Selected Rating: {productRating}</p>
    </div>
  );
}
```

**3. Can I customize the appearance (colors, size) of the stars?**

Yes. The component uses CSS for styling. You can modify the styles directly in the `src/star-rating.styles.css` file.

- **Star Color (Default):** Modify the `color` property in the `.star-rating` class.
- **Star Color (Active/Selected):** Modify the `color` property in the `.star-rating.active` class.
- **Star Size:** Modify the `font-size` property in the `.star-rating` class.
- **Spacing:** Modify properties like `margin` or `padding` within `.star-rating` or the container `.star-rating-container`.

For more complex customizations or integration with CSS-in-JS libraries, you might need to adjust the component's structure or pass style-related props.

**4. Why was Vite chosen for this project?**

Vite was chosen for its excellent developer experience. Key benefits include:

- **Fast Cold Starts:** Near-instant server startup using native ES modules.
- **Lightning-Fast Hot Module Replacement (HMR):** Changes in code reflect instantly in the browser without losing application state.
- **Optimized Builds:** Uses Rollup under the hood for highly optimized production builds.

**5. Is this component accessible?**

The current implementation provides basic visual rating functionality. However, it lacks proper accessibility features for users relying on screen readers or keyboard navigation.

To improve accessibility, consider adding:

- `role="radiogroup"` to the main container (`.star-rating-container`).
- `role="radio"` to each individual star `div`.
- `aria-label` describing each star's value (e.g., "1 star", "2 stars").
- `aria-checked` attribute to indicate the selected star.
- `tabindex` for keyboard focus.
- Keyboard event handlers (e.g., using arrow keys to change the rating and Enter/Space to select).

**6. How can I set an initial rating when the component loads?**

The current component initializes its rating state to `0`. To set an initial rating from the parent component, you would need to modify `StarRating` to accept an `initialRating` or `value` prop and use it to set the initial state.

_Example modification in `star-rating.component.jsx`:_

```jsx
import { useState, useEffect, useCallback } from "react";
import "./star-rating.styles.css";

// Accept initialRating prop, default to 0
const StarRating = ({
  maxRating = 5,
  onChange = () => {},
  initialRating = 0,
}) => {
  // Initialize state with initialRating
  const [currentRating, setCurrentRating] = useState(initialRating);
  const [hoveredRating, setHoveredRating] = useState(0);

  // Update internal state if initialRating prop changes (optional, depends on desired behavior)
  useEffect(() => {
    setCurrentRating(initialRating);
  }, [initialRating]);

  useEffect(() => {
    // Avoid calling onChange on initial mount unless initialRating > 0
    // Or adjust logic based on desired behavior (call always, or only on user interaction)
    if (currentRating !== initialRating || initialRating > 0) {
      // Example condition
      onChange(currentRating);
    }
  }, [currentRating, onChange, initialRating]); // Add initialRating to dependency array if needed

  const setRatingClickHandler = useCallback(
    (ratingValue) => {
      // Clear rating only if clicking the currently selected star
      const newRating = currentRating === ratingValue ? 0 : ratingValue;
      setCurrentRating(newRating);
    },
    [currentRating]
  );

  // ... rest of the component (render logic remains the same)
  // ...
};

export default StarRating;
```

Then, in the parent:

```jsx
<StarRating initialRating={3} onChange={handleRatingUpdate} />
```

**7. Why is `useCallback` used for the click handler?**

`useCallback` is used to memoize the `setRatingClickHandler` function. This means the function itself is not recreated on every render _unless_ its dependencies (`currentRating` in this case) change. While the performance benefit in this simple component might be negligible, it's good practice, especially if this handler were passed down to further child components that rely on referential equality for optimization (e.g., using `React.memo`).
