# jquery validation 설정

## 정규식으로 휴대폰 번호 체크하기
### 1. 문제상황
회원등록 jsp화면을 작성중에 휴대폰번호형식을 검증해야하는 상황이 발생하였다.
일반적인 방법으로는 input text를 3개를 만들어서 값을 입력을 받은 다음에 javascript로 다시 그값을 조합시켜서 등록시 처리하도록 하는 방법이 있다.
하지만 너무 번거롭고 지저분하게 느껴졌다. 기존에 jquery validation 플러그인을 사용하고 있으니 이것을 이용해서 깔끔하게 하고 싶었다.
### 2. 해결방법
* jquery validation에서 제공하는 정규식검증 기능을 찾아본다.
* 우리나라 전화번호 규격에 맞는 정규식패턴이 있는지 찾아본다. 
### 3. 해결계획
* 열심히 구글링한다.
* stackoverflow에서 찾아본다.
### 4. 필요한 재료
* jQuery.validator.addMethod : jquery validation 에 validate메소드를 추가하는 방법
* addMethod를 통해 추가한 validation method를 rules에서 선언해서 사용한다.
### 5. 최종모습
```javascript
        jQuery.validator.addMethod("phone", function (phone_number, element) {
            phone_number = phone_number.replace(/\s+/g, "");
            return this.optional(element) || phone_number.length > 9 &&
                phone_number.match(/^\(?([\d\s]{2}|[\d\s]{3})-([\d\s]{3}|[\d\s]{4})-[\d\s]{4}$/);
        }, "010-1234-1234 형식으로 입력하세요.");

        $("#userForm").validate({
            rules: {
                userTel: {
                    required: true,
                    phone: true
                }
            },
            //validation이 끝난 이후의 submit 직전 추가 작업할 부분
            submitHandler: function(form) {
                var f = confirm('등록하시겠습니까?');
                if(f){
                    form.submit();
                } else {
                    return false;
                }
            }
        });
```
### 6. 뒤돌아보며
전화번호에 해당하는 정규식기능을 찾아보면서 나와 딱 맞는 정규식구문을 찾지 못했다.
내가 원했던것은 중간자리가 3자리 또는 4자리를 허용하는 것이었는데 내가 찾은것은 딱 3자리만 허용하는 것이었다.
그래서 | 를 이용해서 둘다 허용하는 방법도 알게되어 좋았다.
* 3자리만 허용할때 : [\d\s]{3}
* 3자리 또는 4자리를 허용할때 : ([\d\s]{3}|[\d\s]{4})

## remote로 userid 중복점검체크하기
```javascript
        $("#userForm").validate({
            rules: {
                userEmail: {
                    required: true,
                    remote: {
                        url: "/cms/user/checkUserEmailDuplication.do",
                        type: "get",
                        data: {
                            userEmail: function() {
                                var userEmail = $("#txtEmail").val();
                                return userEmail;
                            }
                        }
                    }
                }
            }
            //validation이 끝난 이후의 submit 직전 추가 작업할 부분
            submitHandler: function(form) {
                var f = confirm('등록하시겠습니까?');
                if(f){
                    form.submit();
                } else {
                    return false;
                }
            }
        });
    }
```