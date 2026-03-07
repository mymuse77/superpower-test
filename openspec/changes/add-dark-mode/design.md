# 深色模式技术设计

## 1. 技术方案概述

采用 CSS 变量（Custom Properties）实现主题系统，通过 JavaScript 控制 `data-theme` 属性来切换主题。

## 2. 架构设计

### 2.1 主题系统架构

```
┌─────────────────────────────────────────┐
│           ThemeProvider                 │
│  (React Context / Vue Provide)          │
└─────────────┬───────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────┐
│         CSS Variables                   │
│  (--bg-primary, --text-primary, etc.)   │
└─────────────┬───────────────────────────┘
              │
              ▼
┌─────────────────────────────────────────┐
│         All Components                  │
│  (使用 CSS 变量而非硬编码颜色)           │
└─────────────────────────────────────────┘
```

### 2.2 主题存储

| 存储键 | 值 | 说明 |
|--------|-----|------|
| `theme` | `light` \| `dark` \| `system` | 用户选择的主题模式 |

## 3. 实现细节

### 3.1 CSS 变量定义

```css
/* 浅色主题 */
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f5f5f5;
  --text-primary: #1a1a1a;
  --text-secondary: #666666;
  --border-color: #e0e0e0;
}

/* 深色主题 */
[data-theme="dark"] {
  --bg-primary: #1a1a1a;
  --bg-secondary: #2d2d2d;
  --text-primary: #ffffff;
  --text-secondary: #b0b0b0;
  --border-color: #404040;
}
```

### 3.2 主题切换 Hook/Composable

```typescript
// useTheme.ts
const useTheme = () => {
  const [theme, setTheme] = useState<'light' | 'dark' | 'system'>('system');

  // 应用主题到 document
  const applyTheme = (theme: string) => {
    const root = document.documentElement;
    if (theme === 'system') {
      const isDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
      root.setAttribute('data-theme', isDark ? 'dark' : 'light');
    } else {
      root.setAttribute('data-theme', theme);
    }
  };

  // 初始化和切换逻辑...
};
```

### 3.3 系统主题监听

```typescript
// 监听系统主题变化
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
  if (storedTheme === 'system') {
    document.documentElement.setAttribute('data-theme', e.matches ? 'dark' : 'light');
  }
});
```

## 4. 需要修改的文件

| 文件 | 修改内容 |
|------|----------|
| `styles/globals.css` | 添加 CSS 变量定义 |
| `hooks/useTheme.ts` | 新建主题管理 Hook |
| `components/ThemeToggle.tsx` | 新建主题切换组件 |
| `pages/settings.tsx` | 添加主题设置选项 |
| 各页面组件 | 使用 CSS 变量替换硬编码颜色 |

## 5. 组件适配清单

- [ ] 按钮 (Buttons)
- [ ] 输入框 (Inputs)
- [ ] 卡片 (Cards)
- [ ] 导航栏 (Navbar)
- [ ] 侧边栏 (Sidebar)
- [ ] 表格 (Tables)
- [ ] 对话框 (Dialogs)
- [ ] 下拉菜单 (Dropdowns)
- [ ] 加载状态 (Loaders)
- [ ] 图表 (Charts) - 如有

## 6. 测试策略

1. **手动测试**: 在深色模式下检查所有页面
2. **自动化测试**: 截图对比工具检测视觉回归
3. **可访问性测试**: 验证对比度符合 WCAG 2.1 AA 标准