## Portfolio Module

> Base URL: `https://asterdex.com/bapi/futures`
> All Portfolio endpoints are **private** and require authentication.

---

### Pro Summary

**`PRIVATE`** `POST /v1/private/campaign/portfolio/summary/pro`

Returns trading summary statistics for the Pro portfolio within a given time period, including funding fees, commissions, trade counts, trade volumes, and grid strategy metrics.

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `period` | `String` | No | Time period. Enum: `24h`, `7d`, `14d`, `30d`, `all`. Defaults to all-time if omitted. |

**Response** — `ProSummaryResp`

| Field | Type | Description |
|-------|------|-------------|
| `fundingFee` | `BigDecimal` | Cumulative funding fee for the period |
| `latestFundingFee` | `BigDecimal` | Most recent funding fee |
| `totalCommission` | `BigDecimal` | Total commission paid for the period |
| `latestCommission` | `BigDecimal` | Most recent commission payment |
| `totalTradeCount` | `Long` | Total number of trades |
| `longTradeCount` | `Long` | Number of long trades |
| `shortTradeCount` | `Long` | Number of short trades |
| `totalTradeVol` | `BigDecimal` | Total trade volume |
| `longTradeVol` | `BigDecimal` | Long trade volume |
| `shortTradeVol` | `BigDecimal` | Short trade volume |
| `latestUpdated` | `Date` | Timestamp of the last data update |
| `periodStart` | `Date` | Start time of the statistics period |
| `periodEnd` | `Date` | End time of the statistics period |
| `gridTotalCommission` | `BigDecimal` | Total commission for grid strategy |
| `gridLatestCommission` | `BigDecimal` | Most recent grid commission |
| `gridTotalTradeCount` | `Long` | Total trade count for grid strategy |
| `gridLongTradeCount` | `Long` | Long trade count for grid strategy |
| `gridShortTradeCount` | `Long` | Short trade count for grid strategy |
| `gridTotalTradeVol` | `BigDecimal` | Total trade volume for grid strategy |
| `gridLongTradeVol` | `BigDecimal` | Long trade volume for grid strategy |
| `gridShortTradeVol` | `BigDecimal` | Short trade volume for grid strategy |
| `gridLatestUpdated` | `Date` | Last update timestamp for grid data |
| `gridPeriodStart` | `Date` | Grid statistics period start |
| `gridPeriodEnd` | `Date` | Grid statistics period end |

**Request Example**

```json
{
  "period": "7d"
}
```

**Response Example**

```json
{
  "code": "000000",
  "data": {
    "fundingFee": "12.34",
    "latestFundingFee": "0.56",
    "totalCommission": "5.00",
    "latestCommission": "0.10",
    "totalTradeCount": 200,
    "longTradeCount": 120,
    "shortTradeCount": 80,
    "totalTradeVol": "500000.00",
    "longTradeVol": "300000.00",
    "shortTradeVol": "200000.00",
    "latestUpdated": "2026-06-08T00:00:00Z",
    "periodStart": "2026-06-01T00:00:00Z",
    "periodEnd": "2026-06-08T00:00:00Z",
    "gridTotalCommission": "1.00",
    "gridLatestCommission": "0.05",
    "gridTotalTradeCount": 50,
    "gridLongTradeCount": 25,
    "gridShortTradeCount": 25,
    "gridTotalTradeVol": "100000.00",
    "gridLongTradeVol": "50000.00",
    "gridShortTradeVol": "50000.00",
    "gridLatestUpdated": "2026-06-08T00:00:00Z",
    "gridPeriodStart": "2026-06-01T00:00:00Z",
    "gridPeriodEnd": "2026-06-08T00:00:00Z"
  }
}
```

---

### Portfolio Line Chart

**`PRIVATE`** `POST /v1/private/campaign/portfolio/overview/v2/line/chart`

Returns time-series data points for the portfolio equity curve. Each point contains balance and PnL broken down by asset type (perp, spot, staking) for rendering a multi-line chart.

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `period` | `String` | No | Time period. Enum: `24h`, `7d`, `14d`, `30d`, `all`. |

**Response** — `List<PortfolioLineChartResp>`

