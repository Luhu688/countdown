# App Router 迁移指南

## 1. 项目技术栈分析

### 当前技术栈（Page Router）

| 类别 | 技术/依赖 | 版本 | 用途 |
|------|-----------|------|------|
| 框架 | Next.js | 13.4.12 | React框架，使用Page Router |
| 核心库 | React | 18.2.0 | UI构建 |
| 状态管理 | React Context API | - | 全局状态管理（ThemeContext, TimerContext） |
| 样式 | Tailwind CSS | 3.3.3 | 实用优先的CSS框架 |
| 动画 | Framer Motion | 10.15.0 | 流畅的动画和过渡效果 |
| 日期处理 | date-fns | 2.30.0 | 日期和时间操作 |
| UI组件 | React Icons | 4.10.1 | 图标库 |
| 颜色选择 | React Colorful | 5.6.1 | 颜色选择器组件 |
| 声音 | use-sound | 4.0.1 | 音频播放 |
| 唯一ID | UUID | 9.0.0 | 生成唯一标识符 |
| 中日历 | solarlunar | 2.0.7 | 农历和阳历转换 |
| 扫码 | qrcode.react | 3.1.0 | 二维码生成 |
| 滚动检测 | react-intersection-observer | 9.5.2 | 元素可见性检测 |
| 工具 | autoprefixer, postcss | - | CSS处理 |

### 目标技术栈（App Router）

| 类别 | 技术/依赖 | 版本 | 用途 |
|------|-----------|------|------|
| 框架 | Next.js | 13.4.12+ | React框架，使用App Router |
| 核心库 | React | 18.2.0 | UI构建 |
| 状态管理 | React Context API | - | 全局状态管理（ThemeContext, TimerContext） |
| 样式 | Tailwind CSS | 3.3.3 | 实用优先的CSS框架 |
| 动画 | Framer Motion | 10.15.0 | 流畅的动画和过渡效果 |
| 日期处理 | date-fns | 2.30.0 | 日期和时间操作 |
| UI组件 | React Icons | 4.10.1 | 图标库 |
| 颜色选择 | React Colorful | 5.6.1 | 颜色选择器组件 |
| 声音 | use-sound | 4.0.1 | 音频播放 |
| 唯一ID | UUID | 9.0.0 | 生成唯一标识符 |
| 中日历 | solarlunar | 2.0.7 | 农历和阳历转换 |
| 扫码 | qrcode.react | 3.1.0 | 二维码生成 |
| 滚动检测 | react-intersection-observer | 9.5.2 | 元素可见性检测 |
| 工具 | autoprefixer, postcss | - | CSS处理 |

## 2. 功能转换需求

### 2.1 路由结构转换

| Page Router | App Router | 说明 |
|-------------|------------|------|
| `pages/_app.js` | `app/layout.js` | 全局布局组件，提供Context和全局样式 |
| `pages/_document.js` | `app/layout.js` + `app/document.js` | 文档结构定义 |
| `pages/index.js` | `app/page.js` | 主页面组件 |
| - | `app/loading.js` (可选) | 加载状态组件 |
| - | `app/not-found.js` (可选) | 404页面 |
| - | `app/error.js` (可选) | 错误处理组件 |

### 2.2 核心组件转换

| 组件 | Page Router 实现 | App Router 实现 | 转换要点 |
|------|-----------------|----------------|----------|
| 全局布局 | `_app.js` + `Layout.js` | `app/layout.js` | 根布局需包含所有Context Provider |
| 主题管理 | `ThemeContext.js` | `ThemeContext.js` | 需在根布局中提供，保持不变 |
| 计时器管理 | `TimerContext.js` | `TimerContext.js` | 需在根布局中提供，保持不变 |
| 背景组件 | `GradientBackground.js` | `GradientBackground.js` | 需标记为客户端组件（`'use client'`） |
| 计时器显示 | `TimerDisplay.js` | `TimerDisplay.js` | 需标记为客户端组件（`'use client'`） |
| UI组件 | 所有Modal, Notification等 | 保持不变 | 需标记为客户端组件（`'use client'`） |

### 2.3 数据获取转换

| Page Router | App Router | 说明 |
|-------------|------------|------|
| `getStaticProps` | `fetch()` (Server Components) | 静态数据获取 |
| `getServerSideProps` | `fetch()` (Server Components) | 服务端数据获取 |
| `getInitialProps` | 废弃 | 不再支持，使用新的数据获取方式 |
| 客户端数据获取 | `useEffect` + `fetch` | 保持不变 | 仅在客户端组件中使用 |

