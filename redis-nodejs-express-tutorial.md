# Redis with Node.js & Express — Complete Tutorial

A practical, end-to-end guide to using Redis in an Express application: caching, sessions, rate limiting, and pub/sub.

---

## 1. What is Redis and Why Use It?

Redis is an in-memory key-value data store. Because data lives in RAM, reads and writes are extremely fast (sub-millisecond), which makes it ideal for:

- **Caching** — store expensive database query results or API responses
- **Session storage** — share login sessions across multiple server instances
- **Rate limiting** — track request counts per user/IP
- **Pub/Sub messaging** — real-time notifications between services
- **Queues** — background job processing (with libraries like BullMQ)
- **Leaderboards / counters** — using sorted sets and atomic increments

Redis supports several data structures beyond simple strings: hashes, lists, sets, sorted sets, streams, and more.

---

## 2. Prerequisites

- Node.js 18+ installed
- npm or yarn
- Redis server (locally or via Docker)

### Installing Redis locally

**macOS (Homebrew):**
```bash
brew install redis
brew services start redis
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install redis-server
sudo systemctl enable --now redis-server
```

**Docker (recommended, cross-platform):**
```bash
docker run -d --name redis -p 6379:6379 redis:7-alpine
```

Verify it's running:
```bash
redis-cli ping
# PONG
```

---

## 3. Project Setup

```bash
mkdir redis-express-tutorial
cd redis-express-tutorial
npm init -y
npm install express redis dotenv
npm install -D nodemon
```

Create a `.env` file:
```
PORT=3000
REDIS_URL=redis://localhost:6379
```

Add a dev script to `package.json`:
```json
{
  "scripts": {
    "dev": "nodemon server.js",
    "start": "node server.js"
  }
}
```

---

## 4. Connecting to Redis

We'll use the official `redis` npm package (node-redis v4+), which has a Promise-based API.

Create `redisClient.js`:

```js
// redisClient.js
const { createClient } = require('redis');
require('dotenv').config();

const redisClient = createClient({
  url: process.env.REDIS_URL || 'redis://localhost:6379',
});

redisClient.on('error', (err) => console.error('Redis Client Error', err));
redisClient.on('connect', () => console.log('Connecting to Redis...'));
redisClient.on('ready', () => console.log('Redis client ready'));

async function connectRedis() {
  if (!redisClient.isOpen) {
    await redisClient.connect();
  }
}

module.exports = { redisClient, connectRedis };
```

Create the base `server.js`:

```js
// server.js
const express = require('express');
const { redisClient, connectRedis } = require('./redisClient');
require('dotenv').config();

const app = express();
app.use(express.json());

const PORT = process.env.PORT || 3000;

async function start() {
  await connectRedis();

  app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
  });
}

start();
```

Run it:
```bash
npm run dev
```

You should see `Redis client ready` and `Server running on http://localhost:3000`.

---

## 5. Basic Redis Operations

Let's add routes that demonstrate core Redis data types.

### Strings (GET/SET with expiry)

```js
// routes/strings.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

// Set a value with a 60 second TTL
router.post('/strings/:key', async (req, res) => {
  const { key } = req.params;
  const { value } = req.body;
  await redisClient.set(key, value, { EX: 60 });
  res.json({ message: `Key "${key}" set`, ttl: 60 });
});

// Get a value
router.get('/strings/:key', async (req, res) => {
  const value = await redisClient.get(req.params.key);
  if (value === null) return res.status(404).json({ message: 'Not found' });
  res.json({ key: req.params.key, value });
});

module.exports = router;
```

### Hashes (store objects)

```js
// routes/hashes.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

// Store a user object as a hash
router.post('/users/:id', async (req, res) => {
  const { id } = req.params;
  const { name, email } = req.body;
  await redisClient.hSet(`user:${id}`, { name, email });
  res.json({ message: 'User saved' });
});

// Retrieve the full hash
router.get('/users/:id', async (req, res) => {
  const user = await redisClient.hGetAll(`user:${id}`);
  if (Object.keys(user).length === 0) {
    return res.status(404).json({ message: 'User not found' });
  }
  res.json(user);
});

module.exports = router;
```

### Lists (queues / recent activity)

```js
// routes/lists.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

// Push a new activity log entry (keep only latest 20)
router.post('/activity', async (req, res) => {
  const { message } = req.body;
  await redisClient.lPush('activity_log', message);
  await redisClient.lTrim('activity_log', 0, 19);
  res.json({ message: 'Activity logged' });
});

// Get recent activity
router.get('/activity', async (req, res) => {
  const entries = await redisClient.lRange('activity_log', 0, -1);
  res.json({ entries });
});

module.exports = router;
```

### Sets (unique tags, membership checks)

```js
// routes/sets.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

router.post('/posts/:id/tags', async (req, res) => {
  const { tags } = req.body; // array of strings
  await redisClient.sAdd(`post:${req.params.id}:tags`, tags);
  res.json({ message: 'Tags added' });
});

router.get('/posts/:id/tags', async (req, res) => {
  const tags = await redisClient.sMembers(`post:${req.params.id}:tags`);
  res.json({ tags });
});

module.exports = router;
```

