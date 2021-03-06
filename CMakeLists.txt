cmake_minimum_required(VERSION 3.23.0)

project(fract-ol)
set (SRCS src/main.c)
set (EXEC_NAME fract-ol)

#compiler settings:
set (CMAKE_C_FLAGS "-Wall -Werror -Wextra")
set (CMAKE_C_FLAGS_DEBUG "-g -fsanitize=address") #cmake -DCMAKE_BUILD_TYPE=DEBUG to add debug flags
set (CMAKE_EXPORT_COMPILE_COMMANDS ON) #vscode include path checking

#norm rules so flclean and re work
add_custom_target(fclean make clean)
add_custom_target(re make clean COMMAND make)

#compile MLX42
add_custom_target(build_mlx42 ALL 
                   COMMAND ${CMAKE_MAKE_PROGRAM}
                   WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib/mlx42
                   COMMENT "Original MLX42 makefile target")
add_library(mlx42 STATIC IMPORTED)
set_property(TARGET mlx42 APPEND PROPERTY IMPORTED_CONFIGURATIONS NOCONFIG)
set_target_properties(mlx42 PROPERTIES IMPORTED_LOCATION_NOCONFIG "${CMAKE_CURRENT_SOURCE_DIR}/lib/MLX42/libmlx42.a")
add_dependencies(mlx42 build_mlx42)
target_include_directories(mlx42 INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/lib/MLX42/include)

add_library(glfw SHARED IMPORTED)
set_target_properties(glfw PROPERTIES IMPORTED_LOCATION "/Users/jvan-kra/.brew/opt/glfw/lib/libglfw.dylib")

#make executable with output name specified under EXEC_NAME and sources specified under SRCS
add_executable(${EXEC_NAME} ${SRCS})
target_include_directories(${EXEC_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_link_libraries(${EXEC_NAME} PRIVATE mlx42 glfw)

#optionaly add libft:
add_subdirectory(lib/libft)
target_link_libraries(${EXEC_NAME} PRIVATE ft)
