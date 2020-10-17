# Installation Gesichtserkennung

## Manuelle Installation

1. OpenCV herunterladen und installieren (dauert mehrere Minuten)
`wget https://github.com/cyysky/OpenCV-4.1.2-for-Raspbian/raw/master/opencv_4.1.2-1_armhf.deb`
`sudo dpkg -i opencv_4.1.2-1_armhf.deb`
`sudo apt-get -f install`
`sudo dpkg -i opencv_4.1.2-1_armhf.deb`

2. DLib installieren
    `pip3 install dlib`

3. Gesichtserkennung installieren
    `pip3 install face_recognition`
 
 4. imutils installieren
    `pip3 install imutils`
    
5. numpy installieren
    `pip3 install numpy`

6. libtesserac4 installieren
    `sudo apt install libtesseract4`
  
 7. MagicMirror Erweiterung installieren
`cd ~/MagicMirror/modules/`
`git clone https://github.com/nischi/MMM-Face-Reco-DNN.git`
`cd MMM-Face-Reco-DNN`
`npm install`
`mkdir dataset`

8. Datenset für Gesichter erstellen
- "Dateien" öffnen auf dem Computer
- Rechtsklick - "Neuer Ordner" - Name des Ordners: dataset
- Ordner öffnen
- Pro Person einen Ordner mit dem Namen kleingeschrieben (ä = a,ö = o,ü = u)
- Im Ordner jeder Person mind. 5 Bilder mit dem Namen "img01.jpg", "img02.jpg" usw
- Im Ordner "dataset" Rechtsklick - "Im Terminal öffnen"
- Dateien auf den Raspberry Pi übertragen
- `scp -r ./* pi@[IP-ADRESSE-VON-PI]:~/MagicMirror/modules/MMM-Face-Reco-DNN/dataset/.`
- `exit`

9. Gesichter für die Erkennung vorbereiten (dauert einige Minuten)
 `cd tools`
`python3 encode.py -i ../dataset/ -e encodings.pickle -d hog`

10. Modul einrichten
 `nano ~/MagicMirror/config/config.js`
 In der Datei ganz am Ende der Liste (nach dem letzten `},` ) einfügen
 ```js
{
    module: 'MMM-Face-Reco-DNN',
    config: {
      // Logout 15 seconds after user was not detected any more
      // If they are detected within this period, the delay will start again
      logoutDelay: 15000,
      // How often the recognition starts in milliseconds
      // With a Raspberry Pi 3+ it works well every 2 seconds
      checkInterval: 2000,
      // Module set used for when there is no face detected ie no one is in front of the camera
      noFaceClass: 'noface',
      // Module set used for when there is an unknown/unrecognised face detected
      unknownClass: 'unknown',
      // Module set used for when there is a known/recognised face detected
      knownClass: 'known',
      // Module set used for strangers and if no user is detected
      defaultClass: 'default',
      // Set of modules which should be shown for any user ie when there is any face detected
      everyoneClass: 'everyone',
      // Set of modules that are always shown - show if there is a face or no face detected
      alwaysClass: 'always',
      // XML to recognize with haarcascade
      cascade: 'modules/MMM-Face-Reco-DNN/tools/haarcascade_frontalface_default.xml',
      // Pre-encoded pickle with the faces
      encodings: 'modules/MMM-Face-Reco-DNN/tools/encodings.pickle',
      // Use Raspberry Pi camera or another type
      // 1 = RasPi camera, 0 = other camera
      usePiCamera: 1,
      // If using another type of camera, you can choose
      // i.e. 0 = /dev/video0 or 'http://link.to/live'
      source: 0,
      // Rotate camera
      rotateCamera: 0,
      // Method of facial recognition
      // dnn = deep neural network, haar = haarcascade
      method: 'dnn',
      // Which face detection model to use
      // "hog" is less accurate but faster on CPUs
      // "cnn" is a more accurate deep-learning model which is GPU/CUDA accelerated
      detectionMethod: 'hog',
      // How long in milliseconds modules take to hide and show
      animationSpeed: 0,
      // Path to Python to run the face recognition
      // null or '' means default path
      pythonPath: null,
      // Should a welcome message be shown using the MagicMirror alerts module?
      welcomeMessage: true,
      // Capture new pictures of recognized people, if unknown we save it in folder "unknown"
      // So you can extend your dataset and retrain it afterwards for better recognitions
      extendDataset: false,
      // If extendDataset is true, you need to set the full path of the dataset
      dataset: 'modules/MMM-Face-Reco-DNN/dataset/',
      // How much distance between faces to consider it a match. Lower is more strict.
      tolerance: 0.6,
      // allow multiple concurrent user logins, 0=no, any other number is the maximum number of concurrent logins
      multiUser: 0,
    }
},
```

11. Module für Gesichtserkennung einrichten
Bei allen Modulen die immer angezeigt werden sollen diese Zeile hinzufügen:
`classes: 'always',`

Bei allen Modulen die nur für einen bestimmten Benutzer angezeigt werden sollen (z.B nathanael)
`classes: 'nathanael',`

Es können auch mehrere Klassen angegeben werden z.B `classes: 'nathanael peter',`
 
