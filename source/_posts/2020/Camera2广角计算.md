---
title: Camera2广角计算
catalog: true
date: 2020-03-28 23:50:48
subtitle:
header-img:
tags:
- Camera2
---

```
public void calculateSensorAngle(CameraCharacteristics cc, String cameraID) {
    // 获取摄像头广角角度
    SizeF sensorSize = cc.get(CameraCharacteristics.SENSOR_INFO_PHYSICAL_SIZE);
    float[] focalLengths = cc.get(CameraCharacteristics.LENS_INFO_AVAILABLE_FOCAL_LENGTHS);

    float w = sensorSize.getWidth();
    float h = sensorSize.getHeight();
    Log.d(TAG, "calculateSensorAngle, Camera " + cameraID + " has sensorSize == " + Float.toString(2.0f * w) + ", " + Float.toString(2.0f * h));
    for (int focusId = 0; focusId < focalLengths.length; focusId++) {
        float focalLength = focalLengths[focusId];
        float horizonalAngle = (float) Math.toDegrees(2 * Math.atan(w / focalLength));
        float verticalAngle = (float) Math.toDegrees(2 * Math.atan(h / focalLength));
        Log.d(TAG, "calculateSensorAngle, Camera " + cameraID + "/f" + focusId + " has focalLength == " + Float.toString(focalLength));
        Log.d(TAG, "calculateSensorAngle, horizonalAngle = " + Float.toString(horizonalAngle) + ", verticalAngle = " + Float.toString(verticalAngle));

    }
}
```
