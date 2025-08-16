# Awesome Kartikey React Star Rating Component

A simple, reusable, and interactive star rating component built with React and Vite. Allows users to select a rating visually using stars.

![Star Rating Component Demo](https://iamkartikey.vercel.app/project-screenshots/react-star-rating.png)

## Features

- **Interactive Rating:** Click stars to set a rating.
- **Hover Effect:** Stars highlight dynamically as you hover over them.
- **Clear Rating:** Click the currently selected star again to clear the rating (set to 0).
- **Customizable Maximum Rating:** Easily set the total number of stars (default is 5).
- **Rating Callback:** Provides an `onChange` prop to get the selected rating in the parent component.
- **Simple Integration:** Easy to drop into any React application.
- **Built with Modern Tools:** Uses React Hooks and Vite for a fast development experience.

## Tech Stack

- **[React](https://reactjs.org/) (v18.2.0):** A JavaScript library for building user interfaces.
- **[Vite](https://vitejs.dev/):** Next Generation Frontend Tooling for fast development and optimized builds.
- **JavaScript (ES6+):** Core programming language.
- **CSS:** Styling the component.

## Setup and Installation

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/awesome-kartikey/react-star-rating.git
    cd react-star-rating
    ```

2.  **Install dependencies:**

    ```bash
    npm install
    # or
    yarn install
    ```

3.  **Run the development server:**

    ```bash
    npm run dev
    # or
    yarn dev
    ```

    This will start the Vite development server, typically at `http://localhost:5173`.

4.  **Build for production:**
    ```bash
    npm run build
    # or
    yarn build
    ```
    This creates an optimized production build in the `dist` folder.

## Usage

Import the `StarRating` component into your React application and use it like any other component.

```jsx
import React, { useState } from "react";
import StarRating from "./path/to/star-rating.component"; // Adjust the import path

function MyProductPage() {
  const [rating, setRating] = useState(3); // Initial rating

  const handleRatingChange = (newRating) => {
    console.log("New rating selected:", newRating);
    setRating(newRating);
  };

  return (
    <div>
      <h1>Rate This Product</h1>
      <p>Your rating: {rating} star(s)</p>
      <StarRating
        maxRating={5} // Optional: default is 5
        onChange={handleRatingChange}
        // You might want to pass the initial rating to the component
        // if you enhance it to accept a 'defaultRating' prop.
        // Currently, it initializes with 0 internally.
      />
      {/* If you want the component to reflect the initial state 'rating',
          you'd need to modify StarRating to accept a 'value' or 'initialRating' prop
          and set its internal state accordingly using useEffect.
          Example modification (in StarRating.jsx):
          const StarRating = ({ maxRating = 5, onChange = () => {}, initialRating = 0 }) => {
              const [currentRating, setCurrentRating] = useState(initialRating);
              // ... rest of the component
          }
      */}
    </div>
  );
}

export default MyProductPage;
```

### Props

- `maxRating` (Number, optional): The total number of stars to display. Defaults to `5`.
- `onChange` (Function, optional): A callback function that gets invoked whenever the rating changes. It receives the new rating value (Number) as its only argument. Defaults to an empty function.
