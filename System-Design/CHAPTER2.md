# Chapter 2: Back-of-the-Envelope Estimation — Important Formulas

A quick-reference cheat sheet for the estimation formulas used in system design interviews: QPS, DAU, storage, availability, and unit conversions.

## Table of Contents

- [The 5 Formulas to Memorize First](#the-5-formulas-to-memorize-first)
- [1. QPS (Queries Per Second)](#1-qps-queries-per-second)
- [2. DAU (Daily Active Users)](#2-dau-daily-active-users)
- [3. Storage Estimation](#3-storage-estimation)
- [4. Media Storage (Twitter-like Example)](#4-media-storage-twitter-like-example)
- [5. Availability and Downtime](#5-availability-and-downtime)
- [6. Data Unit Conversions](#6-data-unit-conversions)
- [7. Latency Unit Conversions](#7-latency-unit-conversions)
- [Interview Tip](#interview-tip)

---

## The 5 Formulas to Memorize First

| # | Formula | Expression |
|---|---------|------------|
| 1 | Average QPS | $\text{Daily Requests} \div 86{,}400$ |
| 2 | Peak QPS | $\text{Average QPS} \times \text{Peak Multiplier}$ |
| 3 | Daily Storage | $\text{Items per Day} \times \text{Size per Item}$ |
| 4 | Storage Over Time | $\text{Daily Storage} \times \text{Number of Days}$ |
| 5 | Annual Downtime | $8{,}760 \times (1 - \text{Availability Decimal})$ hours |

---

## 1. QPS (Queries Per Second)

**Total daily requests**

$$
\boxed{\text{Daily Requests} = \text{DAU} \times \text{Requests per User per Day}}
$$

**Average QPS**

$$
\boxed{\text{Average QPS} = \frac{\text{Daily Requests}}{86{,}400}}
$$

> **Remember:** 1 day = 86,400 seconds.

**Peak QPS**

$$
\boxed{\text{Peak QPS} = \text{Average QPS} \times \text{Peak Multiplier}}
$$

**Example:** If average QPS = 4,000 and the peak multiplier is 3, then peak QPS = **12,000**.

---

## 2. DAU (Daily Active Users)

If you know Monthly Active Users (MAU) and the percentage active daily:

$$
\boxed{\text{DAU} = \text{MAU} \times \text{Daily Active Percentage}}
$$

**Example:** 300 million MAU, with 50% active daily:

$$
300M \times 0.5 = 150M \text{ DAU}
$$

---

## 3. Storage Estimation

**Daily storage**

$$
\boxed{\text{Daily Storage} = \text{Items per Day} \times \text{Average Size per Item}}
$$

**Storage over a period**

$$
\boxed{\text{Total Storage} = \text{Daily Storage} \times \text{Number of Days}}
$$

**Example:** 20 TB/day for 365 days:

$$
20 \times 365 = 7{,}300 \text{ TB} = 7.3 \text{ PB}
$$

---

## 4. Media Storage (Twitter-like Example)

When only a percentage of posts contain media:

$$
\boxed{
\begin{aligned}
\text{Daily Media Storage} ={}& \text{DAU} \times \text{Posts per User per Day} \\
& \times \text{Media Percentage} \times \text{Average Media Size}
\end{aligned}
}
$$

**Example from the chapter's assumptions:**

$$
150M \times 2 \times 0.1 \times 1 \text{ MB} = 30 \text{ TB/day}
$$

(10 percent of posts contain media, so the media percentage is 0.1.)

---

## 5. Availability and Downtime

**Downtime percentage**

$$
\boxed{\text{Downtime Percentage} = 100 - \text{Availability Percentage}}
$$

(Both values are expressed in percent, e.g. 100 − 99.9 = 0.1.)

**Annual downtime**

$$
\boxed{\text{Downtime} = \text{Total Time} \times (1 - \text{Availability as a Decimal})}
$$

For a year:

$$
\text{Total Time} = 365 \times 24 = 8{,}760 \text{ hours}
$$

**Example:** 99.9% availability:

$$
8{,}760 \times (1 - 0.999) = 8.76 \text{ hours}
$$

---

## 6. Data Unit Conversions

| Unit | Approximate conversion |
|------|------------------------|
| 1 byte | 8 bits |
| 1 KB | 1,000 bytes |
| 1 MB | 1,000 KB |
| 1 GB | 1,000 MB |
| 1 TB | 1,000 GB |
| 1 PB | 1,000 TB |

> **Note:** The book also discusses binary units, where each step is a power of 1,024. For quick capacity estimates, decimal rounding is often convenient — just state your assumption.

---

## 7. Latency Unit Conversions

| Unit | Equivalent |
|------|------------|
| 1 millisecond (ms) | 1,000 microseconds (μs) |
| 1 microsecond (μs) | 1,000 nanoseconds (ns) |
| 1 second | 1,000 milliseconds |

These conversions help you compare cache, memory, disk, and network latency.

---

## Interview Tip

> Always **state your assumptions**, **keep units consistent**, and **round** your estimates rather than getting stuck on exact arithmetic.
