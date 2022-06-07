cmake_minimum_required(VERSION 3.8.0)
project(movie_publisher)

find_package(catkin REQUIRED)

catkin_package()

install(PROGRAMS
  scripts/add_movie_to_bag
  scripts/movie_to_bag
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

catkin_install_python(PROGRAMS
  nodes/movie_publisher_node
  scripts/fix_bag_timestamps
  scripts/merge.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(FILES
  launch/movie_publisher.launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
