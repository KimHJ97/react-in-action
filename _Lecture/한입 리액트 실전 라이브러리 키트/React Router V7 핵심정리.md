# React Router V7 핵심정리

## 1. React Router 설치 및 기본 설정

- `라이브러리 설치`

```bash
npm i react-router@7
```

- `main.tsx`
  - BrowserRouter는 브라우저 주소와 리액트 앱의 UI를 동기화하는 역할을 하며, 감싸지 않으면 라우팅이 동작하지 않는다

```javascript
import App from "./App.tsx";
import { BrowserRouter } from "react-router";

createRoot(document.getElementById("root")!).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
);
```

<br/>

## 2. 라우팅 설정 (Routes / Route)

- `App.tsx`
  - App 컴포넌트의 리턴문에 `Routes` 컴포넌트를 배치하고, 그 안에 `Route` 컴포넌트를 자식으로 넣는다
  - Route에 `path` prop으로 경로를, `element` prop으로 렌더링할 컴포넌트를 지정한다
  - Routes는 현재 경로와 일치하는 Route를 찾아 해당 element를 렌더링한다

```javascript
function App() {
  return (
    <Routes>
      <Route path="/" element={<div>Home</div>} />
      <Route path="/sign-in" element={<div>SignIn</div>} />
      <Route path="/sign-up" element={<div>SignUp</div>} />
    </Routes>
  );
}
```

<br/>

## 3. 페이지 컴포넌트 분리

- `src/pages` 폴더를 생성하여 각 페이지별 컴포넌트를 별도 파일로 관리한다
- `home-page.tsx`, `sign-in-page.tsx`, `sign-up-page.tsx` 등으로 파일을 생성한다
- App 컴포넌트에서 각 페이지 컴포넌트를 import하고 Route의 element로 지정한다

```javascript
import { Route, Routes } from "react-router";
import IndexPage from "./pages/index-page";
import SignInPage from "./pages/sign-in-page";
import SignUpPage from "./pages/sign-up-page";

function App() {
  return (
    <Routes>
      <Route path="/" element={<IndexPage />} />
      <Route path="/sign-in" element={<SignInPage />} />
      <Route path="/sign-up" element={<SignUpPage />} />
    </Routes>
  );
}
```

<br/>

## 4. 레이아웃 라우트 (Layout Routes)

- 동일한 레이아웃을 공유할 Route들을 부모 `<Route>` 컴포넌트로 감싼다
- 부모 Route의 `element` prop에 레이아웃 컴포넌트를 지정한다
- 레이아웃 컴포넌트 내부에 Outlet 컴포넌트(react-router에서 import)를 배치해야 자식 페이지가 렌더링된다
- `Outlet`을 배치하지 않으면 레이아웃만 렌더링되고 페이지 컴포넌트는 표시되지 않는다

```javascript
function AuthLayout() {
  return (
    <div>
      <header>Auth!</header>
      <Outlet />
    </div>
  );
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<IndexPage />} />

      <Route element={<AuthLayout />}>
        <Route path="/sign-in" element={<SignInPage />} />
        <Route path="/sign-up" element={<SignUpPage />} />
      </Route>
    </Routes>
  );
}
```
