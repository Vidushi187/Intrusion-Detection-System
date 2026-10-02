# Business Requirements Document (BRD)

**Project Name:** Intrusion Detection on Sensory Data Using Reinforcement Learning  
**Reference Project:** Carleton University Network Technology Research (ITEC 5103\)

---

## 1\. Document Conventions & Overview

### 1.1 Conformance Terminology

The key words "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", and "MAY" in this document are to be interpreted as described in RFC 2119:

* **SHALL / MUST**: Defines an absolute and mandatory requirement.  
* **SHOULD / RECOMMENDED**: Defines an action that is valid and recommended, but where valid business reasons may exist to deviate under specific conditions.  
* **MAY / OPTIONAL**: Defines a truly discretionary or optional item.

### 1.2 Prioritization Scheme (MoSCoW)

All business requirements are assigned a priority based on the MoSCoW framework:

* **Must Have (M)**: Critical requirements essential for system viability, contractual compliance, and core security functionality.  
* **Should Have (S)**: High-impact requirements necessary for optimal performance and operational efficiency.  
* **Could Have (C)**: Desirable enhancements to be implemented if resources and timeline allow.  
* **Won't Have (W)**: Explicitly deferred features out of scope for the current release.

---

## 2\. Executive Summary

### 2.1 Purpose

This Business Requirements Document (BRD) formalizes the operational, functional, and governance requirements for an intelligent, autonomous Intrusion Detection System (IDS) tailored for large-scale sensory networks and critical infrastructure. The platform leverages model-free Reinforcement Learning (Q-learning) to dynamically evaluate streaming telemetry, detect anomalous network behavior, classify sophisticated attack patterns, and adapt to evolving threats without relying solely on static signature databases.

### 2.2 Background & Operational Context

Critical infrastructure systems—such as municipal water supplies, power grids, healthcare networks, and industrial manufacturing facilities—increasingly depend on Wireless Sensor Networks (WSNs) and connected Internet-of-Things (IoT) field devices. While these sensory deployments offer granular monitoring and operational telemetry, they introduce severe cyber-physical attack vectors. Traditional intrusion detection architectures fall into two primary categories:

1. **Host-Based Intrusion Detection Systems (HIDS)**: Deployed on individual server nodes or controllers, providing deep internal visibility but suffering from vulnerability to host infection and high compute overhead.  
2. **Network-Based Intrusion Detection Systems (NIDS)**: Deployed at perimeter routers or traffic aggregation switches, protecting endpoints remotely but struggling under massive sensory traffic volume, packet velocity, and high rates of false alarms.

Conventional signature-based systems only detect known malicious patterns and completely fail against zero-day intrusions or polymorphic attacks. Conversely, conventional statistical anomaly detection systems frequently trigger false-positive alert floods, overwhelming Security Operations Center (SOC) analysts and disrupting mission-critical services.

### 2.3 Problem Statement

Modern critical infrastructure networks generate heterogeneous sensory traffic characterized by high volume, varying velocity, and non-stationary distribution. Existing security monitoring infrastructure suffers from four critical deficiencies:

1. **Inability to Learn Dynamically**: Static rules fail against emerging threat vectors and novel intrusion techniques.  
2. **Severe Class Imbalance**: Legitimate normal traffic outnumbers malicious packets by orders of magnitude, causing standard classifiers to bias toward the majority class.  
3. **Temporal Correlation Bias**: Consecutive network observations exhibit tight temporal dependencies, causing learning algorithms to overfit transient bursts.  
4. **Lack of Actionable Granularity**: Binary alert systems (flagging only "anomaly" vs. "normal") fail to inform triage teams whether an event is a high-volume Denial of Service (DoS), an administrative privilege escalation (User-to-Root), or a reconnaissance probe.

An adaptive, reinforcement-learning-driven analytics engine is required to observe sensory network telemetry, estimate threat states, predict high-confidence threat classifications, and maintain high detection accuracy with minimized false alarm rates.

