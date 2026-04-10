cmake_minimum_required(VERSION 3.25)

project(mwm LANGUAGES C)


add_library(CommonFlags INTERFACE)

target_compile_options(CommonFlags INTERFACE
        -fno-strict-aliasing
        -fstack-protector-strong
        -Wall
        -Wdate-time
        -Werror=format-security
        -Wformat
        -Wno-comment
        -Wno-unused
)

target_compile_definitions(CommonFlags INTERFACE
        _FILE_OFFSET_BITS=64
        _FORTIFY_SOURCE=2
        HAVE_CONFIG_H
)


set(MWMSRC "clients/mwm")
set(WSMLIB "${MWMSRC}/WmWsmLib")


add_library(Wsm STATIC)

target_sources(Wsm
        PRIVATE
        ${WSMLIB}/debug.c
        ${WSMLIB}/disp.c
        ${WSMLIB}/free.c
        ${WSMLIB}/pack.c
        ${WSMLIB}/recv.c
        ${WSMLIB}/send.c
        ${WSMLIB}/util.c
        ${WSMLIB}/utm_send.c
        PRIVATE
        FILE_SET HEADERS
        TYPE HEADERS
        FILES
        ${WSMLIB}/utm_send.h
        ${WSMLIB}/wsm_funcs.h
        ${WSMLIB}/wsm_proto.h
)

target_link_libraries(Wsm PUBLIC CommonFlags)


add_executable(mwm)

target_sources(mwm
        PRIVATE
        ${MWMSRC}/WmCDecor.c
        ${MWMSRC}/WmCDInfo.c
        ${MWMSRC}/WmCEvent.c
        ${MWMSRC}/WmCPlace.c
        ${MWMSRC}/WmCmd.c
        ${MWMSRC}/WmColormap.c
        ${MWMSRC}/WmError.c
        ${MWMSRC}/WmEvent.c
        ${MWMSRC}/WmFeedback.c
        ${MWMSRC}/WmFunction.c
        ${MWMSRC}/WmGraphics.c
        ${MWMSRC}/WmIDecor.c
        ${MWMSRC}/WmIPlace.c
        ${MWMSRC}/WmIconBox.c
        ${MWMSRC}/WmImage.c
        ${MWMSRC}/WmInitWs.c
        ${MWMSRC}/WmKeyFocus.c
        ${MWMSRC}/WmMain.c
        ${MWMSRC}/WmManage.c
        ${MWMSRC}/WmMenu.c
        ${MWMSRC}/WmProperty.c
        ${MWMSRC}/WmProtocol.c
        ${MWMSRC}/WmResCvt.c
        ${MWMSRC}/WmResParse.c
        ${MWMSRC}/WmResource.c
        ${MWMSRC}/WmSignal.c
        ${MWMSRC}/WmWinConf.c
        ${MWMSRC}/WmWinInfo.c
        ${MWMSRC}/WmWinList.c
        ${MWMSRC}/WmWinState.c
        ${MWMSRC}/WmWsm.c
        ${MWMSRC}/WmXSMP.c
        ${MWMSRC}/version.c
)

target_compile_options(mwm PRIVATE
        -I${PROJECT_SOURCE_DIR}/include
        -I${PROJECT_SOURCE_DIR}/lib
)

target_compile_definitions(mwm PRIVATE
        BITMAPDIR="/usr/include/X11/bitmaps"
        CDE_CONFIGURATION_TOP="/usr/etc/dt"
        CDE_INSTALLATION_TOP="/usr/dt"
        LARGECURSORS
        MWMRCDIR="/etc/X11/mwm"
        R2_COMPAT
        UNMAP_ON_RESTART
)

target_link_libraries(mwm
        PUBLIC CommonFlags
        PRIVATE Wsm
        PUBLIC Xm Xt Xext X11
)
