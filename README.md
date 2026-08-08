# UR Personal AI Gym Trainer

> **Real-time computer-vision fitness coaching with exercise-specific form analysis and AI voice feedback.**

**Live Demo:** https://ur-personal-ai-gym-trainer.streamlit.app/

---

## Overview

**UR Personal AI Gym Trainer** is a real-time AI fitness application that uses a webcam stream, MediaPipe Pose Landmarker, OpenCV, and exercise-specific computer-vision logic to analyze workout movements.

Instead of treating every exercise as the same pose-classification problem, the system uses a **modular detector architecture**: each supported exercise has its own movement and form rules. The resulting metrics are passed through a coaching pipeline that can generate corrective feedback and convert it into speech.

The project demonstrates an end-to-end AI application combining:

- Real-time video processing
- Human pose estimation
- Exercise-specific movement analysis
- Repetition and form tracking
- LLM-assisted coaching
- Text-to-speech feedback
- Streamlit-based deployment

---

## Why This Project Stands Out

A typical fitness demo stops at pose detection. This project connects the complete pipeline:

```text
Camera
  ↓
WebRTC video stream
  ↓
OpenCV frame processing
  ↓
MediaPipe Pose Landmarker
  ↓
Exercise-specific detector
  ↓
Movement metrics + repetition state
  ↓
Form-issue detection
  ↓
AI coaching pipeline
  ↓
Text-to-speech
  ↓
Real-time user feedback
```

The architecture is intentionally modular, making it possible to add new exercises without rewriting the complete video-processing pipeline.

---

## Key Features

### Real-Time Pose Analysis

Uses **MediaPipe Pose Landmarker** to extract human body landmarks from incoming video frames.

The pipeline handles:

- Pose detection
- Landmark tracking
- Exercise-specific joint relationships
- Movement state
- Form conditions
- Skeleton visualization

### Exercise-Specific Detection

| Exercise | Example signals |
|---|---|
| Squats | Depth, back angle, repetitions |
| Push-ups | Body alignment, hip position, repetitions |
| Biceps Curls | Torso swing, elbow drift, repetitions |
| Shoulder Press | Extension status, back arch, repetitions |
| Lunges | Balance, repetitions |

### Form Feedback

The system identifies exercise-specific issues instead of providing generic feedback.

Examples include:

- Insufficient squat depth
- Excessive forward lean
- Poor push-up alignment
- Hip sagging or excessive hip elevation
- Excessive torso swing during curls
- Elbow drifting during curls
- Excessive lower-back arch during shoulder press
- Loss of balance during lunges
- No pose detected / user outside the camera frame

### AI Coaching

The coaching pipeline distinguishes between workout events and detected form problems.

Supported events include:

- Workout started
- Set completed
- Workout completed
- No pose detected
- Exercise-specific form issues

A feedback cooldown prevents continuous form warnings from repeatedly triggering voice output.

### Voice Coaching

Generated coaching text is converted into audio using **gTTS**, allowing users to receive feedback without constantly looking at the screen.

---

## Technology Stack

| Layer | Technology |
|---|---|
| Application UI | Streamlit |
| Real-time video | Streamlit-WebRTC |
| Computer Vision | OpenCV |
| Pose Estimation | MediaPipe Pose Landmarker |
| Video Frames | PyAV |
| Numerical Processing | NumPy |
| AI Coaching | Groq |
| Text-to-Speech | gTTS |
| Language | Python |
| Deployment | Streamlit Community Cloud |

---

## Architecture

### High-Level Architecture

```text
                         ┌─────────────────────┐
                         │     User Camera     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │   Streamlit-WebRTC  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │        OpenCV       │
                         │ Frame preprocessing │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ MediaPipe Pose      │
                         │     Landmarker      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Exercise Detector   │
                         └──────────┬──────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    ▼                               ▼
          ┌──────────────────┐             ┌──────────────────┐
          │ Movement Metrics │             │  Form Evaluation │
          │ Reps / Status    │             │  Issue Detection │
          └────────┬─────────┘             └────────┬─────────┘
                   │                                │
                   └──────────────┬─────────────────┘
                                  ▼
                       ┌─────────────────────┐
                       │  Coaching Pipeline  │
                       │    + Groq / LLM     │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │        gTTS         │
                       └──────────┬──────────┘
                                  │
                                  ▼
                           🔊 Voice Feedback
```

### Detector Architecture

```text
services/vision/exercise_video_processor.py
                    │
                    ├── SquatDetector
                    ├── PushUpDetector
                    ├── BicepsCurlDetector
                    ├── ShoulderPressDetector
                    └── LungesDetector
```

Each detector receives pose landmarks and produces exercise-specific metrics. This keeps exercise logic isolated and makes the system easier to maintain and extend.

---

## Project Structure

```text
personal_gym_trainer-ai-/
│
├── main.py
│
├── services/
│   ├── coaching/
│   │   ├── voice_pipeline.py
│   │   └── tts.py
│   │
│   ├── vision/
│   │   └── exercise_video_processor.py
│   │
│   └── config/
│       └── workout_config.py
│
├── detectors/
│   ├── squat.py
│   ├── pushup.py
│   ├── biceps_curl.py
│   ├── shoulder_press.py
│   └── lunges.py
│
├── ml_models/
│   └── pose_landmarker_full.task
│
├── requirements.txt
├── packages.txt
├── .python-version
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.11
- Webcam
- Internet connection for AI/TTS services
- Groq API key for LLM-based coaching

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd personal_gym_trainer-ai-
```

### 2. Create a virtual environment

Windows:

```powershell
python -m venv .venv
.venv\Scripts\activate
```

macOS/Linux:

```bash
python3.11 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create `.env` in the project root:

```env
GROQ_API_KEY=your_groq_api_key
```

Never commit API keys or secrets to Git.

### 5. Run the application

```bash
streamlit run main.py
```

Open the URL shown by Streamlit and allow camera access.

---

## Deployment

The application is configured for Streamlit Community Cloud.

### `.python-version`

```text
3.11
```