flowchart LR

    A\[Sensory Network Telemetry\\nTraffic Logs & Sensor Bursts\] \--\> B\[Data Ingestion & Normalization\\nOne-Hot Encoding & Range Scaling\]

    B \--\> C\[Reinforcement Learning Engine\\nQ-Learning & Experience Replay\]

    C \--\> D\[Threat Classification Module\]

    D \--\> E\[Normal Traffic\\nAuto-Approved\]

    D \--\> F\[Classified Attack Alert\\nDoS, Probe, U2R, R2L\]

    F \--\> G\[Incident Response & SOC Dispatch\]

---

## 3\. Business Goals & Success Metrics (KPIs)

### 3.1 Business Objectives

* **BG-1 (High-Fidelity Anomaly Detection)**: Protect critical infrastructure networks from unauthorized intrusion by achieving robust anomaly identification on sensory traffic streams.  
* **BG-2 (Multi-Tier Threat Taxonomy)**: Automatically categorize detected malicious events into specific threat families (DoS, Probe, U2R, R2L) to accelerate targeted operational incident response.  
* **BG-3 (False Positive Mitigation)**: Minimize false alarms to prevent SOC operator desensitization and reduce manual triage expenditures.  
* **BG-4 (Learning Stability & Generalization)**: Ensure the underlying decision engine stabilizes across iterative learning episodes without catastrophic forgetting or temporal correlation bias.

### 3.2 Key Performance Indicators (KPIs)

The success of the platform SHALL be measured against the following empirical benchmarks established during laboratory and simulation testing:

| KPI ID | Metric Name | Definition & Formula | Minimum Target | Target Achieved in Benchmark |
| :---- | :---- | :---- | :---- | :---- |
| **KPI-01** | Binary Detection Accuracy | \$\\frac{\\text{True Positives} \+ \\text{True Negatives}}{\\text{Total Samples}}\$ | \$\\ge 95.0%\$ | **\$97.99%\$** (Adam Optimizer) |
| **KPI-02** | Binary Detection Recall | \$\\frac{\\text{True Positives}}{\\text{True Positives} \+ \\text{False Negatives}}\$ | \$\\ge 98.0%\$ | **\$99.17%\$** |
| **KPI-03** | Binary Detection Precision | \$\\frac{\\text{True Positives}}{\\text{True Positives} \+ \\text{False Positives}}\$ | \$\\ge 96.0%\$ | **\$97.88%\$** |
| **KPI-04** | Balanced F1-Score | \$\\frac{2 \\times \\text{Precision} \\times \\text{Recall}}{\\text{Precision} \+ \\text{Recall}}\$ | \$\\ge 0.95\$ | **\$0.9824\$** |
| **KPI-05** | Multi-Class Threat Accuracy | Correct classification across Normal, DoS, Probe, U2R, and R2L | \$\\ge 90.0%\$ | **\$92.54%\$** (Baseline SGD) / **\$96.59%\$** (Adam) |
| **KPI-06** | Experience Replay Stability | Metric consistency across batch training episodes using buffer sampling | Variance \$\< 3.0%\$ | **\$92.81%\$** consistent accuracy at buffer size 300 |
| **KPI-07** | Pipeline Processing Cadence | Batch transformation and state evaluation time per standard batch window | \$\\le 100\\text{ ms}\$ / batch | Within standard telemetry ingest limits |

---

## 4\. Project Scope

### 4.1 In-Scope Capabilities

1. **Sensory Network Data Ingestion**:  
   * Continuous batch-based reading of structured network telemetry records.  
   * Transformation of multi-modal features including connection duration, protocol identifiers (TCP, UDP, ICMP), service types (HTTP, FTP, Telnet, Private, etc.), and TCP status flags (SF, REJ, S0, etc.).  
   * Extraction of host-based traffic volume metrics, service error rates, and host-variance counters.  
2. **Automated Feature Engineering & Encoding**:  
   * Categorical feature transformation via one-hot encoding without manual intervention.  
   * Numerical min-max feature scaling normalized to bounded continuous intervals \$\[0, 1\]\$.  
   * Multi-label threat classification mapping from raw granular attack strings to four standardized operational classes (DoS, Probe, U2R, R2L).  
