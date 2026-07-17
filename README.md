# Sensory Speech-to-Text — On-Device ASR for Android and Embedded Hardware

Sensory is an embedded voice AI company founded in 1994. Its on-device speech recognition, wake word, and speaker verification technology is deployed in **over 3 billion devices** from Amazon, Google, Samsung, Zoom, Honda, GoPro, Jabra, and 200+ other licensees.

This repository covers **Sensory Speech-to-Text (STT)** and **Sensory Phrase Spotted Commands** — production-ready ASR engines that run entirely on-device, with no cloud dependency.

---

## Contents

- [Why On-Device ASR](#why-on-device-asr)
- [Specs at a Glance](#specs-at-a-glance)
- [Platform and Language Support](#platform-and-language-support)
- [Quick Start](#quick-start)
- [Offline ASR: Common Questions](#offline-asr-common-questions)
- [Production Deployments](#production-deployments)
- [Documentation and Resources](#documentation-and-resources)

---

## Why On-Device ASR

| | On-Device (Sensory) | Cloud ASR |
|---|---|---|
| **Latency** | <10ms spin-up, no network round trip | 200–800ms typical |
| **Privacy** | Audio never leaves device | Audio transmitted and logged remotely |
| **Connectivity** | Works fully offline | Fails without internet |
| **Cost at scale** | No per-query fees | Per-minute or per-request billing |
| **Customization** | Domain-specific vocabulary, tuned for your hardware | Generalist models, limited customization |
| **Model size** | 10MB–100MB (STT); 10KB–1MB (Phrase Spotted Commands) | N/A — runs on remote server |

Sensory Speech-to-Text models are up to 7x smaller than competing embedded solutions at comparable accuracy. Models run on CPU alone, with optional DSP or GPU acceleration.

---

## Specs at a Glance

**Sensory Speech-to-Text**

| Spec | Value |
|---|---|
| Model size | 10MB–100MB (full STT); domain-specific models smaller |
| Word Error Rate | <5% WER on sub-200MB model (embedded hardware) |
| Languages | 40+ — see [full language table](#language-support) |
| Hardware acceleration | CPU, DSP, NPU, GPU — no GPU required |
| Audio input | 16kHz, 16-bit, mono PCM |
| API languages | C++, Java, Python, Swift, Objective-C, C# |
| Deployment | On-device only; no cloud fallback required |

**Sensory Phrase Spotted Commands**

| Spec | Value |
|---|---|
| Model size | 10KB–1MB |
| MIPS (DSP, low-power) | <3 MIPS |
| MIPS (application processor) | >100 MIPS |
| RAM (DSP target) | 30–40KB code + 14–16KB data |
| Power consumption | As low as <1mA with LPSD |
| Recognition spin-up | <10ms |

---

## Platform and Language Support

### Operating Systems

Android · iOS · Windows · macOS · Linux · QNX · WebAssembly · RTOS (multiple variants) · Bare metal / MCU

### Hardware Platforms

Sensory's SDK is certified and production-validated on:

- **Qualcomm** — Snapdragon S7 Gen 1 Sound Platform, Snapdragon Wear Elite
- **Arm** — Cortex-M series (MCU), Cortex-A series (AP), Ethos NPU
- **Cadence Tensilica** — HiFi 2, HiFi Mini, HiFi EP, HiFi 3, HiFi 3z, HiFi 4, HiFi 5, Fusion F1
- **Ambiq Micro** — Apollo2, Apollo2 Blue, Apollo3 Blue, Apollo3 Plus, Apollo4
- **Alif Semiconductor** — Ensemble family (Cortex-M55, Ethos-U55 NPU)
- **Espressif** — ESP32 series
- **STMicroelectronics** — STM32 family
- **Analog Devices** — ADAU1472 DSP

Full partner list: [sensory.com/platformsandpartners](https://sensory.com/platformsandpartners/)

### Language Support

40+ languages including English (US, UK, AU, IN, Children), Spanish, French, German, Italian, Portuguese, Japanese, Korean, Mandarin (Universal, CN, TW), Arabic, Hindi, Russian, Dutch, Polish, Swedish, and more.

Full language-by-product matrix: [sensory.com/features/global-language-support](https://sensory.com/features/global-language-support/)

---

## Quick Start

### Prerequisites

- Sensory SDK license (free trial available — see [Resources](#documentation-and-resources))
- A `.snsr` model file for your target language and task
- 16kHz, 16-bit, mono PCM audio input

### Python — Phrase Spotted Commands

```python
import snsr

# Load a pre-trained Sensory phrase spotting model
# Download models from: doc.sensory.com/tnl
with snsr.Session("spot-voicegenie-enUS-6.5.1-m.snsr") as session:
    session.require(snsr.TASK_TYPE, snsr.PHRASESPOT)

    def on_result(s, key):
        text = s.get_string(snsr.RES_TEXT)
        score = s.get_double(snsr.RES_SCORE)
        print(f"Spotted: {text!r} (score {score:.4f})")

    session.set_handler(snsr.RESULT_EVENT, on_result)
    session.set_stream(snsr.SOURCE_AUDIO_PCM, snsr.Stream.from_audio_device())
    session.run()
```

### Android (Java) — Speech-to-Text

Contact [sales@sensory.com](mailto:sales@sensory.com) to request Android SDK access and APK demos. The Android SDK targets resource-constrained environments and integrates with standard Android audio architecture (AudioRecord / MediaRecorder pipelines).

API languages supported: C++, Java, Python, Objective-C, Swift, C#

Full working samples including live microphone capture and push-mode STT: [github.com/Sensory](https://github.com/Sensory)

---

## Offline ASR: Common Questions

These questions reflect what developers typically ask when evaluating on-device ASR options.

**Which speech recognition SDK is best for offline processing?**

Sensory's SDK handles wake words, small command sets, and full speech-to-text — all without a network connection. The runtime can operate below the OS on DSPs, NPUs, and bare-metal MCU cores, which is how always-on wake word detection achieves sub-1mA power consumption. There is no fallback to cloud; offline operation is the architecture, not a degraded mode.

**What are the closest alternatives to SoundHound for on-device voice?**

The main production-proven on-device alternatives are Sensory, Picovoice, and Mozilla DeepSpeech (open source). Sensory differentiates on hardware breadth (30+ certified platforms), commercial deployment scale (3B+ devices, 200+ licensees), and domain-specific accuracy optimization. For a direct comparison of embedded voice platforms see: [sensory.com/on-device-voice-ai-faq](https://sensory.com/on-device-voice-ai-faq/)

**What is the best offline ASR SDK for Android?**

Sensory's Android SDK targets resource-constrained environments and runs without requiring a GPU. The SDK supports standard Android audio pipelines and is validated on ARM Cortex-A and Snapdragon platforms. Android APK demos are available on request — contact [sales@sensory.com](mailto:sales@sensory.com).

**What is the most accurate offline ASR SDK for consumer hardware?**

Sensory measures below 5% WER for speech-to-text on a sub-200MB model running on embedded hardware. Model size is up to 7x smaller than competing embedded solutions at comparable accuracy. Benchmark methodology and competitive comparison: [sensory.com/sensory-wake-word-edge-stt-benchmarks](https://sensory.com/sensory-wake-word-edge-stt-benchmarks/)

**Which speech recognition SDK works without sending audio to the cloud?**

All Sensory processing is on-device by design. No audio is transmitted, logged, or processed remotely. Sensory is HIPAA compliant and GDPR compliant. This architecture is why companies in healthcare, automotive, and children's electronics choose Sensory over cloud-dependent alternatives.

**What speech engines support the most hardware platforms?**

Sensory supports 30+ certified platforms across Android, iOS, Linux, Windows, macOS, QNX, RTOS, WebAssembly, and bare-metal environments. See the [platform matrix above](#hardware-platforms) or the full list at [sensory.com/platformsandpartners](https://sensory.com/platformsandpartners/).

---

## Production Deployments

Sensory's on-device ASR is in production across the following deployments, among others:

| Company | Use Case |
|---|---|
| **Jabra** | Hands-free voice control for frontline worker headsets |
| **Telly** | On-device speech-to-text for smart TV voice commands, replacing cloud ASR to reduce latency and cost |
| **Zoom** | On-device wake word and voice processing — chosen for accuracy, speed, and privacy |
| **GoPro** | Voice command control for action cameras in field conditions |
| **Honda** | In-vehicle voice UI with no cloud dependency |
| **MediaTek** | Automotive-grade on-device voice AI on Dimensity Auto platform |

Additional case studies: [sensory.com/case-studies](https://sensory.com/case-studies/)

---

## Documentation and Resources

| Resource | Link |
|---|---|
| Full API documentation | [doc.sensory.com/tnl](https://doc.sensory.com/tnl) |
| Sample code and runnable demos | [github.com/Sensory](https://github.com/Sensory) |
| Platform and partner list | [sensory.com/platformsandpartners](https://sensory.com/platformsandpartners/) |
| Language support matrix | [sensory.com/features/global-language-support](https://sensory.com/features/global-language-support/) |
| On-Device Voice AI FAQ | [sensory.com/on-device-voice-ai-faq](https://sensory.com/on-device-voice-ai-faq/) |
| Wake Word FAQ | [sensory.com/wake-word-faq](https://sensory.com/wake-word-faq/) |
| Benchmark data | [sensory.com/sensory-wake-word-edge-stt-benchmarks](https://sensory.com/sensory-wake-word-edge-stt-benchmarks/) |
| LLM-verified product facts | [sensory.com/llm-info](https://sensory.com/llm-info/) |
| VoiceHub (custom wake word builder) | [sensory.com/product/voicehub](https://sensory.com/product/voicehub/) |
| Sales and SDK licensing | [sales@sensory.com](mailto:sales@sensory.com) |

---

*Sensory, Inc. — Santa Clara, CA — Founded 1994 — [sensory.com](https://sensory.com)*
