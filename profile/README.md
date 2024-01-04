### [Notion](https://www.notion.so/c62f036daadd4c2bb7e554d9689ccdbc?pvs=21) 

## 프로젝트 개요
> 
> `"Spotify 컨트롤러"`
> 
> 이 프로젝트는 STM32 와 NestJS를 활용하여 쉽게 **Spotify를 조작할 수 있는 IOT 시스템**입니다. STM32에선 버튼을 이용하여 사용자가 Spotify에서 재생, 일시정지, 다음곡, 이전곡 등의 명령을 원격으로 제어할 수 있습니다. 버튼 신호는 PC를 거쳐 NestJS로 구축된 백엔드 서버로 전송되며, 이 서버에서 Spotify API와 통신하여 음악을 제어합니다. 또한 현재 재생 중인 곡의 정보를 LCD에 실시간으로 표시하며, 웹 페이지에서 Spotify 계정을 통해 로그인하여 가장 많이 들은 아티스트와 곡, 최근에 들은 곡을 확인 할 수 있습니다. 이를 통해 사용자에게 편리하고 새로운 경험을 제공할 수 있습니다.

### 주제 선정 이유
>
> 평소 `spotify` 라는 앱을 사용하여  노래를 듣다, 제가 `spotify` 라는 어플에서 멜론 처럼 자주 듣는 노래와 아티스트가 무엇인지 통계적으로 보고 싶다는 생각이 들었습니다. 또한 이 프로젝트를 기획할 당시에 키보드에 LCD가 부착되어 디스플레이 처럼 사용할 수 있는 제품을 보게 되었는데 이 제품을 보며 작은 음악 컨트롤러가 있으면 좋을 것 같다고 느꼈고, 제가 좋아하는 분야라서 즐기면서 할 수 있을 것 같아 개발하게 되었습니다.

### 사용한 기술 스택

#### Backend
> ##### docker-mysql, nestjs

#### Frontend
> ##### 사용 언어 및 라이브러리 : React , TypeScript, styled-components,

#### Hardware
    제어 보드 : stm32F103C8T6 Micro USB controller<br>사용 언어 및 IDE : C , STM32CubeIDE
    
    (사용 센서)
    
    - PCT-2-6W 꽂음형 2:6 구분확장 커넥터 (와이드형)
    - 1602 I2C 캐릭터LCD
    - RAMPS 1.4용 기계식 엔드스탑 스위치
    - TM1637 ( 4 digit 7-Segments LED 모듈 )
    
    (통신 방식)
    - CDC USB Serial 통신, I2C 통신
    
#### PC
    
    사용 언어 : Python
    
    사용 모듈 : serial, socket, requests, threading

### 기능들

1. 노래 제어
2. 현재 노래 상태 LCD에표시
3. 많이 들은 노래 및 아티스트 표시
4. 어떤 노래를 들었는지 표시
5. Spotify 계정 로그인 기능


### 담당 역할

백엔드 : 김세진

STM32 및 센서 제어 : 임시온

프론트엔드: 임시온, 김세진

디자인 및 기획 : 임시온, 김세진

---

