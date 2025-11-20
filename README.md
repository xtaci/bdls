[![GoDoc][1]][2] [![License][3]][4] [![Build Status][5]][6] [![Go Report Card][7]][8] [![Coverage Statusd][9]][10] [![Sourcegraph][11]][12]

[1]: https://godoc.org/github.com/xtaci/bdls?status.svg
[2]: https://godoc.org/github.com/xtaci/bdls
[3]: https://img.shields.io/github/license/xtaci/bdls
[4]: LICENSE
[5]: https://travis-ci.org/xtaci/bdls.svg?branch=master
[6]: https://travis-ci.org/xtaci/bdls
[7]: https://goreportcard.com/badge/github.com/xtaci/bdls?bdls
[8]: https://goreportcard.com/report/github.com/xtaci/bdls
[9]: https://codecov.io/gh/xtaci/bdls/branch/master/graph/badge.svg
[10]: https://codecov.io/gh/xtaci/bdls
[11]: https://sourcegraph.com/github.com/xtaci/bdls/-/badge.svg
[12]: https://sourcegraph.com/github.com/xtaci/bdls?badge

# BDLS Consensus

## Introduction

BDLS is an innovative BFT consensus algorithm that features safety and liveness by
presenting a mathematically proven secure BFT protocol that is resilient in open networks such as
the Internet. With BDLS, we invented a new random beacons to ensure verifiable
unpredictability and fairness of validators. More importantly, BDLS overcomes many
problems, such as DoS attacks, as well as the deadlock problem caused by unreliable
p2p/broadcast channels. These problems are all very relevant to existing realistic open
network scenarios, and are the focus of extensive work in improving Internet security, but it
is an area largely ignored by most in mainstream BFT protocol design.(Paper: https://eprint.iacr.org/2019/1460.pdf)

For this library, to make the runtime behavior of consensus algorithm predictable as function:
y = f(x, t), where 'x' is the message it received, and 't' is the time while being called,
  then'y' is the deterministic status of consensus after 'x' and 't' applied to 'f',
it has been designed in a deterministic scheme, without parallel computing, networking, and
the correctness of program implementation can be proven with proper test cases.

For more information on the BDLS consensus, you could view here https://medium.com/sperax/bdls-protocol-best-efficiency-best-security-best-performance-4cc2770608dd

## Features

1. Pure algorithm implementation in deterministic and predictable behavior, easily to be integrated into existing projects, refer to [DFA](https://en.wikipedia.org/wiki/Deterministic_finite_automaton) for more.
2. Well-tested on various platforms with complicated cases.
3. Auto back-off under heavy payload, guaranteed finalization(worst case gurantee).
4. Easy integratation into Blockchain & non-Blockchain consensus, like [WAL replication](https://en.wikipedia.org/wiki/Replication_(computing)#Database_replication) in database.
5. Builtin network emulation for various network latency with comprehensive statistics.

## Documentation

For complete documentation, see the associated [Godoc](https://godoc.org/github.com/xtaci/bdls).

## Performance

```
DATE: 2020/03/18
OS: Linux 4.19.84-microsoft-standard #1 SMP Wed Nov 13 11:44:37 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
MEM: 64GB
CPU: AMD Ryzen 7 2700X Eight-Core Processor

TERMINOLOGY: 

DECIDE.AVG = Average finalization time for each height.
DECIDE.ROUNDS = The rounds where decides has made.
PEER.NUM = Actual participantion.
PJ.NUM = Participants(Quorum) 
NET.MSGS = Total network number of messages exchanged in all heights.
NET.BYTES = Total network bytes exchanged in all heights.
MSG.AVGSIZE = Average message size.(Tested with 1KB State.)
NET.MSGRATE = Network message rate(messages/second).
PEER.RATE = Peer's average bandwidth.
DELAY.MIN = Actual minimal network latency(network latency is randomized with normal distribution).
DELAY.MAX = Actual maximal network latency.
DELAY.AVG = Actual average latency.
DELAY.EXP = Expected Latency set to consensus algorithm.

COMMANDS:
$ go test -v -cpuprofile=cpu.out -memprofile=mem.out -timeout 2h

TESTING CASES:
=============

Case 1: 20 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 1.47s      | 1;1;1;1;1     | 20       | 20     | 9765     | 16.1M     | 1.7K        | 66.32/s     | 110.1K/s  | 59.61ms   | 135.47ms  | 100.02ms  | 100ms     |
| 2.3s       | 1;1;1;1;1     | 20       | 20     | 9756     | 16.2M     | 1.7K        | 42.31/s     | 70.6K/s   | 121.64ms  | 273.65ms  | 200.12ms  | 200ms     |
| 3.11s      | 1;1;1;1;1     | 20       | 20     | 9758     | 15.9M     | 1.7K        | 31.30/s     | 50.9K/s   | 177.7ms   | 421.95ms  | 300.04ms  | 300ms     |
| 4.76s      | 1;1;1;1;1     | 20       | 20     | 9756     | 15.9M     | 1.7K        | 20.48/s     | 33.4K/s   | 308.55ms  | 674.98ms  | 499.04ms  | 500ms     |
| 8.85s      | 1;1;1;1;1     | 20       | 20     | 9753     | 15.8M     | 1.7K        | 11.02/s     | 17.8K/s   | 638.02ms  | 1.38348s  | 999.99ms  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 2: 30 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 1.53s      | 1;1;1;1;1     | 30       | 30     | 22152    | 36.2M     | 1.7K        | 96.17/s     | 156.5K/s  | 55.18ms   | 141.39ms  | 100.09ms  | 100ms     |
| 2.33s      | 1;1;1;1;1     | 30       | 30     | 22152    | 36.3M     | 1.7K        | 63.23/s     | 104.2K/s  | 125.74ms  | 275.21ms  | 199.87ms  | 200ms     |
| 3.14s      | 1;1;1;1;1     | 30       | 30     | 22137    | 36.2M     | 1.7K        | 46.90/s     | 76.7K/s   | 176.14ms  | 415.37ms  | 300.16ms  | 300ms     |
| 4.75s      | 1;1;1;1;1     | 30       | 30     | 22136    | 35.9M     | 1.7K        | 31.03/s     | 50.3K/s   | 317.97ms  | 695.47ms  | 499.76ms  | 500ms     |
| 8.9s       | 1;1;1;1;1     | 30       | 30     | 22135    | 36M       | 1.7K        | 16.57/s     | 26.9K/s   | 532.09ms  | 1.34651s  | 1.00002s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 3: 50 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 1.82s      | 1;1;1;1;1     | 50       | 50     | 59819    | 104.3M    | 1.8K        | 131.20/s    | 227.1K/s  | 56.7ms    | 137.95ms  | 99.91ms   | 100ms     |
| 2.59s      | 1;1;1;1;1     | 50       | 50     | 61951    | 102.9M    | 1.7K        | 95.61/s     | 155.7K/s  | 115.92ms  | 289.78ms  | 200.05ms  | 200ms     |
| 3.32s      | 1;1;1;1;1     | 50       | 50     | 61916    | 101.8M    | 1.7K        | 74.52/s     | 122.2K/s  | 170.95ms  | 421.28ms  | 300.02ms  | 300ms     |
| 4.9s       | 1;1;1;1;1     | 50       | 50     | 61905    | 101.6M    | 1.7K        | 50.50/s     | 82.8K/s   | 288.33ms  | 731.75ms  | 500.06ms  | 500ms     |
| 8.97s      | 1;1;1;1;1     | 50       | 50     | 61906    | 101.4M    | 1.7K        | 27.60/s     | 45.2K/s   | 570.08ms  | 1.42545s  | 1.00002s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 4: 80 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 5.6s       | 2;2;1;2;2     | 80       | 80     | 267894   | 1.8G      | 7.1K        | 119.58/s    | 834.2K/s  | 53.01ms   | 150.23ms  | 99.9ms    | 100ms     |
| 3.13s      | 1;1;1;1;1     | 80       | 80     | 153622   | 278.2M    | 1.9K        | 122.58/s    | 217.2K/s  | 110.94ms  | 285.03ms  | 199.81ms  | 200ms     |
| 3.74s      | 1;1;1;1;1     | 80       | 80     | 156056   | 261.3M    | 1.7K        | 104.26/s    | 171.5K/s  | 164.08ms  | 429.22ms  | 299.94ms  | 300ms     |
| 5.24s      | 1;1;1;1;1     | 80       | 80     | 158652   | 260.2M    | 1.7K        | 75.64/s     | 122.2K/s  | 273.16ms  | 718.12ms  | 500.22ms  | 500ms     |
| 9.38s      | 1;1;1;1;1     | 80       | 80     | 159054   | 261M      | 1.7K        | 42.35/s     | 68.8K/s   | 553.89ms  | 1.44921s  | 1.00012s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 5: 100 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 9.5s       | 2;2;3;2;2     | 100      | 100    | 505578   | 3.5G      | 7.3K        | 106.42/s    | 764.4K/s  | 52.3ms    | 145.48ms  | 99.92ms   | 100ms     |
| 7.43s      | 2;2;2;1;1     | 100      | 100    | 361084   | 2.3G      | 6.6K        | 97.18/s     | 626.3K/s  | 100.07ms  | 300.16ms  | 199.57ms  | 200ms     |
| 7.66s      | 1;2;1;2;1     | 100      | 100    | 330107   | 2G        | 6.3K        | 86.08/s     | 527.8K/s  | 167.46ms  | 444.85ms  | 299.79ms  | 300ms     |
| 5.78s      | 1;1;1;1;1     | 100      | 100    | 241856   | 404.4M    | 1.7K        | 83.56/s     | 136.7K/s  | 239.32ms  | 736.61ms  | 499.73ms  | 500ms     |
| 9.54s      | 1;1;1;1;1     | 100      | 100    | 248825   | 409.1M    | 1.7K        | 52.12/s     | 85.2K/s   | 560.41ms  | 1.48048s  | 1.00002s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------

Case 6: 20 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 2.59s      | 2;1;2;2;1     | 13       | 20     | 5016     | 8.2M      | 1.7K        | 29.79/s     | 49.6K/s   | 58.49ms   | 140.78ms  | 100ms     | 100ms     |
| 9.98s      | 1;2;3;4;2     | 13       | 20     | 6264     | 9.6M      | 1.6K        | 9.65/s      | 15.1K/s   | 118.56ms  | 269.81ms  | 200.2ms   | 200ms     |
| 7.2s       | 1;1;2;3;1     | 13       | 20     | 5016     | 8.2M      | 1.7K        | 10.72/s     | 17.8K/s   | 198.85ms  | 408.56ms  | 300.19ms  | 300ms     |
| 30.95s     | 5;2;1;1;1     | 13       | 20     | 5640     | 8.9M      | 1.6K        | 2.80/s      | 4.5K/s    | 304.56ms  | 663.24ms  | 499.11ms  | 500ms     |
| 23.43s     | 2;2;2;1;2     | 13       | 20     | 5328     | 8.6M      | 1.7K        | 3.50/s      | 5.7K/s    | 621.09ms  | 1.37182s  | 1.00037s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 7: 30 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 24.06s     | 1;2;1;7;4     | 20       | 30     | 17381    | 25.7M     | 1.5K        | 7.22/s      | 10.9K/s   | 60.17ms   | 143.24ms  | 99.98ms   | 100ms     |
| 27.88s     | 2;6;2;1;4     | 20       | 30     | 17383    | 25.7M     | 1.5K        | 6.23/s      | 9.4K/s    | 123.31ms  | 269.15ms  | 200.06ms  | 200ms     |
| 15.14s     | 3;1;2;2;4     | 20       | 30     | 15101    | 23.1M     | 1.6K        | 9.97/s      | 15.6K/s   | 186.63ms  | 422.51ms  | 299.87ms  | 300ms     |
| 11.47s     | 3;1;1;2;1     | 20       | 30     | 12060    | 19.7M     | 1.7K        | 10.51/s     | 17.4K/s   | 307.34ms  | 682.54ms  | 499.59ms  | 500ms     |
| 44.77s     | 3;2;4;1;2     | 20       | 30     | 15100    | 23.1M     | 1.6K        | 3.37/s      | 5.3K/s    | 622.36ms  | 1.34913s  | 999.89ms  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 8: 50 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 6.23s      | 4;3;3;1;1     | 33       | 50     | 41680    | 65M       | 1.6K        | 40.50/s     | 64.2K/s   | 55.59ms   | 139.67ms  | 100.03ms  | 100ms     |
| 10.51s     | 2;1;4;4;1     | 33       | 50     | 41664    | 64.4M     | 1.6K        | 24.02/s     | 37.8K/s   | 117.5ms   | 290.2ms   | 199.97ms  | 200ms     |
| 15.37s     | 2;3;3;1;4     | 33       | 50     | 43776    | 66.8M     | 1.6K        | 17.25/s     | 26.8K/s   | 179.9ms   | 421.71ms  | 299.84ms  | 300ms     |
| 10.91s     | 2;1;2;2;1     | 33       | 50     | 33216    | 54.9M     | 1.7K        | 18.45/s     | 30.9K/s   | 303.06ms  | 713.69ms  | 500.5ms   | 500ms     |
| 38.8s      | 3;3;1;2;3     | 33       | 50     | 41664    | 64.4M     | 1.6K        | 6.51/s      | 10.2K/s   | 609.65ms  | 1.38302s  | 1.00107s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 9: 80 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 5.25s      | 2;2;3;2;3     | 53       | 80     | 121369   | 520.3M    | 4.4K        | 87.18/s     | 377.5K/s  | 59.25ms   | 149.4ms   | 99.95ms   | 100ms     |
| 6s         | 1;2;2;2;2     | 53       | 80     | 91790    | 152.8M    | 1.7K        | 57.64/s     | 96.5K/s   | 120.06ms  | 286.27ms  | 199.98ms  | 200ms     |
| 5.87s      | 2;1;2;1;1     | 53       | 80     | 80728    | 138.1M    | 1.8K        | 51.84/s     | 88.6K/s   | 175.44ms  | 423.91ms  | 300.17ms  | 300ms     |
| 11.21s     | 2;1;2;1;2     | 53       | 80     | 86217    | 142.9M    | 1.7K        | 29.02/s     | 48.5K/s   | 291.37ms  | 734.8ms   | 500.38ms  | 500ms     |
| 21.97s     | 1;1;2;2;2     | 53       | 80     | 86216    | 142.9M    | 1.7K        | 14.80/s     | 24.9K/s   | 533.87ms  | 1.43307s  | 1.00042s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 10: 100 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s correctly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 8.92s      | 4;2;3;2;4     | 67       | 100    | 248185   | 1.7G      | 7K          | 82.99/s     | 577.1K/s  | 50.01ms   | 144ms     | 99.98ms   | 100ms     |
| 8.28s      | 2;2;3;2;3     | 67       | 100    | 175047   | 286.4M    | 1.7K        | 63.08/s     | 103.9K/s  | 116.41ms  | 287.7ms   | 199.87ms  | 200ms     |
| 10.21s     | 2;3;1;1;3     | 67       | 100    | 156156   | 279M      | 1.8K        | 45.63/s     | 81.9K/s   | 157.92ms  | 422.21ms  | 300.11ms  | 300ms     |
| 19.24s     | 1;4;1;2;1     | 67       | 100    | 146918   | 239M      | 1.7K        | 22.79/s     | 37.5K/s   | 289.88ms  | 705.65ms  | 500.13ms  | 500ms     |
| 38.13s     | 1;2;4;2;1     | 67       | 100    | 155760   | 248.7M    | 1.6K        | 12.19/s     | 19.8K/s   | 558.98ms  | 1.49546s  | 1.00018s  | 1s        |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 11: 50 Fully Connected Participants in 100ms,200ms,300ms,500ms,1s incorrectly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 2.9s       | 2;2;2;2;2     | 50       | 50     | 116591   | 438.1M    | 3.8K        | 160.49/s    | 605.2K/s  | 58.21ms   | 145.52ms  | 99.98ms   | 50ms      |
| 2.81s      | 1;1;2;1;1     | 50       | 50     | 81768    | 250.3M    | 3.1K        | 116.32/s    | 343.2K/s  | 98.55ms   | 281.13ms  | 199.73ms  | 100ms     |
| 3.61s      | 2;1;1;1;1     | 50       | 50     | 82816    | 219.3M    | 2.7K        | 91.68/s     | 239K/s    | 175.29ms  | 441.01ms  | 299.71ms  | 150ms     |
| 4.45s      | 1;1;1;1;1     | 50       | 50     | 73038    | 114.1M    | 1.6K        | 65.56/s     | 101.8K/s  | 287.19ms  | 743.13ms  | 500ms     | 250ms     |
| 7.99s      | 1;1;1;1;1     | 50       | 50     | 73225    | 114.2M    | 1.6K        | 36.62/s     | 56.9K/s   | 606.02ms  | 1.41356s  | 999.36ms  | 500ms     |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+

Case 12: 50 Partially Connected Participants in 100ms,200ms,300ms,500ms,1s incorrectly set expected delay
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| DECIDE.AVG | DECIDE.ROUNDS | PEER.NUM | PJ.NUM | NET.MSGS | NET.BYTES | MSG.AVGSIZE | NET.MSGRATE | PEER.RATE | DELAY.MIN | DELAY.MAX | DELAY.AVG | DELAY.EXP |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
| 3.45s      | 2;2;3;3;3     | 33       | 50     | 50621    | 80.2M     | 1.6K        | 88.78/s     | 141.4K/s  | 58.45ms   | 141.37ms  | 100.02ms  | 50ms      |
| 8.49s      | 2;3;4;4;2     | 33       | 50     | 55392    | 119M      | 2.2K        | 39.54/s     | 86.3K/s   | 119.07ms  | 279.23ms  | 200.16ms  | 100ms     |
| 4.57s      | 1;1;2;2;2     | 33       | 50     | 42720    | 144.1M    | 3.5K        | 56.65/s     | 192.2K/s  | 174.43ms  | 422.48ms  | 300.09ms  | 150ms     |
| 8.04s      | 2;1;1;3;1     | 33       | 50     | 38497    | 60.9M     | 1.6K        | 28.99/s     | 46.3K/s   | 297.08ms  | 699.25ms  | 499.54ms  | 250ms     |
| 18.06s     | 1;2;3;2;1     | 33       | 50     | 40608    | 63.2M     | 1.6K        | 13.63/s     | 21.4K/s   | 595.42ms  | 1.45499s  | 1.00061s  | 500ms     |
+------------+---------------+----------+--------+----------+-----------+-------------+-------------+-----------+-----------+-----------+-----------+-----------+
```

See also overload benchmark: [PI4-OVERLOAD.TXT](benchmarks/PI4-OVERLOAD.TXT)

## Specification

1. Consensus messages are specified in [message.proto](message.proto), users of this library can encapsulate this message in a carrier message, like gossip in TCP.
2. Consensus algorithm is **NOT** thread-safe, it **MUST** be protected by some synchronization mechanism, like `sync.Mutex` or `chan` + `goroutine`.

## Usage

1. A testing IPC peer -- [ipc_peer.go](ipc_peer.go)
2. A testing TCP node -- [TCP based Consensus Emualtor](cmd/emucon)

## Status

GA
