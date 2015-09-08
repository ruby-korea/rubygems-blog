---
title: gem yank의 정책 변경
layout: post
author: Arthur Nogueira Neves
author_email: arthurnn@gmail.com
---

# 경고: `gem yank`의 정책 변경

`gem yank`는 2015년 4월 20일부터 gem을 완전히 제거하게 됩니다!

## Why?

"취소(yank)"는 RubyGems.org에 등록된 gem의 인덱스를 제거할 수 있는 방법으로, 보통 의도치 않은 `gem push`의 제거나 gem 이름을 다른 사람에게 넘기고 싶을 때 사용합니다.

우리의 정책은 릴리스 취소(Yank)된 .gem 파일을 완전히 제거하지 않는 것이었습니다만, 시간이 흐름에 따라 이 정책은 수많은 지원 작업들을 만들었습니다. 대부분 실수로 올린 외부에 노출하면 안되는 코드에 대한 대응이었으며, 때문에 우리 팀원들은 이를 찾고 제거하는 데에 수 주에서부터 몇 달의 시간을 소비했습니다.

새로운 동작에 대한 설명: `gem yank`는 이제 S3와 CDN으로부터 .gem file을 제거합니다. 그러나 이것은 [웹 훅](http://ruby-korea.github.io/rubygems-guides/rubygems-org-api/#webhook-methods)을 통해 비공식적인 미러 페이지로 gem이 복사되거나, 누군가가 다운로드 하는 것을 막을 수는 없습니다. 그러므로 만약 올려서는 안되는 코드를 포함한 gem을 올렸다면 여전히 API 키, URL, 그리고 다른 민감한 정보들을 초기화해야할 필요가 있습니다.

같은 버전을 올렸을 때에 대한 정책은 이전과 마찬가지로, gem의 한 버전을 두 번 릴리스 할 수 없습니다. 그러므로 어떤 버전을 `gem yank`로 취소하고, 변경한 뒤에 다시 올릴 수 없습니다. 이러한 경우에는 버전 숫자를 올려야 합니다.

## 마지막으로

`gem yank`의 동작에 대한 우리의 생각은 원래 누군가가 악의적인, 또는 실수로 gem을 제거한 경우를 대비하기 위한 것이었습니다. 그러나 우리는 최근 몇 년간 gem을 저장하기 위해서 [버저닝](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html)이 지원되는 아마존 S3 저장소를 사용하고 있습니다. 그러므로 gem이 제거된 뒤, 필요하다면 손쉽게 복원할 수 있습니다.

이 정책 변경으로 [지원 팀에 걸리는 부하](http://help.rubygems.org)가 줄어들기를 바랍니다. 지금까지 gem yank를 다뤄본 분들의 인내심에 감사합니다.
