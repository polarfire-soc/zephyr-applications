# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.20.0)

# Print out build type

find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})

project(smp_pi)

target_sources(app PRIVATE src/main.c)
