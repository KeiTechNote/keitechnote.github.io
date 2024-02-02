---
layout: single
title: github.io 블로그 만들기 (시즌2 요약)
date: 2024-01-29 05:30 +09:00
published: true
categories: [Vault]
tag: [Blog, Dev, github.io, 블로그, minimal-mistakes-jekyll, jekyll, theme, 테디노트, 요약]
toc: true
author_profile: false
---

**[주의사항]** 
본 Post는 테디노트님의 [깃헙(Github) 블로그 만들기 - 시즌2](https://youtube.com/playlist?list=PLIMb_GuNnFwfQBZQwD-vCZENL5YLDZekr&si=WBE1G2_86xWWPU5F) 내용을 정리한 글입니다. 문제시 삭제될 수 있습니다.
{: .notice--danger}

본 Post 에서 다루지 않은 다양한 설정방법은 [Minimal-Mistakes-Jekyll 가이드 문서](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) 에서 살펴보기 바랍니다. 

### 목차 (Table of Contents) 설정하기

Markdown 에서 목차는 `#` 으로 지정되는 Heading 에 대해서 적용됩니다. `#` 이 늘어날 수록 `#` 가 적은 Heading 의 하위 목차가 됩니다. 
Post 에서 목차 사용 여부는 상단에서 설정으로 지정할 수 있습니다. 

- 설정 위치 : 각 Post

```md
---
(생략)
toc: true
---
```
### 목차 (Table of Contents) 고정하기

TOC 를 고정시키면 Post 에서 스크롤을 아래로 이동시키면 TOC 가 유지되면서 현재 위치에 색상이 표현됩니다. 

- 설정 위치 : 각 Post

```md
---
(생략)
toc_sticky: true
---
```

### 목차 (Table of Contents) 타이틀 변경하기

TOC 타이틀을 "On This Page" 에서 원하는 이름으로 변경할 수 있습니다. 

- 설정 위치 : 각 Post

```md
---
(생략)
toc_label: 목차
---
```

### 목차 (Table of Contents) 아이콘 변경하기

TOC 아이콘을 원하는 모습으로 변경할 수 있습니다. 
설정 방법은 각 Post 에서 다음의 옵션을 추가하면 됩니다. 
이때, 추가할 아이콘은 [Font Awesome](https://fontawesome.com/v5/search?o=r&m=free&s=solid) 에서 찾을 수 있습니다. 
(사용법은 [Youtube 아이콘 추가하기](./2024-01-27-github-blog-season2.md#메인-화면-사이드바에-youtube-아이콘-추가하기)를 참고하기 바랍니다.)

- 설정 위치 : 각 Post

```md
---
(생략)
toc_icon: "fas fa-th-list"
---
```

### 목차 (Table of Contents) 설정을 모든 Post 일괄 적용하기

지금까지 각 설정은 Post 에서 진행했습니다. 초기 블로그를 설정하고 있다면, Post 가 많지 않아 쉽게 수정할 수 있지만, 
오랬동안 블로그를 운영해 왔다면 각 Post 마다 하나씩 적용하는 것은 무리가 있습니다. 
또한 다음 Post 에 작성할 때, 설정이 누락되는 문제도 발생할 수 있습니다. 
따라서, 모든 Post 에서 동일한 설정이 적용될 수 있도록 하겠습니다. 
Post 에 설정했던 정보를 잘라내어 _config.yml 파일에 추가합니다. 

- 설정 위치 : _config.yml > defaults > values

```yml
(생략)
defaults:
    (생략)
    values:
        (생략)
        toc: true
        toc_sticky: true
        toc_label: Table of Contents
        toc_icon: "fas fa-th-list"
```

### 구글, 네이버 검색 엔진 등록하기

사이트 로그인과 설정등의 절차가 있으므로 테디노트님의 [EP09. 구글, 네이버 검색엔진 등록하기](https://youtu.be/OxRZrg0u6h4?si=Q5Kmd7Rm9KLKYZGr) 내용을 참고하시기 바랍니다.

> 참고
- [구글 서치엔진](https://search.google.com/search-console?hl=ko)
- [네이버 검색엔진](https://searchadvisor.naver.com/)

### 커스텀 스타일 적용하기

`{: .notice--danger}` 와 같이 약속된 예약어를 사용하면, 지정된 스타일을 적용되도록 되어 있습니다. 
현재 minimal-mistakes-jekyll 에 등록되어 있는 예약어외에 입맛에 맞게 추가할 수도 있습니다. 
우선 예약어를 작성합니다. 

- 설정 파일 : _sass > minimal-mistakes > _utilities.scss

```css
(생략)
// 이미지를 추가하면 지정된 크기가 50% resize 됩니다. 
.img-width-half {
    height: 50%;
    width: 50%;
}
```

다음과 같이 사용합니다. 중앙 정렬도 함께 적용합니다. 

`![샘플](샘플 이미지 경로){: .img-width-half .aligh-center}`

![샘플](/assets/images/logo.png){: .img-width-half .aligh-center}


### 폰트 종류 설정하기

구글 [폰트](https://fonts.google.com/?subset=korean&noto.script=Kore) 에서 원하는 스타일의 폰트를 확인한 후 등록합니다. 

- 설정 파일 : _sass > minimal-mistakes.scss

```css
(생략)
/* Google Fonts */
@import url("https://fonts.googleapis.com/css?family=Noto+Serif+KR");

```

사용할 폰트 등록이 완료되었으면, 사용할 수 있도록 설정합니다. 

- 설정 파일 : _scss > minimal_mistakes > _variables.scss

```css
(생략)
/* system typefaces */
(생략)
    $sans-serif: -apple-system, BlinkMacSystemFont, "Noto Serif KR", "Roboto", "Segoe UI", "Helvetica Neue", "Lucida Grande", Arial, sans-serif !default;
```

### 폰트 크기 설정하기 

본문 폰트 크기(font-size)는 1em으로 설정되어 있으며, 0.1 단위로 조절할 수 있습니다. 
본문 폰트 크기는 _page 파일에서 설정합니다.

- 설정 파일 : _sass > _page.scss 

```css
(생략)
  p,
  li,
  dl {
    font-size: 0.8em;
  }
```

### Post 상단 확대하기 (사이드바 제거하기)

Post 설정의 SideBar 내용을 주석처리합니다.

- 설정 위치 : 각 Post

```md
---
(생략)
sidebar:        <- 제거 or 주석처리(#)
    nav: "docs" <- 제거 or 주석처리(#)
---
```

Post 의 CSS 가 정의된 `_page.scss` 를 수정합니다. 

- 설정 위치 : _sass > minimal-mistakes > _page.scss 

CSS 파일 중 `.page` 내용을 살펴보면 두 곳에 폭을 설정하는 `width` 가 있으며 모두 100% 사용하지 않고 계산을 통해 일부만을 사용하는 것을 알 수 있습니다. Post 를 100% 활용하기 위해 차감되는 두 부분을 다음과 같이 변경합니다. 

```
변경 전 ) width: calc(100% - #{$right-sidebar-width-narrow}); 
변경 후 ) width: calc(100%);
```

```css
(생략)
.page {
  @include breakpoint($large) {
    float: right;
    width: calc(100%); <- 변경된 부분
    padding-right: $right-sidebar-width-narrow;
  }

  @include breakpoint($x-large) {
    width: calc(100%); <- 변경된 부분
    padding-right: $right-sidebar-width;
  }
  (생략)
}
```

CSS 파일은 색상, 폰트 등 보여주는 부분들을 조절하기 때문에 Post 화면 새로 고침으로 적용된 사항을 확인할 수 있습니다. 
만약 Post 에서 Sidebar 를 수정(제거 or 주석처리)하지 않고 사용한다면, Sidebar 에 표기되던 부분은 Post 이전에 표기됩니다. 

### Post 하단 확대하기 (사이드바 제거하기)

Post 하단 화면은 `related` 로 정의하고 있습니다. Post 화면에서 수정합니다. 

- 설정 파일 : _sass > minimal-mistakes > _pages.scss

```
변경 전 ) width: calc(100% - #{$right-sidebar-width-narrow});
변경 후 ) width: calc(100%); 
```

```css
(생략)
.page__related {
    (생략)
    @include breakpoint($large) {
        float: right;
        width: calc(100%); <- 수정한 부분
    }

    @include breakpoint($x-large) {
        width: calc(100%); <- 수정한 부분
    }
    (생략)
}
```

### 네비게이션 수정하기 (사이드바 제거하기)

Post 를 선택했을 때, 네비게이션에서 Post 위치 표기 방식을 변경합니다. Post 시작위치에 맞춰 좌측 정렬을 하기 위해 width 를 수정합니다. 

- 설정 위치 : _sass > _navigation.scss

```
변경 전) width: calc(100% - #{$right-sidebar-width-narrow});
변경 후) width: calc(100%);
```

```css
(생략)
.breadcrumbs {
  (생략)
  ol {
    (생략)
    @include breakpoint($large) {
      float: right;
      width: calc(100%); <- 수정한 부분
    }

    @include breakpoint($x-large) {
      width: calc(100%); <- 수정한 부분
    }
  }
}
```

### LaTeX 수식 지원 설정하기

설정에는 `mathjax-support.html` 파일이 필요합니다. 따라서, 테디노트님의 [EP21. LaTeX 수식 문법을 지원하는 mathjax를 블로그에 적용하기](https://youtu.be/3O08iA_BFbM?si=jqq_1F3WB4_W0PN8) 내용을 참고하시기 바랍니다.

다운받은 mathjax-support.html 파일을 다음의 경로에 저장합니다.

- 저장 위치 : _include

저장한 html 파일을 LaTeX 문법을 사용하는 Post 에서 불러오도록 설정합니다. 

- 설정 위치 : _includes > head > custom.html

```html
{% raw %}
{% if page.use_math %}
    {% include mathjax-support.html %}
{% endif %}
{% endraw %}
```

코드는 `page` (즉 Post) 에서 상단의 설정부분에 `use_math` 값이 `true` 인 경우, `mathjax-support.html` 파일을 `include` (포함) 하는 코드입니다. 따라서, LaTeX 문법을 사용하는 Post 상단 설정 부분에 다음의 값을 추가하면 LaTeX 문법이 정상적으로 처리됩니다. `use_math` 값이 없거나 `false` 라면, 동작하지 않습니다. 

```md
---
(생략)
use_math: true
---
```