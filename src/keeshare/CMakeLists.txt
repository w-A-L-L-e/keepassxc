if(WITH_XC_KEESHARE)
    set(WITH_XC_KEESHARE_INSECURE ON PARENT_SCOPE)

    if(WITH_XC_KEESHARE_SECURE)
        find_package(QuaZip REQUIRED)
    else()
        # Try to find libquazip5, if found, enable secure sharing
        find_package(QuaZip QUIET)
        if(QUAZIP_INCLUDE_DIRS AND QUAZIP_LIBRARIES)
            message(STATUS "Found QUAZIP: Enabling WITH_XC_KEESHARE_SECURE")
            set(WITH_XC_KEESHARE_SECURE ON)
            set(WITH_XC_KEESHARE_SECURE ON PARENT_SCOPE)
        endif()
    endif()

    include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})

    set(keeshare_SOURCES
        SettingsPageKeeShare.cpp
        SettingsWidgetKeeShare.cpp
        DatabaseSettingsPageKeeShare.cpp
        DatabaseSettingsWidgetKeeShare.cpp
        group/EditGroupWidgetKeeShare.cpp
        group/EditGroupPageKeeShare.cpp
        KeeShare.cpp
        KeeShareSettings.cpp
        ShareObserver.cpp
        Signature.cpp
    )

    add_library(keeshare STATIC ${keeshare_SOURCES})
    if(WITH_XC_KEESHARE_SECURE)
        target_include_directories(keeshare SYSTEM PRIVATE ${QUAZIP_INCLUDE_DIR})
        target_link_libraries(keeshare Qt5::Core Qt5::Widgets ${GCRYPT_LIBRARIES} ${QUAZIP_LIBRARIES} ${crypto_ssh_LIB})
    else()
        target_link_libraries(keeshare Qt5::Core Qt5::Widgets ${GCRYPT_LIBRARIES} ${crypto_ssh_LIB})
    endif()
else(WITH_XC_KEESHARE)
    set(WITH_XC_KEESHARE_INSECURE OFF PARENT_SCOPE)
    set(WITH_XC_KEESHARE_SECURE OFF PARENT_SCOPE)
endif(WITH_XC_KEESHARE)
