#pytracker

A python package for interacting with a webcam for gaze tracking &amp; pupillometry. I'm a cognitive scientist, so my emphasis is on something that can be easily integrated into cognitive science experiments (including multi-core implementation to avoid slowing down your stim presentation process), but portions of the code may be of interest to game developers too.

Currently only has accounting for head movements in x,y,z planes only; translations in pitch, roll and yaw are on the to-do list. 


##Software dependencies

 - numpy (http://numpy.org)
 - scipy (http://scipy.org)
 - OpenCV (http://opencv.org)
 - billiard (https://github.com/celery/billiard)


##Hardware dependencies

Eye tracking is more reliable if using an infrared sensitive camera and a source of infrared illumination. This is because most users' skin and iris color will be a light grey under these conditions, making detection of a dark pupil easier. Consumer webcams can easily be modified (how-to video coming soon) to see in the infrared by removing the infrared block-filter included by the manufacturer and adding an infrared pass-fileter (this one is <$10: http://goo.gl/eCK7yv ). An infrared light source can be obtained cheaply from many places, including a Nintendo Wii "sensor" bar (which is really just an infrared light source). The wireless Wii sensor bars can be moded (how-to video coming soon) to be USB powered if you don't want to keep wasting batteries. 

Finally, the code expects that the user has a black dot on their head, placed between the eyes and just above the brow. This dot serves as a reference point for the head's location so that eye movements can be dissociated from head movements. The tracker additionally expects that the dot's size is a standard hole-punch size. A good way to create such dots is to use a laser printer (these use infrared opaque inks) to print a page of solid black onto a sheet of adhesive label paper, then use a hole punch to punch out the dots as needed. (The use of such reference points may be eliminated in the future if I can achieve reliable enough automatic extraction of reference points from the face itself)


##Example usage

```python
if __name__ == '__main__':
	import pytracker
	import time
	camIndex = 0
	camRes = [1920,1080]
	previewDownsize = 2
	faceDetectionScale = 4
	eyeDetectionScale = 2
	timestampMethod = 2
	viewingDistance = 53.0
	stimDisplayWidth = 59.5
	stimDisplayRes = [1024,768]
	stimDisplayPosition = [0,0]
	mirrorDisplayPosition = [1025,0] #i.e. put the window mirroring the stim display on the next monitor
	mirrorDownSize = 2
	previewLoc = [1024,0] #i.e. put the window mirroring the stim display on the next monitor
	manualCalibrationOrder = False
	calibrationDotSizeInDegrees = 1
	saccadeAlertSizeInDegrees = 3
	tracker = pytracker.trackerClass(camIndex=camIndex,camRes=camRes,previewDownsize=previewDownsize,previewLoc=previewLoc,faceDetectionScale=faceDetectionScale,eyeDetectionScale=eyeDetectionScale,timestampMethod=timestampMethod,viewingDistance=viewingDistance,stimDisplayWidth=stimDisplayWidth,stimDisplayRes=stimDisplayRes,stimDisplayPosition=stimDisplayPosition,mirrorDisplayPosition=mirrorDisplayPosition,mirrorDownSize=mirrorDownSize,manualCalibrationOrder = manualCalibrationOrder, calibrationDotSizeInDegrees=calibrationDotSizeInDegrees,saccadeAlertSizeInDegrees=saccadeAlertSizeInDegrees)
	tracker.start()
	tracker.start()
	while True:
		time.sleep(1)
		if not tracker.qFrom.empty():
			message = tracker.qFrom.get()
			if message=='done':
				break
```

If the full face is visible, you should be able to click "Auto" to automatically acquire the pupils and fiduciary mark. If the "Auto" fails, click "Manual" then: 
1. click the center of the fiduciary mark
2. move the mouse until the cursor is outside the fiduciary mark (you'll see a circle that expands around the mark)
3. click again to acquire the fiduciary mark
4. click the center of the left pupil
5. click the center of the right pupil

Regardless of whether using "Auto" or "Manual", you should instruct the subject to stare straight ahead and refrain from blinking or moving during the acquisition. 

Following acquisition, ask the subject to blink intermittently and move their eyes intermittently. If either the blinks aren't detected or the eyes get lost during movements, try moving the slider on the right of the preview window to different values.