| Field | Type | Description |
|-------|------|-------------|
| `dt` | `String` | Data point timestamp / label |
| `balance` | `BigDecimal` | Total portfolio balance at this point |
| `pnl` | `BigDecimal` | Total portfolio PnL at this point |
| `perpBalance` | `BigDecimal` | Perpetual contract balance |
| `perpPnl` | `BigDecimal` | Perpetual contract PnL |
| `perpTradePnl` | `BigDecimal` | Realized PnL from perp trades |
| `shieldTradePnl` | `BigDecimal` | Realized PnL from shield trades |
| `spotBalance` | `BigDecimal` | Spot account balance |
| `spotPnl` | `BigDecimal` | Spot PnL |
| `stakingBalance` | `BigDecimal` | Staking balance |
| `stakingPnl` | `BigDecimal` | Staking PnL |
| `predictionPnl` | `BigDecimal` | Prediction (Earn) PnL |
| `allSPnl` | `BigDecimal` | Combined spot + prediction PnL |
| `period` | `String` | Period enum value for this data point |
| `futureUid` | `Long` | User's futures UID |

**Request Example**

```json
{
  "period": "30d"
}
```

**Response Example**

```json
{
  "code": "000000",
  "data": [
    {
      "dt": "2026-05-09",
      "balance": "10000.00",
      "pnl": "200.00",
      "perpBalance": "7000.00",
      "perpPnl": "150.00",
      "perpTradePnl": "100.00",
      "shieldTradePnl": "20.00",
      "spotBalance": "2000.00",
      "spotPnl": "30.00",
      "stakingBalance": "1000.00",
      "stakingPnl": "20.00",
      "predictionPnl": "10.00",
      "allSPnl": "40.00",
      "period": "30d",
      "futureUid": 123456789
    }
  ]
}
```

---

### Portfolio Line Calendar

**`PRIVATE`** `POST /v1/private/campaign/portfolio/overview/line/calendar`

Returns daily-granularity portfolio data points within a specified date range, used to render a calendar-style or date-range chart. Unlike the period-based line chart, this endpoint queries by explicit start and end dates.

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `startDate` | `String` | **Yes** | Start date (inclusive), format: `yyyy-MM-dd` |
| `endDate` | `String` | **Yes** | End date (inclusive), format: `yyyy-MM-dd` |

**Response** — `List<PortfolioLineChartResp>`

Same structure as [Portfolio Line Chart](#portfolio-line-chart). The `dt` field contains the date in `yyyy-MM-dd` format.

**Request Example**

```json
{
  "startDate": "2026-05-01",
  "endDate": "2026-05-31"
}
```

**Response Example**

```json
{
  "code": "000000",
  "data": [
    {
      "dt": "2026-05-01",
      "balance": "9800.00",
      "pnl": "-20.00",
      "perpBalance": "6800.00",
      "perpPnl": "-30.00",
      "perpTradePnl": "-25.00",
      "shieldTradePnl": "5.00",
      "spotBalance": "2000.00",
      "spotPnl": "10.00",
      "stakingBalance": "1000.00",
      "stakingPnl": "0.00",
      "predictionPnl": "0.00",
      "allSPnl": "10.00",
      "period": null,
      "futureUid": 123456789
    }
  ]
}
```

**Error Cases**

| Scenario | Behavior |
|----------|----------|
| `startDate` or `endDate` missing | Returns validation error (400) |
| Date range too large | Keep range ≤ 90 days (recommended) |

---

## Appendix

### Period Enum Values

| Enum | String Value | Description |
|------|-------------|-------------|
| `H_24` | `24h` | Last 24 hours |
| `D_7` | `7d` | Last 7 days |
| `D_14` | `14d` | Last 14 days |
| `D_30` | `30d` | Last 30 days |
| `ALLTIME` | `all` | All time |

### Common Response Wrapper

All endpoints return a standard wrapper:

```json
{
  "code": "000000",
  "message": null,
  "data": { ... },
  "success": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `code` | `String` | `"000000"` indicates success |
| `message` | `String` | Error message, `null` on success |
| `data` | `Object` | Response payload |
| `success` | `Boolean` | `true` on success |
