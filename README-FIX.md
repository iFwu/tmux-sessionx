# tmux-sessionx 修复说明

这是 [omerxx/tmux-sessionx](https://github.com/omerxx/tmux-sessionx) 的一个 fork，修复了快捷键无法正常工作的问题。

## 问题描述

原始代码中，`sessionx.sh` 脚本在 `run_plugin()` 函数中使用了 `extra_options` 关联数组来获取配置值。但是在某些函数中（如 `get_sorted_sessions`、`input` 等），这些函数在 `extra_options` 数组被正确评估之前就尝试访问它，导致获取不到配置值，进而导致快捷键绑定失败。

## 修复方案

将所有 `${extra_options["key"]}` 的访问方式替换为直接调用 `$(tmux_option_or_fallback "@sessionx-key" "default")`。这样可以确保无论何时调用，都能正确获取到配置值。

## 修改的文件

- `scripts/sessionx.sh`

## 具体修改

```bash
# 原代码
filtered_sessios=${extra_options["filtered-sessions"]}
default_window_mode=${extra_options["window-mode"]}
filter_current_session=${extra_options["filter-current"]}
custom_paths=${extra_options["custom-paths"]}
custom_path_subdirectories=${extra_options["custom-paths-subdirectories"]}
bind_back=${extra_options["bind-back"]}

# 修改后
filtered_sessios=$(tmux_option_or_fallback "@sessionx-filtered-sessions" "")
default_window_mode=$(tmux_option_or_fallback "@sessionx-window-mode" "off")
filter_current_session=$(tmux_option_or_fallback "@sessionx-filter-current" "true")
custom_paths=$(tmux_option_or_fallback "@sessionx-custom-paths" "")
custom_path_subdirectories=$(tmux_option_or_fallback "@sessionx-custom-paths-subdirectories" "false")
bind_back=$(tmux_option_or_fallback "@sessionx-bind-back" "ctrl-b")
```

## 使用方法

在你的 tmux 配置中，将插件源改为这个 fork：

```bash
# 使用 TPM (Tmux Plugin Manager)
set -g @plugin 'iFwu/tmux-sessionx'
```

然后重新加载 tmux 配置或重新安装插件即可。