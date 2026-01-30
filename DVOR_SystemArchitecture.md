# DVOR - System Architecture

```mermaid
graph TD
    subgraph Row[" "]
        direction LR
        subgraph "️⃣ Input Layer"
            Webcam[📷 Webcam<br/>Single Webcam]
            Mirror[🪞 Mirror<br/>Angled Position]
            Light[💡 Lighting<br/>Controlled Lighting]
        end
        
        subgraph "️⃣ Image Acquisition"
            Capture[📸 Image Capture Module<br/>Captures both views in single frame]
            RawImage[(🖼️ Raw Image<br/>RGB Image)]
        end
    end
    
    subgraph Row[" "]
        direction LR
        subgraph "️⃣ Image Processing"
            Preprocessing[🔧 Image Preprocessing<br/>- Adjust brightness/contrast<br/>- Noise reduction<br/>- Separate Top/Side View]
            Segmentation[✂️ Object Segmentation<br/>- Separate orange from background<br/>- Detect Marker<br/>- Binary Masking]
            Calibration[📏 Scale Calibration<br/>- Detect Marker<br/>- Convert Pixel → mm/cm]
        end
        
        subgraph "️⃣ Feature Extraction"
            GeometricFE[📐 Geometric Feature<br/>Extraction<br/>- Diameter<br/>- Height<br/>- Cross-sectional Area<br/>- Roundness]
            FeatureVector[(📊 Feature Vector<br/>Feature Data Set)]
        end
    end
    
    subgraph Row[" "]
        direction LR
        subgraph "️⃣ AI/ML Module"
            MLModel[🤖 Machine Learning Model<br/>- Training in Progress<br/>- Trained Model]
            Prediction[🎯 Volume Prediction<br/>Predict Volume]
        end
        
        subgraph "️⃣ Output Layer"
            Display[🖥️ Display Results<br/>- Volume value cm³<br/>- Processed images<br/>- All features]
            Storage[(💾 Data Storage<br/>- Save images<br/>- Save results<br/>- Log data)]
        end
        
        subgraph "️⃣ User Interface"
            GUI[🖱️ GUI Application<br/>- Capture button<br/>- Real-time display<br/>- Save/Export]
        end
    end
    
    %% Connections
    Webcam -->|Direct top view of orange| Capture
    Mirror -->|Reflected side view| Webcam
    Light --> Webcam
    
    Capture --> RawImage
    RawImage --> Preprocessing
    
    Preprocessing --> Segmentation
    Segmentation --> Calibration
    
    Calibration --> GeometricFE
    GeometricFE --> FeatureVector
    
    FeatureVector --> MLModel
    MLModel --> Prediction
    
    Prediction --> Display
    Display --> Storage
    
    GUI -.->|Control| Capture
    Display -.->|Display in| GUI
    Storage -.->|Retrieve| GUI
    
    %% Styling
    style Webcam fill:#fcfstyle Mirror fill:#b9ddb
    style MLModel fill:#ff8astyle Prediction fill:#ffbd
    style Display fill:#8c8style GUI fill:#fff```

---

## 📋 Layer Details

### ️⃣ **Input Layer**

| Component | Function | Technology |
|-----------|----------|------------|
| **Webcam** | Captures orange in both Top View (direct) and Side View (reflected) in single frame | RGB Webcam (≥MP) |
| **Mirror** | Reflects side view of orange, positioned at appropriate angle | High-quality smooth mirror |
| **Lighting** | Provides consistent illumination | LED White Light |

---

### ️⃣ **Image Acquisition**

- **Image Capture Module**
  - Captures views in a single frame
  - Saves as RGB file (JPG/PNG)
  - High resolution for accuracy

---

### ️⃣ **Image Processing**

#### a) **Image Preprocessing**
```
Input: Raw RGB Image
Process:
  - Color correction
  - Noise reduction (Gaussian Blur, Median Filter)
  - Contrast enhancement
  - Separate Top View and Side View from single frame
Output: Processed Images (views)
```