### Sorted Sets (leaderboards)

```js
// routes/leaderboard.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

router.post('/leaderboard/:player/score', async (req, res) => {
  const { score } = req.body;
  await redisClient.zAdd('leaderboard', [{ score, value: req.params.player }]);
  res.json({ message: 'Score updated' });
});

router.get('/leaderboard/top/:n', async (req, res) => {
  const n = parseInt(req.params.n, 10);
  // withScores + REV order for highest first
  const top = await redisClient.zRangeWithScores('leaderboard', 0, n - 1, { REV: true });
  res.json({ leaderboard: top });
});

module.exports = router;
```

Wire these into `server.js`:

```js
const stringsRoutes = require('./routes/strings');
const hashesRoutes = require('./routes/hashes');
const listsRoutes = require('./routes/lists');
const setsRoutes = require('./routes/sets');
const leaderboardRoutes = require('./routes/leaderboard');

app.use(stringsRoutes);
app.use(hashesRoutes);
app.use(listsRoutes);
app.use(setsRoutes);
app.use(leaderboardRoutes);
```

---

## 6. Building a Cache-Aside Layer

The most common Redis use case: cache expensive database calls.

```js
// routes/products.js
const express = require('express');
const router = express.Router();
const { redisClient } = require('../redisClient');

// Simulated slow "database" call
async function fetchProductFromDB(id) {
  await new Promise((r) => setTimeout(r, 800)); // simulate latency
  return { id, name: `Product ${id}`, price: (Math.random() * 100).toFixed(2) };
}

router.get('/products/:id', async (req, res) => {
  const { id } = req.params;
  const cacheKey = `product:${id}`;

  try {
    const cached = await redisClient.get(cacheKey);
    if (cached) {
      return res.json({ source: 'cache', data: JSON.parse(cached) });
    }

    const product = await fetchProductFromDB(id);

    // Cache for 5 minutes
    await redisClient.set(cacheKey, JSON.stringify(product), { EX: 300 });

    res.json({ source: 'database', data: product });
  } catch (err) {
    console.error(err);
    res.status(500).json({ message: 'Server error' });
  }
});

// Invalidate cache when data changes
router.delete('/products/:id/cache', async (req, res) => {
  await redisClient.del(`product:${req.params.id}`);
  res.json({ message: 'Cache invalidated' });
});

module.exports = router;
```

**Pattern to remember:** check cache → on miss, fetch from source → write to cache → return. Always set a TTL so stale data eventually expires even if you forget to invalidate it manually.

### Generic caching middleware

You can generalize this into reusable middleware:

```js
// middleware/cache.js
const { redisClient } = require('../redisClient');

function cache(keyFn, ttlSeconds = 300) {
  return async (req, res, next) => {
    const key = keyFn(req);
    try {
      const cached = await redisClient.get(key);
      if (cached) {
        return res.json(JSON.parse(cached));
      }

      // Monkey-patch res.json to cache the outgoing response
      const originalJson = res.json.bind(res);
      res.json = (body) => {
        redisClient.set(key, JSON.stringify(body), { EX: ttlSeconds }).catch(console.error);
        return originalJson(body);
      };

      next();
    } catch (err) {
      next(err); // fail open — don't block the request if Redis has issues
    }
  };
}

module.exports = cache;
```

Usage:
```js
const cache = require('../middleware/cache');

router.get(
  '/products/:id',
  cache((req) => `product:${req.params.id}`, 300),
  async (req, res) => {
    const product = await fetchProductFromDB(req.params.id);
    res.json({ source: 'database', data: product });
  }
);
```

---

## 7. Session Management with Redis

Storing sessions in Redis lets you scale Express across multiple instances without losing login state.

```bash
npm install express-session connect-redis
```

```js
// server.js (additions)
const session = require('express-session');
const { RedisStore } = require('connect-redis');

app.use(
  session({
    store: new RedisStore({ client: redisClient, prefix: 'sess:' }),
    secret: process.env.SESSION_SECRET || 'change-this-secret',
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: process.env.NODE_ENV === 'production', // requires HTTPS
      httpOnly: true,
      maxAge: 1000 * 60 * 60, // 1 hour
    },
  })
);
```

Example login route:

```js
app.post('/login', (req, res) => {
  const { username } = req.body;
  req.session.user = { username, loggedInAt: Date.now() };
  res.json({ message: 'Logged in', session: req.session.user });
});

app.get('/me', (req, res) => {
  if (!req.session.user) return res.status(401).json({ message: 'Not logged in' });
  res.json(req.session.user);
});

app.post('/logout', (req, res) => {
  req.session.destroy(() => res.json({ message: 'Logged out' }));
});
```

Each session is stored as a Redis key like `sess:<sessionId>`, so any server instance reading from the same Redis can validate the session — perfect for load-balanced deployments.

---

## 8. Rate Limiting

Protect an API endpoint from abuse using Redis counters.