## 작동 Flow
![image](https://github.com/xyz-intern/.github/assets/80656700/f795dab8-e4a9-42fe-ac79-fbb8d912116a)
![image](https://github.com/xyz-intern/.github/assets/80656700/05f30820-2170-4701-8549-d6078a36c54d)

---

### 개발을 맡은 부분

**임시온**

- STM32
    - LCD Display 제어
        
        > I2C를 활용하여 LCD Display 제어하기
        글자의 크기가 LCD의 크기보다 크다면 옆으로 돌아가는 스크롤 기능
        LCD Display에 모드를 줘서 버튼을 눌렀을 때에 다른 화면 나오게 구현
        음악이 재생되고 있지 않을 때 글자 Blink 기능 구현
        곡 정보, 재생 시간, 볼륨 크기 출력
        DISPLAY mode 변경 기능 만들기
        > 
    - CDC USB Serial 통신
        
        > 버튼 눌렀을 때 CDC Serial 통신으로 명령어 전달
        START 비트와 STOP 비트를 받아서 보다 많은 크기의 데이터 수신
        Split 함수 구현
        재생 시간 Count 하기
        > 
    - Ramps 1.4 Switch 제어
        
        > 인터럽트 callback 함수로 버튼 제어
        하드웨어 적 노이즈 잡기 ( debouncing )
        더블클릭 기능 만들기
        > 
    - TM1637 4 digit 7-segments
        
        > Count 기능 및 제어하기
        > 
- PC
    - Serial 통신( with STM32 ) 에서 명령어 받아 처리
    - Socket 통신( with NestJS ) 으로 userId 저장
    - papago API
    - NestJS(서버) 에서 만든 API에 Requests 요청 보내기
    - 재생시간이 끝났을 때 다음곡 자동 요청
    - STM32로 곡 정보 및 재생시간&볼륨 보내기
- Frontend
    - Image Slider 제작
    - CSS 반응형 만들기

**김세진**

- Backend
    1. Token 발급
        - 프론트에서 받은 인가 코드로 spotify에 토큰 요청
        - 토큰 발급 시 database에 저장
        - 브라우저로 Cookie(refreshToken, userId) 전송
        - 토큰 만료 시 재발급 로직 추가
        - 로그인 시 유저 정보 가져오기
    2. Playlist 
        - 가장 인기있는 TOP Artist 50
        - 최근에 들은 곡
        - 가장 많이 들은 아티스트
        - 가장 많이 들은 노래
    3. Sptify 노래 제어
        - command에 따라 play/stop/next/previous로 노래 제어(재생 상태 전송)
            - 곡 정보 database에 저장
            - PC에게 곡 정보 전달
        - spotify에서 현재 곡 정보 가져오기
        - 노래 재생 시 볼륨 제어
    4. Docker-mysql
        - Nestjs와 Database 연결
        - token과 playlist 관련 entity를 저장
    5. PC와 Socket 통신
        - 로그인 시 PC에게 “userId” or 곡 요청 시 “current_ms | duration_ms” 전달
            - “userId” → command 전송 시 userId와 함께 요청
            - “current_ms | duration_ms” → 현재 곡이 끝나고 다음 곡 정보 자동 요청
    
- Frontend
    1. Spotify 로그인/로그아웃
        - Spotify에서 로그인 진행 후 redirect url로 인증 코드 발급
        - 서버에서 토큰 발급을 위해 인증 코드 넘기기
        - 로그아웃 시 token, cookie 삭제
    2. API
        - 가장 인기있는 TOP Artist 50
        - 최근에 들은 곡
        - 가장 많이 들은 아티스트
        - 가장 많이 들은 노래
    3. 토큰 재발급
        - 로그인 후, 59분 뒤 서버에게 refreshToken으로 토큰 재발급 요청하기
        - 토큰 만료 일 때 요청시 Error 발생 및 로그아웃 처리
    4. React → Typescript로 마이그레이션
        - 기존에 react로 작성한 프로젝트를 typescript로 변경
    5. styled-component
        - Error Style & GlobalStyle 등 각 화면에 맞는 UI 제작
  
### Design
> [피그마](https://www.figma.com/file/a4LrxIM9Z0ye1A0bPzDAQ0/my-spotify?type=design&node-id=0%3A1&mode=design&t=aTWvn86EcJFIlTVX-1)

## 결과물

  ### 웹 화면
  
  - swagger
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/563b56ae-d38e-481a-8e48-e776d703d57e)

  - web
      
      1) 메인 페이지
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/74b8de4d-f306-4a46-8ea7-41eee65d04d4)

      2) 최근 재생한 곡을 보여주는 페이지
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/8833e7dd-32e2-4da5-8297-fdf7d7bb645d)
      
      3) 많이 재생한 곡들을 보여주는 페이지
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/cc51d386-412f-48a9-a539-8184cd757f01)
      
      4) 가장 많이 들은 아티스트를 보여주는 페이지
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/516bd2f4-b5a8-4406-aa16-dd2f0f2431e4)
      
      로그인 페이지
      
      ![image](https://github.com/xyz-intern/.github/assets/80656700/e5ce3be4-15c9-4edc-9705-9ca0265932db)
    

### 시연 영상(클릭해주세요)
[![Youtube Link](https://github.com/xyz-intern/.github/assets/80656700/14b01b78-ca5b-4b18-96b1-026d426a3b56)](https://www.youtube.com/watch?v=n4ySKIVe4-8)
