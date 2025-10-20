# adventure-webui 汉化版

[![MIT 许可证](https://img.shields.io/badge/license-MIT-blue)](license.txt)

这是为 [MiniMessage](https://github.com/KyoriPowered/adventure-text-minimessage) 提供的一个网页界面。

你可以在这里访问在线演示: https://webui.advntr.dev/

---

### API

该项目包含一个开发者 API，可用于创建一个简单的“编辑器”系统。该系统允许你将 MiniMessage 字符串提交到服务器，并返回一个令牌（token），终端用户可以使用该令牌对字符串进行编辑。
编辑后的结果可以保存回服务器，从而实现流畅的编辑体验。

更多信息请参阅仓库 Wiki: https://github.com/KyoriPowered/adventure-webui/wiki/Editor-API

### 部署（生产环境）

下面是针对如何在生产环境运行该应用的说明（已包含本仓库的构建/打包选项）。请注意，需要使用 JDK 21。

快速运行（开发模式）

项目中存在基于 Kotlin Multiplatform 的特定运行任务。开发时请使用以下命令（注意：该项目的通用 `run` 任务在当前多平台配置下不可用，改用 `jvmRun`）：

```bash
./gradlew jvmRun -PisDevelopment
```

该命令会在开发模式下启动服务器（默认监听 `http://localhost:8080`）。

生产构建与运行（可单文件运行的 JAR）

如果你希望得到一个可以直接在目标机器上用 JRE 运行的单文件可执行 JAR（fat jar），仓库已添加 Shadow 插件并提供了 `fatJar` 任务。推荐的构建命令如下：

```bash
# 生成生产用的 JS bundle，构建 JVM jar，并生成包含所有依赖的单文件 jar（跳过测试以加速）
./gradlew jsBrowserProductionWebpack jvmJar fatJar -x test
```

构建产物通常会在 `build/libs/` 中，文件名类似 `adventure-webui-<version>.jar`。该 JAR 包含运行时依赖并在 Manifest 中指定了主类（`io.ktor.server.netty.EngineMain`），可以直接这样运行：

```bash
java -jar build/libs/adventure-webui-unspecified.jar
```

如果需要使用自定义配置文件（覆盖打包在 JAR 内的 `application.conf`），可在运行时通过 JVM 参数指定：

```bash
java -Dconfig.file=/etc/adventure-webui/application.conf -jar /opt/adventure-webui/adventure-webui-<version>.jar
```

或者直接通过系统属性关闭开发模式：
```bash
java -Dio.ktor.development=false -jar build/libs/adventure-webui-<version>.jar
```

如果你更喜欢使用包管理或 systemd 管理服务，可以将生成的 JAR 和配置文件放到 `/opt/adventure-webui/`，并使用如下 systemd 单元（示例）：

```
[Unit]
Description=Adventure WebUI
After=network.target

[Service]
Type=simple
WorkingDirectory=/opt/adventure-webui
ExecStart=/usr/bin/java $JAVA_OPTS -Dconfig.file=/opt/adventure-webui/application.conf -jar /opt/adventure-webui/adventure-webui-<version>.jar
Restart=on-failure
User=webui
Group=webui

[Install]
WantedBy=multi-user.target
```

其他打包/镜像选项

- `jib` 插件已在 `build.gradle.kts` 中配置，你可以使用 Gradle 的 `jibBuildTar` 或 `jibDockerBuild` 在本地生成容器镜像，或在 CI 中将镜像推送到你的 registry。
- README 中提到的官方镜像 `ghcr.io/kyoripowered/adventure-webui/webui:latest` 可作为快速部署选项（如果你信任镜像来源）。

运行时与运维注意事项

- 默认端口：8080（位于 `src/jvmMain/resources/application.conf` 中，可通过外部配置覆盖）。
- 日志：使用 Logback，可通过 `logback.xml` 配置日志输出位置与级别。
- TLS：建议在反向代理（如 Nginx 或 Kubernetes Ingress）处终止 TLS。
- 健康检查：在生产部署中为 readiness/liveness 配置探针。
- Secrets：不要将秘钥写入仓库，使用环境变量或平台的 Secret 管理。

### 贡献

欢迎任何形式的贡献。对于新功能或拼写/风格修正，请先在 Issue 中说明或在我们 Discord 上沟通，以便我们保持方向一致。

### 致谢

本项目基于 MiniDigger 的 [MiniMessageViewer](https://github.com/MiniDigger/MiniMessageViewer)。

所用字体可以在此处找到：https://fonts2u.com/minecraft-regular.font
