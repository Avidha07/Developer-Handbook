# Chapter 2: Important Formulas

## 1. QPS (Queries Per Second)

**Total Daily Requests**

`Daily Requests = DAU × Requests per User per Day`

**Average QPS**

`Average QPS = Daily Requests / 86,400`

Remember: 1 day = 86,400 seconds.

**Peak QPS**

`Peak QPS = Average QPS × Peak Multiplier`

Example: If average QPS = 4,000 and the peak multiplier is 3:

`Peak QPS = 4,000 × 3 = 12,000`

## 2. DAU (Daily Active Users)

If you know Monthly Active Users (MAU) and the percentage active daily:

`DAU = MAU × Daily Active Percentage`

Example: 300 million MAU, with 50% active daily:

`DAU = 300M × 0.5 = 150M`

## 3. Storage Estimation

**Daily Storage**

`Daily Storage = Items per Day × Average Size per Item`

**Storage Over a Period**

`Total Storage = Daily Storage × Number of Days`

Example: 20 TB/day for 365 days:

`Total Storage = 20 × 365 = 7,300 TB = 7.3 PB`

## 4. Media Storage (Twitter-like Example)

When only a percentage of posts contain media:

`Daily Media Storage = DAU × Posts per User per Day × Media Percentage × Average Media Size`

Example:

`150M × 2 × 10% × 1 MB = 30 TB/day`

## 5. Availability and Downtime

**Downtime Percentage**

`Downtime % = 100% - Availability %`

**Annual Downtime**

`Downtime = Total Time × (1 - Availability as a Decimal)`

For a year:

`Total Time = 365 × 24 = 8,760 hours`

Example: 99.9% availability:

`Downtime = 8,760 × (1 - 0.999) = 8.76 hours`

## 6. Data Unit Conversions

| Unit | Approximate Conversion |
|---|---|
| 1 byte | 8 bits |
| 1 KB | 1,000 bytes |
| 1 MB | 1,000 KB |
| 1 GB | 1,000 MB |
| 1 TB | 1,000 GB |
| 1 PB | 1,000 TB |

**Note:** Binary units use multiples of 1,024 instead of 1,000. State your assumption when estimating storage.

## 7. Latency Unit Conversions

| Unit | Equivalent |
|---|---|
| 1 millisecond (ms) | 1,000 microseconds (μs) |
| 1 microsecond (μs) | 1,000 nanoseconds (ns) |
| 1 second | 1,000 milliseconds |

These conversions help compare cache, memory, disk, and network latency.

## The 5 Formulas to Memorize First

1. **Average QPS:** Daily Requests / 86,400
2. **Peak QPS:** Average QPS × Peak Multiplier
3. **Daily Storage:** Items per Day × Size per Item
4. **Storage Over Time:** Daily Storage × Number of Days
5. **Annual Downtime:** 8,760 × (1 - Availability as a Decimal)

**Interview Tip:** Always state your assumptions, keep units consistent, and round estimates rather than getting stuck on exact arithmetic.
