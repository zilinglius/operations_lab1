# enroll-conflict：实验 1 的统一示例

一个只有标准库的小服务：判断两个教学班的上课时间是否冲突，对应 L-05 课程实践里“冲突检测新规则”的场景。**服务本身不是实验的重点**，它的作用是给流水线提供一个能构建、能测试、能让测试失败的对象。

## 规则

两个教学班冲突，当且仅当：同一个星期、上课周次有交集、节次区间有重叠。上课周次分三种：`all`（每周）、`odd`（单周）、`even`（双周）。单周班与双周班在同一时段上课不冲突。

## 接口

| 方法与路径 | 作用 |
|---|---|
| `GET /healthz` | 存活检查，返回 `ok` |
| `GET /version` | 返回版本号、提交号、构建号、Go 版本（JSON） |
| `POST /api/conflict` | 请求体 `{"a": 教学班, "b": 教学班}`，返回 `{"conflict": true/false}` |

教学班的字段：`id`、`weekday`（1～7）、`start`、`end`（节次，含）、`weeks`。

## 在本地运行

需要 Go 1.26 或更高版本；`make` 目标还需要 GNU make 和 GNU tar（Linux 或 WSL2）。

```bash
make fmt-check vet test                    # 质量门禁
go run . -addr 127.0.0.1:8080              # 直接运行
make package VERSION=1.0.0 BUILD_NO=1      # 打包到 dist/
dist/enroll-conflict_1.0.0_linux_amd64/enroll-conflict -version
```

`make package` 的三个参数 `VERSION`、`BUILD_NO`、`COMMIT` 就是制品的三个追溯标识；不传时分别是 `dev`、`local` 和本地 `HEAD`，表示“没有经过流水线”。

## 文件

| 文件 | 说明 |
|---|---|
| `conflict.go`、`conflict_test.go` | 冲突规则及其测试 |
| `main.go`、`main_test.go` | HTTP 服务、版本信息及其测试 |
| `Makefile` | 本地与 CI 共用的构建入口 |
| `scripts/trace.sh` | 追溯脚本：由制品回答“它来自哪次提交” |
| `Dockerfile` | 选做 O1 使用 |
| `LAB1.md` | 小组的证据与分析记录，需要填写并提交 |

仓库里没有 `.github/workflows/`：流水线由小组在实验中自己编写。
