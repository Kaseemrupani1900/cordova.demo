# cordova.demo

Accura Scan OCR is used for Optical character recognition.

Accura Scan Face Match is used for Matching 2 Faces, Source face and Target face. It matches the User Image from a Selfie vs User Image in document.

Accura Scan Authentication is used for your customer verification and authentication. Unlock the True Identity of Your Users with 3D Selfie Technology

Below steps to setup Accura Scan's SDK to your project.

# Add Android Or iOS Platform
```
$ cordova platform add android@9.1.0
$ cordova platform add ios
```

# 1.Setup Android

Step 1: Add it in your root build.gradle at the end of repositories.

```
allprojects {
    repositories {
        ...
	google()
	jcenter()
        maven {
            url 'https://jitpack.io'
            credentials { username "jp_ssguccab6c5ge2l4jitaj92ek2" }
        }
    }
}
```

Step 2: Add it in your app/build.gradle file.

```
android {

    defaultConfig {
        ...
        ndk {
            // Specify CPU architecture.
            // 'armeabi-v7a' & 'arm64-v8a' are respectively 32 bit and 64 bit device architecture 
            // 'x86' & 'x86_64' are respectively 32 bit and 64 bit emulator architecture
            abiFilters 'armeabi-v7a', 'arm64-v8a', 'x86', 'x86_64'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    packagingOptions {
        pickFirst 'lib/arm64-v8a/libcrypto.so'
        pickFirst 'lib/arm64-v8a/libssl.so'

        pickFirst 'lib/armeabi-v7a/libcrypto.so'
        pickFirst 'lib/armeabi-v7a/libssl.so'

        pickFirst 'lib/x86/libcrypto.so'
        pickFirst 'lib/x86/libssl.so'

        pickFirst 'lib/x86_64/libcrypto.so'
        pickFirst 'lib/x86_64/libssl.so'
	}
	
}
```


# Add Cordova KYC Plugin
```
$ cordova plugin add cordova_demo
```
# Add Dependencies

```
$ cordova plugin add cordova-plugin-file

$ cordova plugin add cordova-plugin-whitelist

$ cordova plugin add cordova-plugin-add-swift-support
```

# Run Cordova Build Command

`$ cordova build`

# 2.Setup AccuraScan Licenses Into Your Projects

Generate your Accura license from ***https://accurascan.com/developer/dashboard***

### 1: License Path For Android ###
```
platforms/android/app/src/main/assets/key.license
platforms/android/app/src/main/assets/accuraface.license
```
Replace Your Licenses In These Locations.

### 2: License Path For iOS ###
```
Place Both The License In Your "Resources" Folder, And Add The Licenses To The Target.
```

# javascript(index.js)

## Plugin access in Javascript
`cordova.plugins.CordovaDemoPlugin`

In your cordova javascript event get plugin

```
document.addEventListener('deviceready', onDeviceReady, false);

var accura;

function onDeviceReady() {

     // Cordova is now initialized.

     accura = cordova.plugins.CordovaDemoPlugin;

}
```

# 3.Get License Configuration From SDK. It Returns All Active Functionalities Of Your License.
### Setting up License
```
function getMetadata() {
    $("#ocr-img, #fm-img").fadeOut();
    $("#back-btn").fadeIn();
    accura.getMetadata(function(results) {
        if(results.isValid) {
//            alert("License Loaded " + results.sdkVersion);
            $("#main-div").fadeIn();

            if(results.isMRZ) {
                for (let i = 0; i < mrzNames.length; i++) {
                    $("#tables").append(
                        "<thead class='mrz-head'><th><button class='btn btn-round' style='width:100%;height:50px;background-color: #818589;color: white;font-size: 20px;' onclick='startMRZ(this.value)' value='" + mrzTypes[i] + "'>" + mrzNames[i] + "</button></th></tr></thead>"
                    )
                }
            }

            if (results.isBarcode) {
                results.barcodes.forEach(function (barcode, i) {
                    barcodesTypes.push(barcode.type);
                    barcodesNames.push(barcode.name);
                    if (i === 0) {
                        barcodeSelected = barcode.type;
                    }
                    $('#barcode-types').append(
                        '<option value="' + barcode.type + '">' + barcode.name + '</option>'
                    )
                })
                $("#tables").append(
                    "<thead class='barcode-head'><th><button class='btn btn-round' style='width:100%;height:50px;background-color: #818589;color: white;font-size: 20px;' onclick='startBarcode1()'>Barcode</button></th></tr></thead>"
                )
            }

            if (results.isBankcard) {
                $("#tables").append(
                        "<thead class='bank-head'><th><button class='btn btn-round' style='width:100%;height:50px;background-color: #818589;color: white;font-size: 20px;' onclick='startBankcard()'>Bank Card</button></th></tr></thead>"
                )
            }

            if(results.isOCR) {
                if(results.countries.length > 0) {
                    countries = results.countries;
                    results.countries.forEach(function (country, i) {
                        var uid = i + "_" + country.id;
                        if(i === 0) {
                            countrySelected = countrySelectedForCard = uid;
                        }
                        $("#tables").append(
                            "<thead class='ocr-head'><th><button class='btn btn-danger btn-round' style='width:100%;height:50px;color: white;font-size: 20px;' onclick='getCardModal(this.id)' id='" + i + "'>" + country.name + "</button></th></tr></thead>"
                        )
                    })
                }
            }

        } else {
            alert("License Not Loaded");
        }
    }, function(error) {
        console.log("error FAIL", error);
    });
}
```

