# Computer Graphics

VSCode + MSYS2 + vcpkg 기반 OpenGL 실습 프로젝트

---

## 개발 환경

| 항목 | 내용 |
|------|------|
| OS | Windows 11 |
| 편집기 | VSCode |
| 컴파일러 | MSYS2 UCRT64 (g++) |
| 빌드 시스템 | CMake + Ninja |
| 패키지 관리 | vcpkg (x64-mingw-dynamic) |

---

## 사전 설치 항목

> **C/C++ 컴파일러만 사용할 경우 (CMake 없이):** 1번만 설치하면 됩니다. 단, 프로젝트 루트의 `CMakeLists.txt`를 삭제해야 VSCode가 CMake 프로젝트로 인식하지 않습니다.

### 1. MSYS2
- [msys2.org](https://www.msys2.org) 에서 설치
- **MSYS2 UCRT64** 터미널에서 아래 명령어 실행:

```bash
pacman -Syu
pacman -S mingw-w64-ucrt-x86_64-toolchain
pacman -S mingw-w64-ucrt-x86_64-cmake
pacman -S mingw-w64-ucrt-x86_64-ninja
```

- 환경변수 PATH에 추가:
```
C:\msys64\ucrt64\bin
```

### 2. vcpkg
PowerShell:
```powershell
mkdir -Force C:\Dev
cd C:\Dev
git clone https://github.com/microsoft/vcpkg vcpkg
cd vcpkg
.\bootstrap-vcpkg.bat
```

CMD:
```cmd
mkdir C:\Dev
cd C:\Dev
git clone https://github.com/microsoft/vcpkg vcpkg
cd vcpkg
bootstrap-vcpkg.bat
```

- 라이브러리 설치:
```powershell
C:\Dev\vcpkg\vcpkg.exe install glfw3:x64-mingw-dynamic gl3w:x64-mingw-dynamic glm:x64-mingw-dynamic fmt:x64-mingw-dynamic --host-triplet=x64-mingw-dynamic
```

### 3. VSCode 확장
- C/C++ (Microsoft)
- CMake Tools (Microsoft)
- CMake (twxs)

---

## 프로젝트 구조

```
Computer_Graphics/
├── Lab1/
│   ├── source/
│   │   ├── Source.cpp
│   │   ├── MyGlWindow.cpp
│   │   ├── MyGlWindow.h
│   │   └── Loader.h
│   └── shaders/
│       ├── simple.vert
│       └── simple.frag
├── CMakeLists.txt
├── .vscode/
│   ├── settings.json
│   ├── tasks.json
│   ├── launch.json
│   └── c_cpp_properties.json
└── README.md
```

---

## 실습 전환 방법

`CMakeLists.txt` 상단의 `CURRENT` 변수만 바꾸면 됩니다.

```cmake
set(CURRENT Lab1)  # Lab2, Lab3 ... 로 변경
```

---

## VSCode 유저 설정

`Ctrl+Shift+P` → `Open User Settings (JSON)` 에 아래 내용 추가:

```json
"launch": {
    "configurations": []
},
"cmake.options.advanced": {
    "build": { "statusBarVisibility": "inherit", "inheritDefault": "visible" },
    "launch": { "statusBarVisibility": "inherit", "inheritDefault": "visible" },
    "debug": { "statusBarVisibility": "inherit", "inheritDefault": "visible" }
},
"cmake.buildDirectory": "${workspaceFolder}/build",
"cmake.generator": "Ninja",
"cmake.configureArgs": [
    "-DCMAKE_TOOLCHAIN_FILE=C:/Dev/vcpkg/scripts/buildsystems/vcpkg.cmake",
    "-DVCPKG_TARGET_TRIPLET=x64-mingw-dynamic",
    "-DVCPKG_HOST_TRIPLET=x64-mingw-dynamic",
    "-DCMAKE_C_COMPILER=C:/msys64/ucrt64/bin/gcc.exe",
    "-DCMAKE_CXX_COMPILER=C:/msys64/ucrt64/bin/g++.exe",
    "-DCMAKE_MAKE_PROGRAM=C:/msys64/ucrt64/bin/ninja.exe"
]
```

---

## VSCode 키바인딩 설정

`Ctrl+Shift+P` → `Open Keyboard Shortcuts (JSON)` 에 아래 내용으로 교체:

```json
[
    {
        "key": "ctrl+alt+c",
        "command": "workbench.action.tasks.build"
    },
    {
        "key": "ctrl+alt+r",
        "command": "workbench.action.tasks.test"
    },
    {
        "key": "f5",
        "command": "workbench.action.debug.run",
        "when": "debuggersAvailable && debugState != 'initializing'"
    },
    {
        "key": "ctrl+f5",
        "command": "-workbench.action.debug.run",
        "when": "debuggersAvailable && debugState != 'initializing'"
    },
    {
        "key": "f5",
        "command": "cmake.launchTarget",
        "when": "cmake:enableFullFeatureSet && !cmake:hideDebugCommand && !inDebugMode"
    },
    {
        "key": "ctrl+f5",
        "command": "-cmake.launchTarget",
        "when": "cmake:enableFullFeatureSet && !cmake:hideDebugCommand && !inDebugMode"
    },
    {
        "key": "ctrl+f5",
        "command": "workbench.action.debug.start",
        "when": "debuggersAvailable && debugState == 'inactive'"
    },
    {
        "key": "f5",
        "command": "-workbench.action.debug.start",
        "when": "debuggersAvailable && debugState == 'inactive'"
    },
    {
        "key": "ctrl+f5",
        "command": "workbench.action.debug.continue",
        "when": "debugState == 'stopped'"
    },
    {
        "key": "f5",
        "command": "-workbench.action.debug.continue",
        "when": "debugState == 'stopped'"
    }
]
```

> F5 = 디버거 없이 직접 실행, Ctrl+F5 = 디버거 실행

---

## CMake Tools 초기 설정 (처음 열 때)

1. `Ctrl+Shift+P` → `CMake: Configure` 실행
2. `Ctrl+Shift+P` → `Tasks: Run Task` → `CMake: build (ninja)` 실행
3. `Ctrl+Shift+P` → `CMake: Set Launch/Debug Target` → `demo` 선택
4. 이후 F5로 실행

---

## 빌드 및 실행

### 빌드
```
Ctrl+Shift+P → Tasks: Run Task → CMake: build (ninja)
```

### 실행
```
F5 → Run: CMake target (demo)
```

### 한 번에 빌드 + 실행
```
Ctrl+Shift+P → Tasks: Run Task → CMake: build & run
```

---

## 사용 라이브러리

| 라이브러리 | 용도 |
|-----------|------|
| glfw3 | 윈도우 생성 및 입력 처리 |
| gl3w | OpenGL 함수 로더 |
| glm | 수학 라이브러리 (벡터, 행렬) |
| fmt | 문자열 포맷 |

---

## 트러블슈팅

자세한 트러블슈팅은 [TROUBLESHOOTING.md](TROUBLESHOOTING.md) 참고.

| 증상 | 원인 | 해결 |
|------|------|------|
| `Building for: NMake Makefiles` | 잘못된 cmake 또는 VS generator | [TROUBLESHOOTING.md #1](TROUBLESHOOTING.md#1-별도-설치된-cmake-문제) |
| `glfw3 not found` | VCPKG_ROOT 환경변수 충돌 | [TROUBLESHOOTING.md #2](TROUBLESHOOTING.md#2-vcpkg_root-환경변수-충돌) |
| F5 눌러도 gdb로 실행됨 | CMake Tools kit/target 미설정 | [TROUBLESHOOTING.md #3](TROUBLESHOOTING.md#3-cmake-tools-kit--launch-target-설정) |
| generator 캐시 충돌 오류 | 기존 build 폴더 캐시 | build 폴더 삭제 후 재실행 |