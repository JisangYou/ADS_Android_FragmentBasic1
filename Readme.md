# ADS04 Android

## 수업 내용
- Fragment학습

## Code Review

### mainActivity

```java
public class MainActivity extends AppCompatActivity implements ListFragment.Callback {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initFragment();
    }

    public void initFragment(){
        // 1. 프래그먼트 매니저
        FragmentManager manager = getSupportFragmentManager();
        // 2. 트랜잭션 처리자
        FragmentTransaction transaction = manager.beginTransaction(); // 프래그먼트를 처리하기 위한 트랜잭션을 시작
        // 3. 액티비티 레이아웃에 프래그먼트를 부착하고
        Log.d("Activity","==========before add()");
        transaction.add(R.id.container, new ListFragment()); // <- ListFragment.onAttach(this);

        Log.d("Activity","==========after add()");
        // 4. 커밋해서 완료
        transaction.commit();
        Log.d("Activity","==========after commit");
    }

    @Override
    public void goDetail(){
        Log.d("Activity","==========before commit()");
        getSupportFragmentManager()
                .beginTransaction()
                .add(R.id.container, new DetailFragment())
                .addToBackStack(null) // <- 이 명령어를 호출하면 트랜잭션 전체를 스택에 담는다

                .commit();
        Log.d("Activity","==========after commit");
    }

    @Override
    protected void onStart() {
        Log.d("Activity","==========onStart()");
        super.onStart();
    }

    @Override
    protected void onResume() {
        Log.d("Activity","==========onResume()");
        super.onResume();
    }
}
```

### ListFragment

```Java
public class ListFragment extends Fragment {


    public ListFragment() {
        // Required empty public constructor
        // 여기는 코드를 작성하면 안된다
    }

    @Override
    public void onAttach(Context context) { // <- 이 컨텍스트가 나를 삽입한 액티비티이다.
        // 1. 나를 사용한 액티비티가 내가 제공한 인터페이스를 구현했는지 확인
        if(context instanceof Callback){
            callback = (Callback) context; // 2. 구현했다면 인터페이스로 캐스팅해서 사용
        }

        super.onAttach(context);
    }

    @Override
    public void onDetach() {
        Log.d("Fragment","==========onDetach()");
        super.onDetach();
    }

    Callback callback = null;

    Button goDetail;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_list, container, false);
        goDetail = (Button) view.findViewById(R.id.button);
        goDetail.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                callback.goDetail(); // 내가 설계해 놓은 인터페이스를 호출한다
                // 나를 사용하는 측은 이 인터페이스를 강제로 구현해야 한다.
            }
        });
        return view;
    }

    public interface Callback {
        public void goDetail();
    }

    @Override
    public void onStart() {
        Log.d("Fragment","==========onStart()");
        super.onStart();
    }

    @Override
    public void onResume() {
        Log.d("Fragment","==========onResume()");
        super.onResume();
    }
}
```

### DetailFragment

```Java

public class DetailFragment extends Fragment {


    public DetailFragment() {
        // Required empty public constructor
    }


    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.fragment_blank, container, false);
    }

}

```


## 보충설명

### Fragment란?

> 프래그먼트는 액티비티의 일부를 차지하는 UI 컴포넌트.
> 프래그먼트를 사용하는 가장 큰 목적은 분활된 화면들을 독립적으로 구성하고, 그 상태를 관리하는데에 있다.
- 프래그먼트는 다른 뷰들과는 달리 별도의 생명 주기를 가지고 있음.
- 다른 점은 View를 상속받지 않음
- 프래그먼트는 액티비티가 보여줄 화면을 2개 이상 분할하는 용도로 사용


