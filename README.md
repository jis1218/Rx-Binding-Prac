##### Rx Binding 연습

##### Gradle(app)에
```
compile 'com.jakewharton.rxbinding2:rxbinding-appcompat-v7:2.0.0'
```
##### 추가해준다. (Jake Wharton은 천재)

##### editText와 Rx 연결
```java
RxTextView.textChangeEvents(findViewById(R.id.editId));
```
##### 이것을 Observable 변수에 담아준다.
```java
Observable<TextViewTextChangeEvent> idEmitter = RxTextView.textChangeEvents(findViewById(R.id.editId));
```

##### pwEmitter를 하나 더 만들어서 Observable.combineLatest로 결합해준다.
```java
Observable<TextViewTextChangeEvent> pwEmitter = RxTextView.textChangeEvents(findViewById(R.id.editPw));
Observable.combineLatest(idEmitter, pwEmitter,(id, pw)-> id.text().length()>5 && pw.text().length()>8)
                .subscribe(
                        flag -> findViewById(R.id.btnSignin).setEnabled(flag)
                );
```

##### 위의 람다식을 풀어서 쓰면 다음과 같다.
```java
Observable.combineLatest(idEmitter, pwEmitter, new BiFunction<TextViewTextChangeEvent, TextViewTextChangeEvent, Boolean>() {
            @Override
            public Boolean apply(TextViewTextChangeEvent id, TextViewTextChangeEvent pw) throws Exception {

                return id.text().length()>5 && pw.text().length()>8;
            }
        }).subscribe(new Consumer<Boolean>() {
            @Override
            public void accept(Boolean o) throws Exception {
                findViewById(R.id.btnSignin).setEnabled(o);

            }
        });
```

##### textChangeEvents는 Observable을 return 한다. 따라서 Observable에 저장하지 않고 따로 쓸 수가 있다.
```java
RxTextView.textChangeEvents(findViewById(R.id.editText)) //textChangeEvents도 Observable을 return 한다.
        .subscribe(ch-> Log.i("RxBinding","word"+ch.text()));
```

##### 아래 코드는 좀 더 공부해야 함(map의 정확한 쓰임이 무엇인가???)
```java
RxView.clicks(findViewById(R.id.button))
                .map(button->new Random().nextInt()+"") // button에는 Button 타입의 오브젝트가 리턴되는데, 이를 문자타입으로 변경
                .subscribe(b->{ //subscribe에서는 map에서 변형된 타입을 받게 된다.
                    ((EditText) findViewById(R.id.editText)).setText(b);
                });
```
