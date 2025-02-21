cmake_minimum_required(VERSION 2.8)

if(NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  if(DEFINED ENV{VITASDK})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
  else()
    message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
  endif()
endif()

project(VitaDB C CXX)
include("${VITASDK}/share/vita.cmake" REQUIRED)
set(VITA_APP_NAME "VitaDB Downloader")
set(VITA_TITLEID  "VITADBDLD")
set(VITA_VERSION  "01.00")

execute_process(COMMAND git describe --abbrev=6 --dirty --always
	OUTPUT_VARIABLE GIT_HASH
	OUTPUT_STRIP_TRAILING_WHITESPACE
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fno-optimize-sibling-calls -fdiagnostics-color=always -g -Wl,-q,--allow-multiple-definition -D_GNU_SOURCE -Wall -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-optimize-sibling-calls -fpermissive -g -fdiagnostics-color=always -Wno-write-strings -D_GNU_SOURCE -std=c++11")

add_executable(VitaDB
  source/dialogs.cpp
  source/main.cpp
  source/network.cpp
  source/player.cpp
  source/promoter.cpp
  source/utils.cpp
  source/ioapi.c
  source/unzip.c
  source/sha1.c
  source/md5.c
)

target_link_libraries(VitaDB
  -Wl,--whole-archive pthread -Wl,--no-whole-archive
  imgui
  m
  z
  stdc++
  vitaGL
  vitashark
  SceShaccCgExt
  mathneon
  curl
  ssl
  SDL2_mixer
  SDL2
  vorbisfile
  vorbis
  ogg
  mpg123
  mikmod
  modplug
  FLAC
  crypto
  taihen_stub
  kubridge_stub
  SceAvPlayer_stub
  SceAppMgr_stub
  SceAppUtil_stub
  SceAudio_stub
  SceAudioIn_stub
  SceCtrl_stub
  SceCommonDialog_stub
  SceDisplay_stub
  SceKernelDmacMgr_stub
  SceGxm_stub
  SceNet_stub
  SceShaccCg_stub
  SceSysmodule_stub
  ScePower_stub
  SceTouch_stub
  SceVshBridge_stub
  ScePromoterUtil_stub
  SceMotion_stub
  SceIme_stub
  SceHid_stub
)

vita_create_self(eboot.bin VitaDB UNSAFE)
vita_create_vpk(VitaDB.vpk ${VITA_TITLEID} eboot.bin
  VERSION ${VITA_VERSION}
  NAME ${VITA_APP_NAME}
  FILE ${CMAKE_SOURCE_DIR}/livearea/icon0.png sce_sys/icon0.png
       ${CMAKE_SOURCE_DIR}/livearea/default_gate.png sce_sys/livearea/contents/default_gate.png
       ${CMAKE_SOURCE_DIR}/livearea/bg0.png sce_sys/livearea/contents/bg0.png
       ${CMAKE_SOURCE_DIR}/livearea/template.xml sce_sys/livearea/contents/template.xml
	   ${CMAKE_SOURCE_DIR}/Roboto.ttf Roboto.ttf
)
