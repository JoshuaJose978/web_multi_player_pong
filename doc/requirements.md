# 2 Player Pong Game - Requirements

## Project Overview
Create a networked 2-player Pong game using Python (server) and HTML5 Canvas (client) that allows players to compete over local area networks (LAN).

## Core Requirements

### Technology Stack
- **Language**: 
  - Server: Python
  - Client: JavaScript (HTML5 Canvas)
- **Game Framework**: HTML5 Canvas for rendering
- **Communication**: WebSocket protocol for real-time bidirectional communication
- **Deployment**: 
  - Server: Dockerized application serving both game logic and web client
  - Client: Browser-based (zero installation)
- **Architecture**: Client-Server model with WebSocket communication

### Repository Structure
The repository must contain:
- **Server code**: 
  - Hosts game sessions and handles game logic
  - WebSocket server for real-time communication
  - HTTP server for serving static client files
- **Client code**: 
  - HTML5 Canvas-based web application
  - JavaScript for game rendering and input handling
  - Static files (HTML, CSS, JavaScript) served by the server

### Networking Requirements

#### Host/Server Functionality
- Users run a single Docker container to host a game session
- Server provides both:
  - **HTTP server**: Serves static client files (HTML/CSS/JS)
  - **WebSocket server**: Handles real-time game communication
- Server binds to `0.0.0.0:5000` (configurable via environment variable)
- Single port (5000) for both HTTP and WebSocket traffic
- Server displays its LAN IP address on startup (e.g., "Visit http://192.168.1.5:5000")
- Server uses the host machine's compute resources
- Handle game state synchronization between players (server is authoritative)
- Server host does NOT play - it only facilitates the game between 2 clients

#### Client Functionality
- **Browser-based**: Zero installation, runs in any modern web browser
- Players navigate to `http://SERVER_IP:5000` to access the game
- HTML5 Canvas for game rendering
- WebSocket connection for real-time communication with server
- Keyboard input handling for paddle control
- Responsive UI with connection status, game state display
- Works on desktop browsers (Chrome, Firefox, Safari, Edge)
- Automatic reconnection handling on connection loss

#### Network Performance
- **Efficient network usage**: Minimize bandwidth consumption
- WebSocket binary frames or JSON for game state updates
- Optimize packet frequency and size (target: 30-60 updates/second)
- Handle latency gracefully with client-side interpolation

### Connectivity Scenarios

#### Local Network (LAN) Only
- **Use case**: Play over local WiFi router without requiring internet
- **Connection method**: Players open browser and navigate to server URL
- Server displays URL on startup: `http://192.168.1.X:5000`
- Players must know the server's URL before connecting
- No automatic server discovery mechanism
- Players in the same room/network can connect via browser
- Low latency, high reliability
- No internet connectivity required
- No NAT traversal or WAN support needed

### Docker Requirements
- **Single containerized application** serving both game server and web client
- Dockerfile for building the server image
- docker-compose.yml for easy deployment (optional, but recommended)
- **Simple startup**: `docker run -p 5000:5000 pong-server`
- Server exposes port 5000 for both HTTP and WebSocket
- Environment variables for configuration:
  - `PORT` (default: 5000)
  - `LOG_LEVEL` (default: INFO)
  - `GAME_SPEED`, `WINNING_SCORE`, etc.
- Container includes:
  - Python WebSocket server
  - Static file server for HTML/CSS/JS
  - All game logic
- Clear documentation for running and configuration

## Gameplay Rules

### Player Connection
- **Exactly 2 players** per game session (both are browser clients)
- **First-come-first-served**: First 2 clients to connect get to play
- Third or additional connection attempts see "Game in progress" message and must wait
- Waiting clients see status: "Waiting for player 2..." 
- If a player disconnects mid-game, game pauses for 5 seconds waiting for reconnection
- If disconnected player doesn't reconnect within 5 seconds, game ends
- After game ends, next waiting client can join to start a new game
- Clear connection status indicators in browser UI (Connected, Waiting, Disconnected)

### Game Start Sequence
- Game requires both players (2 clients) to be connected
- Once both players are connected, a 3-second countdown timer begins
- Game starts automatically after countdown completes
- Countdown displays: "Game starting in 3... 2... 1... GO!"

### Game End and Replay
- Game ends when one player reaches winning score (default: 11 points)
- Winner is announced on both clients with celebration animation
- Players can play again by clicking "Play Again" button or pressing spacebar
- Game restarts when both players ready up (new countdown)
- Players can leave anytime by closing browser tab
- Server automatically cleans up disconnected sessions

## Technical Considerations
- **Frame synchronization**: Server is authoritative, sends game state to clients at fixed intervals
- **Smooth rendering**: Client-side interpolation between server updates for smooth gameplay
- **Reconnection handling**: 5-second grace period for disconnected players
- **Error handling**: Graceful degradation with clear error messages to users
- **Input handling**: Client sends input events to server, server validates and updates game state
 