```js
// middleware/rateLimit.js
const { redisClient } = require('../redisClient');

function rateLimit({ windowSeconds = 60, maxRequests = 10 } = {}) {
  return async (req, res, next) => {
    const identifier = req.ip; // or req.user.id if authenticated
    const key = `ratelimit:${identifier}`;

    try {
      const current = await redisClient.incr(key);

      if (current === 1) {
        // first request in this window — set expiry
        await redisClient.expire(key, windowSeconds);
      }

      if (current > maxRequests) {
        const ttl = await redisClient.ttl(key);
        return res.status(429).json({
          message: 'Too many requests',
          retryAfterSeconds: ttl,
        });
      }

      next();
    } catch (err) {
      next(err);
    }
  };
}

module.exports = rateLimit;
```

Apply it:

```js
const rateLimit = require('./middleware/rateLimit');

app.use('/api/', rateLimit({ windowSeconds: 60, maxRequests: 20 }));
```

This gives each client 20 requests per rolling 60-second window. `INCR` is atomic in Redis, so this is safe under concurrent traffic without extra locking.

For production-grade rate limiting with more algorithms (sliding window, token bucket), consider the `rate-limit-redis` package alongside `express-rate-limit`.

---

## 9. Pub/Sub — Real-Time Messaging

Redis Pub/Sub is useful for broadcasting events between services (e.g., notifying all server instances when something changes).

Note: node-redis requires a **separate client connection** for subscribing, since a subscriber connection can't run normal commands.

```js
// pubsub.js
const { createClient } = require('redis');

async function createPubSubClients() {
  const publisher = createClient({ url: process.env.REDIS_URL });
  const subscriber = publisher.duplicate();

  await publisher.connect();
  await subscriber.connect();

  return { publisher, subscriber };
}

module.exports = { createPubSubClients };
```

Example: broadcast a "new order" event and have another part of the app react to it.

```js
// server.js (additions)
const { createPubSubClients } = require('./pubsub');

async function setupPubSub() {
  const { publisher, subscriber } = await createPubSubClients();

  await subscriber.subscribe('orders', (message) => {
    console.log('New order event received:', JSON.parse(message));
    // e.g., push a WebSocket notification, update an in-memory cache, etc.
  });

  return publisher;
}

let orderPublisher;

app.post('/orders', express.json(), async (req, res) => {
  const order = { id: Date.now(), ...req.body };
  await orderPublisher.publish('orders', JSON.stringify(order));
  res.status(201).json({ message: 'Order created', order });
});

// during startup
start().then(async () => {
  orderPublisher = await setupPubSub();
});
```

In a multi-instance deployment, every instance subscribed to `orders` receives the message, which is how you can fan out real-time updates (e.g., to connected WebSocket clients) across all your servers.

---

## 10. Error Handling & Best Practices

- **Always set TTLs on cache keys.** Never let cached data live forever by accident.
- **Fail open, not closed.** If Redis is down, your app should usually still serve requests (perhaps slower, from the database) rather than crash. Wrap Redis calls in try/catch and fall through to the source of truth on error.
- **Use meaningful key naming conventions**, like `resource:id:field` (e.g. `user:42:profile`), to keep keys organized and avoid collisions.
- **Avoid the `KEYS` command in production** — it blocks the server while scanning the whole keyspace. Use `SCAN` for iterating over keys instead.
- **Reuse a single client connection** per process rather than creating new connections per request.
- **Handle reconnection.** node-redis automatically retries by default, but log `error` and `reconnecting` events so you can monitor connection health.
- **Set `maxmemory-policy`** in your Redis config (e.g., `allkeys-lru`) if you're using Redis purely as a cache, so it evicts old data instead of running out of memory.

Graceful shutdown example:

```js
process.on('SIGTERM', async () => {
  console.log('Shutting down gracefully...');
  await redisClient.quit();
  process.exit(0);
});
```

---

## 11. Running Everything with Docker Compose

To spin up both your Express app and Redis together:

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - '3000:3000'
    environment:
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
    ports:
      - '6379:6379'
    volumes:
      - redis-data:/data

volumes:
  redis-data:
```

A minimal `Dockerfile`:

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

Run it:
```bash
docker compose up --build
```

---

## 12. Project Structure Recap

```
redis-express-tutorial/
├── server.js
├── redisClient.js
├── pubsub.js
├── .env
├── docker-compose.yml
├── Dockerfile
├── middleware/
│   ├── cache.js
│   └── rateLimit.js
└── routes/
    ├── strings.js
    ├── hashes.js
    ├── lists.js
    ├── sets.js
    ├── leaderboard.js
    └── products.js
```

---

## 13. Where to Go Next

- **BullMQ** — Redis-backed job queues for background processing
- **RedisJSON / RediSearch** — modules for storing and querying JSON documents directly in Redis
- **Redis Streams** — an append-only log structure, good for event sourcing
- **Cluster mode** — for horizontal scaling of Redis itself once a single instance isn't enough

This tutorial covered the core patterns you'll use in the vast majority of real-world apps: caching, sessions, rate limiting, and pub/sub — all backed by a fast, atomic, in-memory data store.
