# Designing a Hybrid Video Codec
## 01. Image Compression System.
## 02. Video Compression System. 

### Block Matching and Prediction:

```python
def block_matching_and_prediction(ref_frame_y, curr_frame_y, mb_size=16, search_p=8):
'''
This function removes temporal redundancy between adjacent frames
'''
  return predicted_frame, motion_vectors, modes
```
Measures the image dimensions and calculates how many extra pixels are needed to make the height and width perfectly divisible by 16. It then pads the image by duplicating the edge pixels.

Because arbitrary video resolutions are rarely perfect multiples of the MB size, Boundary Padding is strictly enforced. Edge duplication ensures that artificially introduced pixels do not create high-frequency artifacts that would corrupt the subsequent spatial DCT compression pipeline.

Initializes empty matrices to hold the reconstructed image, the resulting $(dx, dy)$ vectors, and the mode flags indicating whether the block used spatial or temporal prediction.

Steps through the image exactly 16 pixels at a time, extracting the current target macroblock.

**INTRA Prediction:** Serves as the system's spatial fallback. In scenarios where temporal correlation fails (e.g., sudden scene cuts or newly uncovered background areas), motion estimation will yield high error rates. A DC-prediction mode provides a baseline spatial error metric, ensuring the codec always has a reliable fallback state.

**INTER Prediction:** The codec employs a Block-Matching Algorithm (BMA) to estimate motion. To optimize the high computational complexity of an exhaustive search, a spatial sub-sampling technique (step size of 2) is used, significantly accelerating algorithm execution while maintaining acceptable prediction accuracy. The objective function minimized during this search is the Sum of Absolute Differences (SAD), representing the residual error energy.

**The Decision Block:** The codec features an Intelligent Mode Decision Block. It dynamically compares the temporal prediction cost ($SAD_{INTER}$) against the spatial prediction cost ($SAD_{INTRA}$) on a macroblock-by-macroblock basis. This ensures optimal rate-distortion performance by actively rejecting motion vectors that model random noise rather than true structural movement.

### The helper
```python
ef get_video_frames(video_path, frame_skip=5):
    """Extracts two frames separated by a gap to ensure noticeable motion."""
    return frame1, frame2
```
Video signals sampled at high temporal frequencies (e.g., 60 Hz) exhibit extremely small frame-to-frame physical displacement. To rigorously test the robustness of the motion estimation engine, a temporal subsampling factor (frame_skip = 5) is introduced to simulate lower framerates and artificially induce larger motion vectors between the evaluated reference and target frames.

