# Redis with Node.js & Express — Complete Tutorial
# Redis-এর সাথে Node.js ও Express — সম্পূর্ণ টিউটোরিয়াল

A practical, end-to-end guide to using Redis in an Express application: caching, sessions, rate limiting, and pub/sub.

> **বাংলায়:** এটি একটি ব্যবহারিক, শেষ-থেকে-শেষ গাইড যা Express অ্যাপে Redis ব্যবহার করা শেখায়: ক্যাশিং, সেশন, রেট লিমিটিং ও পাব/সাব।

---

## 1. What is Redis and Why Use It? — Redis কী এবং কেন ব্যবহার করবেন?

> **বাংলায়:** Redis হলো একটি ইন-মেমোরি কী-ভ্যালু ডেটা স্টোর যা খুব দ্রুত (সাব-মিলিসেকেন্ড)। ক্যাশিং, সেশন, রেট লিমিট, পাব/সাব ও লিডারবোর্ডের জন্য ব্যবহৃত হয়।

Redis is an in-memory key-value data store. Because data lives in RAM, reads and writes are extremely fast (sub-millisecond), which makes it ideal for:

> **বাংলায়:** Redis একটি ইন-মেমোরি কী-ভ্যালু ডেটা স্টোর। ডেটা RAM-এ থাকায় পড়া ও লেখা খুব দ্রুত (সাব-মিলিসেকেন্ড), তাই এটি নিচের কাজগুলোর জন্য আদর্শ:
> - **ক্যাশিং** — ব্যয়বহুল DB ক্যোয়েরি বা API রেস্পন্স সাময়িক জমা রাখা
> - **সেশন স্টোরেজ** — একাধিক সার্ভার ইনস্ট্যান্সে লগইন সেশন শেয়ার করা
> - **রেট লিমিটিং** — ইউজার/IP প্রতি রিকোয়েস্ট গোনা
> - **পাব/সাব মেসেজিং** — সার্ভিসের মধ্যে রিয়েল-টাইম নোটিফিকেশন
> - **কিউ** — ব্যাকগ্রাউন্ড জব (BullMQ-এর মতো)
> - **লিডারবোর্ড/কাউন্টার** — সর্টেড সেট ও অ্যাটমিক ইনক্রিমেন্ট

Redis supports several data structures beyond simple strings: hashes, lists, sets, sorted sets, streams, and more.

> **বাংলায়:** সাধারণ স্ট্রিং ছাড়াও Redis হ্যাশ, লিস্ট, সেট, সর্টেড সেট, স্ট্রিম ইত্যাদি ডেটা স্ট্রাকচার সাপোর্ট করে।

---

## 2. Prerequisites — প্রয়োজনীয়তা (Prerequisites)

> **বাংলায়:** শুরু করার আগে আপনার Node.js 18+, npm/yarn এবং একটি Redis সার্ভার (লোকাল বা Docker) লাগবে।

- Node.js 18+ installed
- npm or yarn
- Redis server (locally or via Docker)

### Installing Redis locally — লোকালে Redis ইনস্টল করা

> **বাংলায়:** নিচে macOS, Ubuntu/Debian ও Docker-এর জন্য Redis ইনস্টল ও চালু করার কমান্ড দেওয়া হয়েছে। Docker ব্যবহার সব প্ল্যাটফর্মে সুবিধাজনক।

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

## 3. Project Setup — প্রজেক্ট সেটআপ

> **বাংলায়:** নতুন ফোল্ডার বানিয়ে Express, redis ও dotenv ইনস্টল করে `.env` ফাইল ও `package.json`-এ dev স্ক্রিপ্ট যোগ করতে হবে।

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

## 4. Connecting to Redis — Redis-এর সাথে কানেক্ট করা

> **বাংলায়:** অফিশিয়াল `redis` npm প্যাকেজ (node-redis v4+) Promise-ভিত্তিক API দেয়। এখানে `redisClient.js` ও বেস `server.js` বানানো হয়েছে যা কানেকশন ত্রুটি হ্যান্ডেল করে।

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

## 5. Basic Redis Operations — Redis-এর মৌলিক অপারেশন

Let's add routes that demonstrate core Redis data types.

