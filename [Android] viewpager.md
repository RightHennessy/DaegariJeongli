작성일 : 2022.05.19

</br>

## 뷰페이저

항상 현재 페이지를 기준으로 좌/우 하나씩, 즉 현재 페이지를 포함하여 최대 세개의 페이지를 생성 및 관리.

`instantiateItem()` 화면에 표시할 페이지뷰를 생성, 파라미터로 전달된 posiitoin에 해당하는 페이지 생성 후 또 다른 파라미터로 전달된 컨테이너(=뷰페이지 객체)에 생성된 페이지뷰를 추가, 마지막으로 페이지 식별을 위한 Object 객체 return

`isViewFromObject()` 뷰페이저 내부적으로 관리되는 키 객체와 페이지뷰가 연관되는지 여부를 확인하는 역할

```python
 @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return (view == (View)object) ;
    }

```

정말 놀랍게도 위에거 언제 쓰는지 모르겠음...ㅇ\_ㅇ

</br>

### WorkFlow

1. 메인액티비티에 뷰페이저 추가
2. 페이지뷰를 위한 레이아웃 작성
3. 페이저 어댑터 상속 및 구현
4. 뷰페이저에 어댑터 지정 : `viewPager.setAdapter`

</br>

### 구현하기

내가 만들거는 버튼 클릭해서 뷰페이지가 넘어가는 기능.. </br>
부드러운 모션을 주기 위해 굳이굳이 viewpager를 이용했다.

(1) 뷰페이저는 나타낼 각각의 fragment가 모두 필요하므로 페이지 갯수만큼 fragmnet와 xml 파일을 생성해준다.

</br>

(2) adpater 코드와 adapter 연결 작업은 다음과 같다.

```kotlin
private class ChartPagerAdapter(fragmentActivity: FragmentActivity) : FragmentStateAdapter(fragmentActivity) {
        override fun createFragment(position: Int): Fragment {
            return when(position){
                0->OneMonthChartFragment()
                else->OneWeekChartFragment()
            }
        }

        override fun getItemCount(): Int = 2
    }

    private fun setChartAdapter(){
        vp_chart.adapter = ChartPagerAdapter(this)
        vp_chart.isUserInputEnabled = false

    }
```

여기서 오류가 나서 화나서.. 3일동안 코딩을 안했다;; </br>
어댑터 연결해주는 부분이었는데 내가 처음에는 lateinit으로 선언하고 나중에 this를 설정을 안해줘서인지 아무튼.. 이유는 정확히 모르겠지만 다시 시도는 안해봤다 (ㅋㅋㅋ) </br>
어차피 페이지가 2개뿐이라 그냥 `creatFragment()` 부분에서 조건문 이용해버렸당 </br>
필요한 함수는 자동으로 알려주는데.. extends 할때 FragmentStateAdapter.. 이거 썼는데 이유는 안찾아봐서 모른다.. 암튼 다른건 밑줄 쳐져서 강제로 못썼음

</br>

(3) 마지막에 버튼 연결해주었당

```kotlin
private fun onClickListener() {
        binding.btnOneMonth.setOnClickListener {
            vp_chart.setCurrentItem(0, true)
        }
        binding.btnOneWeek.setOnClickListener {
            vp_chart.setCurrentItem(1, true)
        }
    }
```

`setCurrentItem()` 의 뒷부분인자가 smoothScoll이다. true로 설정해주면 스무스하게 넘어간다 냠냠..

</br>
참고로 fragment 내에서 함수 호출하고 싶으면

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        makeChart(7)
    }
```

`onViewCreated()` 에서 해야한다. 이유는 나도 모름 (모르는게 왤케 많아)

</br>
아무튼 막혀서 하기 싫어서 약 5일 썼다.. 고작 15줄 쓰는데.. 저번주에 이어 반성한다.