#### b) **Object Segmentation**
```
Input: Processed Images
Process:
  - Background subtraction
  - Color-based segmentation (HSV/LAB color space)
  - Edge detection (Canny)
  - Morphological operations (Opening/Closing)
  - Binary masking
Output: Segmented orange object (binary mask)
```

#### c) **Scale Calibration**
```
Input: Segmented Image + Marker
Process:
  - Detect Marker (Known size reference object)
  - Calculate ratio: Pixel/mm
  - Use for all measurements
Output: Calibration ratio (px/mm)
```

---

### ️⃣ **Feature Extraction**

#### Geometric Features:
. **Diameter**
   - Measured from Top View (D_top)
   - Measured from Side View (D_side)

. **Height**
   - Measured from Side View

. **Cross-sectional Area**
   - Calculated from Top View
   - A = π × (D_top/)²

. **Roundness/Circularity**
   - R = π × Area / Perimeter²
   - Used to assess asymmetry

. **Aspect Ratio**
   - Height / Diameter

**Output:** Feature Vector = [D_top, D_side, Height, Area, Roundness, Aspect_Ratio, ...]

---

### ️⃣ **AI/ML Module**

#### Machine Learning Pipeline:

```
Input: Feature Vector (n features)
         ↓
    [ML Model]
    - Machine Learning Algorithm
    - Model Development in Progress
         ↓
Output: Predicted Volume (cm³)
```

#### Training Process:
```
. Collect data: Orange images + actual volume measurement (Water displacement)
. Extract Features from images
. Split data: Train/Validation/Test set
. Train multiple Models
. Select best Model
. Optimize hyperparameters
. Deploy model
```

---

### ️⃣ **Output Layer**

#### Display Results:
- **Volume Value:** Displayed in cm³ or ml
- **Processed Images:** Shows both views with annotations
- **Features:** Display diameter, height, area data
- **Confidence:** Display confidence score (if available)

#### Data Storage:
- Save original images
- Save measurement results (CSV/Database)
- Log timestamp and other metadata
- Use for retraining model or later analysis

---

### ️⃣ **User Interface**

#### GUI Features:
- **Preview Window:** Shows real-time Webcam feed
- **Capture Button:** Button to photograph and start processing
- **Results Panel:** Display measurement results
- **Save/Export:** Save data to file
- **Settings:** Adjust parameters

---

## 🔧 Technology Stack

| Layer | Technology/Library |
|-------|-------------------|
| **Programming Language** | Python |
| **Image Processing** | OpenCV, PIL/Pillow, scikit-image |
| **Feature Extraction** | NumPy, SciPy |
| **Machine Learning** | scikit-learn, TensorFlow/PyTorch |
| **GUI** | Tkinter / PyQt / Streamlit |
| **Data Storage** | SQLite / CSV / JSON |

---

## 📊 Data Flow Summary

```
🍊 Orange → 📷 Webcam+🪞Mirror → 🖼️ Raw Image (views) → 
🔧 Image Processing → ✂️ Segmentation → 📏 Scale Calibration → 
📐 Feature Extraction → 🤖 AI Model → 🎯 Volume → 
🖥️ Display → 💾 Save Data
```

---

## ⚙️ System Requirements

### Hardware:
- ✅ RGB Webcam (MP or higher)
- ✅ Smooth mirror
- ✅ LED lighting system
- ✅ Computer (CPU: ior better, RAM: 8GB or more)

### Software:
- ✅ Python .8+
- ✅ OpenCV .x
- ✅ scikit-learn / TensorFlow
- ✅ NumPy, Pandas
- ✅ GUI Framework

---

## 🎯 Architecture Advantages

✅ **Modular Design:** Each module is clearly separated, easy to develop and modify  
✅ **Scalable:** Can add features or improve model easily  
✅ **Cost-effective:** Uses single Webcam, reducing costs  
✅ **Automated:** Processes automatically after image capture  
✅ **Non-destructive:** Does not damage the orange sample
