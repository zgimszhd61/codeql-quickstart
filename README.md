# codeql-quickstart

CodeQL 是一个用于执行语义代码分析的查询框架，它由 GitHub 开发并用于安全研究和开源项目的安全审计。以下是在 Linux 系统下安装 CodeQL 并编写一个简单规则进行安全扫描的快速入门教程。

### 安装 CodeQL

1. **安装依赖项**：
   在安装 CodeQL 之前，确保你的系统已经安装了以下依赖项：
   - Git
   - Python 3
   - Java（JDK 11 或更高版本）

2. **下载 CodeQL CLI**：
   访问 GitHub 上的 CodeQL 仓库（https://github.com/github/codeql-cli-binaries）并下载最新版本的 CodeQL CLI 压缩包。

3. **解压 CodeQL CLI**：
   使用命令行工具将下载的压缩包解压到你选择的目录中。例如：
   ```bash
   tar -xzvf codeql-linux64-<version>.tar.gz
   ```

4. **将 CodeQL CLI 添加到 PATH**：
   为了能够在任何目录下使用 CodeQL 命令，你可以将其添加到你的 PATH 环境变量中。编辑你的 shell 配置文件（如 `.bashrc` 或 `.zshrc`），并添加以下行：
   ```bash
   export PATH=$PATH:/path/to/codeql
   ```
   然后，重新加载配置文件或重新打开终端。

5. **下载 CodeQL 代码库**：
   CodeQL 使用特定的查询库来分析代码。你需要克隆这些库到本地：
   ```bash
   git clone https://github.com/github/codeql.git /path/to/codeql-repo
   ```

### 编写一个简单的 CodeQL 规则

1. **创建查询文件**：
   在你的工作目录中创建一个新的 CodeQL 查询文件，例如 `example-query.ql`。你可以使用任何文本编辑器来编写查询。

2. **编写查询**：
   假设我们想要查找所有未经初始化就使用的变量，你可以编写如下查询：
   ```ql
   import cpp

   from Variable v, LocalScopeVariableAccess access
   where
     v.getAnAccess() = access and
     not exists(VariableAccess init | init = v.getInitializer())
   select access, "This variable is used without being initialized."
   ```

### 执行安全扫描

1. **创建数据库**：
   CodeQL 需要一个数据库来存储代码的表示形式。使用 CodeQL CLI 创建数据库：
   ```bash
   codeql database create <database-name> --language=<language> --source-root=<source-code-directory>
   ```
   其中 `<database-name>` 是你为数据库选择的名称，`<language>` 是你要分析的代码语言（例如 `cpp`），`<source-code-directory>` 是你的源代码目录。

2. **运行查询**：
   使用 CodeQL CLI 运行你的查询：
   ```bash
   codeql database analyze <database-name> /path/to/example-query.ql --format=csv --output=results.csv
   ```
   这将分析指定的数据库，并将结果输出到 `results.csv` 文件中。

3. **查看结果**：
   打开 `results.csv` 文件查看查询结果。如果查询发现了任何问题，它们将被列出在这个文件中。

### 样本代码

为了测试你的 CodeQL 查询，你可以创建一个简单的 C++ 示例代码文件 `example.cpp`：
```cpp
#include <iostream>

int main() {
    int uninitialized;
    std::cout << uninitialized; // 这将触发我们的 CodeQL 规则
    return 0;
}
```

将这个文件放在你的 `<source-code-directory>` 目录下，并按照上述步骤创建数据库和运行查询。如果一切设置正确，你的查询应该能够检测到这个未初始化变量的使用。

这个快速入门教程提供了一个基本的框架，你可以在此基础上扩展和深入学习 CodeQL 的更多高级功能。