Success: JSON Response = {

countries: Array[<CountryModels>],

barcodes: Array[],

isValid: boolean,

isOCR: boolean,

isMRZ: boolean,

isBarcode: boolean,

isBankcard: boolean,

sdkVersion: String

}

Error: String

### Setting Up Configuration's, Error Messages And Scaning Title Messages

```
function setupAccuraConfig() {
    if (device.platform == 'iOS') {
        console.log("device.platform == 'iOS''");
        var recogEngineConfig = {
            setBlurPercentage: 60,
            setFaceBlurPercentage: 80,
            setGlarePercentage1: 6,
            setGlarePercentage2: 98,
            isCheckPhotoCopy: false,
            SetHologramDetection: true,
            setLowLightTolerance: 10,
            setMotionThreshold: 25
        }
    } else {
        console.log("device.platform != 'iOS''");
        var recogEngineConfig = {
            setBlurPercentage: 50,
            setFaceBlurPercentage: 50,
            setGlarePercentage1: 6,
            setGlarePercentage2: 98,
            isCheckPhotoCopy: false,
            SetHologramDetection: true,
            setLowLightTolerance: 30,
            setMotionThreshold: 18
        }
    }

    var config = {
        ACCURA_ERROR_CODE_MOTION: "Keep Document Steady",
        ACCURA_ERROR_CODE_DOCUMENT_IN_FRAME: "Keep document in frame",
        ACCURA_ERROR_CODE_BRING_DOCUMENT_IN_FRAME: "Bring card near to frame",
        ACCURA_ERROR_CODE_PROCESSING: "Processing",
        ACCURA_ERROR_CODE_BLUR_DOCUMENT: "Blur detect in document",
        ACCURA_ERROR_CODE_FACE_BLUR: "Blur detected over face",
        ACCURA_ERROR_CODE_GLARE_DOCUMENT: "Glare detect in document",
        ACCURA_ERROR_CODE_HOLOGRAM: "Hologram Detected",
        ACCURA_ERROR_CODE_DARK_DOCUMENT: "Low lighting detected",
        ACCURA_ERROR_CODE_PHOTO_COPY_DOCUMENT: "Can not accept Photo Copy Document",
        ACCURA_ERROR_CODE_FACE: "Face not detected",
        ACCURA_ERROR_CODE_MRZ: "MRZ not detected",
        ACCURA_ERROR_CODE_PASSPORT_MRZ: "Passport MRZ not detected",
        ACCURA_ERROR_CODE_ID_MRZ: "ID card MRZ not detected",
        ACCURA_ERROR_CODE_VISA_MRZ: "Visa MRZ not detected",
        ACCURA_ERROR_CODE_WRONG_SIDE: "Scanning wrong side of document",
        ACCURA_ERROR_CODE_UPSIDE_DOWN_SIDE: "Document is upside down. Place it properly",
        SCAN_TITLE_OCR_FRONT: "Scan Front Side of ",
        SCAN_TITLE_OCR_BACK: "Scan Back Side of ",
        SCAN_TITLE_OCR: "Scan ",
        SCAN_TITLE_MRZ_PDF417_FRONT: "Scan Front Side of Document",
        SCAN_TITLE_MRZ_PDF417_BACK: "Now Scan Back Side of Document",
        SCAN_TITLE_DLPLATE: "Scan Number Plate",
        SCAN_BARCODE: "Scan Barcode",
        SCAN_BANKCARD: "Scan BankCard",
        IS_SHOW_LOGO: 1,
        CAMERA_BG_COLOR: "#80000000",
        CAMERA_BORDER_COLOR: "#D5323F",
        CAMERA_FLIP_IMAGE: 1,
        CAMERA_BACK_BTN: 1,
        CAMERA_TEXT_COLOR: "#FFFFFF",
        CAMERA_TEXT_BORDER_COLOR: "#000000",
        CAMERA_FLIP_ANIMATION: 1,

        feedbackTextSize: 18,
        feedBackframeMessage: "Frame Your Face",
        feedBackAwayMessage: "Move Phone Away",
        feedBackOpenEyesMessage: "Keep Your Eyes Open",
        feedBackCloserMessage: "Move Phone Closer",
        feedBackCenterMessage: "Move Phone Center",
        feedBackMultipleFaceMessage: "Multiple Face Detected",
        feedBackHeadStraightMessage: "Keep Your Head Straight",
        feedBackBlurFaceMessage: "Blur Detected Over Face",
        feedBackGlareFaceMessage: "Glare Detected",
        feedBackLowLightMessage: "Low light detected",
        showlogo: 1,
        livenessURL: "Your Liveness URL",
        setLowLightTolerence: -1,
        setBlurPercentage: 80,
        setGlarePercentage1: -1,
        setGlarePercentage2: -1,

        fmfeedbackTextSize: 18.0,
        fmfeedBackframeMessage: "Frame Your Face",
        fmfeedBackAwayMessage: "Move Phone Away",
        fmfeedBackOpenEyesMessage: "Keep Your Eyes Open",
        fmfeedBackCloserMessage: "Move Phone Closer",
        fmfeedBackCenterMessage: "Move Phone Center",
        fmfeedBackMultipleFaceMessage: "Multiple Face Detected",
        fmfeedBackHeadStraightMessage: "Keep Your Head Straight",
        fmfeedBackBlurFaceMessage: "Blur Detected Over Face",
        fmfeedBackGlareFaceMessage: "Glare Detected",
        fmfeedBackLowLightMessage: "Low light detected",
        fmshowlogo: 1,
        fmsetBlurPercentage: 80,
        fmsetGlarePercentage1: -1,
        fmsetGlarePercentage2: -1

    }

    accura.setupAccuraConfig(config, recogEngineConfig, function(result) {
        console.log("Message:", result);
    }, function(error) {
        console.log("setupAccuraConfig FAIL");
    });
}
```

