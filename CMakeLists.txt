cmake_minimum_required(VERSION 3.21)
project(demo CXX)   # 프로젝트 이름도 demo로

# 현재 실습 폴더 (Lab1, Lab2... 로 바꾸면 됨)
set(CURRENT Lab9)

# ✅ C++20 표준 설정
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# vcpkg 패키지
find_package(glfw3 CONFIG REQUIRED)
find_package(gl3w  CONFIG REQUIRED)   # OpenGL 함수 로더
find_package(glm   CONFIG REQUIRED)
find_package(fmt   CONFIG REQUIRED)

# 소스 추가
file(GLOB_RECURSE SRC_FILES CONFIGURE_DEPENDS
    "${CMAKE_SOURCE_DIR}/${CURRENT}/*.cpp"
    "${CMAKE_SOURCE_DIR}/${CURRENT}/*.c"
    "${CMAKE_SOURCE_DIR}/${CURRENT}/*.h")
add_executable(demo ${SRC_FILES})   # 🔹 실행파일 이름 = demo.exe

# 헤더 경로(선택)
target_include_directories(demo PRIVATE "${CMAKE_SOURCE_DIR}/${CURRENT}")

# gl3w 타깃명 포트 차이 대응
if(TARGET gl3w::gl3w)
  target_link_libraries(demo PRIVATE gl3w::gl3w)
elseif(TARGET unofficial::gl3w::gl3w)
  target_link_libraries(demo PRIVATE unofficial::gl3w::gl3w)
else()
  message(FATAL_ERROR "gl3w target not found. Install gl3w via vcpkg.")
endif()

# 나머지 링크
target_link_libraries(demo PRIVATE glfw glm::glm fmt::fmt)

# Windows 시스템 OpenGL
if(WIN32)
  target_link_libraries(demo PRIVATE opengl32)
endif()

# 경고 옵션(선택)
if (MSVC)
  target_compile_options(demo PRIVATE /W4)
else()
  target_compile_options(demo PRIVATE -Wall -Wextra -Wpedantic)
endif()

if(EXISTS "${CMAKE_SOURCE_DIR}/${CURRENT}/shaders")
  file(COPY ${CMAKE_SOURCE_DIR}/${CURRENT}/shaders DESTINATION ${CMAKE_BINARY_DIR})
endif()
