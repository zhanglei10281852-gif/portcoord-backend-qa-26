# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

API 返回错误时响应中的 request_id 为空，无法把客户端错误与服务日志关联。请修复请求上下文在错误响应链路中的传播。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-26
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-26.git
- parent SHA：90d7f71d482cd988dad41496014eb38b5c96878f

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-26.git bug-repro
cd bug-repro
git checkout --detach 90d7f71d482cd988dad41496014eb38b5c96878f
go test ./internal/server -run "^TestServer_ErrorResponseIncludesRequestID$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/server -run "^TestServer_ErrorResponseIncludesRequestID$" -count=1
--- FAIL: TestServer_ErrorResponseIncludesRequestID (0.00s)
    server_test.go:114: expected request_id in body, got ""
FAIL
FAIL	portcoord/internal/server	0.009s
FAIL

```

stderr：

```text
warning: internal/server/server_test.go has type 100755, expected 100644
warning: internal/server/server_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/server -run "^TestServer_ErrorResponseIncludesRequestID$" -count=1
--- FAIL: TestServer_ErrorResponseIncludesRequestID (0.21s)
    server_test.go:114: expected request_id in body, got ""
FAIL
FAIL	portcoord/internal/server	0.424s
FAIL

```

stderr：

```text
warning: internal/server/server_test.go has type 100755, expected 100644
warning: internal/server/server_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/server -run ^TestServer_ErrorResponseIncludesRequestID$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
