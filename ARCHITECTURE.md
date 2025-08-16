# Application Architecture

This document outlines the architecture of the Awesome Kartikey React Star Rating project.

## 1. Overview

This project is a simple **front-end Single Page Application (SPA)** built using **React** and bundled with **Vite**. Its primary purpose is to provide a reusable **Star Rating UI Component**. The architecture follows standard React best practices, emphasizing component composition and local state management.

## 2. Project Folder Structure

```
react-star-rating/
├── index.html             # HTML entry point, mounts the React app
├── package.json           # Project metadata, dependencies, and scripts
├── vite.config.js         # Vite build tool configuration
├── public/                # Static assets (e.g., favicon) served directly
│   └── vite.svg
└── src/                   # Main application source code
    ├── App.css            # Styles specific to the App component
    ├── App.jsx            # Main application container component
    ├── index.css          # Global styles (currently empty)
    ├── main.jsx           # Entry point for React, renders App into the DOM
    ├── star-rating.component.jsx  # The core reusable Star Rating component logic
    ├── star-rating.styles.css     # Styles specific to the Star Rating component
    └── assets/            # Directory for images, fonts, etc. (currently empty)
```

- **`index.html`**: The main HTML file served. The `div#root` element is where the React application is mounted.
- **`package.json`**: Defines project dependencies (`react`, `react-dom`), development dependencies (`vite`, `@vitejs/plugin-react`), and npm scripts (`dev`, `build`, `preview`).
- **`vite.config.js`**: Configures Vite, notably including the React plugin (`@vitejs/plugin-react`) for JSX support and Fast Refresh.
- **`public/`**: Contains static assets that are copied directly to the build output root directory.
- **`src/`**: Contains all the React application code.
  - **`main.jsx`**: Initializes the React application by rendering the `<App />` component into the `div#root` element in `index.html`. It also wraps `<App />` in `<React.StrictMode>`.
  - **`App.jsx`**: Serves as the main container or root component for this demonstration. It imports and uses the `StarRating` component, manages the state for the _displayed_ rating value (received from `StarRating`), and renders basic surrounding UI.
  - **`star-rating.component.jsx`**: The core, reusable UI component. It handles its own internal state (`currentRating`, `hoveredRating`), manages user interactions (click, hover), renders the star icons, and communicates the selected rating back to its parent via the `onChange` prop.
  - **`*.css`**: CSS files for styling. `star-rating.styles.css` specifically styles the `StarRating` component, promoting modularity. `App.css` styles the demo container.

## 3. Major Components

- **`App` Component (`src/App.jsx`)**:

  - **Responsibility:** Acts as the main application view for demonstration purposes. It showcases how to integrate and use the `StarRating` component.
  - **State:** Manages the `productRating` state using `useState`, which holds the rating value selected _within_ the `StarRating` component.
  - **Functionality:** Renders a title, displays the current `productRating`, and renders the `StarRating` component, passing the `setProductRating` function as the `onChange` callback.

- **`StarRating` Component (`src/star-rating.component.jsx`)**:
  - **Responsibility:** Encapsulates the entire logic and UI for the star rating interaction. Designed to be reusable.
  - **Props:** Accepts `maxRating` (Number of stars) and `onChange` (callback function).
  - **State:** Manages two pieces of internal state using `useState`:
    - `currentRating`: The currently selected rating value (persists after click).
    - `hoveredRating`: The rating value currently being hovered over (temporary visual state).
  - **Functionality:**
    - Renders an array of star elements based on `maxRating`.
    - Attaches `onClick`, `onMouseEnter`, and `onMouseLeave` event handlers to each star.
    - Updates `hoveredRating` on mouse enter/leave for visual feedback.
    - Updates `currentRating` on click using a `useCallback`-memoized handler (`setRatingClickHandler`). Allows clearing the rating by clicking the selected star again.
    - Uses `useEffect` to call the `onChange` prop function whenever `currentRating` changes, notifying the parent component.
    - Applies CSS classes (`active`) conditionally based on `currentRating` and `hoveredRating` to style the stars.

