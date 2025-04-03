
# 🧭 FastAPI 기반 Travel MBTI RAG 분석 API

이 프로젝트는 여행 관련 설문 응답을 기반으로 사용자의 MBTI를 예측하고, MBTI별 성향에 맞는 감성 메시지, 해시태그, 국내 여행지를 추천하는 **RAG 기반 API 서버**입니다.  
Google Gemini API와 MySQL DB를 활용하여 사용자 맞춤형 여행 성향 분석을 제공합니다.

---

## 🗺️ 시스템 구성도 (RAG 구조)

[ 사용자의 설문 응답 ]
↓
[ Gemini API ]

- MBTI 예측
  ↓
  [ MySQL DB 검색 (MBTI 설명, 해시태그) ]
  ↓
  [ Gemini API ]
- 상세 설명 생성
- 추천 지역 생성
- 해시태그 선택
  ↓
  [ 최종 결과 종합 ]
  MBTI + 상세 설명 + 해시태그 + 지역 추천
  ↓
  [ 사용자에게 반환 ]

**RAG(Retrieval-Augmented Generation)** 구조는 다음과 같은 흐름으로 작동합니다:

1. **사용자 설문 응답** → FastAPI에 전달
2. **Gemini API**로 MBTI 유형 예측
3. 예측된 MBTI에 따라 **MySQL DB에서 관련 설명/성향 조회**
4. Gemini에 DB 정보를 포함한 프롬프트로 **감성 메시지, 해시태그, 도시 추천 생성**
5. 종합된 결과를 사용자에게 JSON 형식으로 응답

---

## 📂 폴더 구조

```
fastapi_travel_AI/
│
├── config/
│   └── settings.py           # 환경변수 및 DB 설정 관리
│
├── models/
│   └── request_model.py      # Pydantic 기반 Request 모델 정의
│
├── routers/
│   ├── question.py           # 객관식 질문 생성 API
│   ├── rag.py                # MBTI 분석 + 감성 추천 API
│   ├── feedback.py           # 사용자 MBTI 피드백 API
│   └── stats.py              # 사용자 응답 통계 조회 API
│
├── services/
│   ├── db_service.py         # MySQL 연동 함수 모음
│   └── gemini_service.py     # Gemini API 호출 함수 모음
│
├── .env                      # 환경 변수 저장 파일 (API 키 및 DB 정보)
├── main.py                   # FastAPI 애플리케이션 실행 진입점
└── requirements.txt          # 패키지 의존성 목록
```

---

## 🚀 주요 기능

| 기능                  | 설명                                                              | 엔드포인트                |
| --------------------- | ----------------------------------------------------------------- | ------------------------- |
| 🔸 객관식 질문 생성   | 여행 성향을 분석하기 위한 질문 5개 생성                           | `GET /generate_question`  |
| 🔸 RAG 기반 성향 분석 | 설문 응답 기반으로 MBTI 예측 + 감성 메시지 + 해시태그 + 지역 추천 | `POST /rag_recommend`     |
| 🔸 사용자 피드백 저장 | 예측된 MBTI 결과에 대한 사용자 피드백 저장                        | `POST /feedback`          |
| 🔸 응답 통계 조회    | 사용자 응답 통계를 조회하여 분석 정확도 개선 지원                 | `GET /stats`              |
| 🔸 최근 응답 조회    | 최근 사용자 응답 내역을 조회                                      | `GET /stats/recent_answers`|

---

## 🧪 API 테스트

### ✅ 예시 요청 (`/rag_recommend`)

```json
POST /rag_recommend
{
  "answers": [
    "나는 자연을 좋아해",
    "혼자 여행을 즐긴다",
    "즉흥적인 여행을 좋아해",
    "계획은 최소한으로",
    "현지 음식을 즐긴다"
  ]
}
```

### ✅ 예시 응답

```json
{
  "mbti": "ESFJ",
  "trait": {
    "type": "ESFJ",
    "main_title": "사교적인 배려왕",
    "description": "친화력 1등! 혼자 여행가도 문제없어 ..."
  },
  "recommendation": "ESFJ님은 계획적이면서도 타인을 잘 챙기는 여행 스타일입니다. ...",
  "tags": ["#우정여행", "#도시탐방", "#감성사진"],
  "recommended_regions": ["부산", "여수", "전주"]
}
```

### ✅ 최근 사용자 응답 조회 (`/stats/recent_answers`)

```json
GET /stats/recent_answers

[
  {
    "answer_id": 123,
    "answers": [
      "나는 자연을 좋아해",
      "혼자 여행을 즐긴다",
      "즉흥적인 여행을 좋아해"
    ],
    "mbti_result": "ISTP",
    "created_at": "2025-04-03T10:00:00"
  },
  {
    "answer_id": 124,
    "answers": [
      "새로운 사람 만나는 것을 좋아해",
      "함께 여행하는 것을 선호한다"
    ],
    "mbti_result": "ENFP",
    "created_at": "2025-04-03T11:00:00"
  }
]
```

---

## ✨ 기여 및 확장

이 프로젝트는 학습 및 실습, 실제 서비스로도 확장이 가능합니다.  
PR, 피드백, 기능 추가 언제든 환영합니다 😊