3. **Reinforcement Learning Decision Pipeline**:  
   * Dynamic environment simulation where telemetry batches represent sequential environment states.  
   * Action selection balancing exploratory network evaluation and exploitation of known high-reward threat signatures.  
   * State-action value function updates driven by immediate classification verification rewards.  
   * Experiential replay buffering to decouple consecutive network states and prevent recency bias.  
4. **Multi-Model Optimization & Benchmarking**:  
   * Comparative evaluation against classical gradient optimizers (Stochastic Gradient Descent) and adaptive gradient moment estimation algorithms (Adam, AdaGrad).  
   * Automated metric calculation for Accuracy, Precision, Recall, and F1-score across all test splits.

### 4.2 Out-of-Scope Capabilities

1. **Kernel-Level Packet Interception**: Direct raw packet sniffing, deep packet inspection at wire speeds (100 Gbps), and custom NIC driver engineering are excluded. The system relies on aggregated sensor network logs and standard flow telemetry feeds.  
2. **Automated Destructive Countermeasures**: The system SHALL NOT execute automated, uncontrolled packet dropping, router interface shutdowns, or border gateway protocol (BGP) blackholing. It functions as an intelligent detection, classification, and advisory platform.  
3. **Physical Hardware Firmware Modification**: Embedded programming of individual sensory microcontrollers (e.g., sensor battery duty cycle management) is out of scope.  
4. **Supervised Label Generation**: Manual labeling of live zero-day packets during real-time streaming is excluded; the system must infer classification states based on reinforcement policies.

---

## 5\. Stakeholder Analysis & User Personas

graph TD

    A\[Project Stakeholders\]

    A \--\> B\[Security Operations Center Analyst\]

    A \--\> C\[Critical Infrastructure Operator\]

    A \--\> D\[Network Security Architect\]

    A \--\> E\[Machine Learning Engineer\]

    A \--\> F\[Compliance & Audit Officer\]

### 5.1 Stakeholder Matrix

| Stakeholder Role | Influence | Impact | Primary Interest / Business Need |
| :---- | :---- | :---- | :---- |
| **SOC Analysts** | High | High | Needs high-confidence, actionable threat alerts categorized by attack type rather than raw boolean flags to avoid alert fatigue. |
| **Infrastructure Operators** | High | High | Requires zero disruption to critical system availability, low false-positive operational stops, and high reliability. |
| **Network Security Architects** | High | Medium | Demands secure integration with existing sensory telemetry collectors, compliance with network segmentation, and low compute footprint. |
| **ML & Data Engineers** | Medium | High | Requires reproducible data normalization pipelines, clear state-action definitions, and modular optimizer architectures. |
| **Compliance & Audit Officers** | Medium | Low | Needs auditable threat logs, standard metric reporting, and compliance with operational cybersecurity frameworks (e.g., NIST, NERC CIP). |

### 5.2 User Personas

#### Persona 1: Sarah, Senior Incident Response Lead (SOC Analyst)

* **Demographics**: 8+ years experience monitoring industrial control networks and sensor telemetry.  
* **Pain Points**: Wades through thousands of false alarms per shift caused by benign telemetry jitter; struggles to isolate advanced persistent threats (APTs) hidden within high-volume noise.  
* **Needs**: High precision and categorized alerts distinguishing volumetric floods (DoS) from quiet reconnaissance (Probes) and privilege escalation attempts (U2R).

#### Persona 2: Marcus, Critical Infrastructure Operations Manager

* **Demographics**: 15+ years managing SCADA, smart grid telecommunications, and sensory actuators.  
* **Pain Points**: Security solutions that introduce unacceptable latency or falsely trigger automated failsafes that bring down critical power/water distributions.  
* **Needs**: Reliable system uptime, high recall ensuring zero undetected breaches, and predictable execution behavior.

---

## 6\. Business Requirements & Functional Scope

### 6.1 Data Ingestion & Preprocessing Requirements (BR-1.0)

