# GIT 사용 가이드

기본적으로 Github Flow (https://docs.github.com/ko/get-started/quickstart/github-flow) 를 따르도록 한다.

## Branch Naming Convention
- 브랜치명은 `feature/:todo`, `hotfix/:todo` 형태로 만든다.
- 커밋메시지는 자유롭게 작성한다.
- PR 은 제목은 구현한 기능에 대해서 내용은 구현하면서 작성한 커밋메시지들을 기반으로 작성한다. 필요하지 않은 경우에는 모든 커밋메시지를 쓸 필요는 없다.
- 커밋 메세지는 변경된 내용을 설명하는 명확한 문장으로 작성한다.
- 커밋 단위는 기능 단위로 한다.

## 이슈
- 이슈 트래커를 사용한다면 버그나 기능 요청을 추적하고, 해당 이슈에대한 PR을 작성하여 코드 변경사항을 논의하고 검토한다.
- 코드리뷰전 리뷰어는 PR에 커멘트를 단다.
- Pull Request Squashing 병합 전 여러개의 작은 커밋을 단일 커밋으로 스쿼시 한다.