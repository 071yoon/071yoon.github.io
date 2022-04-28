---
layout: post
title: NEXT-Layouts
subtitle: NEXT Layouts의 정의
categories: next.js
tags: [next.js, javascript, web, study]
---

## Layouts

React는 페이지를 컴포넌트로 나누는것이 가능하다. 이러한 컴포넌트들은 페이지간에 재사용이 가능하고, 예를 들어, 모든 페이지마다 같은 네비게이션바를 사용한다.

### Single Shared Layout with Custom App

만약 전체 어플에 대하여 하나의 레이아웃만 사용한다면, Custom App 을 만들어, 해당 레이아웃으로 둘러 쌀 수 있다. 해당 컴포넌트가 모든 페이지마다 재사용되므로, 이러한 컴포넌트의 상태정보는 계속 유지된다.

| 예를 들어, input value가 들어와도 고정된다

```javascript
import Layout from "../components/layout";

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

### Per-Page Layout

만약 여러 레이아웃이 필요한 경우에, `getLayout` 이라는 property를 사용 할 수 있다. 컴포넌트안에 getLayout을 사용해서, 표현할지 안할지로 정해서 사용 가능하다.

```javascript
import Layout from "../components/layout";
import NestedLayout from "../components/nested-layout";

export default function Page() {
  return {
    /** Your content */
  };
}

Page.getLayout = function getLayout(page) {
  return (
    <Layout>
      <NestedLayout>{page}</NestedLayout>
    </Layout>
  );
};
```

이렇게 선언 후 실제로 사용할 때는 다음과 같다.

```javascript
export default function MyApp({ Component, pageProps }) {
  // Use the layout defined at the page level, if available
  const getLayout = Component.getLayout || ((page) => page);

  return getLayout(<Component {...pageProps} />);
}
```

타입스크립트를 이용하면 Props의 type에 주의해야된다.

```typescript
export default function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  // Use the layout defined at the page level, if available
  const getLayout = Component.getLayout ?? ((page) => page);

  return getLayout(<Component {...pageProps} />);
}
```

### Data Fetching

레이아웃안에, Client-Side에서 `useEffect`로 데이터를 가져올 수 도 있고, 라이브러리를 활용하여 `SWR`(React Query와 유사)로 가져올 수 도 있다. 하지만, Page가 아니기에, `getStaticProps`나 `getServerSideProps`를 사용 할 수는 없다.

```javascript
import useSWR from "swr";
import Navbar from "./navbar";
import Footer from "./footer";

export default function Layout({ children }) {
  const { data, error } = useSWR("/api/navigation", fetcher);

  if (error) return <div>Failed to load</div>;
  if (!data) return <div>Loading...</div>;

  return (
    <>
      <Navbar links={data.links} />
      <main>{children}</main>
      <Footer />
    </>
  );
}
```
