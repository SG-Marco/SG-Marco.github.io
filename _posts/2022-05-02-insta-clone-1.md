---
layout: 'single'
title: '인스타그램 클론 1차'
---

***
https://github.com/SG-Marco/insta_clone_1_1



***


<video width="100%" height="100%" controls="controls">
  <source src="/assets/insta_clone_1.mp4" type="video/mp4">
</video>


+ 모달 부분

js 부분은 아직 미흡하여 검색한 내용대로 사용. 
모달 버튼 누르면 모달 활성화되면서 body 태그 부분의 스크롤을 비활성화. (overflow: hidden)
비활성화 시키는 클래스를 만든 이후 js로 조건에 따라 add or remove.
모달 꺼지는 경우는 
 1. 취소버튼 
 2. 모달 배경 클릭
 3. esc버튼 클릭

```html

<!--modal-->
<div class="my-modal-bg" id="my-modal-bg">
    <div class="my-modal-case" id="my-modal-case">
        <div class="my-modal" id="my-modal">
            <button class="my-modal-btn btn1">신고</button>
            <button class="my-modal-btn btn2">팔로우 취소</button>
            <button class="my-modal-btn btn3">게시물로 이동</button>
            <button class="my-modal-btn btn4">공유 대상...</button>
            <button class="my-modal-btn btn5">링크 복사</button>
            <button class="my-modal-btn btn6">퍼가기</button>
            <button class="my-modal-btn btn7 modal_cancel_btn7" id="modal_cancel_btn7">취소</button>
        </div>

    </div>
</div>

```

```css

/*modal===================================*/


#my-modal-bg{
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(0, 0, 0, 0.65);
    display: none;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    z-index: 100;
    transition: opacity 0.5s;
}

#my-modal-active-btn_1{
    cursor: pointer;
}

.my-modal-case, #my-modal-case{
    width: 260px;
    flex-shrink: 1;
    justify-content: center;
    max-height: calc(100% - 40px);
    flex-direction: column;
    align-items: stretch;
    position: relative;
}
@media (min-width: 736px) {
    .my-modal-case , #my-modal-case{
        width: 400px;
    }
}

.my-modal, #my-modal {
    background-color: rgba(255, 255, 255, 1);
    border-radius: 12px;
    max-height: 100%;
    overflow: hidden;
    flex-shrink: 0;
    position: relative;
    align-items: stretch;
    display: flex;
    flex-direction: column;
}

.my-modal-btn {
    background-color: transparent;
    cursor: pointer;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
    font-size: 14px;
    line-height: 1.5;
    min-height: 48px;
    padding: 8px;
    text-align: center;
    user-select: none;
    vertical-align: middle;
    display: inline-block;
    border: 0;
    border-top: 1px solid rgba(var(--b6a,219,219,219),1);
}


.no-scroll{
    overflow: hidden;
}
```

***
+ insta_clone_1_1.js


`````JavaScript

    // ...버튼 누르면 모달창 켜짐
const modal = document.getElementById("my-modal-bg");
const btnModal_1 = document.getElementById("my-modal-active-btn_1");
const btnModal_2 = document.getElementById("my-modal-active-btn_2");
const btnModal_3 = document.getElementById("my-modal-active-btn_3");

btnModal_1.addEventListener("click", e => {
    modal.style.display = "flex";
    document.querySelector('body').classList.add('no-scroll');
});
btnModal_2.addEventListener("click", e => {
    modal.style.display = "flex";
    document.querySelector('body').classList.add('no-scroll');
});
btnModal_3.addEventListener("click", e => {
    modal.style.display = "flex";
    document.querySelector('body').classList.add('no-scroll');
});

// 모달 메뉴에서 취소버튼 누르면 꺼짐
const closeBtn = modal.querySelector(".modal_cancel_btn7")
closeBtn.addEventListener("click", e => {
    modal.style.display = "none"
    document.querySelector('body').classList.remove('no-scroll');
})
// 모달창 밖의 회색영역 클릭하면 모달 꺼짐
modal.addEventListener("click", e => {
    const evTarget = e.target
    if(evTarget.classList.contains("my-modal-bg")) {
        modal.style.display = "none"
        document.querySelector('body').classList.remove('no-scroll');

    }
})

// esc누르면 모달 꺼짐
window.addEventListener("keyup", e => {
    if(modal.style.display === "flex" && e.key === "Escape") {
        modal.style.display = "none"
        document.querySelector('body').classList.remove('no-scroll');
    }
})

``````


********
+ footer 부분 구현
```css
footer {
    flex-grow: 1;
    flex-direction: column;
    align-items: center;
    margin: 0;
    top: 100px;
    right: 0;
    width: 100%;
    max-width: 293px;
    height: 100vh;
    margin-bottom: 30px;
    position: fixed;
    left: 54%;
    transform: translateX(43%);
    padding-top: 20px;
}
```
position: fixed 인 경우 부모 태그의 영향을 받지 않는데 이 부분을 transform 속성을 사용하여 인스타그램 페이지와 비슷하게 동작하도록 설정.
수치는 경험적으로 알아낼 수 밖에 없었다. 실제 인스타그램에서는 어떻게 구현했는지 모른다.



*******
+ a 태그의 효과 없애기
```css
a{
        text-decoration: none;
}
a:hover { text-decoration:none !important }
```
링크가 걸린 부분은 파란색과 밑줄로 표시되는데 text-decoration 을 없애주면 밑줄을 비활성화 시킨다.
색상은 다시 지정해 주어야 한다.
마우스를 위로 올렸을 때 역시 효과가 활성화 되는데 이것은 hover 옵션을 이용하여 없앨 수 있다.

저 두 속성을 하나로 묶을 수 있는지의 여부를 모르겠다. 


*******
+ 나열된 리스트들을 가로로 배치하면서 사이에 특정 기호나 문구 삽입
```css
insta_infos:not(:last-of-type)::after{
    content: '\00B7';
    color: rgba(var(--edc,199,199,199),1);
    margin: 0 .25em 0 .25em;
}
```

::after를 이용하여 적용 가능하다. 마지막 리스트 뒤에는 없어야 하므로 last-of-type는 제외.
\00B7은 점. 



*******
+ 특정 길이 이상의 문장을 말줄임표로
```css
.content_content{
    display: block;
    width: 400px;
    margin-left: 10px;
    font-weight: 400;
     text-align: left;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    word-wrap: break-word;
    /*display: -webkit-box;*/
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
}
```
display: inline 에서는 안된다고 한다.
텍스트는 좌로 정렬 후 white-space: nowrap을 이용하여 다음 줄로 넘어가지 않도록 한다.
overflow: hidden으로 넘어가는 부분은 가려주고, text-overflow: ellipsis로 말줄임표를 준다.
-webkit-line-clamp는 보여줄 줄의 수.

********
+ 포스트 우측 상단의 점 세개

`````html

<svg aria-label="옵션 더 보기" color="#262626" fill="#262626" height="24" role="img"
     viewBox="0 0 24 24" width="24">
    <circle cx="12" cy="12" r="1.5"></circle>
    <circle cx="6" cy="12" r="1.5"></circle>
    <circle cx="18" cy="12" r="1.5"></circle>
</svg>

`````
circle 태그를 이용하여 점을 그려버리는 식으로 표현했더라.
이런 방식은 상단바 검색창의 돋보기 모양도 같다. 




























