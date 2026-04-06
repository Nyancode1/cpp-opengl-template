# 트러블슈팅 가이드

---

## 1. 별도 설치된 CMake 문제

### 증상
```
-- Building for: NMake Makefiles
CMake Error: Running 'nmake' '-?' failed with: no such file or directory
```

### 원인
`C:\Program Files\CMake\bin`이 PATH에 있어서 MSYS2의 cmake 대신 별도 설치된 cmake가 실행됨.
이 cmake는 기본 generator가 NMake라서 Ninja 대신 NMake를 잡아버림.

### 해결
**방법 A (권장):** `C:\Program Files\CMake\bin`을 PATH에서 삭제

환경변수 편집 → 시스템 변수 Path → `C:\Program Files\CMake\bin` 선택 → 삭제 → VSCode 재시작

**방법 B:** PATH에서 `C:\msys64\ucrt64\bin`을 `C:\Program Files\CMake\bin`보다 위로 이동

터미널에서 확인:
```powershell
where cmake   # C:\msys64\ucrt64\bin\cmake.exe 가 먼저 나와야 함
where ninja
where g++
```

---

## 2. VCPKG_ROOT 환경변수 충돌

### 증상
```
Could not find a package configuration file provided by "glfw3"
```
또는
```
warning: ignoring mismatched VCPKG_ROOT environment value C:\vcpkg
```

### 원인
환경변수 `VCPKG_ROOT`가 실제 vcpkg 설치 경로와 다른 곳을 가리키고 있음.
예: vcpkg는 `C:\Dev\vcpkg`에 있는데 `VCPKG_ROOT=C:\vcpkg`로 설정되어 있는 경우.

### 해결
환경변수 편집 → `VCPKG_ROOT` 찾아서:
- `C:\Dev\vcpkg`로 수정하거나
- 삭제

이후 build 폴더 삭제 후 재실행:
```
rmdir /s /q build
```

---

## 3. CMake Tools Kit / Launch Target 설정

### 증상
- `CMake: Set Launch/Debug Target` 해도 아무것도 안 뜸
- F5 눌러도 gdb 디버거로 실행됨
- 하단 `▶` 버튼 눌러도 반응 없음

### 원인
CMake Tools가 올바른 kit을 사용하지 않거나 configure가 안 된 상태.

### 해결 순서

**1. VS2026 잔여 kit 제거**

`Ctrl+Shift+P` → `Edit User-Local CMake Kits` → VS2026 관련 항목 전부 삭제

삭제할 항목 예시:
```json
{
    "name": "Visual Studio Community 2026 Release - ...",
    "visualStudio": "0aec9d67",
    ...
}
```

**2. Kit 재스캔 및 선택**

```
Ctrl+Shift+P → CMake: Scan for Kits
Ctrl+Shift+P → CMake: Select a Kit → GCC 15.x.x x86_64-w64-mingw32 (ucrt64) 선택
```

**3. Configure 및 Build**

```
Ctrl+Shift+P → CMake: Configure
Ctrl+Shift+P → CMake: Build
```

이후 F5 정상 동작.

---

## 4. VS2022/VS2026 설치된 환경 (generator 캐시 충돌)

### 증상
```
Error: generator "Ninja" does not match previously used generator "Visual Studio 17 2022"
```

### 원인
이전에 Visual Studio generator로 configure된 build 폴더가 남아 있음.

### 해결
build 폴더 삭제 후 재configure:
```powershell
Remove-Item build -Recurse -Force
```
또는
```cmd
rmdir /s /q build
```

---

## 5. F5가 디버거(gdb)로 실행될 때

### 원인
키바인딩이 기본값이라 F5가 `Debug: Start Debugging`에 묶여 있음.

### 해결
README의 [키바인딩 설정](README.md#vscode-키바인딩-설정) 섹션대로 `keybindings.json` 교체.

F5 = 디버거 없이 직접 실행 (`cmake.launchTarget`)으로 바뀜.
