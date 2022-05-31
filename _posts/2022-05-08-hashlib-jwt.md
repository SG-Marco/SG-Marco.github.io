---
layout : 'single'
title : 'hashlib & JWT'
---

1. hashlib

서버에 비밀번호를 저장할 때 raw 데이터를 저장하는 것은 보안상 위험이 있어 암호화 하게 된다.
그중 한가지 방법이 hashlib. python 기본 라이브러리로 제공된다.

```python
pw_hash = hashlib.sha256(pw_receive.encode('utf-8')).hexdigest()
```
이런 식으로 암호화가 되면 decode는 불가능하다.

encode 된 암호를 서버에 저장하고, 사용자가 로그인시 암호를 입력하게 되면 같은 방식으로 암호화한 후 비교하면 된다.

강화된 보안을 위해 여러가지 암호화 방식을 복합적으로 사용 가능하다.


2. JWT (JSON Web Token)

암호화를 한 정보라는 점에선 위의 방법과 유사성이 있으나 가장 큰 차이는 decode의 여부에 있다.
```python
 # encode
 payload = {
            '_id': result["_id"],
            'exp': datetime.datetime.utcnow() + datetime.timedelta(seconds=600)
        }
 token = jwt.encode(payload, SECRET_KEY, algorithm='HS256')

 return jsonify({'result': 'success', 'token': token})
 
 # decode
 payload = jwt.decode(token_receive, SECRET_KEY, algorithms=['HS256'])
 ```
 payload에 원하는 정보를 담을 수 있다. 
 SECRET_KEY 를 통해 자신만이 해독할 수 있도록 보안을 강화할 수 있다.
 만료 시간 발행도 가능하다.
 암호화 했던 방식을 안다면 decode를 할 수 있고 안에 실려있는 payload에 있는 data를 볼 수 있게 된다.
        
