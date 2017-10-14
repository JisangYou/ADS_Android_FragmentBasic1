# Fragment
## Fragment란?

- 프래그먼트는 액티비티의 일부를 차지하는 UI 컴포넌트.
- 프래그먼트는 다른 뷰들과는 달리 별도의 생명 주기를 가지고 있음.
- 다른 점은 View를 상속받지 않음
- 프래그먼트는 액티비티가 보여줄 화면을 2개 이상 분할하는 용도로 사용

#### mainActivity에서 프래그먼트 세팅
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
- 프래그먼트 매니저는 프래그먼트를 다루는 작업을 해주는 개체로 프래그먼트를 추가, 삭제 또는 교체의 작업을 할 수있게 해줌.
- 이런 작업들은 프래그먼트를 변경할 때 오류가 생기면 다시 원상태로 돌릴 수 이썽야 하므로 트랜잭션 객체를 만들어 실행
- 액티비티에서 프래그먼트를 제어하려면, 프래그먼트매니저와 트랜잭션의 과정이 필요.


#### detailFragment
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
- 기본적인 Frament 구성방법
- inflate를 사용하여, xml레이아웃과 연결하는 방식.
- 마찬가지로

#### ListFragment
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

- 프래그먼트는 생명주기가 있음.
- 로그를 찍어서 확인가능
- 한 화면에 두개 이상의 프래그먼트로 구성이 가능하며, 액티비티는 한화면이기때문에 거기에 담겨있는 프래그먼트는 생명주기가 필요
- 생명주기관련 메서드(화면에 보이기 전)
- onAttach 메서드 - 프래그먼트가 액티비티와 연결될 때 호출됨
- onCreate 메서드 - 프래그먼트가 초기화 될때 호출
- onCreateView메서드- 메서드와 관련되는 뷰 계층을 만들어서 리턴함
- onActivityCreated 메서드 - 프래그먼트와 연결된 액티비티가 onCreate 메서드의 작업을 완료했을때 호출됨
- onStart 메서드 - 프래그먼트와 연결된 액티비티가 onStart되어 사용자에게 프래그먼트가 보일 때 호출됨
- onResume - 프래그먼트와 연결된 액티비티가 onResume되어 사용자와 상호작용할 수 있을 때 호출됨.

- 생명주기 관련 메서드(화면에서 보이지 않게 될 시)
- onpause 메서드 - 프래그먼트와 연결된 액티비티가 onpause되어 사용자와 상호작용을 중지할 때 호출됨
- onStop 메서드 - 프래그먼트와 연결된 액티비티가 onStop되어 화면에서 더 이상 보이지 않을 때나 프래그먼트의 기능이 중지되었을 때 호출됨
- onDestroyView 메서드 - 프래그먼트와 관련된 뷰 리소스를 해제할 수 있도록 호출됨
- onDestroy 메서드 - 프래그먼트의 상태를 마지막으로 정리할 수 있도록 호출됨
- onDetach 메서드 - 프래그먼트와 액티비티와 연결을 끊기 바로 전에 호출됨



### 특이사항

- 데이터 전달시에 Activity간에는 intent로 전달하나, Fragment는 bundle로 데이터를 전달

- 프래그먼트는 생명주기가 있으며, 액티비티에 담겨있기때문에 상호간에 생명주기가 관리가 필수적임.

- callback은 추후에 보충설명 필요
