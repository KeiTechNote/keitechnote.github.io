---
layout: single
title: github.io 블로그 만들기 (1)
date: 2024-01-28 05:30 +09:00
published: true
categories: [Vault]
tag: [Blog, Dev, github.io, 블로그, minimal-mistakes-jekyll, jekyll, theme]
toc: true
author_profile: false
#search: false
#sidebar:
#    nav: "docs"
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

사이트 가입과 설정등의 절차가 있으므로 테디노트님의 [EP05. 댓글 & 구글 애널리틱스 추가하기](https://youtu.be/anXaW9xhgcU?si=oxzrfTsX3b-bptDT) 내요을 참고하기 바랍니다.

- 설정 위치 : _config.yml > comments 

> **설정값**
- `disqus` : 코멘트 서비스 제공자
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