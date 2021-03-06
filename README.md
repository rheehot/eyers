# eyers
아이어즈 커뮤니티 어플리케이션 개발
## 현재 리펙토링 진행중이여서 실행이 안됩니다.   
## 이전 작업 결과물 
<img width="1440" alt="KakaoTalk_20200707_115038267_05" src="https://user-images.githubusercontent.com/50267433/91796039-35ad8f00-ec5a-11ea-8594-19336576ba83.png">  
<img width="1440" alt="KakaoTalk_20200707_115038267_08" src="https://user-images.githubusercontent.com/50267433/91796066-478f3200-ec5a-11ea-9586-d5981fbcaf7f.png">
<img width="1440" alt="KakaoTalk_20200627_183740202" src="https://user-images.githubusercontent.com/50267433/91795665-32fe6a00-ec59-11ea-9e3b-ef0d5b02a356.png">   
<img width="1440" alt="KakaoTalk_20200707_115038267_04" src="https://user-images.githubusercontent.com/50267433/91796093-5675e480-ec5a-11ea-9180-0cdaa700b746.png">
<img width="1437" alt="KakaoTalk_20200707_115038267_03" src="https://user-images.githubusercontent.com/50267433/91796128-65f52d80-ec5a-11ea-8eb1-c11cd448ec10.png">

# 리팩토링 회고록   
## 2020/10/05 :    
* login 기능
    * WebConfig 의 antMatchers()를 하는데 어떻게 ROLE_ 비교하는지 궁금
    * SpringSecurity 에서 따로 User 가지고 사용
    * OAuth2 같은 경우는 바로 연동되서 사용할 수 있음  
    * 하지만 SpringSecurity 는 일반 User 를 어떻게 인식하지? 라는 생각이 나옴
    * 검색해보니 UserDetails 와 UserDetailsService 를 구현해야함
    * CustomUserDetails 클래스 만들고 UserDetails 구현
        * 처음에 User 에 했는데 분리하는게 좋을 것 같아 분리
        * 중복된 메서드 이름도 있고
        * 해당 설정 말고 다른 설정으로 변경할때 클래스 만들어서 변경만 해주면 되니까
        * 즉 변경시 유지보수 편리하게 하려고 만듬 
    * UserService 에는 UserDetailsService 구현
    * SpringSecurity 테스트를 위해서 모든 권한 접속 허용토록 바꿈
    * h2-console 로 user 데이터를 넣으니 userRepository 가 갱신되지 않아서 인식못함
        * 안풀린 궁금증 : userRepository 가 언제 데이터베이스를 가져오는지도 봐야겠다.  
    * CommandLineRunner 로 데이터 넣어서 돌리니 인식되어 로그인 가능
    * **하지만 Serializable 에러가 발생**    
    * User 에 Serializable 사용하면 됨
    * 근데 왜 사용해? 그전에는 안해도 되었는데?    
    * 그리고 Serializable 검색해봄
        * 직렬화는 좋지만 단점도 강함
        * 객체에 필드 하나만 추가해도 다 변경해주어야함
        * 그래서 최대한 값이 안바뀌고 안쓰는게 좋음 즉, 오래 보관하고 중요한 클래스에서는 사용 x  
        * 그런데 User는 중요한데...
    * 해당 문제점 아직 해결하지 못함 

## 2020/10/08 :             
* 패스워드 암호화 추가             
* 현재 CustomUserDetails 가 적용되지 않아서 에러가 발생하는 것 같음
* 이동욱 개발자님이 말하신 하나하나 하고 나중에 리팩토링 해야한다는 말의 의미를 깨달음
* 우선 User에 UserDetails를 넣어서 해보자   
   * 기본부터하고 나중에 Custom 하는 방법을 배워야겠다고 생각합니다.
   * 현재 User 에 UserDetails 를 추가함으로써 이전 시리얼라이저블 문제점을 해결했습니다.   
* 현재 Controller 개발 및 UI 작성해야함  

## 2020/10/27 :             
* 로그인 기능 구현 완료              
    * SpringSecurity 로그인은 일반적으로 Session 을 지원하지는 않는다.
    * 그러므로 UserService 의 loadUser() 에서 httpSession 을 통해 넣어주어야한다.   
* login 후 뒤로가기를 막기 위해 login 을 위한 Controller 메서드를 만들었습니다.
 
