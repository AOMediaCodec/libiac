cmake_minimum_required(VERSION 3.6)

project (iamfdec)

message(status,"+++++++++++iamfdec+++++++++++++")

option(MULTICHANNEL_BINAURALIZER "Enable multichannel binaural rendering" OFF)
option(HOA_BINAURALIZER "Enable HOA binaural rendering" OFF)
option(SUPPORT_VERIFIER "Output vlogging file" OFF)

include_directories (include)
aux_source_directory (src DIR_IAMFDEC_SRC)

set(LIB_DIR  "${CMAKE_INSTALL_PREFIX}/lib")
set(INCLUDE_IAMF_DIR  "${CMAKE_INSTALL_PREFIX}/include/iamf")
set(INCLUDE_DEP_EXTERNAL_WAV_DIR  "../../../dep_external/include/wav")
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Werror=unused-variable")
if((CMAKE_SYSTEM_NAME MATCHES "Darwin") OR (CMAKE_SYSTEM_NAME MATCHES "Android"))
else()
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wl,--no-as-need")
endif()

if(MULTICHANNEL_BINAURALIZER)
  message(STATUS "Enable multichannel binaural rendering")
  add_definitions(-DENABLE_MULTICHANNEL_TO_BINAURAL=1)
endif()

if(HOA_BINAURALIZER)
  message(STATUS "Enable HOA binaural rendering")
  add_definitions(-DENABLE_HOA_TO_BINAURAL=1)
endif()

if(SUPPORT_VERIFIER)
  message(STATUS "Output vlogging file")
  add_definitions(-DSUPPORT_VERIFIER=1)
endif()


message(status,${INCLUDE_IAMF_DIR})
include_directories(
    ${INCLUDE_IAMF_DIR}
    ${INCLUDE_DEP_EXTERNAL_WAV_DIR}
)

if(MULTICHANNEL_BINAURALIZER OR HOA_BINAURALIZER)
link_directories(
  ${LIB_DIR}/binaural
  ${LIB_DIR}
)
else()
link_directories(
  ${LIB_DIR}
)
endif()

add_executable (iamfdec ${DIR_IAMFDEC_SRC})

if(MULTICHANNEL_BINAURALIZER OR HOA_BINAURALIZER)
  if(MULTICHANNEL_BINAURALIZER)
    target_link_libraries (iamfdec iamf m iamf2bear efl objectmodel panning pml rbbl rcl rrl visr)
  endif()
  if(HOA_BINAURALIZER)
    target_link_libraries (iamfdec iamf m iamf2resonance)
  endif()
else()
  target_link_libraries (iamfdec iamf m)
endif()

