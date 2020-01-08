---
layout: post
title: Swifty PostgreSQL
date: '2017-02-28 16:41:55'
---

![](https://raw.githubusercontent.com/NghiaTranUIT/nghiatranuit.github.io/master/resources/2017/02/background.png)

I would like to announce my pet project which I’m currently working on 🤗

## Swifty PostgreSQL

Swifty PostgreSQL is PostgreSQL driver, written by Swift 3.0, run flawlessly in macOS 10.12.

The Swifty warpper PostgreSQL’s pointer and C functions, and handle allocate/dellocate memory automatically.

Offer ability to connect many database simultaneously, modeling all PostgreSQL’s enum and constants.

Field model adopt Presentable protocol, so you can get rawString or realData depend on kind of data eaiser, all expensive operations are lazy computed as well.

It’s also a small component in [Titan app](https://github.com/NghiaTranUIT/Titan-Postgresql) (an PostgreSQL client).

![](https://img.shields.io/badge/Swift-3.0-blue.svg?style=flat) ![](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat) ![](https://img.shields.io/npm/l/express.svg?style=flat) ![](https://img.shields.io/badge/platform-osx-green.svg?style=flat)

## Features

- PostgreSQL 9.6 driver
- Written purely Swift 3.0
- Implement base model: Table, Row, Field, Decoder, Connection, Database, Query Result, Connection Result, …
- Following by Protocol-Oriented Programming as possible.
- Prefix searching
- Auto-complete query
- Fully Test (Quick + Nimble)

## Roadmap

– [x] Sketch  
 – [x] Base Foundation (50%)  
 – [ ] Fully Common PostgreSQL  
 – [ ] Prefix searching – implement by Prefix Tree  
 – [ ] Smart Query  
 – [ ] Write Test

## Github

[Github](”https://github.com/NghiaTranUIT/Swifty-PostgreSQL)

## Contributor

It would be greatly appreciated when you make a pull-quest 🤗

## License

Swifty PostgreSQL is available under the MIT license. See the LICENSE file for more info.