| Requirement ID | Priority | Requirement Title | Requirement Description | Acceptance Criteria |
| :---- | :---- | :---- | :---- | :---- |
| **BR-1.1** | **Must Have** | Ingestion of Telemetry Records | The system SHALL ingest structured network connection records comprising network duration, protocol type, service destination, connection flags, payload byte counters, and host-level error metrics. | Ingest pipeline successfully parses 41 foundational network telemetry attributes from standard tabular streams without data loss. |
| **BR-1.2** | **Must Have** | Categorical Encoding | The system SHALL convert categorical telemetry features (protocol types, service protocols, connection flags) into binary one-hot encoded representations. | Categorical attributes are mapped into discrete binary flags; all output columns contain strictly 0 or 1 values. |
| **BR-1.3** | **Must Have** | Min-Max Normalization | The system SHALL scale all continuous numerical attributes to a uniform bounded range of \$\[0.0, 1.0\]\$ based on feature-specific boundary limits. | All continuous numerical features fall strictly within the interval \$\[0.0, 1.0\]\$; zero-variance columns default safely to 0 without division-by-zero errors. |
| **BR-1.4** | **Must Have** | Data Batch Segmentation | The system SHALL segment processed telemetry into configurable batches (defaulting to 100 records per evaluation window) to simulate sequential environment steps. | Data is sequentially processed in batches of 100; batch boundaries maintain record integrity and feature alignment. |
| **BR-1.5** | **Should Have** | Automated Data Cleansing | The system SHOULD detect and handle malformed fields, out-of-range sensor readings, or invalid flag states prior to state generation. | Malformed rows are isolated into an exception log and do not abort the active batch evaluation cycle. |

### 6.2 Threat Taxonomy & Attack Mapping Requirements (BR-2.0)

| Requirement ID | Priority | Requirement Title | Requirement Description | Acceptance Criteria |
| :---- | :---- | :---- | :---- | :---- |
| **BR-2.1** | **Must Have** | Binary Anomaly Classification | The system SHALL evaluate input telemetry records and assign a binary classification indicating whether the event represents Normal Traffic (Class 0\) or Malicious Traffic (Class 1). | System outputs binary classification decisions for all records with an overall accuracy of \$\\ge 95.0%\$. |
| **BR-2.2** | **Must Have** | Multi-Class Threat Categorization | The system SHALL map granular attack signatures into four major functional threat categories: Denial of Service (DoS), Network Probing (Probe), User-to-Root (U2R), and Remote-to-Local (R2L). | Granular threat labels are correctly classified into the 5 primary system actions (Normal \+ 4 Attack Families) with classification accuracy \$\\ge 90.0%\$. |
| **BR-2.3** | **Must Have** | DoS Attack Family Recognition | The system SHALL classify resource-exhaustion and flooding attacks (including Back, Land, Neptune, Pod, Smurf, Teardrop, Apache2, ProcessTable, Mailbomb, UDPStorm) under the DoS threat family. | Injected DoS attack patterns trigger the DoS classification action; validation accuracy meets or exceeds \$92.0%\$. |
| **BR-2.4** | **Must Have** | Probe Attack Family Recognition | The system SHALL classify surveillance and network reconnaissance attacks (including IPsweep, Nmap, Portsweep, Satan, Mscan, Saint) under the Probe threat family. | Port scanning and host sweep telemetry is categorized as Probe with a recall rate of \$\\ge 90.0%\$. |
| **BR-2.5** | **Should Have** | Privilege Escalation (U2R) Recognition | The system SHOULD identify unauthorized local superuser access attempts (including Buffer Overflow, LoadModule, Perl, Rootkit, SqlAttack, Xterm) under the U2R threat family. | Telemetry records exhibiting root access attempts and unauthorized system privilege requests are flagged as U2R. |
| **BR-2.6** | **Should Have** | Remote Intrusion (R2L) Recognition | The system SHOULD identify unauthorized remote access attempts (including Guess Password, FTP Write, Imap, Multihop, Phf, Spy, WarezClient, WarezMaster, Sendmail, Worm) under the R2L threat family. | Remote brute force and exploit attempts are categorized as R2L. |

