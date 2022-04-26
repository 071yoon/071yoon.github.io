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

언제 `getStaticProps`를 사용할까?

1.  data가 user의 request전에 rendering에 필요로 할 때!

2.  data가 headless CMS에서 올 때

3.  data가 외부적으로 캐시가 가능할 때

4.  페이지가 빨리 pre-render 되어아 할 때

`getStaticProps` 가 오직 Server-Side 에서 작동하기에, Client 딴에서는 절대 실행이 되어서는 안된다. 심지어 브라우저에서는, JS bundle에 포함도 안되어 있다. 즉 API route에서 가져온다기보다, Server딴에서 코드를 바로 넣어 줄 수 있다.

`getStaticProps`는 페이지에서만 만들어져야된다! 즉 component딴에서는 호출되면 안됨!

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

언제 `getStaticPaths`를 사용할까?

보통 dynamic route를 통하여 정적으로 페이지를 pre-render 하며

1. headless CMS에서 데이터가 올 때(모니터가 연결되어있지 않거나, 키보드나 마우스 등의 주변장치가 연결되어있지 않은 서버)

2. DB 에서 데이터가 올 때

3. FIlesystem 에서 데이터가 올 때

4. 데이터가 외부로 캐시가능할 때

5. 페이지가 매우 빠르게 pre-render 되어야 할 때
   1. `getStaticProps`는 `HTML` 과 `JSON` 파일을 만들어, CDN으로 캐시 될 수 있다

`getStaticPaths`는 오직 빌드 과정에서만 돌며, 런타임 환경에서는 작동하지 않는다. 오직, `getStaticProps`를 쓰는 다이나믹 루트를 통해서만 export 될 수 있으며, non-page file에서 export 할 수 없다! (ex. components)

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

자세히 보면, `getStaticProps` 와 매우 유사해 보이지만, 차이점은 `getServerSideProps`는 build 시에 처음만 하는것이 아닌 매번 요청마다 작동한다는 점이다!

즉 `getServerSidePRops`는, 오직 Server-Side 에서만 굴러가고, 브라우저딴에서는 실행하지 않는다. 순서를 보면 다음과 같다.

1. 어떠한 페이지를 request 한다

2. `getServerSideProps` 가 요청시에 실행된다

3. 해당 props와 같이 페이지가 pre-render 된다.

4. 링크를 통하여, 해당 페이지를 Client-Side에 요청시에 Next.js가 Server에 API 요청을 보내 `getServerSidePRops`를 실행한다.

그럼 `getServerSideProps`는 페이지 렌더링에 필요한 JSON을 리턴하고, 이러한 부분들은 Next.js 에서 자동을 해준다!

중요한 부분은 `getServerSideProps`를 독립적인 함수로 빼서 export 해야된다. 만약 page component 안에 넣어두면 제대로 작동하지 앟는다.

그럼 언제 사용할까? 한 페이지를 data가 fetch되는 request time 에 렌더링을 할 때 사용한다. 만약, request 시간에 렌더링을 할 필요가 없다면 Client Side 나 `getStaticProps`를 사용하는게 더 효율적이다.

### Incremental Static Regeneration

`Next.js`는 사이트의 빌드 후 static page를 만들거나 업데이트를 허용해준다. 사용되는 기능이 ISR(Incremental Static Regeneration)이며, 전체 사이트를 다시 build 하지 않아도, static-generation을 허용해준다.

사용하기 위해서는 다음과 같이 `getStaticProps`에 `revalidate`를 사용해야 된다.

```javascript
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}

//serverside 에서 빌드되는 함수
export async function getStaticProps() {
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  return {
    pros: {
      posts,
    },
    // ! 10초에 한번씩 OR request 가 들어오면 page를 re-generate 하려한다
    revalidate: 10,
  };

  //마찬가지로 server-side 에서 build시 실행
  export async function getStaticPaths() {
    const res = await fetch("https://.../posts");
    const posts = await res.json();

    const paths = posts.map((post) => ({
      params: { id: post.id },
    }));

    //build 시에 이것만! pre-render 한다!
    // {fallback: blocking} 은 server-render
    return { paths, fallback: "blocking" };
  }

  export default Blog;
}
```

