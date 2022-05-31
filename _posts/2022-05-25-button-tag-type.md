---
layout : single
title : "HTML Button Tag Type 의 중요성"
---

### Button Types
#### There are three types of buttons:

+ submit — Submits the current form data. (This is default.)
+ reset — Resets data in the current form.
+ button — Just a button. Its effects must be controlled by something else (that is, with JavaScript).


### Difference between `<button type="submit">` and `<input type="submit">`

Both `<button type="submit">` and `<input type="submit">` display as buttons and cause the form data to be submitted to the server.  

The difference is that `<button>` can have content, whereas `<input>` cannot (it is a null element).  

While the button-text of an `<input>` can be specified, you cannot add markup to the text or insert a picture.    

So `<button>` has a wider array of display options.

There are some problems with <button> on older, non-standard browsers (such as Internet Explorer 6), but the vast majority of users today will not encounter them.



Read more: https://html.com/attributes/button-type/#ixzz7UFvVrWze

---

프로젝트에서 오류가 발생했는데 마치 json파일을 js에서 때때로(사실 거의 항상) 전달받지 못하는 듯 하게 보였다.   
그래서 json 오류에 대해 열심히 알아보았다.  
fetch를 사용했는데 대신에 axios를 사용하라느니 await가 어쩌고 저쩌고.   
time delay도 주고 해 봤으나 모두 실패했었다.   
결론은 HTML의 Button Tag의 type이 잘못된 것이었다.   

발견한 방법은 개발자도구 콘솔창에서 오류 발생시 말풍선으로 경고를 주는 것이었다.   
개발자도구가 익숙하지 않기도 했고 Button type should be 'Button', 'Submit' or 'Reset'...    
이런 말이어서 저 문제는 해당 사항이 없을거라고 판단했었다.   
하지만 Button의 속성이 submit이 default여서 발생한 문제였다.  
submit은 자동으로 새로고침 하는 속성이 있다.  

항상 오류 메세지를 자세히 읽는 습관을 들이자.
