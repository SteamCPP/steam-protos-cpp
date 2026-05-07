# steam-protos-cpp
Raw Steam Protos and the CMakeLists to generate them.

## Usage

Include it:
```cmake
find_package(SteamProtos REQUIRED)
target_link_libraries(MyTarget PRIVATE SteamProtos::SteamProtos)
```