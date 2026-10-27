# CloakMesh 隧道引擎（tunnel-sdk-c）

CloakMesh 的隧道客户端引擎，fork 自 [OpenZiti ziti-tunnel-sdk-c](https://github.com/openziti/ziti-tunnel-sdk-c)（Apache License 2.0）。

> **上游说明**：数据面、SDK 与隧道逻辑全部来自上游。CloakMesh 仅提供品牌化的命令名（`cloakmesh-tunnel`）与中文文档。

## 构建

与上游一致（需要 CMake、C 编译器与子模块）：

```bash
git submodule update --init --recursive
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
```

产物：`build/programs/ziti-edge-tunnel/ziti-edge-tunnel`

## 对外命令名

```bash
install -m 0755 branding/bin/cloakmesh-tunnel /usr/local/bin/cloakmesh-tunnel
install -m 0755 build/programs/ziti-edge-tunnel/ziti-edge-tunnel /usr/local/bin/ziti-edge-tunnel
cloakmesh-tunnel --version        # 与 ziti-edge-tunnel 完全等价
```

命名策略与「为什么不改 CMake 目标名」见 [`branding/README.md`](branding/README.md)。

## 与其它仓库的关系

| 仓库 | 关系 |
|---|---|
| `cloakmesh-core` | 控制器/路由器（本引擎连接的零信任网络） |
| `cloakmesh-desktop-edge-win` | Windows 客户端，内置本引擎 |
| `cloakmesh-tunnel-apple` / `cloakmesh-tunnel-android` | 移动端客户端 |

## 许可

Apache License 2.0，与上游一致。