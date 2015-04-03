We are going to need everything from travis.yml ( in titanium_mobile)

language: objective-c
jdk:
  - oraclejdk7
install:
  - ANDROID_SDK_PACKAGE=android-sdk_r24.0.1-macosx.zip
  - ANDROID_NDK_PACKAGE=android-ndk-r8c-darwin-x86.tar.bz2
  - export TITANIUM_ANDROID_API="21"
  - # Need to install jq to process the JSON
  - brew update
  - brew install jq # process JSON 
  - brew install scons # for building SDK
  -
  - sudo npm install -g titanium
  -
  - export TITANIUM_ROOT=`ti sdk list -o json | jq -r '.defaultInstallLocation'`
  - export TITANIUM_SDK=`ti sdk list -o json | jq -r '.installed[.activeSDK]'`
  - mkdir -p "$TITANIUM_ROOT/sdks/"
  - 
  - export SDKS="$TRAVIS_BUILD_DIR/sdks"
  - echo $SDKS
  - mkdir -p "$SDKS" 
  - if [ ! -d "$SDKS/android-sdk-macosx" ]; then cd "$SDKS"; wget http://dl.google.com/android/$ANDROID_SDK_PACKAGE; unzip -o $ANDROID_SDK_PACKAGE; fi
  - export ANDROID_SDK=${PWD}/android-sdk-macosx
  - export PATH=${PATH}:${ANDROID_SDK}/tools:${ANDROID_SDK}/platform-tools
  -
  -   # Install required Android components.
  - echo yes | android -s update sdk --no-ui --all --filter tools
  - echo yes | android -s update sdk --no-ui --all --filter platform-tools
  - echo yes | android -s update sdk --no-ui --all --filter extra-android-support 
  - echo yes | android -s update sdk --no-ui --all --filter android-8
  - echo yes | android -s update sdk --no-ui --all --filter android-10
  - echo yes | android -s update sdk --no-ui --all --filter android-$TITANIUM_ANDROID_API
  - echo yes | android -s update sdk --no-ui --all --filter addon-google_apis-google-$TITANIUM_ANDROID_API
  -
  - # Install required Android NDK
  - if [ ! -d "$SDKS/android-ndk-r8c" ]; then wget http://dl.google.com/android/ndk/$ANDROID_NDK_PACKAGE; tar xzf $ANDROID_NDK_PACKAGE; fi
  - export ANDROID_NDK=${PWD}/android-ndk-r8c
  -
  - # Install required Titanium build
  - git clone https://github.com/sophrinix/titanium_build.git "$SDKS/titanium_build" 
  - ls "$SDKS/titanium_build"
  -
  - sudo easy_install pyyaml
  - sudo easy_install Pygments
  -
  - export ANDROID_PLATFORM=$ANDROID_SDK/platforms/android-$TITANIUM_ANDROID_API
  - export GOOGLE_APIS=$ANDROID_SDK/add-ons/addon-google_apis-google-$TITANIUM_ANDROID_API

script:
  - export TITANIUM_BUILD="$SDKS/titanium_build"
  - echo $TITANIUM_BUILD
  - cd $TRAVIS_BUILD_DIR
  - source $TITANIUM_BUILD/mobile/driver_travis.sh $TRAVIS_BRANCH
  - npm install
  - npm test
  - ls dist



see also: https://github.com/Sophrinix/titanium_build/tree/master/mobile