# 4.Method For Scan MRZ Documents.
```
function startMRZ(value) {
    accura.startMRZ({enableLogs: false}, value, mrzCountryList, function(result) {
        generateResult(result);
    }, function(error) {
        console.log("startMRZ FAIL", error)
    })
}
```
#### MRZType: String
#### value: passport_mrz or id_mrz or visa_mrz or other_mrz

MRZType: String

default: other_mrz

CountryList: String

default: all or IND,USA

Success: JSON Response {

front_data: JSONObjects?,

back_data: JSONObjects?,

type: Recognition Type,

face: URI?

front_img: URI?

back_img: URI?

}

Error: String

# 5.Method For Scan OCR Documents.
```
function openForCard(id) {

    accura.startOCR({enableLogs: false}, countrySelectedId, countryCard[id].id, countryCard[id].name, countryCard[id].type, function(result) {
        generateResult(result);
    }, function(error) {
        console.log(error);
    });

}
```
CountryId: integer

value: Id of selected country.

CardId: integer

value: Id of selected card.

CardName: String

value: Name of selected card.

CardType: integer

value: Type of selected card.

Success: JSON Response { }

Error: String

# 6.Method For Scan Barcode.
```
function startBarcode(id) {
    accura.startBarcode({ enableLogs: false }, id, function (results) {
        generateResult(results);
    }, function (error) {
        alert(error);
    });
}
```

BarcodeSelected: String

value: Type of barcode documents.

Success: JSON Response { }

Error: String

# 7.Method For Scan Bankcard.
```
function startBankcard() {
    accura.startBankcard({ enableLogs: false }, function (results) {
        generateResult(results);
    }, function (error) {
        alert(error);
    });
}
```
Success: JSON Response { }

Error: String

# 7.Method For Getting Face Match Percentage between two face.
```
function startFaceMatch() {

    var accuraConfig = {enableLogs:false, face_uri: facematchURI};

    accura.startFaceMatch(accuraConfig, function(result) {
            $("#score-div").fadeIn("fast", function() {
                $("#score-div").css("display", "flex")
            });
            $("#ls-score").text("0.00 %");
            if(result.hasOwnProperty("detect")) {
                $("#detect-face").fadeIn();
                getImage("detect-face", result.detect);
                $("#img-div").addClass("justify-content-between");
            }
            if(result.hasOwnProperty("score")) {
                $("#fm-score").text(Number(result.score).toFixed(2) + "%");
            }

    }, function(error) {
        alert(error);
    });

}
```
accuraConfig: JSON Object

