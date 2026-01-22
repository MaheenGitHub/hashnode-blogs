---
title: "Human Activity Recognition with Machine Learning"
seoTitle: "Human Activity Recognition with Machine Learning: From Sensor Data to "
seoDescription: "A hands-on journey into Human Activity Recognition using accelerometer and gyroscope data. This project explores classical ML, DNNs, and LSTM models, achiev"
datePublished: Thu Jan 22 2026 23:47:06 GMT+0000 (Coordinated Universal Time)
cuid: cmkq3pxmm000102i8di5b0xjt
slug: human-activity-recognition-with-machine-learning
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/BBqVpTE4vw4/upload/dc027f914fe02a5d143102e35247f554.jpeg
tags: data-science, machine-learning, deep-learning, time-series, lstm, ai-projects, human-activity-recognition, case-study-ai

---

## **The Motivation: Why Motion Intelligence?**

Every day, our smartphones capture a silent symphony of data through accelerometers and gyroscopes. This data is the key to unlocking innovations in **elderly healthcare (fall detection)**, **industrial ergonomics**, and **personalized fitness**. In this project, I transformed raw, noisy sensor data into a high-precision activity classifier. This journey covers the transition from handcrafted features to automated temporal learning, providing a robust foundation for my upcoming framework, **KineticSense AI**

### **🔑 Key Takeaways**

Key engineering and modeling insights gained from this end-to-end HAR project.

* **Classical ML Wins on Efficiency:** SVM achieved the highest accuracy (**96%**) using handcrafted features.
    
* **Temporal Intelligence:** LSTMs solved the **Vanishing Gradient** problem, making them superior for raw signal streaming.
    
* **Edge Optimized:** Achieved a sub-**0.5ms inference latency**, proving real-world readiness for wearables.
    
* **The GRU Advantage:** GRUs offered the best accuracy-to-latency tradeoff for low-power deployment.
    

## How This Project Evolved (Semester Deliverables)

* **Deliverable 1:** Data understanding, preprocessing, and exploratory analysis
    
* **Deliverable 2:** Classical ML baselines with engineered features
    
* **Deliverable 3:** Deep Neural Networks and regularization strategies
    
* **Deliverable 4:** Sequential modeling with RNN, GRU, and LSTM
    

## **1\. The Architecture: Building the Pipeline**

Before diving into code, I designed a multi-tier pipeline to process `10,299` samples from the **UCI HAR Dataset**. The goal was to compare three distinct AI philosophies: **Statistical Learning**, **Deep Representation**, and **Sequential Memory**.

### **The Engineering Foundation:**

* **Signal Synchronization:** Generated synthetic **50Hz** timestamps to maintain chronological integrity.
    
* **Normalization:** Applied `StandardScaler` to ensure that gravity and angular velocity measurements were on the same scale, preventing model bias.
    
* **Windowing:** Structured data into **<mark>128-step sequences</mark>** (2.56 seconds) with a 50% overlap. This ensures that no motion event is "cut in half" between windows.
    

### **Dataset Insight: Ensuring Class Balance**

Before feeding data into the models, I performed a distribution analysis to ensure the dataset was balanced. As shown below, the samples are well-distributed across all 6 activities and 30 subjects, which is critical to avoid model bias toward any specific movement.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769121257694/d597220e-92a8-445b-92b9-85b8fdd780f3.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769121330697/959e5810-b4de-4c64-bfad-bedc4347cce4.png align="center")

> *Figure 1: Distribution of activity samples and subject contributions, confirming a balanced dataset for training.*

## **2\. The Baseline: High-Precision Classical ML**

I first explored the power of **Domain Knowledge**. Using 561 handcrafted features (Statistical, Signal Energy, FFT, Entropy, etc.), I tested a "Model Zoo" including Random Forest, XGBoost, and SVM.

### **The Breakthrough:**

The **Support Vector Machine (SVM)** emerged as the champion, hitting a staggering **<mark>96.00% accuracy</mark>**.

* **The Insight:** When features are expertly engineered, classical models often outperform deep learning on static data. They are lightweight and ideal for low-power embedded systems.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769121734771/264e0413-3a53-4eb8-96c9-b6a410167eff.png align="center")

> Figure 2: *The SVM Confusion Matrix. Notice the near-perfect diagonal density, with the only slight confusion occurring between the orientationally similar 'Sitting' and 'Standing'.*

## **3\. The Deep Learning Leap: From Features to Representations**

Next, I moved toward **Representation Learning**. I built a Fully Connected DNN with a **256 → 128 → 64** architecture to see if the network could automatically identify patterns within the 561 features without human intervention.

### **The Strategy:**

To ensure the model didn't just "memorize" the training subjects, I implemented:

* **30% Dropout:** To force the network to learn redundant representations.
    
* **EarlyStopping:** To halt training the moment validation loss stopped improving.
    
* **Result:** Achieved **<mark>92.94% accuracy</mark>** with excellent generalization.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769121821059/e6b43c6e-389b-4539-940f-a2b5caf3bd15.png align="center")

> Figure 3: *Training curves showing smooth convergence. The minimal gap between training and validation loss confirms a well-regularized model.*

## **4\. The Sequence Masterclass: Solving for Time**

The final phase was the most challenging: **Sequential Deep Learning**. Instead of using the 561 features, I fed **raw 3D sensor streams** directly into the networks.

### **The Challenge: Vanishing Gradients**

Simple RNNs struggled (85.65%) because they "forgot" the beginning of a motion sequence. To solve this, I utilized **LSTMs (Long Short-Term Memory)**. By using "Gates" to protect information flow, LSTMs create a "gradient highway" that preserves data from the start of the 2.56-second window.

### **Performance Comparison:**

| **Architecture** | **Accuracy (%)** | **F1-Score** | **Latency (ms)** |
| --- | --- | --- | --- |
| **Simple RNN** | 85.65% | 0.855 | 0.76 |
| **GRU** | 90.46% | 0.906 | **0.46** |
| **LSTM** | **91.25%** | **0.914** | 0.47 |

While the Simple RNN struggles with vanishing gradients in complex movements, the LSTM and GRU architectures show superior precision, particularly in distinguishing between 'Walking Upstairs' and 'Walking Downstairs' due to their gated memory mechanisms

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1769122547957/348880eb-fc1e-40db-8ef5-905d6da691be.png align="center")

> *Figure 4: Comparative Confusion Matrices for RNN, GRU, and LSTM.*

## What Didn’t Work (And Why) ⚠️

* Simple RNNs struggled due to vanishing gradients in long sequences
    
* Treating standing and sitting as purely dynamic activities caused misclassification
    
* Early experiments without proper scaling led to unstable convergence
    

## **The Hard-Won Lessons: Developer Insights**

Building this wasn't just about calling `.fit()`. Here are the real-world insights I gained:

* **The orientation paradox:** Distinguishing sitting from standing requires isolating the **Gravity Component** of the accelerometer.
    
* **Efficiency vs. Power:** While the LSTM is the most accurate, the **GRU** is the "Smart Choice" for edge devices because it offers nearly identical performance with lower computational cost.
    
* **Data is King:** No amount of hyperparameter tuning can fix a model if the initial signal windowing is poorly synchronized.
    

## **Final Verdict & Future Vision**

This project demonstrates how Motion Intelligence can move beyond research prototypes toward real-world, deployable systems. With an inference latency of **<mark>0.47ms</mark>**, these models are ready for live deployment.

This foundation is now being transitioned into **KineticSense AI**, where I will be focusing on:

1. **TFLite Conversion:** Optimizing models for mobile and IoT.
    
2. **Real-time Dashboards:** Building a live visualization bridge using Streamlit.
    

> [Github Repo](https://github.com/MaheenGitHub/human-activity-recognition-ML)

## **Open for Collaboration** 🤝

I’m looking to **expand this work beyond the classroom** and transition it into practical applications. If you’re interested in **AI, IoT, or time-series modeling**, here are some ways we could collaborate:

* **Edge Deployment:** Converting models for mobile and wearable devices (TFLite, CoreML)
    
* **Real-Time Dashboards:** Building visual analytics interfaces with Streamlit or Flutter
    
* **Data Expansion:** Collecting or integrating additional real-world sensor datasets
    

Even if the repository is currently private, I welcome **collaboration or mentorship opportunities**.

## **👩‍💻 About Me**

I’m Maheen Fatima, a BSIT student at PUCIT with experience as a Software Engineer Intern at Spacebar and a Teaching Assistant at PUCIT. I’m passionate about Machine Learning, AI, and turning real-world data into practical solutions. I document my learning journey through technical writing and projects, and I’m open to freelance opportunities and collaborations in AI, ML, and IoT.

🔗 **Connect with me on LinkedIn:** [**https://www.linkedin.com/in/maheenfatimaa/**](https://www.linkedin.com/in/maheenfatimaa/)

💻 **GitHub:** [**https://github.com/MaheenGitHub**](https://github.com/MaheenGitHub)

🌐 **Upwork:** [**https://www.upwork.com/freelancers/~017a150168182cf524?mp\_source=share**](https://www.upwork.com/freelancers/~017a150168182cf524?mp_source=share)

*<mark>If you're working on similar Motion Intelligence projects or need an end-to-end HAR pipeline for your product, let's connect!</mark>*