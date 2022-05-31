---
title : "인스타그램 피드 더보기 구현"
---

```javascript
const story_data_id_increment = document.getElementsByClassName("storycards_bottom_story_data");
const story_morebtn_id_increment = document.getElementsByClassName("storycards_bottom_story_morebtn");

function isEllipsisActive(e) {
    return (e.offsetWidth < e.scrollWidth);
}

function show_me_more(id) {
    document.getElementById(id).style.display = "None";
    document.getElementById("story_data"+id.slice(-1)).style.whiteSpace = "unset";
}
for (let i = 0; i < story_data_id_increment.length; i++) {
    story_data_id_increment[i].id = "story_data" + i;
    story_morebtn_id_increment[i].id = "btn_show_more" + i;

    if (isEllipsisActive(document.getElementById("story_data" + i)) != true) {
        document.getElementById("btn_show_more" + i).style.display = "None";
    }
}
```

```css
/*스토리카드 하단 내용 본문*/
.storycards_bottom_story_data{
    display: block;
    width: 430px;
    margin-left: 10px;
    font-weight: 400;
    text-align: left;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    word-wrap: break-word;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;

}

/* 스토리카드 하단 내용 더보기 버튼 */
.storycards_bottom_story_morebtn{
    color: #8e8e8e;
    cursor: pointer;
}
```

#### 1. 아이디값 자동 부여
피드는 여러개가 주루룩 생성되어야 하기 때문에 tag에 id값을 자동으로 부여하는 로직이 필요했다. 
그래서 본문 내용과 "더보기" 버튼에 클래스를 부여하여 스타일을 주고 그 클래스에 해당하는 것들을 document.getElementsByClassName() 메소드를 이용해 가져온다.

for문을 사용하여 각각의 태그에 아이디명+숫자 를 준다. 숫자를 1씩 증가시킨다.

#### 2. 본문의 길이가 길어 말줄임표가 형성 되었는지를 판단
e.offsetWidth < e.scrollWidth
스크롤이 더 길다면 text-overflow: ellipsis 가 활성화 된 것이므로 말줄임표가 형성된것. 
true인지의 여부를 통해 알 수 있다. 

#### 3. 말줄임표가 있을 경우에만 "더보기"버튼 활성화
각각의 아이디에 대하여 말줄임표 활성화여부를 판단한 후 활성화 되지 않았다면 버튼의 display를 None으로 설정하여 숨김.

#### 4. "더보기"를 눌렀을 때 본문 내용을 전부 보여주면서 버튼은 숨긴다.
버튼 onclick 시 함수를 적용한다. 함수의 argument로 this.id를 가져오도록 했다. 
이때 버튼의 아이디만 가져오고 본문의 아이디는 가져오지 못하므로 슬라이싱을 이용해 숫자만 떼어 오는 방식을 사용했다.
이후 해당 아이디의 속성을 바꾼다.
버튼은 display = "None"
본문은 whiteSpace = "unset"
본문의 경우 text-overflow를 바꿔도 될 듯 하다.

#### 5. 아이디 값의 형태.
document.getElementById("story_data"+id.slice(-1)).style.whiteSpace = "unset";
document.getElementById("btn_show_more" + i).style.display = "None";
의 방식으로 메쏘드 안에는 여러 형태를 사용할 수 있다.

("story_data" + i).style.display = "None";
이런 방식은 사용 불가하다.

##### 추가로 공부해야 할 부분 (var, let, const)
var만이 쓰이다가 여러 버그가 발생하는 경우가 있어 나머지 선언들이 나왔다고 한다.
재지정 가능 여부가 중요한 듯 하지만 추후 이해가 더 필요하다.