> **বাংলায়:** এখানে রাউট যোগ করে Redis-এর মূল ডেটা টাইপগুলো (স্ট্রিং, হ্যাশ, লিস্ট, সেট, সর্টেড সেট) দেখানো হয়েছে।

### Strings (GET/SET with expiry) — স্ট্রিং (মেয়াদসহ GET/SET)

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

### Hashes (store objects) — হ্যাশ (অবজেক্ট স্টোর করতে)

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

### Lists (queues / recent activity) — লিস্ট (কিউ / সাম্প্রতিক কার্যকলাপ)

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

### Sets (unique tags, membership checks) — সেট (ইউনিক ট্যাগ, সদস্যপদ চেক)

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

### Sorted Sets (leaderboards) — সর্টেড সেট (লিডারবোর্ড)

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

## 6. Building a Cache-Aside Layer — ক্যাশ-অ্যাসাইড লেয়ার তৈরি করা

The most common Redis use case: cache expensive database calls.

> **বাংলায়:** Redis-এর সবচেয়ে সাধারণ ব্যবহার: ব্যয়বহুল ডেটাবেস কল ক্যাশ করা। ক্যাশে থাকলে সেখান থেকে দ্রুত রিটার্ন করে, না থাকলে DB থেকে এনে ক্যাশে রাখে।

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

> **বাংলায় মনে রাখার নিয়ম:** আগে ক্যাশ চেক করো → না পেলে মূল উৎস থেকে আনো → ক্যাশে লেখো → রিটার্ন করো। সবসময় TTL সেট করো যাতে ভুলে গেলেও পুরোনো ডেটা একসময় মুছে যায়।

### Generic caching middleware — জেনেরিক ক্যাশিং মিডলওয়্যার

You can generalize this into reusable middleware:

> **বাংলায়:** এই ক্যাশিং লজিককে পুনর্ব্যবহারযোগ্য মিডলওয়্যার হিসেবে লিখে যেকোনো রাউটে লাগানো যায়।

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

## 7. Session Management with Redis — Redis দিয়ে সেশন ম্যানেজমেন্ট

Storing sessions in Redis lets you scale Express across multiple instances without losing login state.

> **বাংলায়:** সেশন Redis-এ রাখলে একাধিক Express ইনস্ট্যান্স চালিয়েও লগইন অবস্থা হারাবেন না—সব ইনস্ট্যান্স একই Redis থেকে সেশন পড়তে পারে।

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

> **বাংলায়:** প্রতিটি সেশন `sess:<sessionId>` আকারে Redis কী হিসেবে জমা থাকে, তাই একই Redis পড়া যেকোনো সার্ভার ইনস্ট্যান্স সেশন যাচাই করতে পারে—লোড-ব্যালেন্সড ডিপ্লয়ের জন্য আদর্শ।

## 8. Rate Limiting — রেট লিমিটিং (অপব্যবহার ঠেকাতে)

Protect an API endpoint from abuse using Redis counters.

> **বাংলায়:** Redis কাউন্টার (INCR) ব্যবহার করে একটি API এন্ডপয়েন্টকে অপব্যবহার থেকে রক্ষা করা যায়—প্রতি ক্লায়েন্ট নির্দিষ্ট সময়ে সীমিত রিকোয়েস্ট করতে পারবে।

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

> **বাংলায়:** এটি প্রতি ক্লায়েন্টকে ৬০ সেকেন্ডে সর্বোচ্চ ২০টি রিকোয়েস্ট দেয়। Redis-এ `INCR` অ্যাটমিক বলে কনকারেন্ট ট্রাফিকেও এটি নিরাপদ (আলাদা লক লাগে না)। প্রোডাকশনে স্লাইডিং উইন্ডো বা টোকেন বালতির জন্য `rate-limit-redis` + `express-rate-limit` বিবেচনা করুন।

## 9. Pub/Sub — Real-Time Messaging — পাব/সাব: রিয়েল-টাইম মেসেজিং

Redis Pub/Sub is useful for broadcasting events between services (e.g., notifying all server instances when something changes).

Note: node-redis requires a **separate client connection** for subscribing, since a subscriber connection can't run normal commands.

