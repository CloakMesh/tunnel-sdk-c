# CloakMesh 品牌层（tunnel-sdk-c）

本目录承载 CloakMesh 对上游 `ziti-tunnel-sdk-c` 的品牌化封装。

## 1. 对外命名

| 上游 | CloakMesh | 状态 |
|---|---|---|
| `ziti-edge-tunnel`（可执行文件） | `cloakmesh-tunnel` | 通过 `branding/bin/cloakmesh-tunnel` 包装器提供 |
| 库 `ziti-tunnel-sdk-c` | 保持不变 | 上游 CMake 目标名，内部契约 |

## 2. 为什么用包装器而不是改 CMake

`ziti-edge-tunnel` 这个名字在上游被大约 8 个脚本/配置引用（`docker/docker-entrypoint.sh`、`scripts/install-ubuntu.bash`、`nix/update.sh`、集成测试脚本等）。直接改 CMake 目标名会连带改动这些脚本，且本地没有 Linux/CMake 环境可回归验证，风险高于收益。

包装器的做法是：**构建产物不变，对外命令名统一**。两个名字行为完全一致，参数原样透传。

## 3. 安装

```bash
# 与 ziti-edge-tunnel 放在同一目录即可自动发现
install -m 0755 branding/bin/cloakmesh-tunnel /usr/local/bin/cloakmesh-tunnel
cloakmesh-tunnel --version
```

包装器按顺序查找底层二进制：同目录 → `../lib/cloakmesh/` → `/usr/local/lib/cloakmesh/` → `/usr/local/bin/` → `/usr/bin/`。

## 4. 可选：改 CMake 真正改二进制名（需 Linux 构建验证）

若希望 `make` 直接产出 `cloakmesh-tunnel`，在 `programs/ziti-edge-tunnel/CMakeLists.txt` 的 `add_executable(ziti-edge-tunnel ...)` 之后加：

```cmake
set_target_properties(ziti-edge-tunnel PROPERTIES OUTPUT_NAME cloakmesh-tunnel)
add_custom_command(TARGET ziti-edge-tunnel POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E create_symlink
            $<TARGET_FILE_NAME:ziti-edge-tunnel>
            $<TARGET_FILE_DIR:ziti-edge-tunnel>/ziti-edge-tunnel)
```

第二条命令保证上游脚本引用的 `ziti-edge-tunnel` 仍然存在。**该改动未经本地构建验证**（本机无 CMake/Linux 工具链），落地前请在 Linux 上跑一次 `cmake -B build && cmake --build build` 并执行 `tests/integration`。

## 5. 上游归属

基于 [OpenZiti ziti-tunnel-sdk-c](https://github.com/openziti/ziti-tunnel-sdk-c)（Apache License 2.0）。