### 2.4 API转换

| Page Router API | App Router API | 说明 |
|-----------------|----------------|------|
| `next/head` | `next/head` 或 `metadata` 对象 | 元数据管理 |
| `next/router` | `next/navigation` | 导航和路由管理 |
| `useRouter()` | `useRouter()` (客户端) / `redirect()` (服务器) | 路由操作 |
| `withRouter()` | 废弃 | 不再需要，使用组件内的路由对象 |

## 3. 迁移难点分析

### 3.1 客户端组件与服务器组件的区分

**难点**：需要明确区分哪些组件必须在客户端运行，哪些可以在服务器运行。

**解决方案**：
- 所有使用React Hooks（useState, useEffect等）的组件必须标记为客户端组件
- 所有使用浏览器API的组件必须标记为客户端组件
- 所有Context Provider必须在客户端组件中定义或包装

### 3.2 Context API的使用

**难点**：App Router中Context只能在客户端组件中提供，需要在根布局中正确配置。

**解决方案**：
```jsx
// app/layout.js
import { ThemeProvider } from '../context/ThemeContext';
import { TimerProvider } from '../context/TimerContext';

export default function RootLayout({ children }) {
  return (
    <html lang="zh-CN">
      <body>
        <ThemeProvider>
          <TimerProvider>
            {children}
          </TimerProvider>
        </ThemeProvider>
      </body>
    </html>
  );
}
```

### 3.3 全局样式的导入

**难点**：App Router中全局样式的导入方式发生变化。

**解决方案**：
- 在`app/layout.js`中导入全局样式
- 使用`@layer`指令组织样式优先级
- 考虑使用CSS Modules或Tailwind的`@apply`

### 3.4 Service Worker的配置

**难点**：App Router中Service Worker的注册方式可能需要调整。

**解决方案**：
- 保持现有的`public/register-sw.js`和`public/sw.js`
- 在客户端组件中使用`useEffect`注册Service Worker
- 确保Service Worker与App Router的路由系统兼容

### 3.5 客户端数据获取

**难点**：需要将现有的客户端数据获取逻辑适配到新的组件模型。

**解决方案**：
- 所有使用`useEffect`获取数据的组件必须标记为客户端组件
- 考虑使用React Query等库优化数据获取（可选）

### 3.6 URL参数处理

**难点**：`useRouter()`的API发生变化，需要调整参数获取方式。

**解决方案**：
```jsx
// 旧方式
import { useRouter } from 'next/router';
const { query } = useRouter();

// 新方式
import { useSearchParams } from 'next/navigation';
const searchParams = useSearchParams();
const syncId = searchParams.get('syncId');
```

## 4. 迁移步骤

### 步骤1：创建App Router目录结构

```
mkdir -p app
```

### 步骤2：创建根布局文件

```jsx
// app/layout.js
'use client';

import '../styles/globals.css';
import { ThemeProvider } from '../context/ThemeContext';
import { TimerProvider } from '../context/TimerContext';
import ScrollProgress from '../components/UI/ScrollProgress';
import ScrollHandle from '../components/UI/ScrollHandle';
import OfflineNotification from '../components/UI/OfflineNotification';
import GlobalNotificationManager from '../components/UI/GlobalNotificationManager';

export const metadata = {
  title: 'Live Countdown - Countdown to modernization',
  description: 'Live Countdown - A modern countdown web application',
};

export default function RootLayout({ children }) {
  return (
    <html lang="zh-CN">
      <body>
        <ThemeProvider>
          <TimerProvider>
            <GlobalNotificationManager />
            <ScrollProgress />
            <ScrollHandle />
            <OfflineNotification />
            {children}
          </TimerProvider>
        </ThemeProvider>
      </body>
    </html>
  );
}
```

### 步骤3：创建主页面组件

```jsx
// app/page.js
'use client';

import { useState, useEffect } from 'react';
import { useRouter, useSearchParams } from 'next/navigation';
import { motion, AnimatePresence } from 'framer-motion';
import Layout from '../components/Layout/Layout';
import GradientBackground from '../components/Background/GradientBackground';
import TimerDisplay from '../components/Countdown/TimerDisplay';
import AddTimerModal from '../components/UI/AddTimerModal';
import AddStopwatchModal from '../components/UI/AddStopwatchModal';
import AddWorldClockModal from '../components/UI/AddWorldClockModal';
import TimerTypeModal from '../components/UI/TimerTypeModal';
import LoginModal from '../components/UI/LoginModal';
import { useTimers } from '../context/TimerContext';
import { useTheme } from '../context/ThemeContext';
import { useTranslation } from '../hooks/useTranslation';
import { parseShareUrl } from '../utils/shareUtils';

export default function Home() {
  // 组件实现与原index.js保持一致
  // ...
}
```

