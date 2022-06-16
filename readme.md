# 오디오 앱
## ViewController

```javascript
import UIKit

import AVFoundation

class ViewController: UIViewController, AVAudioPlayerDelegate {

    var audioPlayer : AVAudioPlayer!
    var audioFile : URL!

    let MAX_VOLUME : Float = 10.0

    var progressTimer : Timer!
    let timePlayerSelector:Selector = #selector(ViewController.updatePlayTime)

    @IBOutlet var pvProgressPlay: UIProgressView!
    @IBOutlet var lblCurrentTime: UILabel!
    @IBOutlet var btnPlay: UIButton!
    @IBOutlet var btnPause: UIButton!
    @IBOutlet var btnStop: UIButton!
    @IBOutlet var lblEndTime: UILabel!
    @IBOutlet var slVolume: UISlider!

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        audioFile = Bundle.main.url (forResource: "Sicilian_Breeze", withExtension: "mp3")
        initPlay()
    }
```

### 재생 모드의 초기화

```javascript
func initPlay() {
        do {
            audioPlayer = try AVAudioPlayer(contentsOf: audioFile)
        } catch let error as NSError {
            print("Error-initPlay : \(error)")
        }
        slVolume.maximumValue = MAX_VOLUME
        slVolume.value = 1.0
        pvProgressPlay.progress = 0
        audioPlayer.delegate = self
        audioPlayer.prepareToPlay()
        audioPlayer.volume = slVolume.value
        lblEndTime.text = convertNSTimeInterval2String(audioPlayer.duration)
        lblCurrentTime.text = convertNSTimeInterval2String(0)
        setPlayButtons(true, pause: false, stop: false)
    }
```


### [재생], [일시 정지], [정지] 버튼을 활성화 또는 비활성화하는 함수

```javascript
func setPlayButtons(_ play:Bool, pause: Bool, stop:Bool) {
        btnPlay.isEnabled = play
        btnPause.isEnabled = pause
        btnStop.isEnabled = stop
    }
```


### 00:00 형대의 문자열로 변환함

```javascript
func convertNSTimeInterval2String(_ time:TimeInterval) -> String {
        let min = Int(time/60)
        let sec = Int(time.truncatingRemainder(dividingBy: 60))
        let strTime = String(format: "%02d:%02d", min, sec)
        return strTime
    }
```


### [재생] 버튼을 클릭하였을 때

```javascript
@IBAction func btnPlayAudio(_ sender: UIButton) {
        audioPlayer.play()
        setPlayButtons(false, pause: true, stop: true)
        progressTimer = Timer.scheduledTimer(timeInterval: 0.1, target: self, selector: timePlayerSelector, userInfo: nil, repeats: true)
    }
```


### 0.1초마다 호출되며 재생 시간을 표시함

```javascript
    @objc func updatePlayTime() {
        lblCurrentTime.text = convertNSTimeInterval2String(audioPlayer.currentTime)
        pvProgressPlay.progress = Float(audioPlayer.currentTime/audioPlayer.duration)
    }
```


### [일시 정지] 버튼을 클릭하였을 때

```javascript
@IBAction func btnPauseAudio(_ sender: UIButton) {
        audioPlayer.pause()
        setPlayButtons(true, pause: false, stop: true)
    }
```


### [정지] 버튼을 클릭하였을 때

```javascript
@IBAction func btnStopAudio(_ sender: UIButton) {
        audioPlayer.stop()
        audioPlayer.currentTime = 0
        lblCurrentTime.text = convertNSTimeInterval2String(0)
        setPlayButtons(true, pause: false, stop: false)
        progressTimer.invalidate()
    }
```


### 볼륨 슬라이더 값을 audioplayer.volume에 대입함

```javascript
@IBAction func slChangeVolume(_ sender: UISlider) {
        audioPlayer.volume = slVolume.value
    }
```


### 재생이 종료되었을 때 호출됨

```javascript
func audioPlayerDidFinishPlaying(_ player: AVAudioPlayer, successfully flag: Bool) {
        progressTimer.invalidate()
        setPlayButtons(true, pause: false, stop: false)
    }
}
```


## 동작

<img src="https://user-images.githubusercontent.com/106363908/174159873-5593b040-bd6e-4709-b61c-f385902974d0.png" width="400" height="600">

<img src="https://user-images.githubusercontent.com/106363908/174159940-4b7f1762-6075-4a1b-9446-e75dd318087f.png" width="400" height="600">

<img src="https://user-images.githubusercontent.com/106363908/174159993-3b7d8c3d-83d3-46c9-a182-e33948485df0.png" width="400" height="600">

<img src="https://user-images.githubusercontent.com/106363908/174160021-2ff00210-0525-44fe-9783-567eb79e62e2.png" width="400" height="600">
