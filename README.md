<div align="center">

<img src="https://raw.githubusercontent.com/Devanshu11976/FraudDetectionApp/main/banner.svg" alt="RiskRadar — Real-Time Fraud Detection" width="100%"/>

<br/>

<img src="https://img.shields.io/badge/Frontend-React%2019-7C9CFF?style=for-the-badge&logo=react&logoColor=white&labelColor=1b1f4d" />
<img src="https://img.shields.io/badge/Backend-Node.js%20%2B%20Express-9D8CFF?style=for-the-badge&logo=nodedotjs&logoColor=white&labelColor=1b1f4d" />
<img src="https://img.shields.io/badge/Streaming-Apache%20Kafka-FF6B6B?style=for-the-badge&logo=apachekafka&logoColor=white&labelColor=1b1f4d" />
<img src="https://img.shields.io/badge/Database-MongoDB-5EE7C8?style=for-the-badge&logo=mongodb&logoColor=white&labelColor=1b1f4d" />

<br/><br/>

### Score every transaction. Catch fraud before it settles.

*A real-time fraud detection system that streams financial transactions through a weighted risk-scoring engine and surfaces alerts on a live dashboard.*

</div>

<br/>

---

<br/>

## 🛰️ What is RiskRadar?

Fraud doesn't announce itself — it hides in velocity spikes, impossible travel, mismatched verification codes, and devices that don't quite look right. RiskRadar watches every transaction as it streams through Kafka, scores it against more than a dozen weighted risk factors, and raises an alert the moment something crosses the line.

It's built for the workflow financial teams actually need: ingest → score → alert → review — with a dashboard that turns raw fraud signals into something a human can act on in seconds.

<br/>

## ✨ Features

<table>
<tr>
<td width="33%" valign="top">

### ⚡ Real-Time Processing
- Apache Kafka ingestion
- Continuous transaction scoring
- Microservices-based, horizontally scalable

</td>
<td width="33%" valign="top">

### 🧠 Fraud Detection
- Weighted ML-style scoring
- Velocity & impossible-travel detection
- Device fingerprinting
- Geographic risk analysis

</td>
<td width="33%" valign="top">

### 📊 Dashboard & Monitoring
- Live transaction feed
- Alert triage & status tracking
- Historical analytics
- Risk score visualization

</td>
</tr>
</table>

<br/>

## 🧬 Risk Factors RiskRadar Watches

| Category | Signals |
|:---|:---|
| 🚀 **Velocity** | High-frequency transaction bursts |
| 🌍 **Geography** | Impossible travel distance between transactions |
| 🏪 **Merchant Risk** | Suspicious merchants & high-risk categories |
| 📱 **Device Security** | VPN, Tor, emulators, rooted devices |
| 🔐 **Verification** | CVV and AVS mismatch monitoring |
| 👤 **Account Behavior** | New-account risk, decline patterns |
| 🌙 **Timing** | Unusual late-night transaction activity |

<br/>

## 🏗️ Architecture

<img src="https://raw.githubusercontent.com/Devanshu11976/FraudDetectionApp/main/architecture.svg" alt="RiskRadar system architecture" width="100%"/>

<br/>

## 🔄 How a Transaction Flows Through

1. **Ingestion** — Transactions arrive (generated or external) and are published to the Kafka `transactions` topic, with full metadata: location, device, amount, and more.
2. **Real-time processing** — A Kafka consumer picks up each transaction immediately and extracts scoring features: velocity, geo-distance, timing, verification status.
3. **Fraud scoring** — A weighted algorithm computes a fraud probability between **0 and 1**, then maps it to a risk level: `LOW` → `MEDIUM` → `HIGH` → `CRITICAL`.
4. **Alerting** — Any transaction scoring **≥ 0.4** automatically generates an alert with reason codes explaining exactly why it was flagged.
5. **Dashboard** — The frontend polls the backend for live updates; alerts can be reviewed, assigned, and resolved in real time.
6. **Persistence** — Every transaction and alert is stored in MongoDB, building behavioral profiles over time.

<br/>

## 🧮 Scoring Weights

