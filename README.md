# Sprint Start Analysis

AI-powered biomechanics analysis system for sprint start movements using MediaPipe pose detection.

## Features

- 🎥 Video-based pose detection using MediaPipe
- 📊 Comprehensive biomechanics metrics (reaction time, joint angles, velocity, etc.)
- 🖼️ Annotated video overlay with skeleton and event markers
- 📈 Interactive visualization dashboard
- 🔧 Calibration support for real-world measurements
- 🚀 FastAPI worker for async processing
- 💻 CLI tool for batch processing
- ✅ Full test suite

## Installation

### Requirements

- Python 3.10 or higher
- ffmpeg (for video processing)

### Setup

```bash
# Clone repository
git clone <repository-url>
cd sprint_start_analysis

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

## Quick Start

### 1. Streamlit Dashboard (Recommended)

Launch the interactive web dashboard:

```bash
python run_app.py --mode streamlit
```

Then open your browser to `http://localhost:8501`

**Usage:**
1. Upload a sprint start video (MP4, AVI, MOV)
2. Configure analysis parameters (FPS, athlete height, thresholds)
3. Click "Run Analysis"
4. View results, metrics, and download outputs

### 2. Command Line Interface

Process videos from the command line:

```bash
python cli/run_analysis.py \
    --video input_video.mp4 \
    --fps 120 \
    --athlete_height 1.75 \
    --out_dir results/ \
    --save_landmarks
```

**Arguments:**
- `--video`: Input video path (required)
- `--fps`: Video frame rate (auto-detect if not provided)
- `--detector`: Pose detector ('mediapipe')
- `--out_dir`: Output directory
- `--athlete_height`: Athlete height in meters (for calibration)
- `--velocity_threshold`: Movement detection threshold
- `--save_landmarks`: Save raw landmarks JSON
- `--no_overlay`: Skip overlay video creation

### 3. FastAPI Worker

Launch the API server for programmatic access:

```bash
python run_app.py --mode api
```

API will be available at `http://localhost:8000` with docs at `http://localhost:8000/docs`

**Endpoints:**
- `POST /process` - Submit video for processing
- `GET /status/{job_id}` - Check processing status
- `GET /result/{job_id}` - Get analysis results
- `GET /download/{job_id}/overlay` - Download overlay video

## Project Structure

```
sprint_start_analysis/
├── app_streamlit.py              # Streamlit dashboard
├── run_app.py                    # Application launcher
├── requirements.txt              # Python dependencies
├── README.md                     # This file
├── backend/
│   ├── pose/
│   │   ├── detector_interface.py    # Abstract detector class
│   │   └── mediapipe_detector.py    # MediaPipe implementation
│   ├── processing/
│   │   ├── signal.py                # Signal processing utilities
│   │   ├── metrics.py               # Biomechanics metrics
│   │   └── calibration.py           # Calibration utilities
│   ├── visualization/
│   │   └── overlay.py               # Video overlay creation
│   ├── utils/
│   │   └── io.py                    # File I/O utilities
│   └── api/
│       └── worker_api.py            # FastAPI server
├── cli/
│   └── run_analysis.py              # CLI tool
└── tests/
    └── test_metrics.py              # Unit tests
```

## Metrics Computed

### Event Detection
- **Movement Start**: Frame where hip velocity exceeds threshold
- **First Step**: Frame where foot lifts off ground

### Temporal Metrics
- **Reaction Time**: Time from start to movement onset
- **First Step Time**: Time from movement start to first foot-off

### Kinematics
- **Joint Angles**: Hip, knee, ankle angles over time
- **Angular Velocity**: Rate of change of joint angles
- **Trunk Lean**: Trunk angle from vertical
- **Horizontal Velocity**: Center of mass velocity (calibrated or pixel units)

### Output Files
- `keypoints.csv`: Time-series data of all landmark positions
- `metrics.json`: Summary of all computed metrics
- `overlay.mp4`: Annotated video with skeleton and events
- `landmarks.json`: Raw landmark detections (optional)

## Calibration

The system supports automatic calibration using athlete height:

1. Provide athlete height in meters
2. System estimates pixel height from detected landmarks
3. Computes scaling factor (meters/pixel)
4. Applies to all velocity and distance measurements

Without calibration, measurements are in normalized units or pixels.

## Running Tests

```bash
pytest tests/ -v
```

Tests cover:
- Signal processing (smoothing, derivatives, filtering)
- Joint angle calculations
- Movement detection algorithms
- Edge cases and error handling

## Video Requirements

For best results:
- **View Angle**: Side view perpendicular to sprint direction
- **Lighting**: Good, even lighting with minimal shadows
- **Resolution**: 720p or higher recommended
- **Frame Rate**: 60 fps or higher for detailed analysis
- **Background**: Uncluttered background for better detection
- **Subject**: Full body visible throughout video

## Extending the System

### Adding New Detectors

1. Create new detector class in `backend/pose/`
2. Inherit from `Detector` interface
3. Implement `detect_frame()` and `detect_frames()` methods
4. Update CLI and dashboard to support new detector

### Adding New Metrics

1. Add metric computation function to `backend/processing/metrics.py`
2. Update `compute_all_metrics()` to include new metric
3. Add visualization to dashboard
4. Update tests

## Troubleshooting

### MediaPipe Installation Issues

If MediaPipe fails to install:
```bash
pip install --upgrade pip
pip install mediapipe --no-cache-dir
```

### Video Codec Issues

If overlay video doesn't play:
```bash
# Install ffmpeg
# Ubuntu/Debian
sudo apt-get install ffmpeg

# macOS
brew install ffmpeg

# Windows
# Download from https://ffmpeg.org/download.html
```

### Low Detection Rate

If pose detection fails frequently:
- Ensure good lighting conditions
- Check that full body is visible
- Try adjusting detection confidence thresholds
- Use higher resolution video

## Performance Notes

- Processing time varies with video length and resolution
- Typical: 2-5x real-time on modern CPU
- GPU acceleration available for MediaPipe (requires specific setup)
- For long videos, consider using CLI with progress indicators

## License

MIT License - see LICENSE file for details

## Citation

If you use this software in research, please cite:

```
Sprint Start Analysis System
Version 1.0
https://github.com/yourusername/sprint_start_analysis
```

## Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request

## Support

For issues and questions:
- Open an issue on GitHub
- Check existing documentation
- Review test cases for usage examples

## Roadmap

Future enhancements:
- [ ] OpenPose detector integration
- [ ] Real-time webcam analysis
- [ ] Multi-person tracking
- [ ] Advanced calibration (homography)
- [ ] 3D analysis from multiple cameras
- [ ] Machine learning for technique classification
- [ ] Comparative analysis across multiple trials
- [ ] Export to biomechanics software formats

---

Built with ❤️ using MediaPipe, OpenCV, and Streamlit#
