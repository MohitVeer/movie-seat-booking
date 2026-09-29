## Movie Seat Booking

A small cinema seat picker: choose a movie from a dropdown, click through a grid of seats to select the ones you want, and watch the seat count and total price update as you go. Everything you pick survives a page refresh.

## Overview

The layout is a fixed 6x8 grid of seats grouped into rows inside a `.container`, plus a legend showing what each seat color means and a movie dropdown above it. Ten of the 48 seats are marked `occupied` directly in the HTML and can't be selected. Each `<option>` in the movie dropdown carries its ticket price as the option's `value`, so picking a movie is really just picking a number.

## Why I Built It

This was a practice exercise in event delegation and browser storage: rather than attaching a listener to every seat, one listener on the container figures out which seat was clicked, and `localStorage` is used to remember selections, the chosen movie, and the price across reloads.

## Features

- Movie dropdown where the ticket price is read straight from the selected option's value
- Click any available seat to select or deselect it; occupied seats are inert
- Running seat count and total price, recalculated on every change
- Selections, movie choice, and price persist in `localStorage` and are restored on load
- Seats are also operable from the keyboard (`Tab` to focus, `Enter`/`Space` to toggle), with `aria-label`s that describe each seat's current state for screen readers

Note: there's no final checkout or "Book Now" step. The project's scope is seat selection and price calculation, not an actual booking flow, and since there's no backend to book against, a fake confirm button would only be misleading.

## JavaScript Concepts

- **Event delegation** — a single `click` listener on `.container` checks `e.target.classList` rather than binding a handler per seat
- **NodeList to array conversion** — `[...seats].indexOf(seat)` to turn a static `NodeList` into something `indexOf` works on
- **`localStorage` with JSON** — the array of selected seat indices is serialized with `JSON.stringify`/`JSON.parse` since `localStorage` only stores strings
- **Shared logic between input methods** — both the `click` and `keydown` listeners call the same `toggleSeat` function, so mouse and keyboard stay in sync instead of duplicating the toggle rules
- **Dynamic ARIA attributes** — `aria-label` is recalculated and reapplied whenever a seat's class list changes, so assistive tech announces "available", "selected", or "occupied" correctly

## How It Works

Clicking (or pressing Enter/Space on) a seat calls `toggleSeat`, which bails out immediately if the seat is `occupied`, otherwise toggles the `selected` class and updates that seat's `aria-label`. From there `updateSelectedCount` re-queries every `.selected` seat, maps each one back to its index among the non-occupied seats, and writes that index array to `localStorage` along with the seat count, computed total, and current movie. On load, `populateUI` reads the saved index array and movie index back out of `localStorage`, re-applies the `selected` class and the correct dropdown option, and then runs `setupSeatAccessibility` to give every seat `tabindex="0"`, `role="button"`, and its initial `aria-label`.

## How to Run

Open `index.html` in a browser — no build step or dependencies beyond a Google Fonts import.

## Technologies

HTML, CSS, vanilla JavaScript, the `localStorage` Web Storage API.

## What I Learned

How much simpler event delegation makes a grid of near-identical elements, how to keep a keyboard handler from duplicating a click handler's logic, and how to keep ARIA labels honest as element state changes rather than setting them once at creation.

## Future Improvements

- An actual booking/checkout step, if this were ever backed by a real reservation system
- Configurable seat maps instead of a hardcoded 6x8 grid with hardcoded occupied seats
- Visible focus styling tuned specifically for keyboard navigation, beyond the browser default
