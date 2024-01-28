---
layout: single
title: github.io 블로그 만들기 (시즌1 요약)
date: 2024-01-28 05:30 +09:00
published: true
categories: [Vault]
tag: [Blog, Dev, github.io, 블로그, minimal-mistakes-jekyll, jekyll, theme, 테디노트, 요약]
toc: true
author_profile: false
---

**[주의사항]** 
본 Post는 테디노트님의 [깃헙(Github) 블로그 만들기 - 시즌1](https://youtube.com/playlist?list=PLIMb_GuNnFwfQBZQwD-vCZENL5YLDZekr&si=FeUE2MrsigUqlWuN) 내용을 정리한 글입니다. 문제시 삭제될 수 있습니다.
{: .notice--danger} 

본 Post 에서 다루지 않은 다양한 설정방법은 [Minimal-Mistakes-Jekyll 가이드 문서](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) 에서 살펴보기 바랍니다. 


### Theme Settings 설정하기

- 설정 위치 : _config.yml > minimal_mistakes_skin

```yml
minimal_mistakes_skin: "air"  
```

### Site Settings 설정하기

- 설정 위치 : _config.yml > Site Settings

> **설정값**
- `title` : 블로그에 들어왔을 때 최상단에 표기되는 블로그 이름
- `title_seperator` : 브라우저 창 부분에 표기되는 Post명과 블로그 이름을 나눠주는 구분자
- `subtitle` : title 아래에 표기되는 태그라인
- `name` : 블로그 소유자 이름이나 닉네님
- `description` : 블로그 설명
- `url` : 블로그 도메인 URL
- `logo` : 블로그 타이틀 왼쪽에 나타날 이미지 위치
- `breadcrumbs` : Post 네비게이션 기능 활성화, true 의 경우, Post 상단에 "/categories/title" 형태로 표기

```yml
locale          : "ko-KR"
title           : "D.Kei Tech Note"
title_seperator : "|"
subtitle        : "small prositive action"
name            : "D.Kei"
description     : "D.Kei 기술노트"
url             : "https://keitechnote.github.io"
(생략)
logo            : "/assets/images/logo.png"
breadcrumbs     : true
```

### Site Author 설정하기

- 설정 위치 : _config.yml > Site Author

> **설정값**
- `name` : 블로그 소유자 이름이나 닉네임
- `bio`  : 블로그 설명
- `location` : 위치
- `links` : 다양한 연결 정보를 지정

```yml
(생략)
author:
    name    : "D.Kei"
    bio     : "D.Kei 개발 블로그"
    location: "South Korea"
    links:
        - label: "Email"
          icon : "fas fa-fw fa-envelope-square"
          url  : "mailto: <메일주소>"
        - label: "Website"
          icon : "fas fa-fw fa-link"
          url  : "<사이트 or 블로그 주소>"
        - label: "Github"
          icon : "fab fa-fw fa-github"
          url  : "<Github 주소>"
```

### 기본값 설정하기

- 설정 위치 : _config.yml > defaults

> **설정값**
- `show_date` : Post 작성일 표기 (없는 경우 추가), 날짜 표기 형식은 _config.yml 하단에 설정해 원하는 표기 형식으로 변경할 수 있음 (날짜 표기 형식 : [참고](https://www.shortcutfoo.com/app/dojos/ruby-date-format-strftime/cheatsheet))

```yml
(생략)
defaults:
    (생략)
    values:
        (생략)
        show_date: true

date_format: "%Y-%m-%d"
```

### 댓글 & 구글 애널리틱스 기능 설정하기

사이트 가입과 설정등의 절차가 있으므로 테디노트님의 [EP05. 댓글 & 구글 애널리틱스 추가하기](https://youtu.be/anXaW9xhgcU?si=oxzrfTsX3b-bptDT) 내용을 참고하기 바랍니다.

- 설정 위치 : _config.yml > comments 

> **설정값**
- `provider` : 코멘트 서비스 제공자 설정
- `shortname` : disqus 에 등록된 Shortname


```yml
(생략)
# Site Settings
(생략)
comments:
    provider: "disqus"
    disqus:
        shortname: <disqus Shortname>
```

- 설정 위치 : _config.yml > analytics

> **설정값**
- `provider` : 애널리틱스 서비스 제공자 설정
- `tracking_id` : 서비스 제공자가 구분하는 Tracking ID


```yml
(생략)
# Analytics
analytics:
    provider: "google-gtag"
    google:
        tracking_id : "<Google Analytics Tracking ID"
        anonymize_ip: false      
```

### Outputting 설정하기

- 설정 위치 : _config.yml > Outputting

> **설정값**
- `paginate` : 몇개 Post 부터 페이지로 나눠줄지 설정함
- `timezone` : 타임존

```yml
(생략)
# Outputting
permalink: /:categories/:title/ # 기본값
paginate: 5 # 기본값
paginate_path: /page:num/ # 기본값
timezone: Asia/Seoul 
```

timezone 을 설정한 경우, timezone 정보를 가져올 수 있도록 ```gem install tzinfo-data``` 를 설치합니다. 설치한 정보는 Gemfile 에 등록합니다. 

- 설정 위치 : Gemfile

```
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw]
```

### 카테고리, 태그, 검색 기능 설정하기

jekyll 테마에는 화면 상단에 Categories, Tags 등 목록이 노출되는 기능을 갖고 있으나 이 기능이 주석처리되어 비활성화 되어 있습니다. 따라서, 주석처리되어 있는 내용을 모두 주석해제 후 설정을 추가하면 화면 상단에 원하는 목록을 볼 수 있습니다. 

- 설정 위치 : _config.yml > jekyll-archives

```yml
(생략)
jekyll-archives:
  enabled:
    - categories
    - tags
  layouts:
    category: archive-taxonomy
    tag: archive-taxonomy
  permalinks:
    category: /categories/:name/
    tag: /tags/:name
```

화면에 노출되는 Categories 와 Tags 를 눌렀을 때, 표현될 각각의 페이지를 생성합니다. 

`_pages` 폴더(없으면 _pages 폴더를 생성합니다.) 에 `category-archive.md` 파일을 생성한 후 다음의 내용을 기입합니다. 

- 설정 위치 : _pages > category-archive.md

```md
---
title: "category"
layout: categories
permalink: /categories/
author_profile: true
sidebar_main: true
---
```

`tag-archive.md` 파일을 생성한 후 다음의 내용을 기입합니다.

- 설정 위치 : _pages > tag-archive.md

```md
---
title: "tag"
layout: tags
permalink: /tags/
author_profile: true
sidebar_main: true
---
```

`search.md` 파일을 생성한 후 다음의 내용을 기입합니다. 

- 설정 위치 : _pages > search.md

```md
---
title: "search"
layout: search
permalink: /search/
author_profile: true
sidebar_main: true
---
```


화면 상단에 노출되도록 내비게이션 파일을 수정합니다. 

- 설정 위치 : _data > navigation.yml > main

> **설정값**
- `title` : main 화면 상단에 표기될 이름
- `url` : 이름을 선택했을 때, 경로

```yml
main:
    - title: "Category"
      url: /categories/
    - title: "Tag"
      url: /tags/
    - title: "Search"
      url: /search/
```

### 404 페이지 설정하기

- 설정 위치 : _pages > 404.md

```md
---
title: "Page Not Found"
excerpt: "Page not found. Your pixels are in another canvas."
sitemap: false
permalink: /404.html
---

Sorry, but the page you were trying to view does not exist.
```

또는 "404 Not Found" 이미지를 보여줄 수도 있습니다. 이 경우 404.md 파일의 글씨 대신 다음의 내용을 추가하면 됩니다. 

```md
(생략)
![](https://i.stack.imgur.com/6M513.png)
```
