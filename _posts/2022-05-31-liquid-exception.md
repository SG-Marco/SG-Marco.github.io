---
title "Liquid Exception: Liquid syntax error"
---

Jekyll 을 이용한 Github Pages에서 발생할 수 있는 에러이다.

Jekyll에서 사용되는 liquid는 {{와 }}를 escape 문자로 사용하는데, md문서에 {{, }}가 있는 경우 에러 메시지를 출력한다.

![image](https://user-images.githubusercontent.com/93754352/171115669-8665892c-15bb-404b-8c85-ffb32eda7510.png)

raw와 endraw를 이용해 감싸주어 해결할 수 있다.
이 방법을 markdown으로 적을 수 있는 방법은????

---

_posts
에 들어가서 파일을 클릭해보면 제목 옆에 녹색 틱마크 혹은 빨간 엑스마크가 나온다.
엑스마크를 통해 에러를 확인할 수 있다.
