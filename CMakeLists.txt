# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.20.0)

find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
project(binding_test)

target_sources(app PRIVATE src/main.c src/dhcp.c)

# Fulup note: module specific include declaration should be within rp-lib & libafb repo not within application
include_directories(${CMAKE_SOURCE_DIR}/../modules/lib/rp-lib-utils/src)
include_directories(${CMAKE_SOURCE_DIR}/../modules/lib/afb-libafb/src/libafb)
