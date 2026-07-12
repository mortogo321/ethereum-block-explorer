# Ethereum Block Explorer

A real-time Ethereum block explorer demonstrating a Feathers/Socket.io API subscribed to an Ethereum node over WebSocket RPC, persisting blocks to MySQL, and streaming new blocks live to a React frontend.

## What's inside

- **api** — a FeathersJS service backend that:
  - subscribes to `newBlockHeaders` on an Ethereum node via `web3-eth` over a WebSocket RPC connection
  - persists each new block to MySQL through Objection.js/Knex models (`blocks`, `users`)
  - exposes a `blocks` Feathers service (REST + Socket.io) and emits a `newBlock` event on every insert for real-time updates
  - includes a `users` service with Feathers authentication (JWT + local strategy) scaffolded in
- **web** — a React single-page app that connects to the API over Socket.io via the Feathers client, shows connection status, fetches the latest blocks on connect, and prepends new blocks as they arrive in real time

## Tech stack

**API**
- FeathersJS (Express + Socket.io transports)
- Knex and Objection.js (MySQL)
- web3-eth (Ethereum WebSocket subscription)
- Feathers authentication (JWT, local strategy)
- TypeScript, Mocha/ts-mocha for tests

**Web**
- React (Create React App)
- Feathers client with Socket.io transport
- Sass

## Quickstart

Requires a running MySQL instance and access to an Ethereum node's WebSocket RPC endpoint.

```bash
# API
cd api
yarn
# configure MySQL connection and Ethereum RPC endpoint in config/default.json
yarn dev    # hot-reload dev server, or: yarn start

# Web (in a separate terminal)
cd web
yarn
yarn start
```

API runs on `http://localhost:3030` by default; the web app runs on `http://localhost:3000`.

## Project structure

```
api/
  config/                    # default/production/test config (MySQL connection, Ethereum RPC, auth)
  src/
    services/ethereum/       # subscribes to new block headers over WebSocket RPC
    services/blocks/         # Feathers service + hooks persisting and broadcasting blocks
    services/users/          # Feathers service for authentication
    models/                  # Objection.js models (blocks, users)
    hooks/new-block.ts       # emits the newBlock event after a block is created
  test/                      # service tests

web/
  src/
    feathers/                # Feathers client configured with Socket.io transport
    App.js                   # connection status + live block feed UI
```

## Key endpoints / real-time events

- `GET /blocks` — paginated list of stored blocks (Feathers REST)
- Socket.io event `newBlock` on the `blocks` service — pushed to connected clients whenever a new block is stored
- `users` service (`/users`) — Feathers authentication (JWT + local strategy)
