---
layout: post
title: NEXT-Pages
subtitle: NEXT-Pages에 대한 설명
categories: next.js
tags: [next.js, javascript, web, study]
---

아래는 Next.js 공식문서의 번역본입니다

[공식문서](https://nextjs.org/docs/basic-features/pages)

## Pages

Next.js 의 `Page` 는 `React Component` 로써, `.js`, `.jsx`, `.ts`, `.tsx` 의 형태로 존재한다.

### Pre-rendering

Next.js 는 매 페이지마다 `pre-render`를 지원한다. 즉 Next는 Client가 자바스크립트로 처리하는것이 아니라 매 페이지마다 HTML 을 만들어낸다. 즉 여기서 성능과 [SEO](2022-04-24-why-next-js.md) 측면에서 더 좋다. 이렇게 만들어진 HTML은 정말 페이지에 필요한 자바스크립트 코드만 들어가 있으며, 브라우저에서 로딩이 되면, 해당 자바스크립트 코드로 상호작용을 할 수 있다. -> `hydration`

Pre-rendering 에는 두가지 방법이 존재하는데, `Static Generation` 과 `Server-side Rendering`이 있다. [SSR](2022-04-24-why-next-js.md)같은 경우에는, 저번 포스트에서 설명을 하였으므로 넘어가고, 이번에는 Next에서 공식적으로 추천하는 Static Generation 방법에 대해서 알아보자.

### SSG

어떠한 페이지가 `Static Generation` 이라면, 해당 페이지의 HTML은 빌드시간에 생성된다고 할 수 있다. 즉 `next build` 명령어의 실행과 동시에 페이지의 HTML이 만들어진다고 ㅂ로 수 있다. 그리고 이러한 HTML은 CDN(Contents Delivery Network)을 통하여 캐시 될 수 있다.

Next.js 는 기본적으로 데이터 fetching을 제외한 `Static Generation`으로 작동한다.

```javascript
function About() {
  return <div>About</div>;
}

export default About;
```

아래와 같은 함수는 fetch 할 data가 없으니 바로 HTML로 변환되어 정적으로 하나의 HTML이 나올 것이다!

그럼 Data가 있는 경우를 살펴보자

Data가 있는 경우에 두가지로 쪼갤 수 있다. 1. page의 content가 외부 데이터에 의존할 때 (`getStaticProps`) 2. page의 path가 외부 데이터에 의존할 때 (`getStaticPaths`)

#### 1. content가 의존성일 때

```javascript
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

export default Blog;
```

이러한 형태의 함수를 보낼시에, Next.js 는 async 를 export 하게 도와준다. `getStaticProps` 란 함수를 불러 사용이 가능하며, `pre-render` 과정에서, data를 fetch하게 도와준다. 그럼 수정본 코드를 보면 다음과 같다.

```javascript
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

export async function getStaticProps() {
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

#### 2. path가 의존성일 때

앞서 말했다시피 Next.js는 `dynamic routes`를 허용한다. 예를들어 file route가 `pages/posts/[id].js` 라면, `posts/1` 에 접속 시 `id: 1` 로 접근이 가능할것이다.

이러한 접근을 하려면 `async` 를 `export` 하는 함수 즉 `getStaticPaths`를 사용하여 가능하다.

path의 pre-render과정을 보면 다음과 같다.

```javascript
function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // 빌드 할 시에만 path 로 pre-render
  return { paths, fallback: false };
}
// 이것도 빌드시에 사용
export async function getStaticProps({ params }) {
  // 다이나믹하게 작동한다
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

### SSR

SSR이란, 매 HTML 페이지가 매번 요청시에 Server 측에서 만들어 준다는 것이다. 즉 Server에 가는 부담이 커질 수 밖에 없다.

이건 `export` 와 `async` 를 활용한 `getServerSideProps` 란 함수를 사용하여 구현할 수 있다.

```javascript
function Page({ data }) {
  // Render data...
}

// 매번 불러온다
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // props 로 data 불러오기
  return { props: { data } };
}

export default Page;
```

자세히 보면, `getStaticProps` 와 매우 유사해 보이지만, 차이점은 `getServerSidePRops`는 build 시에 처음만 하는것이 아닌 매번 요청마다 작동한다는 점이다!
