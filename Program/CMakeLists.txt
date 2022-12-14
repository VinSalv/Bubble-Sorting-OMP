# 
# Course: High Performance Computing 2021/2022
# 
# Lecturer: Francesco Moscato	fmoscato@unisa.it
#
# Group:
# Lamberti      Martina     0622701476  m.lamberti61@studenti.unisa.it
# Salvati       Vincenzo    0622701550  v.salvati10@studenti.unisa.it
# Silvitelli    Daniele     0622701504  d.silvitelli@studenti.unisa.it
# Sorrentino    Alex        0622701480  a.sorrentino120@studenti.unisa.it
#
# Copyright (C) 2021 - All Rights Reserved
#
# This file is part of EsameHPC.
#
# Contest-OMP is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# Contest-OMP is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with Contest-OMP.  If not, see <http://www.gnu.org/licenses/>.
#

#
#   @file CMakeLists.txt
#

cmake_minimum_required(VERSION 3.9)
project(ContestOMP
	DESCRIPTION "Radix Sort implementation with OpenMP"
	VERSION 0.1
	LANGUAGES C)

enable_testing()

file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/data/random_numbers.txt
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

add_executable(program_O1 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_O2 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_O3 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_seq_O0 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_seq_O1 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_seq_O2 src/main.c src/radixsort.c include/radixsort.h)
add_executable(program_seq_O3 src/main.c src/radixsort.c include/radixsort.h)
add_executable(radixsort_test test/test.c src/radixsort.c)

target_include_directories(program_O1 PRIVATE include)
target_include_directories(program_O2 PRIVATE include)
target_include_directories(program_O3 PRIVATE include)
target_include_directories(program_seq_O0 PRIVATE include)
target_include_directories(program_seq_O1 PRIVATE include)
target_include_directories(program_seq_O2 PRIVATE include)
target_include_directories(program_seq_O3 PRIVATE include)
target_include_directories(radixsort_test PRIVATE include)

find_package(OpenMP)
if (OPENMP_FOUND)
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif()

find_package(OpenMP)
if(OpenMP_C_FOUND)
target_link_libraries(program_O1 PRIVATE OpenMP::OpenMP_C)
target_link_libraries(program_O2 PRIVATE OpenMP::OpenMP_C)
target_link_libraries(program_O3 PRIVATE OpenMP::OpenMP_C)
endif()

target_compile_options(program_O1 PRIVATE -O1)
target_compile_options(program_O2 PRIVATE -O2)
target_compile_options(program_O3 PRIVATE -O3)
target_compile_options(program_seq_O0 PRIVATE -O0)
target_compile_options(program_seq_O1 PRIVATE -O1)
target_compile_options(program_seq_O2 PRIVATE -O2)
target_compile_options(program_seq_O3 PRIVATE -O3)

target_compile_features(program_O1 PRIVATE c_std_11)
target_compile_features(program_O2 PRIVATE c_std_11)
target_compile_features(program_O3 PRIVATE c_std_11)
target_compile_features(program_seq_O0 PRIVATE c_std_11)
target_compile_features(program_seq_O1 PRIVATE c_std_11)
target_compile_features(program_seq_O2 PRIVATE c_std_11)
target_compile_features(program_seq_O3 PRIVATE c_std_11)
target_compile_features(radixsort_test PRIVATE c_std_11)

set(ELEMENT_TYPE int CACHE STRING "Type of the elements in array.")
target_compile_definitions(program_O1 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_O2 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_O3 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_seq_O0 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_seq_O1 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_seq_O2 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(program_seq_O3 PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})
target_compile_definitions(radixsort_test PRIVATE -DELEMENT_TYPE=${ELEMENT_TYPE})


# ---------------------------------------- TEST -----------------------------------------

add_test(NAME radixsort_test COMMAND radixsort_test)

# ------------------------------------ DOCUMENTATION ------------------------------------
find_package(Doxygen
	REQUIRED dot)

if(DOXYGEN_FOUND)
set(DOXYGEN_GENERATE_XML YES)
doxygen_add_docs(
	docs
	./src/radixsort.c ./include/radixsort.h ./test/test.c
	)
endif()

# ---------------------------------- GENERATE MEASURES ----------------------------------
add_custom_target(
	generate_measures
	COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/measures/measures.bash ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating measures [${CMAKE_CURRENT_SOURCE_DIR}/measures/measures.bash ${CMAKE_CURRENT_BINARY_DIR}]"
	DEPENDS	
	program_O1
	program_O2
	program_O3
	program_seq_O0
	program_seq_O1
	program_seq_O2
	program_seq_O3
	)

add_custom_target(
	change_ref
	COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/measures/measures.bash -p
	COMMENT "Change graph ref [${CMAKE_CURRENT_SOURCE_DIR}/measures/measures.bash -p]"
	)

# --------------------------------- MEASURES EXTRACTION ---------------------------------
find_package(Python3 COMPONENTS Interpreter REQUIRED)
add_custom_target(
	extract_measures
     COMMAND python3 ${CMAKE_CURRENT_SOURCE_DIR}/measures/extract.py
     COMMENT "Generating version header [python3 ${CMAKE_CURRENT_SOURCE_DIR}/measures/extract.py]"
     DEPENDS	
 		${CMAKE_CURRENT_SOURCE_DIR}/measures/extract.py
)