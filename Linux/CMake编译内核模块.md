# CMake编译内核模块

```cmake
cmake_minimum_required(VERSION 2.8)
project(ldd)

set(KERNEL_DIR /usr/src/linux-headers-3.2.0-48-generic-pae)

function(compile_module obj)
  set(TARGET_NAME ${obj})
  add_custom_target(${TARGET_NAME} ALL cp -f ${CMAKE_CURRENT_SOURCE_DIR}/*.c ${CMAKE_CURRENT_BINARY_DIR} COMMAND echo "compiling module ${obj}.ko...")
  list(LENGTH ARGN argn_len)
  set(i 0)
  set(depend_objlist "")
  while( i LESS ${argn_len})  
	list(GET ARGN ${i} argn_value)  
	set(depend_objlist "${depend_objlist} ${argn_value}.o") 
	math(EXPR i "${i} + 1")  
  endwhile() 
  
  add_custom_command(TARGET ${TARGET_NAME}
		     POST_BUILD
		     COMMAND echo "obj-m := ${obj}.o" > ${CMAKE_CURRENT_BINARY_DIR}/Makefile
		     COMMAND echo "${obj}-objs:=${depend_objlist}" >>${CMAKE_CURRENT_BINARY_DIR}/Makefile
		     COMMAND make -C ${KERNEL_DIR} M=${CMAKE_CURRENT_BINARY_DIR} modules
		     )
endfunction()

add_subdirectory(chapter02)
```

