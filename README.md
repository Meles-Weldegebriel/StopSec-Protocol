# StopSec: Real-Time Interference Management for Spectrum Sharing

## Overview
StopSec is a real-time wireless spectrum sharing system that detects and stops interfering users under low-SNR conditions. The system combines PHY-layer watermarking with a closed-loop feedback mechanism to enable fast and reliable interference mitigation without degrading the communication link.

---

## My Contribution
- Designed PHY-layer watermarking and pseudonym encoding for interference identification  
- Implemented real-time SDR-based transmitter and receiver pipelines (USRP + UHD)  
- Developed low-SNR detection and pseudonym decoding algorithms  
- Integrated database-driven feedback control for interference mitigation  
- Conducted over-the-air multi-node experiments under real channel conditions  

---

## System Architecture

![System Architecture](system_architecture.png)

### Workflow
1. Secondary users (SU) transmit OFDM signals with embedded pseudonyms  
2. Primary user (PU) detects interference and decodes pseudonym  
3. PU writes interference report to database  
4. Secondary users query database and stop transmission if detected  

---
## Experimental Setup

![Experimental Setup](implementation_setup.png)

- Multi-node deployment with multiple secondary users (SU1, SU2, SU3)  
- Primary user (PU) monitors interference in real time  
- Distributed SDR nodes deployed across a real wireless environment  
- Experiments conducted under realistic channel conditions (multipath, interference)  

---

## Results

### Pseudonym Detection Performance
![Detection Performance](pseudonym_detection.png)

- Reliable detection down to very low SNR (≈ −10 dB)  
- Graceful degradation with multiple interfering users  

---

### Interference Mitigation Latency vs Bandwidth
![Latency vs Bandwidth](latency_bandwidth.png)

- Faster response with higher bandwidth configurations  
- Demonstrates trade-off between packet duration and system latency  

---

### Multiple Interfering Users
![Multiple Users](multiple_su.png)

- System successfully detects and stops multiple interfering users  
- Increased number of users leads to moderate increase in stopping time  

---

## Technical Highlights
- OFDM-based waveform with subcarrier-level watermarking  
- Correlation-based synchronization and detection  
- Low-SNR signal detection and decoding  
- Real-time SDR streaming and buffer management  
- Hardware-in-the-loop validation with USRP platforms  

---

## Tools & Platforms
- Python  
- UHD API  
- USRP (X310 / B210)  
- GNU Radio (for SDR integration)  

---
  
## Reproducibility
Instructions below.
For POWDER testbed users, if you would like to run experiments using the StopSec protocol, please use the Pseudonymetry profile and follow the following steps. 

This experiment uses rooftop SDR nodes (USRP X310) and compute nodes from the Emulab cluster. 

**1) Instantiate this profile with appropriate parameters**

At the "Parameterize" step, add at least 2 rooftop radios in the CBRS band that are needed for your planned experiment. By default, one database server from the group d430 or d740 will be added. 
Also, speceify the freqeuncy ranges if you are planning to use transmitter(s) in your experiment. Also by default, all the nodes will be setup in one LAN. Try to ping from one node to the other to try if they are in the same LAN.
Once you have these parameters selected, click through the rest of the profile and then click "Finish" to instantiate.  It will take 10 to 15 minutes for the experiment to finish setting up.  Once it is "green", proceed to the next step.

**2) Setting up the experiment**
- once the experiment is ready, the database serever will be indicated on the list view. Other nodes will be indicated as rooftop nodes which the user has to choose. 
- select one of the rooftop nodes to be the primary user (PU)
- select at least one of the other rooftop nodes as the secondary user (SU)
- run the following command on each of the nodes
  ```
  ssh -Y <username>@<radio_hostname>
  ```
  
**3) Cloning StopSec to Each Node**
Run the following command on each node to clone StopSec repository to your nodes. 
  ```
git clone https://github.com/StopSec/StopSec-System.git
  ```
Run the following command on each node to move to the directory that contains the StopSec files.

  ```
cd StopSec-System
  ```

**4) Run Experiments**
1) Install unicorn using the following command. Do that on all of the nodes.
```
sudo apt install uvicorn python3-fastapi
```
2) Run the following command on the Database Server to start the remote database server api
```
uvicorn remote_api:app --host 0.0.0.0 --port 8080
```
3) open two terminals for each of the PU and SU nodes, and run the following command to start the local database api
```
uvicorn local_api:app --host 127.0.0.1 --port 8081
```
4) on the PU run the following command to start StopSec receive operation. 
```
python3 Watermark_RealTime_RX.py -f 3383e6 -r 5e6 -g 30
```
5) on each of the SU nodes start the transmission operation. Note that the start time is in 24 hour format. So, make sure to write when you want to start your experiment. Set the same time in all SUs if you want to see the impact of concurrent interference at the PU. Note also that these are default values. You can change the frequency, sampling rate and gains to see how StopSec performs under various signal-to-noise ratio (SNR), and bandwidth and frequencies. If you choose multiple concurrent SU transmissions, check the latency for the node that is turned off last. You can test for various SU and PU configurations and test the performance of StopSec under these configurations. 
```
python3 Watermark_RealTime_TX.py -f 3383e6 -r 5e6 -g 20 --start_time 6:30
```
6) Record values evaluate the StopSec system. 