> **বাংলায়:** Redis পাব/সাব সার্ভিসের মধ্যে ইভেন্ট ব্রডকাস্ট করতে (যেমন কিছু বদলালে সব ইনস্ট্যান্সকে জানানো) কাজে লাগে। লক্ষ্য রাখবেন: সাবস্ক্রাইব করতে আলাদা ক্লায়েন্ট কানেকশন লাগে, কারণ সাবস্ক্রাইবার কানেকশন সাধারণ কমান্ড চালাতে পারে না।

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

> **বাংলায়:** মাল্টি-ইনস্ট্যান্স ডিপ্লয়ে `orders`-এ সাবস্ক্রাইব করা প্রতিটি ইনস্ট্যান্স মেসেজ পায়—এভাবে রিয়েল-টাইম আপডেট (যেমন কানেক্টেড WebSocket ক্লায়েন্টে) সব সার্ভারে ছড়িয়ে দেওয়া যায়।

## 10. Error Handling & Best Practices — এরর হ্যান্ডলিং ও সেরা চর্চা

> **বাংলায়:** নিচে Redis ব্যবহারের কিছু গুরুত্বপূর্ণ নিয়ম দেওয়া হয়েছে যাতে অ্যাপ নিরাপদ ও দ্রুত থাকে।
> - **সব ক্যাশ কী-তে TTL সেট করুন**—ভুলে ডেটা চিরকাল না থাকুক।
> - **ফেইল ওপেন, ফেইল ক্লোজড নয়**—Redis বন্ধ থাকলে অ্যাপ ক্র্যাশ না করে DB থেকে (ধীরে) সার্ভ করুক।
> - **অর্থপূর্ণ কী-নামিং** ব্যবহার করুন (যেমন `user:42:profile`)।
> - **প্রোডাকশনে `KEYS` কমান্ড এড়িয়ে চলুন**—পুরো কীস্পেস স্ক্যান করে সার্ভার আটকায়; `SCAN` ব্যবহার করুন।
> - **প্রতি প্রসেসে একটিই ক্লায়েন্ট কানেকশন** রি-ইউজ করুন।
> - **রি-কানেকশন হ্যান্ডেল করুন**—`error` ও `reconnecting` ইভেন্ট লগ করুন।
> - শুধু ক্যাশ হিসেবে ব্যবহার করলে `maxmemory-policy` (যেমন `allkeys-lru`) সেট করুন যাতে পুরোনো ডেটা সরে যায়।

Graceful shutdown example:

> **বাংলায়:** নিচে দেখানো হয়েছে কীভাবে SIGTERM সিগন্যাল পেলে Redis কানেকশন ঠিকমতো বন্ধ (quit) করে অ্যাপ শাট ডাউন করতে হয়।

```js
process.on('SIGTERM', async () => {
  console.log('Shutting down gracefully...');
  await redisClient.quit();
  process.exit(0);
});
```

---

## 11. Running Everything with Docker Compose — Docker Compose দিয়ে সব চালানো

> **বাংলায়:** `docker-compose.yml` ও `Dockerfile` দিয়ে Express অ্যাপ ও Redis একসাথে কন্টেইনারে চালানো যায়। `docker compose up --build` কমান্ডে সব চালু হয়।

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

## 12. Project Structure Recap — প্রজেক্ট স্ট্রাকচার সারসংক্ষেপ

> **বাংলায়:** নিচে এই টিউটোরিয়ালে তৈরি করা ফাইল ও ফোল্ডারের গঠন দেওয়া হয়েছে।

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

## 13. Where to Go Next — এরপর কী শিখবেন?

> **বাংলায়:** এগিয়ে যাওয়ার জন্য BullMQ (জব কিউ), RedisJSON/RediSearch (JSON কোয়েরি), Redis Streams (ইভেন্ট সোর্সিং) ও Cluster mode (স্কেলিং) শিখতে পারেন।

This tutorial covered the core patterns you'll use in the vast majority of real-world apps: caching, sessions, rate limiting, and pub/sub — all backed by a fast, atomic, in-memory data store.

> **বাংলায়:** এই টিউটোরিয়ালে বাস্তব অ্যাপের বেশিরভাগ ক্ষেত্রে ব্যবহৃত মূল প্যাটার্নগুলো শেখানো হয়েছে: ক্যাশিং, সেশন, রেট লিমিটিং ও পাব/সাব—একটি দ্রুত, অ্যাটমিক, ইন-মেমোরি ডেটা স্টোরের ওপর ভিত্তি করে।
