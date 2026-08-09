# Two‑Way Digital Paging System (EN2130 project)

Two‑Way Digital Paging System implemented with GNU Radio (Software Defined Radio) for the EN2130 module. This repo contains the GNU Radio Companion flowgraphs, embedded Python blocks, and supporting documents for a two‑way paging demo (transmit, receive, ACKs, and simulated channel).

## Table of contents
- Project overview
- Quick start (requirements & installation)
- Running the system (simulation and hardware)
- What’s in this repository (annotated tree)
- Key flowgraphs & custom blocks
- Configurable parameters & notes
- Troubleshooting

## Project overview
This project demonstrates a two‑way digital paging system using GNU Radio flowgraphs and custom embedded Python blocks. It supports end‑to‑end sending and receiving of PDUs, ACK feedback, optional AES‑GCM PDU encryption/decryption, and a simulated channel model for testing without radio hardware.

## Quick start

Prerequisites
- GNU Radio (use a modern GNU Radio GRC installation; conda or system packages are recommended)
- Python 3.8+
- Python packages:
  - numpy
  - pycryptodome (required for AES‑GCM encrypt/decrypt embedded blocks)


Open a flowgraph in GNU Radio Companion (GRC):
1. Open GRC (gnu-radio-companion).
2. Open one of the flowgraph files under `Project Files/` (for example `sender_PDU.grc` or `receiver_PDU.grc`).
3. Inspect and run the generated Python script from GRC.

## Running the system : two modes

Simulation (no SDR hardware)
- Use the channel model blocks present in the flowgraphs (blocks named `channels_channel_model`) to simulate noise, frequency/time offsets, and taps.
- This is convenient for development and debugging.

Hardware (USRP/other SDR)
- Replace the throttle/channel model blocks with the appropriate source/sink (e.g., UHD: USRP source/sink) in GRC or configure the provided flowgraphs if you add hardware blocks.
- Ensure sample rate and center frequencies are supported by the device.


## What’s in this repository

```
Project Files/         # GNU Radio Companion flowgraphs (.grc) and supporting embedded blocks
  sender_PDU.grc       # Transmitter flowgraph — PDU creation, modulation, TX chain
  receiver_PDU.grc     # Receiver flowgraph — RX chain, demod, PDU extraction, deduplication & ACKs
  sender1.grc, receiver1.grc, tt (Copy).grc, ...  # Alternative/earlier versions and helpers
  README.md            # Notes about the versions in this folder
Communication_Design_Project.pdf  # Project report (design document)
Final_Presentation.pdf             # Final presentation slides
README.md             # (this file)
```

## Key flowgraphs & custom blocks (high level)

- sender_PDU.grc
  - Builds PDUs with header + payload, CRC append, digital modulation (QPSK), and optional AES-GCM encrypt PDU block.
  - Exposes GUI variables for: access_key (frame sync), samp_rate, sps, excess_bw, freq offsets, noise voltage, delay, etc.

- receiver_PDU.grc
  - Performs synchronization, demodulation, CRC check, PDU extraction, deduplication, and ACK transmit path.
  - Includes a `pdu_file_writer` embedded block which writes received PDUs to a file (default path is a Windows example — change it).

- Embedded Python blocks (found inside flowgraphs)
  - AES‑GCM Encrypt (PDU): encrypts PDU payloads (requires PyCryptodome).
  - AES‑GCM Decrypt (PDU): decrypts and verifies frames.
  - PDU Deduplicator: ensures duplicate packets are discarded and issues ACKs.
  - Idle/Packet Injector blocks: generate continuous idle bytes and inject packets when available.
  - ByteToPDUWithIdle and other helper blocks: convert continuous byte streams (with idle) to PDUs.
  - BitLimiter / TaggedPayloadExtractor: tag‑based packet length handling.

Important file notes:
- `pdu_file_writer` default `file_path` is set to a Windows path; update this to a path suitable for your OS before running.
- AES blocks use the key hex default: `00112233445566778899aabbccddeeff`. Change to a secure key if encryption is used.

## Configurable parameters (variables present in GRC)
Common user‑exposed variables in the flowgraphs:
- access_key : access code used by digital.correlate access search
- samp_rate : sampling rate used throughout the chain (default ~1e6)
- sps : samples per symbol
- excess_bw : root raised cosine excess bandwidth
- freq_offset, time_offset, noise_volt : simulation channel parameters
- delay, equalizer : GUI sliders exposed for testing timing/delay and equalizer behavior

Look at the GUI variable blocks in each `.grc` to see full names and default values.

## Troubleshooting & tips
- If AES blocks print an error about PyCryptodome, install it:
  pip install pycryptodome
- `pdu_file_writer` uses append mode -- if you want a fresh file each run, change the block’s `append` parameter or the file path.
- If you see frequent CRC failures, reduce noise or adjust `noise_volt`, `freq_offset`, `time_offset` in the channel model to match expected channel conditions.
- For hardware runs, replace the simulated channel block with your SDR source/sink and set correct center frequency and device arguments.
- If the flowgraph uses Windows paths (e.g., `C:\\Users\\...`), change them to your platform’s path before running.


## Documents
- Communication_Design_Project.pdf : full design report with diagrams and results
- Final_Presentation.pdf : slides used for the project final presentation