```javascript
const weights = {
  velocityCount:        0.15,  // Transaction frequency
  amountDelta:           0.10,  // Amount pattern deviations
  geoDistance:           0.18,  // Geographic anomalies
  isNightTime:           0.08,  // Unusual timing
  isSuspiciousMerchant:  0.15,  // Risky merchants
  isSuspiciousCategory:  0.12,  // Risky categories
  isHighAmount:          0.08,  // Large transactions
  isVPN:                 0.05,  // VPN detection
  isTor:                 0.10,  // Tor network detection
  cvvFail:               0.12,  // CVV verification failure
  avsFail:               0.08,  // Address verification failure
  suspiciousDevice:      0.10,  // Device fingerprinting
  cardTesting:           0.12,  // Card testing patterns
  newAccount:            0.07   // New account risk
};
```

Thresholds live in `backend/server.js`:

```javascript
const CRITICAL_THRESHOLD = 0.7;
const HIGH_THRESHOLD     = 0.5;
const MEDIUM_THRESHOLD   = 0.3;
const ALERT_THRESHOLD    = 0.4; // Alerts fire above this score
```

Both are tunable — turn the dial up or down depending on how aggressive you want detection to be.

<br/>

## 🚀 Quick Start

### With Docker (recommended)

```bash
git clone <repository-url>
cd FraudDetectionApp-main
```

Set up backend environment variables in `backend/.env`:
```
MONGODB_URI=mongodb://localhost:27017/fraud_detection
KAFKA_BROKERS=localhost:9092
KAFKA_BROKER=localhost:9092
PORT=5000
```

Start everything:
```bash
docker-compose up -d
```
This brings up Zookeeper, Kafka, MongoDB, and the backend API. Then start the frontend:
```bash
npm install
npm run dev
```

App available at **http://localhost:5173**

### Local development (no Docker for app code)

```bash
cd backend && npm install        # backend deps
cd .. && npm install             # frontend deps
docker-compose up zookeeper kafka mongodb -d   # infra only
cd backend && npm run dev        # start backend
npm run dev                      # start frontend (new terminal)
```

<br/>

## 📡 API Reference

<table>
<tr>
<td valign="top">

**Transactions**
```
POST   /api/transactions
GET    /api/transactions
```

**Alerts**
```
GET    /api/alerts
PUT    /api/alerts/:alertId
```

</td>
<td valign="top">

**Stats & System**
```
GET    /api/stats
GET    /api/health
DELETE /api/cleanup/all   (password protected)
```

</td>
</tr>
</table>

<br/>

## 🧪 Testing It Out

Fire a synthetic high-risk transaction straight at the API:

```bash
curl -X POST http://localhost:5000/api/transactions \
  -H "Content-Type: application/json" \
  -d '{
    "id": "TEST001",
    "accountId": "ACC001",
    "amount": 75000,
    "merchant": "UNKNOWN_MERCHANT",
    "category": "CRYPTO",
    "location": {"city": "Mumbai", "country": "IN"},
    "cvvMatch": false,
    "avsMatch": false,
    "isVPN": true
  }'
```

This combination — unknown merchant, crypto category, failed CVV, active VPN — should land solidly in `HIGH` or `CRITICAL` territory.

<br/>

## 🎯 Where This Fits

- **Financial institutions** — real-time payment fraud detection, automated alerting, audit trails
- **E-commerce platforms** — order fraud prevention, account takeover detection
- **Banking applications** — transaction monitoring, card fraud, AML compliance

<br/>

## 🩺 Troubleshooting

| Symptom | Check |
|:---|:---|
| Kafka won't connect | Zookeeper must be running *before* Kafka; verify `KAFKA_BROKERS` and that port `9092` is free |
| MongoDB won't connect | `docker ps` to confirm the container is up; verify `MONGODB_URI` in `backend/.env`; confirm port `27017` is reachable |
| Frontend can't reach API | Confirm backend is running on port `5000`; check CORS config; rule out a local firewall |

<br/>

---

<div align="center">

<sub>Built by <strong>Devanshu Sharma</strong> · provided for educational and development purposes.</sub>

</div>
