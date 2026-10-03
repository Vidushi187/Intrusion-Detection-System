# Intrusion-Detection-System

**Project Overview**

This project delivers an autonomous, real-time intrusion and anomaly detection framework designed to safeguard sensory networks and critical infrastructure systems against sophisticated cyber threats. Developed as part of advanced graduate research in cloud and datacentre networking at Carleton University, the platform employs a model-free Reinforcement Learning approach using Deep Q-Learning (DQN) to analyze high-velocity network telemetry and classify traffic into normal behavior and four major attack categories: Denial of Service (DoS), Network Probing (Probe), User-to-Root (U2R), and Remote-to-Local (R2L). By integrating an experiential memory replay buffer to eliminate temporal correlation bias and leveraging the Adam optimizer across multi-layer neural network states, the system achieves approximately 98% binary detection accuracy and over 96% multi-class classification precision, providing resilient, low-latency threat visibility without relying on static signature databases.

Tech Stack: Python, TensorFlow, Keras, Scikit-learn, Pandas, NumPy, Matplotlib, Deep Q-Networks (DQN), Adam, SGD, AdaGrad, KDDCup99 / NSL-KDD Datasets, Jupyter Notebook, Git, GitHub 
