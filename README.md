# 🎯 ToolpathViz

**XtreeE Toolpath Visualizer** - An interactive 3D web application for visualizing and analyzing XtreeE toolpath data with advanced rendering and metadata inspection capabilities.

![Three.js](https://img.shields.io/badge/Three.js-r128-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Status](https://img.shields.io/badge/status-active-success)

---

## ✨ Features

### 🎨 **Interactive 3D Visualization**
- **Real-time 3D rendering** using Three.js with smooth OrbitControls
- **Continuous polyline rendering** across all layers for seamless toolpath visualization
- **Dynamic camera positioning** that automatically frames the toolpath on load
- **Grid and axes helpers** for spatial reference and orientation
- **High-quality rendering** with antialiasing and optimized lighting

### 🌈 **Advanced Color Mapping**
- **Variable-based coloring**: Color the toolpath by any numerical metadata variable
- **Gradient visualization**: Smooth HSL color gradients from blue (min) to red (max)
- **Interactive legend**: Real-time legend showing min/max values and color scale
- **Default layer coloring**: Consistent cyan coloring when no variable is selected
- **Automatic normalization**: Smart handling of data ranges and edge cases

### 📊 **Metadata Inspection**
- **Object Information**: View object-level metadata and properties
- **Analysis Data**: Access computed analysis metrics and statistics
- **Toolpath Data Range**: Inspect min/max ranges for all variables
- **Layer Count**: Quick summary of total layers in the toolpath
- **Organized sidebar**: Clean, categorized display of all metadata

### 📁 **Flexible File Loading**
- **Drag & drop support**: Simply drag JSON files onto the upload zone
- **File browser**: Click to browse and select files from your system
- **Sample files**: Built-in sample toolpaths for quick testing and demos
- **Deep linking**: Share specific toolpaths via URL parameters (`?file=filename.json`)
- **URL state management**: Browser history integration for easy navigation

### 🎮 **User Controls**
- **Reset View**: Instantly recenter and reframe the camera on the toolpath
- **Load New File**: Clear current visualization and load a different file
- **Color By Selector**: Dropdown to switch between different coloring variables
- **Orbit Controls**: Rotate, pan, and zoom with mouse/touch gestures
- **Responsive Design**: Adapts to different screen sizes and orientations

---

## 🚀 Getting Started

### Prerequisites
- A modern web browser (Chrome, Firefox, Safari, Edge)

### Usage

#### **Loading a Toolpath**

**Option 1: Upload Your Own File**
- Drag and drop a JSON file onto the upload zone
- Or click the upload zone to browse for a file

**Option 2: Use Sample Files**
- Select from the dropdown menu in the upload zone
- Sample files are loaded from the `/data` directory

**Option 3: Deep Linking**
- Share a direct link: `http://localhost:8000?file=ScreenBase_v0_044902894.json`
- The specified file will load automatically

#### **Exploring the Visualization**

1. **Navigate the 3D View**
   - **Left click + drag**: Rotate the camera around the toolpath
   - **Right click + drag**: Pan the camera
   - **Scroll wheel**: Zoom in/out
   - **Reset View button**: Return to the default framed view

2. **Change Color Mapping**
   - Use the "Color By" dropdown in the sidebar
   - Select "Default" for uniform cyan coloring
   - Select any variable to apply gradient coloring
   - The legend updates automatically to show the value range

3. **Inspect Metadata**
   - Scroll through the sidebar to view all metadata sections
   - Object Information, Analysis Data, and Toolpath Data Range are organized separately
   - Layer count is displayed at the bottom

---

## 📋 JSON Data Format

### Key Requirements:
- **Positions**: Array of comma-separated "x,y,z" coordinate strings
- **Variable Arrays**: Must have the same length as Positions array
- **Layer Naming**: Layers should be named "Layer_N" where N is sequential
- **Numerical Data**: All variable values should be parseable as numbers

---

## 🛠️ Technical Details

### **Architecture**
- **Frontend Framework**: Vanilla JavaScript (no build step required)
- **3D Rendering**: Three.js r128
- **Controls**: OrbitControls for camera manipulation
- **Styling**: Modern CSS with dark theme and glassmorphism

### **Rendering Pipeline**
1. Parse JSON and extract all layer data
2. Sort layers numerically for correct ordering
3. Build single continuous BufferGeometry with all positions
4. Calculate per-vertex colors based on selected variable
5. Create LineBasicMaterial with vertex colors enabled
6. Add to scene and auto-frame camera

### **Color Gradient Algorithm**
- Uses HSL color space for smooth transitions
- Hue range: 240° (blue) to 0° (red) via 180° (cyan) and 60° (yellow)
- Saturation: 100% for vibrant colors
- Lightness: 50% for optimal visibility
- Handles edge cases (min=max, NaN, Infinity)

### **Coordinate System**
- Input: X, Y, Z from JSON positions
- Output: X (right), Z (up), -Y (forward)
- Y-axis is negated to correct for coordinate system differences
- Grid aligned to XZ plane

---

## 📦 Sample Files

The `/data` directory includes several example toolpaths:

- `ScreenBase_v0_044902894.json` - Screen base structure
- `S_Mur_2_v0_089206760.json` - Wall section
- `ProtoFolie_v1_070908868.json` - Prototype foil design
- `LargeScalePrototype_v1_075728131.json` - Large-scale prototype
- `FieldsColumn_v3_069786330.json` - Column with field patterns

---

## 🎨 Customization

### **Changing the Color Scheme**

Edit the gradient in `index.html` (lines 168-173):
```css
.legend-gradient {
    background: linear-gradient(to top,
        hsl(240, 100%, 50%) 0%,   /* Blue (min) */
        hsl(180, 100%, 50%) 25%,  /* Cyan */
        hsl(120, 100%, 50%) 50%,  /* Green */
        hsl(60, 100%, 50%) 75%,   /* Yellow */
        hsl(0, 100%, 50%) 100%);  /* Red (max) */
}
```

And update the `getColorFromGradient` function (lines 371-376):
```javascript
function getColorFromGradient(value, min, max) {
    if (min === max) return new THREE.Color().setHSL(0.7, 0.56, 0.45);
    const normalized = (value - min) / (max - min);
    const hue = (1.0 - normalized) * 0.66; // Adjust multiplier for different hue range
    return new THREE.Color().setHSL(hue, 1.0, 0.5);
}
```

### **Adjusting Camera Distance**

Modify the distance multiplier in `visualizeToolpath` (line 476):
```javascript
const cameraDistance = (maxDim / 2 / Math.tan(fov / 2)) * 2.5; // Change 2.5 to adjust
```

---

## 🐛 Troubleshooting

### **Sample files won't load**
- **Cause**: Opening `index.html` directly in browser (file:// protocol)
- **Solution**: Use a local web server (see Getting Started)

### **Toolpath appears mirrored or rotated**
- **Cause**: Different coordinate system conventions
- **Solution**: Adjust the coordinate mapping in line 441 of `index.html`

### **Colors not showing correctly**
- **Cause**: Invalid or non-numerical data in variable arrays
- **Solution**: Check that all variable values are valid numbers

### **Legend shows "N/A"**
- **Cause**: Min/max values are Infinity or NaN
- **Solution**: Ensure the selected variable has valid numerical data

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

### **Development Workflow**
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License.

---

## 🙏 Acknowledgments

- **Three.js** - 3D graphics library
- **OrbitControls** - Camera control implementation

---
---

**Made with ❤️ for the XtreeE community**