위의 코드를 해석해보면, request 요청이 와서 build 시간에 page가 만들어진다면, cache 된 page를 보여준다.

1. 첫 요청 후 10초안의 다른 요청들은 cache에 저장된다

2. 10초 후, 다음 요청은 아직도 cache된 정보를 보여준다 -> stale (바꼈을 수 도 있음!)

3. Next.js가 페이지에 regeneration을 건다

4. page가 정상적으로 만들어졌다면, Next.js가 캐시를 사용하지 못하게 막은 후, 업데이트된 페이지를 보여준다!

| 만약 4번에서 실패했다면, 예전 페이지가 바뀌지 않았을 것이다

### On-demand Revalidation

만약 `revalidate` 시간을 60으로 잡는다면, 모든 방문자들은 1분동안 모두 같은 버전을 볼것이다. 여기서 유일하게 캐시를 비활성화 하는 방법은 다른 사람이 1분후에 접속을 하는것이다.

Next.js 의 `12.1.0` 버전 이후, on-demand Incremental Static Regenration을 지원하며, 사이트의 업데이트를 더 손쉽게 사용할 수 있게 해준다.

1. headless CMS의 정보가 만들어지거나 업데이트됨

2. Metadata 변화를 알아챔

`getStaticProps`안에, `revalidate`를 명시해주지 않아도, 된다. 만약 빠졌다면, Next.js가 자동으로 FALSE로 주고, `unstable-revalidate`요청시에만 업데이트를 진행한다.

```javascript
export default async function handler(req, res) {
  if (req.query.secret !== process.env.MY_SECRET_TOKE) {
    return res.status(401).json({ message: "Invalid token" });
  }
  try {
    await res.unstable_revalidate("path-to-revalidate");
    return res.json({ revalidated: true });
  } catch (err) {
    return res.status(500).send("Error revalidating");
  }
}
```

### Client-side data fetching with useEffect

React 에서는 data fetching 을 하기 위해 SWR을 지원한다. 우선 SWR에 대해서 알아보자.

#### SWR

SWR은 stale-while-revalidate의 약자로, 먼저 캐시(Stale한 상태)에서 데이터를 받고, fetch 요청 후 최종적으로 최신 데이터를 가져오는 전략이다.

간단한 코드를 보면 다음과 같다

```javascript
import useSWR from "swr";

function Profile() {
  const { data, error } = useSWR("/api/user", fetcher);

  if (error) return <div>failed to load</div>;
  if (!data) return <div>loading...</div>;
  return <div>hello {data.name}!</div>;
}
```

위의 코드를 보면, `useSWR`은 `key` 와 `fetcher`를 받아, 고유한 식별자가 `fetcher`로 전달되는 것이다. 주된 장점들은 다음과 같다.

- 빠르고, 가볍고, 재사용 가능한 데이터 가져오기
- 내장된 캐시 혹은 요청의 중복을 제거
- 실시간으로 이루어진다
- 전송 및 프로토콜에 상관없음
- SSR/ ISR/ SSG 모두 사용 가능
- TypeScript에 적용된다

그럼 이러한 SWR을 이용하여 profile data를 더 손쉽게 가져 올 수 있다. 그럼 SWR의 캐시로 데이터를 활용하는 코드를 보면 다음과 같다.

```javascript
import useSWR from 'swr'

const fetcher = (...args) => fetch(...args).then((res) => res.json())

functoin Profile() {
  const { data, error } = useSWR('/api/profile-data', fecther)

  if (error)
    return <div>Failed to load</div>
  if (!data) return <div>Loading...</div>

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.bio}</p>
    </div>
  )
}
```