```Java
    프래그먼트가 화면 분할을 위한 것이라고 얘기할 때 액티비티의 화면과 구분 할 수 있어야 한다.
    프래그먼트가 화면 분할을 위한 것이라고 얘기할 때 사용하는 '화면' 이라는 말과, 액티비티에서 사용하는 '화면'이라는 말은
    서로 다르다. 똑같이 XML 레이아웃으로 만들기는 하지만, 액티비티에 사용되면 안드로이드 시스템에서 관리하는 화면이 된다.
    하지만 프래그먼트에서 사용하면 단순히 액티비티 위에 올라와 있는 화면의 일부가 된다.
```
![fragment](http://cfile23.uf.tistory.com/image/2656484B5851F5F9138D58)

### mainActivity에서 프래그먼트 세팅

![fragmentSetting](https://mblogthumb-phinf.pstatic.net/20150904_225/zxy826_1441341034302HIlIY_JPEG/13.PNG?type=w2)

```Java
public void initFragment(){
       // 1. 프래그먼트 매니저
       FragmentManager manager = getSupportFragmentManager();
       // 2. 트랜잭션 처리자
       FragmentTransaction transaction = manager.beginTransaction();
        // 프래그먼트를 처리하기 위한 트랜잭션을 시작

       // 3. 액티비티 레이아웃에 프래그먼트를 부착하고
       transaction.add(R.id.container, new ListFragment()); // <- ListFragment.onAttach(this);  
       // 4. 커밋해서 완료
       transaction.commit();

   }
```
- Fragment Manager은 프래그먼트를 다루는 작업을 해주는 개체로 프래그먼트를 추가, 삭제 또는 교체의 작업을 할 수있게 해줌.
- ex) transaction.(add,replace,remove)로 할 수 있음.
- 이런 작업들은 프래그먼트를 변경할 때 오류가 생기면 다시 원상태로 돌릴 수 있어야 하므로 트랜잭션 객체를 만들어 실행
- 액티비티에서 프래그먼트를 제어하려면, 프래그먼트매니저와 트랜잭션의 과정이 필요.
- 프래그먼트는 생명주기가 있음.
- 로그를 찍어서 확인가능
- 한 화면에 두개 이상의 프래그먼트로 구성이 가능하며, 액티비티는 한화면이기때문에 거기에 담겨있는 프래그먼트는 생명주기가 필요
- 프래그먼트는 항상 액티비티 안에서 추가되어야 하며 프래그먼트의 생명주기는 직접적으로 주인 액티비티(host activity)의 생명주기에 영향을 받는다.



### 생명주기관련 메서드(화면에 보이기 전)


![Fragment 생명주기](http://postfiles11.naver.net/20150411_74/horajjan_142873028804948zgc_PNG/%BD%BA%C5%A9%B8%B0%BC%A6_2015-04-11_%BF%C0%C8%C4_2.31.05.png?type=w3)


```Java

onAttach 메서드 - 프래그먼트가 액티비티와 연결될 때 호출됨
onCreate 메서드 - 프래그먼트가 초기화 될때 호출
onCreateView메서드- 메서드와 관련되는 뷰 계층을 만들어서 리턴함
onActivityCreated 메서드 - 프래그먼트와 연결된 액티비티가 onCreate 메서드의 작업을 완료했을때 호출됨
onStart 메서드 - 프래그먼트와 연결된 액티비티가 onStart되어 사용자에게 프래그먼트가 보일 때 호출됨
onResume - 프래그먼트와 연결된 액티비티가 onResume되어 사용자와 상호작용할 수 있을 때 호출됨.

```

- 생명주기 관련 메서드(화면에서 보이지 않게 될 시)

```Java

onpause 메서드 - 프래그먼트와 연결된 액티비티가 onpause되어 사용자와 상호작용을 중지할 때 호출됨
onStop 메서드 - 프래그먼트와 연결된 액티비티가 onStop되어 화면에서 더 이상 보이지 않을 때나 프래그먼트의 기능이 중지되었을 때 호출됨
onDestroyView 메서드 - 프래그먼트와 관련된 뷰 리소스를 해제할 수 있도록 호출됨
onDestroy 메서드 - 프래그먼트의 상태를 마지막으로 정리할 수 있도록 호출됨
onDetach 메서드 - 프래그먼트와 액티비티와 연결을 끊기 바로 전에 호출됨

```

### 프래그먼트 동적 생성 

```Java
프래그먼트 매니저(FragmentManager) 객체를 생성한다
FragmentManager fm = getFragmentManager();
프래그먼트-트랙잭션 객체를 생성한다
FragmentTransaction ft = fm.beginTransaction();
프래그먼트 클래스의 인스턴스를 생성한다
ExampleFragment fragment = new ExampleFragment();
프래그먼트-트랙잭션 객체를 사용하여 뷰그룹에 프래그먼트 객체를 추가한다 
ft.add(R.id.fragment_container, fragment, "tagName");
세번째 매개변수로 제공된 태그는 프래그먼트를 식별하는데 사용된다
FragmentManager.findFragmentByTag("tagName");
commit() 메서드로 작업을 완료되었음을 알린다 
ft.commit();
commit() 메서드는 작업의 무결성(Integrity)을 보장한다 

```




### 출처

- 출처 : [안드로이드] 프래그먼트(Fragment)에 대해 알아보자 |작성자 zzozzo
- 출처 : https://m.blog.naver.com/PostView.nhn?blogId=zxy826&logNo=220472419046&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F

## TODO

- 이 부분 callback 관련해서 연구해보기(이해가 조금 안되는 부분)
```java
  @Override
    public void onAttach(Context context) { // <- 이 컨텍스트가 나를 삽입한 액티비티이다.
        // 1. 나를 사용한 액티비티가 내가 제공한 인터페이스를 구현했는지 확인
        if(context instanceof Callback){
            callback = (Callback) context; // 2. 구현했다면 인터페이스로 캐스팅해서 사용
        }

        super.onAttach(context);
    }
```

## Retrospect

- 프래그먼트는 생명주기 관련해서 까다롭다고 하는데, 어느부분에서 어떻게 까다로운지 잘 모르겠음.
- 앞으로 많이 사용할 것 같은 기술이므로 많이 다뤄봐야 할 거 같음.

## Output
- 생략

