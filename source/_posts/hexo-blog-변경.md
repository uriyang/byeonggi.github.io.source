---
title: hexo-blog 변경
date: 2017-08-10 23:48:03
categories: 
- hexo

tags:
- hexo

---

# 1. hexo 블로그 사용 시작

지킬 블로그 사용하다가 설치와 환경 세팅면에서 까다로움을 느껴서
본인은 루비 유저가 아닌지라... 결국 최근에 
hexo라는 nodejs 기반의 정적 사이트 생성기를 도입을 하게되었다!!

그리고 기본 테마가 예쁜게 많고, 역시 문서화가 아주 훌륭하다는 점이 바꾸게 된 계기이다.


# 2. 간단 사용법 

- 기본적으로 NODEJS 와 NPM 이 깔려있어야 한다.
- [설치 상세 안내](https://hexo.io/ko/docs/index.html)


- 기본적인 설치 명령어 

``` bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```
- POST 쓰기 

``` bash
hexo new [layout] <title>
``` 
- 로컬에서 미리보기

``` bash
hexo serve
``` 


# 3. 사용 후기

일단 지킬보다 접근성은 좋은 것 같다.
단점은 정적 소스만 빌드해서 올리기 때문에 , 따로 레포를 파서 블로그 생성 소스를 
따로 관리해야하는 단점이 있다. => 이 부분은 보안할 방법이 있는지 확인해봐야겠다. 