### 步骤4：标记客户端组件

为所有使用React Hooks或浏览器API的组件添加`'use client'`指令：

```jsx
// 例如：components/UI/GlobalNotificationManager.js
'use client';

import { useEffect } from 'react';
// 组件实现...
```

### 步骤5：更新导航API

```jsx
// 旧代码
import { useRouter } from 'next/router';
const router = useRouter();
const syncId = router.query.syncId;

// 新代码
import { useSearchParams } from 'next/navigation';
const searchParams = useSearchParams();
const syncId = searchParams.get('syncId');
```

### 步骤6：更新Service Worker注册

确保Service Worker在客户端组件中注册：

```jsx
// 可以在app/layout.js中添加
'use client';

import { useEffect } from 'react';

export default function RootLayout({ children }) {
  useEffect(() => {
    // 注册Service Worker
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('/register-sw.js')
          .then(registration => {
            console.log('SW registered: ', registration);
          })
          .catch(registrationError => {
            console.log('SW registration failed: ', registrationError);
          });
      });
    }
  }, []);

  // ...
}
```

### 步骤7：测试与验证

1. 运行开发服务器：`npm run dev`
2. 验证所有功能正常工作：
   - 主题切换
   - 计时器功能
   - 模态框打开/关闭
   - 通知功能
   - 响应式设计
   - 离线功能
3. 构建测试：`npm run build`
4. 部署测试（可选）

## 5. 代码优化建议

### 5.1 客户端与服务器组件分离

**建议**：将组件分为客户端组件和服务器组件，以提高性能。

```jsx
// 服务器组件（无需'use client'）
export default function ServerComponent() {
  // 可以直接获取数据
  const data = await fetch('https://api.example.com/data').then(res => res.json());
  return <div>{data}</div>;
}

// 客户端组件（需要'use client'）
'use client';
export default function ClientComponent() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### 5.2 使用新的元数据API

**建议**：使用App Router的`metadata`对象替代`next/head`。

```jsx
// app/layout.js
export const metadata = {
  title: 'Live Countdown',
  description: 'A modern countdown web application',
  openGraph: {
    title: 'Live Countdown',
    description: 'A modern countdown web application',
    images: ['/logo.png'],
  },
};
```

### 5.3 优化Context使用

**建议**：将Context拆分为更小的部分，只在需要的地方提供。

```jsx
// 原方式：一个大的Context
<ThemeProvider>
  <TimerProvider>
    <UserProvider>
      {children}
    </UserProvider>
  </TimerProvider>
</ThemeProvider>

// 优化方式：按需提供
<ThemeProvider>
  {children}
</ThemeProvider>

// 在需要的组件中
<TimerProvider>
  <TimerRelatedComponents />
</TimerProvider>
```

## 6. 风险评估

| 风险 | 可能性 | 影响 | 缓解措施 |
|------|--------|------|----------|
| 客户端组件标记错误 | 高 | 组件无法正常工作 | 全面测试所有组件，确保正确标记 |
| 路由API使用错误 | 中 | 导航和参数获取失败 | 更新所有路由相关代码，使用新API |
| Context提供方式错误 | 中 | 状态管理失效 | 遵循App Router的Context使用规则 |
| Service Worker兼容性问题 | 低 | 离线功能失效 | 测试Service Worker注册和功能 |
| 构建或部署失败 | 中 | 应用无法发布 | 提前进行构建测试，确保兼容性 |

## 7. 迁移工具

| 工具 | 用途 | 链接 |
|------|------|------|
| Next.js Migration Plugin | 自动迁移Page Router到App Router | https://nextjs.org/docs/app/building-your-application/upgrading/app-router-migration |
| ESLint Plugin | 检测不兼容的API使用 | https://nextjs.org/docs/app/building-your-application/configuring/eslint |

## 8. 结论

将项目从Page Router迁移到App Router是一个中等复杂度的任务，主要涉及：

1. 目录结构重构
2. 组件标记（客户端/服务器）
3. API更新（路由、数据获取等）
4. Context Provider配置

迁移后将获得App Router的所有优势：
- 更好的性能（服务器组件）
- 更灵活的布局系统
- 改进的数据获取方式
- 更好的开发体验

建议按照本文档的步骤逐步迁移，确保每个阶段都进行充分测试，以降低迁移风险。