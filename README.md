## Welcome to the pathfinder-battleship github repo.

I want to make a game similar to the game battleship.

There will be a board that is divided into a grid.

You will face off against a computer or another human.

The difference is instead of guessing where their opponent's "battleship" is they will choose from a variety of pathfinding algorithms to use to find the opponent's "battleship".

The person who picks the algorithm that finds the other's battleship first wins. I want the algorithms to be visualized on the screen too so the players can see how the algorithms work visually.

### I will divide this project up into 4 parts.

Part 1: Board configuration

Part 2: Game logic

Part 3: User scoreboard

Part 4: Styling

### The technologies I will be using for the front end:
- Vue 3/Nuxt 3
- Typescript

### For the database:
- Supabase (initially for prototyping)
- Rust
  - Axum
  - Sqlx
  - Sea-orm
  - Tokio
  - Tower
  - Serde

### Front End Hosting:
- Netlify

### Backend Hosting (when using rust):
- Shuttle.rs

### Steps to run locally:
1. Clone the repo
2. cd pathfinder-battleship
3. npm i
4. npm run dev
5. Enjoy