classDiagram

    class ThreatTaxonomy {

        \<\<enumeration\>\>

        NORMAL

        DOS

        PROBE

        U2R

        R2L

    }

    class DoSAttacks {

        Neptune

        Smurf

        Back

        Teardrop

        Pod

        Land

        Apache2

        Mailbomb

        ProcessTable

        UDPStorm

    }

    class ProbeAttacks {

        Portsweep

        IPsweep

        Nmap

        Satan

        Mscan

        Saint

    }

    class U2RAttacks {

        Buffer\_Overflow

        LoadModule

        Perl

        Rootkit

        SqlAttack

        Xterm

    }

    class R2LAttacks {

        Guess\_Password

        FTP\_Write

        Imap

        Multihop

        Phf

        Spy

        WarezClient

        WarezMaster

        Sendmail

        Worm

    }

    ThreatTaxonomy \<|-- DoSAttacks : maps to

    ThreatTaxonomy \<|-- ProbeAttacks : maps to

    ThreatTaxonomy \<|-- U2RAttacks : maps to

    ThreatTaxonomy \<|-- R2LAttacks : maps to

### 6.3 Reinforcement Learning Decision Engine Requirements (BR-3.0)

| Requirement ID | Priority | Requirement Title | Requirement Description | Acceptance Criteria |
| :---- | :---- | :---- | :---- | :---- |
| **BR-3.1** | **Must Have** | State Representation | The system SHALL represent environment states using normalized sensory feature vectors derived from each connection batch window. | Each state vector encapsulates the normalized telemetry features corresponding to the active batch window. |
| **BR-3.2** | **Must Have** | Action Space Definition | The system SHALL support an action space of size 2 for binary anomaly detection (Normal vs. Attack) and size 5 for multi-class threat categorization (Normal, DoS, Probe, U2R, R2L). | Action space is dynamically initialized to either 2 or 5 discrete action channels depending on the operational profile. |
| **BR-3.3** | **Must Have** | Feedback Reward Attribution | The system SHALL allocate a positive feedback reward (+1.0) when the selected classification action matches the verified environmental label, and zero reward (0.0) when misclassified. | Reward calculation occurs synchronously upon action execution; cumulative rewards are recorded per training episode. |
| **BR-3.4** | **Must Have** | Q-Value State-Action Updates | The system SHALL update state-action quality values using the Bellman optimality principle incorporating current rewards, a discount factor (\$\\gamma\$), and the maximum expected future Q-value. | Q-matrix updates follow the Bellman formulation; convergence demonstrates progressive maximization of cumulative reward. |
| **BR-3.5** | **Must Have** | Exploration vs. Exploitation Policy | The system SHALL implement an \$\\epsilon\$-greedy exploration policy that initializes with high exploration (\$\\epsilon=1.0\$) and decays exponentially over training iterations to favor exploitation. | Exploration rate decays monotonically according to the schedule \$\\epsilon\_t \= \\epsilon\_0 \\times \\text{decay}^{\\text{epoch} \\times \\text{iteration}}\$, transitioning from stochastic selection to greedy policy. |
| **BR-3.6** | **Must Have** | Experience Replay Memory Buffer | The system SHALL maintain an experiential memory buffer storing past transitions (state, action, reward, next state) and draw random mini-batch samples during optimization. | Memory buffer stores up to 300 historical transitions; random uniform sampling breaks temporal correlation and yields metric variance under \$3.0%\$. |
| **BR-3.7** | **Should Have** | Adaptive Optimizer Support | The system SHOULD utilize adaptive moment estimation (Adam) as the primary learning optimizer to adjust individual learning rates across sparse sensory features. | Adam optimizer achieves \$\\ge 96.0%\$ multi-class accuracy, outperforming classical Stochastic Gradient Descent (\$88\\text{--}92%\$). |

