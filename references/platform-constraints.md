# Seedance 2.0 Platform Constraints

## Input Specifications

### Images
- Formats: jpeg, png, webp, bmp, tiff, gif
- Maximum: 9 images per generation
- Size limit: < 30 MB per image

### Videos
- Formats: mp4, mov
- Maximum: 3 videos
- Total duration: 2-15 seconds
- Size limit: < 50 MB total
- Pixel range: 409600 (640x640, 480p) to 927408 (834x1112, 720p)
- Note: Using reference videos increases generation cost

### Audio
- Formats: mp3, wav
- Maximum: 3 audio files
- Total duration: <= 15 seconds
- Size limit: < 15 MB total

### Text
- Natural language (Chinese or English)

## Output Specifications
- Duration: 4-15 seconds (user selectable)
- Includes auto-generated sound effects/music
- Quality modes: Seedance 2.0 (standard) and Seedance 2.0 Fast (faster queue)

## Interaction Limits
- Maximum 12 files total across all modalities
- Prioritize materials with highest visual/rhythm impact

## Entry Points
- First/Last Frame (首尾帧): simple first-frame image + prompt
- Universal Reference (全能参考): multi-modal combinations
- 智能多帧 and 主体参考 NOT available with Seedance 2.0

## CRITICAL: Realistic Human Face Restriction
Platform prohibits uploading materials containing realistic human faces (images and videos). System auto-blocks such materials. Anime/stylized/3D faces are acceptable. Always warn users proactively.

## Extension Rules
- Generation duration = new portion length only (not original + extension)
- Always emphasize continuity with original ending

## @ Reference Notation
- @图片1, @图片2... for images
- @视频1, @视频2... for videos
- @音频1, @音频2... for audio
- Each must have a clearly stated role

## Official Website
- https://jimeng.jianying.com/
