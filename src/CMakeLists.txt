# Copyright (C) 2016-2019  Yannick Jadoul
#
# This file is part of Parselmouth.
#
# Parselmouth is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# Parselmouth is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with Parselmouth.  If not, see <http://www.gnu.org/licenses/>

include(add_sources)

include_directories(BEFORE ${CMAKE_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR})

if (UNIX)
	add_compile_options(-Wall -Wextra -Wno-unknown-pragmas -Wno-register) # error: ISO C++17 does not allow 'register' storage class specifier - in Python 2 header files
elseif (WIN32)
	add_compile_options(/wd5033) # "warning C5033: 'register' is no longer a supported storage class" - in Python 2 header files
endif()

add_sources(parselmouth.cpp)
add_subdirectory(parselmouth)

pybind11_add_module(parselmouth ${SRC})

set_target_properties(parselmouth PROPERTIES CXX_STANDARD 17 CXX_STANDARD_REQUIRED TRUE)

set_target_properties(praat PROPERTIES POSITION_INDEPENDENT_CODE TRUE)
# Fixing "ld: warning: direct access in function '...' from file '...' to global weak symbol '...' from file '...' means the weak symbol cannot
# be overridden at runtime. This was likely caused by different translation units being compiled with different visibility settings."
set_target_properties(praat PROPERTIES C_VISIBILITY_PRESET hidden CXX_VISIBILITY_PRESET hidden)

target_link_libraries(parselmouth PRIVATE praat)

install(TARGETS parselmouth DESTINATION ${CMAKE_INSTALL_PREFIX})
