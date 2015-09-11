---
title: 기록 작성하기, 실은 다시 쓰기.
layout: post
author: Arthur Nogueira Neves
author_email: arthurnn@gmail.com
---

# 문제점
RubyGems.org는 점점 관리하기 힘들어지고 있었습니다. 코드의 관점이 아니고, git
저장소가 너무 커지고 있었죠. 저장소가 500MB를 넘었기 때문에 누군가가 저장소를
클론할 때마다 굉장히 오랜 시간이 걸렸습니다. 코드 자체는 전혀 크지 않았지만
우리가 사용하는 젬을 모두 제공해야 했습니다.
왜 RubyGems.org 젬의 의존성을 모두 제공해야 하는지 궁금해하실 수도 있습니다.
대부분의 프로젝트는 배포되었을 때 RubyGems.org로부터 간단히 젬을 설치할 수
있습니다. 하지만 RubyGems.org에 자신을 사용할 수 없게 되는 심각한 버그가 있을
수도 있습니다. 이런 버그를 해결하는 패치를 배포하기 위해서는 RubyGems.org
코드베이스가 RubyGems.org 서비스에 의존해서는 안 됩니다.
100개가 넘는 젬을 제공하기 위해서는 공간이 필요하고, 새로운 젬이 업데이트 될
때마다 지난 버전의 젬은 영원히 기록에 남습니다. Git은 분산 버전 관리 시스템이고,
당신이 저장소를 클론할 때마다 모든 브랜치, 태그, 그들이 가리키는 모든 기록을
복사하는 셈이 됩니다. 이 말은, 저장소는 그저 커지고 점점 더 클론하기 힘들어질
것이란 겁니다.

([GitHub 이슈](https://github.com/rubygems/rubygems.org/issues/610))

# 대안 해결법
`git clone --depth=1`을 실행하는 것은 쉬운 해결법이 될 수 있습니다. 하지만
저장소를 클론하는 모든 사람이 `depth` 옵션에 대해 알고 있어야 한다는 문제가
있습니다. 또한 기록을 로컬에 복사하지 않기 때문에 검색이나 `git-blame` 같은 것이
동작하지 않을 것이란 문제도 있습니다.

# 해결법
`vendor/cache` 폴더를 다른 git 저장소에 만들고, 이를 git 서브모듈로 추가합니다.
`vendor/cache` 폴더가 주요 저장소에 속해 있지 않는다면, 그 폴더의 기록은 주요
저장소에서 추적되지 않을 것입니다. 이로써 RubyGems.org 저장소는 젬이 업데이트 할
때마다 엄청나게 커지지 않아도 될 것입니다.

하지만 이는 저장소의 크기가 600MB라는 문제를 해결해주진 못합니다. 이를 해결하기
위해선, 제공하는 모든 파일을 기록에서 지우기 위해 모든 기록을 새로 써야 하는
상황이었습니다. 그리고 그게 우리가 한 일입니다. 모든 기록을 새로 기록하고 있었기
때문에 우린 추가로 몇 가지 큰 폴더와 파일을 기록에서 지우기로 했습니다.

* server/rubygems.html
* rubygems.txt
* server/rubygems.txt
* vendor/bundler_gems
* vendor/gems
* vendor/rails
* vendor/plugins

마지막으로 우린 `vendor/cache`를 기록에서 지우고 [다른 저장소](https://github.com/rubygems/rubygems.org-vendor)로
옮겼습니다.

# 왜?
RubyGems.org는 오픈 소스 프로젝트고, 기여는 언제나 환영하기에, 작고 빠른
저장소는 커뮤니티에 있어서 프로젝트에 접근하기 쉽게 만듭니다.

# 최종 결과
<pre>
<code class="bash">
$ git clone git@github.com:rubygems/rubygems.org-backup.git
$ du -skh .
536M    .

$ git clone git@github.com:rubygems/rubygems.org.git
$ du -skh .
11M    .
</code>
</pre>

# 개발에 준 영향

## 모든 사람이 리베이스 해야 합니다
`rubygems/rubygems.org`에 풀 리퀘스트를 만든 사람은 모두 새로운 기록에 대해
리베이스 해야 합니다. 로컬에서 `rubygems/rubygems.org`의 클론을 지우고 다시
클론하거나, `git fetch --all; git pull --rebase`를 실행하면 됩니다.

## 의존성 설치
아무것도 변하지 않았으며 `bundle install`이 해결할 것입니다.

## 업데이트 또는 새로운 젬 추가하기
`Gemfile`에 새로운 젬을 추가하거나 `bundle update gem_name`을 실행하세요. 그리고
`Gemfile`과 `Gemfile.lock`의 변경만을 포함한 풀 리퀘스트를 보내세요.
`vendor/cache` 폴더를 업데이트 하거나 벤더 저장소에 풀 리퀘스트를 보낼 필요가
없어졌습니다. RubyGems 팀이 벤더 폴더를 업데이트 할 것입니다.