## 4. Data Flow

The data flow is primarily unidirectional, flowing from user interaction within `StarRating` up to the `App` component via a callback.

1.  **Initialization:** `App` initializes `productRating` state (e.g., to 0). `StarRating` initializes its internal `currentRating` (to 0) and `hoveredRating` (to 0).
2.  **User Hover:** User moves the mouse over a star in `StarRating`.
    - `onMouseEnter` triggers, calling `setHoveredRating` with the star's value.
    - `StarRating` re-renders, updating the `className` of stars based on the new `hoveredRating` (and `currentRating`) for visual highlighting.
    - `onMouseLeave` triggers when the mouse leaves a star, calling `setHoveredRating(0)`. `StarRating` re-renders.
3.  **User Click:** User clicks a star in `StarRating`.
    - `onClick` triggers, calling the memoized `setRatingClickHandler`.
    - `setRatingClickHandler` updates the internal `currentRating` state via `setCurrentRating`. If the clicked star matches the `currentRating`, it sets it to 0 (clears selection); otherwise, it sets it to the clicked star's value.
    - `StarRating` re-renders due to the state change.
4.  **State Propagation:** The `useEffect` hook within `StarRating`, which depends on `currentRating`, executes.
    - It calls the `onChange` prop function (which is `setProductRating` passed from `App`).
    - The `setProductRating` function in `App` is called with the new rating value.
5.  **Parent Update:** `App` component's state (`productRating`) is updated.
    - `App` re-renders, displaying the updated `productRating` value.

```mermaid
sequenceDiagram
    participant User
    participant StarRating Component
    participant App Component

    User->>+StarRating Component: Hover over star (e.g., star 4)
    StarRating Component->>StarRating Component: onMouseEnter -> setHoveredRating(4)
    StarRating Component->>StarRating Component: Re-render (Highlight stars 1-4)
    User->>+StarRating Component: Click star (e.g., star 4)
    StarRating Component->>StarRating Component: onClick -> setRatingClickHandler(4)
    StarRating Component->>StarRating Component: setCurrentRating(4)
    Note over StarRating Component: useEffect hook triggered by currentRating change
    StarRating Component->>App Component: Calls onChange(4) (which is setProductRating)
    App Component->>App Component: setProductRating(4)
    App Component->>App Component: Re-render (Display "Current Rating: 4")
    StarRating Component-->>-User: Display 4 stars selected
    App Component-->>-User: Display "Current Rating: 4"

    User->>+StarRating Component: Hover leaves star
    StarRating Component->>StarRating Component: onMouseLeave -> setHoveredRating(0)
    StarRating Component->>StarRating Component: Re-render (Keep stars 1-4 selected, no hover highlight)
    StarRating Component-->>-User: Display 4 stars selected

```

## 5. Design Decisions

- **Component Reusability:** `StarRating` is designed as a self-contained, reusable component with a clear API (props `maxRating`, `onChange`), making it easy to integrate elsewhere.
- **Local State Management:** State directly related to the component's UI behavior (hover state, selected state) is managed locally within `StarRating` using `useState`. This keeps the component encapsulated.
- **Controlled Component (via Callback):** While `StarRating` manages its internal state, it communicates changes upwards via the `onChange` prop. This allows the parent component (`App`) to be aware of and react to the rating changes, fitting a common pattern in React.
- **Hook-Based:** Utilizes core React Hooks (`useState`, `useEffect`, `useCallback`) for state, side effects, and memoization, following modern React practices.
- **CSS Modules (Implicit):** Although not strictly CSS Modules, separating styles into `star-rating.styles.css` promotes modularity and prevents style collisions compared to a single global stylesheet.
- **Performance (`useCallback`):** `useCallback` is used for the click handler. While optimization might be minor here, it demonstrates awareness of memoization techniques to prevent unnecessary function recreations, which can be important in more complex scenarios.
- **Vite Tooling:** Leverage Vite for its speed and efficiency during development and for optimized production builds.
