# DVOR - System Flowchart

```mermaid
flowchart TD
    Start([🚀 Start DVOR System])
    
    Init[🔧 Initialize System<br/>Launch, Connect Webcam, Load Model]
    
    CheckWebcam{📷 Webcam<br/>Ready?}
    ErrorWebcam[❌ Display Error<br/>Webcam not ready]
    
    Ready[✅ System Ready<br/>Waiting for orange placement]
    
    PlaceOrange[🍊 User Places Orange<br/>on capture platform]
    
    WaitCapture{🖱️ User Presses<br/>Capture?}
    
    CaptureImage[📸 Capture Image<br/>Views in Single Frame]
    
    SaveRaw[💾 Save Raw Image<br/>Raw Image]
    
    %% Image Processing
    Preprocess[🔧 Image Preprocessing<br/>Adjust, Reduce Noise, Separate Views]
    
    Segment[✂️ Object Segmentation<br/>Separate Orange & Binary Mask]
    
    CheckSegment{✅ Segmentation<br/>Successful?}
    ErrorSegment[❌ Error<br/>Object Not Found or Unclear]
    
    DetectMarker[📏 Detect Marker<br/>Detect Reference Object]
    
    CheckMarker{✅ Marker<br/>Found?}
    UseDefault[⚠️ Use Default<br/>Calibration]
    
    Calibrate[📐 Scale Calibration<br/>Calculate Pixel/mm ratio]
    
    ExtractFeatures[📊 Feature Extraction<br/>Diameter, Height, Area, Roundness]
    
    CreateVector[📈 Create Feature Vector<br/>Input Data for ML Model]
    
    %% ML Prediction
    LoadModel[🤖 Load ML Model<br/>Load trained model]
    
    Predict[🎯 Volume Prediction<br/>Using ML Model]
    
    PostProcess[📝 Post Processing<br/>Validate & Convert Units]
    
    CheckValid{✅ Volume value<br/>Reasonable?}
    ErrorPrediction[❌ Warning<br/>Abnormal Value Detected]
    
    %% Output
    DisplayResults[🖥️ Display Results<br/>Volume, Images, Features, Confidence]
    
    SaveOption{💾 Save<br/>Results?}
    
    SaveData[💿 Save Data<br/>Images, CSV, Timestamp, Logs]
    
    Continue{🔄 Measure<br/>Next Orange?}
    
    End([🏁 End])
    
    %% Connections
    Start --> Init
    Init --> CheckWebcam
    CheckWebcam -->|No| ErrorWebcam
    ErrorWebcam --> End
    CheckWebcam -->|Yes| Ready
    
    Ready --> PlaceOrange
    PlaceOrange --> WaitCapture
    WaitCapture -->|Wait| WaitCapture
    WaitCapture -->|Capture| CaptureImage
    
    CaptureImage --> SaveRaw
    SaveRaw --> Preprocess
    Preprocess --> Segment
    
    Segment --> CheckSegment
    CheckSegment -->|No| ErrorSegment
    ErrorSegment -->|Retry| WaitCapture
    CheckSegment -->|Yes| DetectMarker
    
    DetectMarker --> CheckMarker
    CheckMarker -->|No| UseDefault
    CheckMarker -->|Yes| Calibrate
    UseDefault --> ExtractFeatures
    Calibrate --> ExtractFeatures
    
    ExtractFeatures --> CreateVector
    CreateVector --> LoadModel
    LoadModel --> Predict
    
    Predict --> PostProcess
    PostProcess --> CheckValid
    CheckValid -->|No| ErrorPrediction
    ErrorPrediction --> DisplayResults
    CheckValid -->|Yes| DisplayResults
    
    DisplayResults --> SaveOption
    SaveOption -->|Yes| SaveData
    SaveOption -->|No| Continue
    SaveData --> Continue
    
    Continue -->|Yes| PlaceOrange
    Continue -->|No| End
    
    %% Styling
    style Start fill:#8c8style End fill:#estyle CaptureImage fill:#fcfstyle Predict fill:#ff8astyle DisplayResults fill:#ffdf
    style ErrorWebcam fill:#ef0
    style ErrorSegment fill:#ef0
    style ErrorPrediction fill:#ffbd
    style CheckWebcam fill:#90caf9
    style CheckSegment fill:#90caf9
    style CheckMarker fill:#90caf9
    style CheckValid fill:#90caf9
```