stateDiagram-v2

    \[\*\] \--\> Ingestion: New Telemetry Batch

    Ingestion \--\> StateExtraction: Normalize & Format

    StateExtraction \--\> PolicyEvaluation: Observe State (s)

    

    state PolicyEvaluation {

        \[\*\] \--\> CheckEpsilon

        CheckEpsilon \--\> RandomExploration: Random Number \< Epsilon

        CheckEpsilon \--\> GreedyExploitation: Random Number \>= Epsilon

        RandomExploration \--\> ActionSelected: Select Random Action (a)

        GreedyExploitation \--\> ActionSelected: Select ArgMax Q(s, a)

    }

    PolicyEvaluation \--\> RewardComputation: Apply Action (a)

    RewardComputation \--\> BufferStorage: Store (s, a, r, s') in Replay Buffer

    BufferStorage \--\> ModelUpdate: Sample Mini-Batch & Update Weights

    ModelUpdate \--\> Ingestion: Advance to Next State (s')

### 6.4 Reporting, Audit & Operational Alerting Requirements (BR-4.0)

| Requirement ID | Priority | Requirement Title | Requirement Description | Acceptance Criteria |
| :---- | :---- | :---- | :---- | :---- |
| **BR-4.1** | **Must Have** | Performance Metric Generation | The system SHALL automatically compute and report Accuracy, Precision, Recall, and F1-Score at the completion of each evaluation cycle. | Comprehensive evaluation summary table is generated with values rounded to 4 decimal places. |
| **BR-4.2** | **Must Have** | Threat Classification Alerts | The system SHALL generate real-time operational alert payloads upon detecting non-normal traffic, specifying the attack category and confidence metric. | Output alerts include Timestamp, Source/Destination telemetry summary, Threat Category, and Q-Confidence Value. |
| **BR-4.3** | **Should Have** | Comparative Baseline Reporting | The system SHOULD maintain and display benchmark comparisons against baseline classifiers (e.g., standard SGD vs. Adam, with vs. without Replay Memory). | Comparative performance deltas are logged in tabular formats for architecture review. |
| **BR-4.4** | **Could Have** | Anomaly Visualization Dashboard | The system MAY export classification distributions, confusion matrices, and ROC curves to standardized analytical spreadsheets and visual reports. | Metrics and tabular distributions are exportable to standard spreadsheet and graphic formats. |

---

## 7\. Business Rules & Operational Policies

### Rule 1: Dynamic Anomaly Triage Policy

* **BRULE-01 (Severity Level Allocation)**:  
  * When a traffic anomaly is classified as **U2R (User-to-Root)**: The event SHALL be marked as **Critical Severity**, triggering immediate incident logging and administrative notification.  
  * When classified as **DoS (Denial of Service)**: The event SHALL be marked as **High Severity**, initiating rate-limiting alerts to network engineers.  
  * When classified as **Probe**: The event SHALL be marked as **Medium Severity**, logging source IP reconnaissance activity for correlation.  
  * When classified as **R2L (Remote-to-Local)**: The event SHALL be marked as **High Severity**, flagging potential unauthorized access vectors.

### Rule 2: Experience Buffer Memory Scaling

* **BRULE-02 (Memory Replay Thresholds)**:  
  * The experiential memory buffer SHALL maintain a minimum capacity of 100 historical transitions before initiating replay training.  
  * For production-grade sensory environments, the buffer size SHALL be maintained at 300 transitions to ensure statistical independence while bounding RAM utilization.

### Rule 3: Training Epoch Termination

* **BRULE-03 (Convergence & Overfitting Prevention)**:  
  * The model SHALL execute up to 600 training episodes under standard training runs.  
  * Training runs exceeding 600 episodes SHALL trigger automated early-stopping checks if accuracy metrics fail to improve by at least \$0.1%\$ across 50 consecutive episodes, preventing over-specialization to transient sensor noise.

---

## 8\. Assumptions, Constraints & Dependencies

### 8.1 Assumptions

1. **Telemetry Integrity**: Incoming network connection records and sensor data logs are assumed to be delivered uncorrupted from the data aggregation layer.  
2. **Benchmark Representation**: The benchmark dataset (KDDCup99 / NSL-KDD) provides an academically validated baseline representing real-world industrial and critical infrastructure intrusion patterns.  
3. **Hardware Availability**: Standard commodity server hardware (multi-core CPU, minimum 8GB RAM) is sufficient for batch training and inference cycles without requiring specialized FPGA acceleration.

### 8.2 Constraints

1. **Computational Training Latency**: Complete retraining cycles over large parameter spaces require approximately 20 to 25 minutes per 600-episode run on standard compute environments. Training operations SHALL be scheduled during off-peak windows or on dedicated offline nodes.  
2. **Batch Window Constraint**: State observation is constrained by the 100-connection batch window. Intrusion attempts spanning fewer than 5 packets within a massive normal burst must rely on statistical anomaly weighting to register.  
3. **No Direct Code Blocks in Specifications**: All downstream functional specifications SHALL express business logic, data structures, and state transitions using plain technical descriptions and Mermaid diagrams without raw code samples.

### 8.3 Dependencies

1. **Data Preprocessing Dependencies**: Availability of tabular manipulation libraries (e.g., Pandas, NumPy) capable of executing vectorized min-max scaling and one-hot encoding.  
2. **Neural Network Runtime**: Availability of a deep learning execution framework (e.g., Keras, TensorFlow) capable of compiling fully connected layers with ReLU activation and Adam optimization.  
3. **Telemetry Ingest Feeds**: Upstream syslog, flow-collector, or sensor gateway infrastructure providing continuous tabular feeds.

---

## 9\. System Context & End-to-End Workflow

sequenceDiagram

    autonumber

    actor Sensor as Sensor Nodes / Network Switch

    participant Ingest as Data Ingestion & Preprocessing

    participant Env as RL Environment (State Matrix)

    participant Engine as Q-Learning Neural Network

    participant Replay as Experience Replay Buffer

    actor SOC as SOC Security Analyst

    Sensor-\>\>Ingest: Stream Network Connection Telemetry

    Ingest-\>\>Ingest: Clean, One-Hot Encode & Min-Max Normalize

    Ingest-\>\>Env: Deliver Batch of 100 Connection States

    Env-\>\>Engine: Transmit Current State Vector (s)

    Engine-\>\>Engine: Compute Q(s, a) via Policy Network

    Engine-\>\>Env: Execute Action a (Classify Normal / Threat)

    Env-\>\>Env: Verify Action Against Environmental Truth

    Env-\>\>Replay: Store Experience (s, a, r, s')

    Replay-\>\>Engine: Sample Mini-Batch (Size 300\) for Weight Update

    Engine-\>\>Engine: Update Loss via Adam Optimizer

    alt Malicious Threat Detected (DoS, Probe, U2R, R2L)

        Engine-\>\>SOC: Dispatch Real-Time Categorized Threat Alert

    else Normal Traffic Verified

        Engine-\>\>Engine: Log Silent Verification

    end

---

## 10\. Glossary of Terms

* **ASCH-IDS (Adaptively Supervised and Clustered Hybrid IDS)**: A benchmark hybrid intrusion detection methodology combining misuse detection and anomaly detection modules.  
* **Denial of Service (DoS)**: A category of cyber-attack seeking to exhaust server memory, processing power, or network bandwidth to render services unavailable to legitimate users.  
* **Experience Replay Memory**: A technique in reinforcement learning where past transitions are stored in a rolling buffer and randomly sampled during neural network training to break temporal autocorrelation.  
* **F1-Score**: The harmonic mean of Precision and Recall, measuring the overall balance between false positives and false negatives.  
* **Intrusion Detection System (IDS)**: A software or hardware appliance that monitors network traffic or system events for policy violations and unauthorized activity.  
* **Min-Max Normalization**: A data transformation method scaling numeric values into a standardized continuous interval \$\[0, 1\]\$.  
* **MoSCoW**: A requirement prioritization framework classifying items as Must Have, Should Have, Could Have, or Won't Have.  
* **NSL-KDD / KDDCup99**: Standardized, academically recognized benchmark datasets representing comprehensive normal and malicious network connection records.  
* **Probe (Probing / Reconnaissance)**: An attack category where an adversary scans network ports, host vulnerabilities, and topology to map out defense perimeters.  
* **Q-Learning**: A model-free reinforcement learning algorithm seeking to determine the optimal policy by learning state-action value functions (Q-values).  
* **Remote-to-Local (R2L)**: An attack category where an unauthorized remote attacker exploits network vulnerabilities to gain local user access.  
* **User-to-Root (U2R)**: An attack category where a local standard user executes buffer overflows or system exploits to escalate privileges to superuser/root access.  
* **Wireless Sensor Network (WSN)**: A spatially distributed network of autonomous sensor devices monitoring physical or environmental conditions.