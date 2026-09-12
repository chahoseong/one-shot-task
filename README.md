# OneShotTask

> 멀티태스킹을 멈추고, 지금 당장 가장 중요한 **'단 하나의 작업(The One Task)'**에 온전히 몰입(One-Shot)할 수 있도록 돕는 안드로이드 집중 도구입니다.

---

## 🛠️ 개발 환경 설정

### Android CLI

본 프로젝트는 AI 에이전트 및 터미널 기반 개발 자동화를 위해 **Google Android CLI**를 사용합니다. 자세한 소개와 사용법은 [Android CLI 공식 문서](https://developer.android.com/tools/agents/android-cli)를 참고하세요.

#### 1. 설치

터미널(PowerShell 또는 CMD)에서 OS에 맞는 설치 명령어를 실행합니다.

* **Windows:**
  ```cmd
  curl -fsSL https://dl.google.com/android/cli/latest/windows_x86_64/install.cmd -o "%TEMP%\i.cmd" && "%TEMP%\i.cmd"
  ```
* **macOS (Apple Silicon):**
  ```bash
  curl -fsSL https://dl.google.com/android/cli/latest/darwin_arm64/install.sh | bash
  ```
* **Linux:**
  ```bash
  curl -fsSL https://dl.google.com/android/cli/latest/linux_x86_64/install.sh | bash
  ```

#### 2. 초기화 및 환경 점검

새 터미널 창을 열고 아래 명령어를 실행하여 CLI 환경과 에이전트 스킬을 초기화합니다.

```bash
# 1. Android CLI 환경 및 기본 스킬 초기화
android init

# 2. 설치 확인
android --version
```

### 안드로이드 디바이스 무선 디버깅

안드로이드 11 이상 기기를 케이블 없이 무선으로 연결하여 빌드 및 디버깅하는 방법입니다.

#### 1. 사전 준비: adb 환경 변수 등록

터미널 어디서든 `adb` 명령어를 사용할 수 있도록 Android SDK의 `platform-tools` 경로를 Path에 등록합니다.

* **Windows (PowerShell):**
  ```powershell
  [Environment]::SetEnvironmentVariable("Path", $env:Path + ";$env:LOCALAPPDATA\Android\Sdk\platform-tools", "User")
  ```
* **macOS:**
  ```bash
  echo 'export PATH=$PATH:$HOME/Library/Android/sdk/platform-tools' >> ~/.zshrc && source ~/.zshrc
  ```
* **설치 확인:**
  ```bash
  adb --version
  ```

#### 2. 디바이스 설정

스마트폰의 **설정 ➡️ 개발자 옵션 ➡️ '무선 디버깅'**을 활성화합니다.

#### 3. 연결 방법

##### Case A: 동일한 Wi-Fi 네트워크인 경우 (기본)
1. **페어링 (최초 1회):**
   - 스마트폰: `기기와 페어링 코드로 페어링` 터치 ➡️ 6자리 코드 및 `IP:포트` 확인
   - PC 터미널:
     ```bash
     adb pair <기기_IP>:<페어링_포트>
     # 6자리 페어링 코드 입력
     ```
2. **연결:**
   - 스마트폰: 무선 디버깅 메인 화면의 `IP 주소 및 포트` 확인 *(페어링 포트와 다름에 주의)*
   - PC 터미널:
     ```bash
     adb connect <기기_IP>:<디버깅_포트>
     ```

##### Case B: 다른 네트워크 환경인 경우 (Tailscale 활용)
PC와 스마트폰이 서로 다른 네트워크(예: PC는 유선랜/회사망, 스마트폰은 LTE/5G)에 있을 때 사용합니다.

1. **준비:** PC와 스마트폰에 [Tailscale](https://tailscale.com)을 설치하고 동일 계정으로 로그인합니다.
2. **연결:** 스마트폰 Tailscale 앱에 표시된 가상 IP(`100.x.y.z`)를 확인한 후 연결합니다.
   ```bash
   adb connect <스마트폰_Tailscale_IP>:<디버깅_포트>
   ```

#### 4. 연결 상태 확인

```bash
adb devices
```