---

## 📖 Flowchart Explanation

### 🟢 **Phase : Initialization**

. **Start System**
   - Launch DVOR program
   - Connect Webcam
   - Load pre-trained Machine Learning Model
   - Check equipment

. **Check Webcam**
   - ✅ If ready → Continue
   - ❌ If not ready → Display Error and end

---

### 🔵 **Phase : Image Capture**

. **System Ready**
   - Display GUI interface, waiting for user

. **Place Orange**
   - User places orange on capture platform
   - Adjust position to be within Webcam view

. **Wait for Capture Command**
   - User presses "Capture" button

. **Capture Image**
   - Capture from Webcam in a single frame
   - Gets views simultaneously:
     • Top View: Direct overhead view of orange
     • Side View: Side reflection from mirror
   - Save raw image (Raw Image)

---

### 🟡 **Phase : Image Processing**

. **Preprocessing**
   - Adjust brightness, sharpness
   - Reduce noise
   - Separate Top View and Side View images

8. **Segmentation**
   - Separate object (orange) from background
   - Use Color-based segmentation
   - Create Binary mask

9. **Check Segmentation**
   - ✅ Success → Continue
   - ❌ Failure → Display Error, retry capture

0. **Detect Marker**
    - Find Reference object for scale calibration
    - ✅ Found → Use to calculate Pixel/mm
    - ❌ Not found → Use Default value

. **Scale Calibration**
    - Convert Pixel values → mm/cm

---

### 🟣 **Phase : Feature Extraction**

. **Extract Features**
    - Measure Diameter
    - Measure Height
    - Calculate Area
    - Calculate Roundness
    - Calculate Aspect Ratio

. **Create Feature Vector**
    - Organize data into Array
    - Ready to input into ML Model

---

### 🔴 **Phase : ML Prediction**

. **Load ML Model**
    - Use pre-trained model

. **Predict Volume**
    - Input Feature Vector into Model
    - Get predicted volume value (cm³)

. **Post Processing**
    - Validate reasonable values
    - Round numbers, convert units

. **Check Validity**
    - ✅ Normal value → Display
    - ⚠️ Abnormal value → Display Warning but still show results

---

### 🟢 **Phase : Output & Save**

8. **Display Results**
    - Volume (cm³)
    - Processed images
    - Feature data
    - Confidence score (if available)

9. **Save Option**
    - User chooses whether to save

0. **Save Data** (if selected)
    - Save images
    - Save results as CSV
    - Save Timestamp and Metadata

. **Continue or End?**
    - ✅ Continue → Place new orange
    - ❌ End → Close program

---

## ⏱️ Estimated Processing Time

| Step | Time (seconds) |
|------|---------------|
| Capture Image | < |
| Preprocessing | 0.- |
| Segmentation | - |
| Feature Extraction | 0.- |
| ML Prediction | < 0.|
| Display Results | < 0.|
| **Total** | **~-seconds/orange** |

---

## 🔄 Continuous Operation (Batch Processing)

For measuring multiple oranges in sequence:

```
🍊 Orange #→ 📸 Capture → 🔄 Process → 🖥️ Display → 💾 Save
                                                            ↓
🍊 Orange #→ 📸 Capture → 🔄 Process → 🖥️ Display → 💾 Save
                                                            ↓
                                  ...and so on
```

---

## ⚠️ Error Handling

| Error Type | Solution |
|------------|----------|
| **Webcam Not Ready** | Check USB connection / Driver |
| **Cannot Segment Object** | Adjust orange position / Check lighting / Retry capture |
| **Marker Not Found** | Use Default calibration / Notify user |
| **Abnormal Volume Value** | Display Warning / Let user verify |
| **Model Not Found** | Check model file / Retrain |

---

## 🎯 Flowchart Highlights

✅ **Clear:** Shows detailed sequence of operations  
✅ **Error Management:** Every Decision Point has alternatives  
✅ **Flexible:** Can measure multiple oranges continuously  
✅ **Automated:** After image capture, system processes automatically  
✅ **Supports Saving:** Can store data for future use
