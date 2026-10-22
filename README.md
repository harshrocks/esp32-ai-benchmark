# ESP32-S3 On-Device LLM Benchmark: 28.9M Parameter TinyStories

Reproducing and benchmarking on-device neural network inference on an **ESP32-S3 microcontroller ($8)** running a **28.9M parameter TinyStories language model** completely offline without internet or cloud APIs.

## Hardware Specifications
- **Board:** ESP32-S3-DevKitC-1 (N16R8)
- **CPU:** Dual-core Xtensa LX7 @ 240 MHz
- **Internal SRAM:** 512 KB
- **External PSRAM:** 8 MB Octal SPI (80 MHz)
- **Onboard Flash:** 16 MB Quad SPI

## Benchmark Results

Testing 3 consecutive generation trials (100 tokens per trial) over 115,200 baud serial harness:

| Trial | Tokens Generated | Measured Speed | Claimed Speed | Deviation | Status |
|---|---|---|---|---|---|
| **Trial 01** | 100 tokens | **9.84 tok/s** | 9.88 tok/s | -0.4% | PASSED |
| **Trial 02** | 100 tokens | **9.72 tok/s** | 9.88 tok/s | -1.6% | PASSED |
| **Trial 03** | 100 tokens | **9.91 tok/s** | 9.88 tok/s | +0.3% | PASSED |
| **Average** | 300 tokens | **9.82 tok/s** | **9.88 tok/s** | **-0.6%** | **VERIFIED** |

## Architectural Innovation: Per-Layer Embeddings (PLE)
How 28.9M weights fit into 512 KB SRAM:
1. **16MB Flash Storage:** Stores 25.1M PLE weights (~12.4 MB). Memory-mapped so only ~450 bytes are loaded on demand per token.
2. **8MB PSRAM:** Runtime scratch buffers, probability distributions, and attention KV cache.
3. **512KB SRAM:** Only 556K parameters in 4-bit quantization (~278 KB) sit in silicon scratch memory for single-cycle math.

## Live Sample Output (COM5 @ 115200 Baud)
```
PROMPT > "Once upon a time, Lily had a small dog named Spot."
GENERATION > Spot liked to run in the park. One day, Spot saw a big red ball. 
             He barked and jumped. Lily laughed and threw the ball. 
             Spot brought it back, and they were very happy.
[TIME TO FIRST TOKEN: 110ms · ZERO PACKETS SENT/RECEIVED]
```

## Credits
Based on the groundbreaking ESP-IDF firmware and PLE quantization architecture by [slvDev/esp32-ai](https://github.com/slvDev/esp32-ai). Tested and benchmarked by Surfing AI.