face_uri: URI

Success: JSON Response { detect: URI? score: Float }

Error: String

# 8.Method For Liveness Check.
```
function startLiveness() {
    var accuraConfig = {enableLogs:false, with_face: true, face_uri: facematchURI};

    accura.startLiveness(accuraConfig, function(result) {
            $("#score-div").fadeIn("fast", function() {
                $("#score-div").css("display", "flex")
            });
            $("#ls-score").text(Number(result.score).toFixed(2) + "%");
            if(result.hasOwnProperty("detect")) {
                $("#detect-face").fadeIn();
                getImage("detect-face", result.detect);
                $("#img-div").addClass("justify-content-between");
            }
            if(result.hasOwnProperty("face_score")) {
                $("#fm-score").text(Number(result.face_score).toFixed(2) + "%");
            }

    }, function(error) {
        console.log(error);
    });

}
```
accuraConfig: JSON Object

face_uri: 'uri of face'

Success: JSON Response {

detect: URI?,

Face_score: Float,

score: Float,

}

Error: String

# 9.Method For Only Facematch.(The Following Are Optional Methods, Use If You Need Only FaceMatch)
## To Open Gallery 1 And 2:-
*For Gallery 1*
```
function openGallery1() {
    var accuraConfig = {
        face_uri1: facematchURI1,
        face_uri2: facematchURI2
    }

    accura.openGallery1(accuraConfig, function(result) {
        if(result.hasOwnProperty("fmImage1")) {
            getImage("face1", result.fmImage1);
            facematchURI1 = result.fmImage1;
        }
        if(result.hasOwnProperty("fm_score")) {
            $("#fm-score1").fadeIn();
            $("#fm-score1").text(Number(result.fm_score).toFixed(2) + "%");
        }
    }, function(error) {
        console.log("openGallery1 FAIL" + error);
    });
}
```

*For Gallery 2*
```
function openGallery2() {
    var accuraConfig = {
        face_uri1: facematchURI1,
        face_uri2: facematchURI2
    }

    accura.openGallery2(accuraConfig, function(result) {
        if(result.hasOwnProperty("fmImage2")) {
            getImage("face2", result.fmImage2);
            facematchURI2 = result.fmImage2;
        }
        if(result.hasOwnProperty("fm_score")) {
            $("#fm-score1").fadeIn();
            $("#fm-score1").text(Number(result.fm_score).toFixed(2) + "%");
        }
    }, function(error) {
        console.log("openGallery2 FAIL", error);
    });

}
```

## To Open Camera For Facematch 1 And 2:
*For Facematch 1:*
```
function startFacematch1() {
    var accuraConfig = {
        face_uri1: facematchURI1,
        face_uri2: facematchURI2
    }

    accura.startFacematch1(accuraConfig, function(result) {
        if(result.hasOwnProperty("fmImage1")) {
            getImage("face1", result.fmImage1);
            facematchURI1 = result.fmImage1;
        }
        if(result.hasOwnProperty("fm_score")) {
            $("#fm-score1").fadeIn();
            $("#fm-score1").text(Number(result.fm_score).toFixed(2) + "%");
        }
    }, function(error) {
        console.log("startFacematch1 FAIL", error);
    });
}
```

*For Facematch 2:*
```
function startFacematch2() {    
    var accuraConfig = {
        face_uri1: facematchURI1,
        face_uri2: facematchURI2
    }

    accura.startFacematch2(accuraConfig, function(result) {
        if(result.hasOwnProperty("fmImage2")) {
            getImage("face2", result.fmImage2);
            facematchURI2 = result.fmImage2;
        }
        if(result.hasOwnProperty("fm_score")) {
            $("#fm-score1").fadeIn();
            $("#fm-score1").text(Number(result.fm_score).toFixed(2) + "%");
        }
    }, function(error) {
        console.log("startFacematch2 FAIL ", error)
    });
}
```
accuraConfig: JSON Object

face_uri1: 'uri of face1'

face_uri2: ’uri of face2’

Success: JSON Response {

fmImage1 - fmImage2: URI?,

fm_score: String, 

}

Error: String

# 10. To Remove Cordova Plugin
` $ cordova plugin rm cordova-demo `
