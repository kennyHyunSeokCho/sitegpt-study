# SiteGPT · 사이트 콘텐츠 기반 질의응답 실습

노마드코더 Fullstack GPT 강의의 SiteGPT 코드를 정리한 학습 저장소입니다. 사이트맵에서 수집한 내용을 검색하고, 문서별 답변 후보를 만든 뒤 출처와 함께 최종 답변을 표시하는 흐름을 살펴봅니다.

## 출처와 정리 범위

원본 코드는 [nomadcoders/fullstack-gpt](https://github.com/nomadcoders/fullstack-gpt)의 [pages/04_SiteGPT.py](https://github.com/nomadcoders/fullstack-gpt/blob/a9ceb1c43dde8ed28fd1cf157773a3665a8aee9e/pages/04_SiteGPT.py)입니다. 기준 커밋은 `a9ceb1c43dde8ed28fd1cf157773a3665a8aee9e`이며, 이 저장소의 해당 Python 파일은 원본을 그대로 보존했습니다.

조현석이 강의를 기반으로 학습한 RAG 흐름의 예제로 정리했습니다. 이번 정리에서 추가한 부분은 프로젝트 설명, 실행 시작 화면과 필요한 의존성 목록입니다. 기업 사이트에 맞춘 별도 수정본이나 업무 도입 성과는 현재 공개 자료에 포함되어 있지 않습니다.

원본 저장소의 GitHub 메타데이터에는 별도 라이선스가 표시되지 않습니다. 원본 코드의 권리와 이용 조건은 원저작자 및 강의 안내를 확인해 주세요.

## 처리 흐름

1. 사용자가 사이트맵 XML URL을 입력합니다.
2. `SitemapLoader`가 페이지를 수집하고 `parse_page`가 헤더·푸터와 일부 불필요한 문자열을 제거합니다.
3. 텍스트를 1,000토큰 단위, 200토큰 겹침으로 나눕니다.
4. OpenAI 임베딩으로 벡터를 만들고 FAISS에서 질문과 관련된 문서를 검색합니다.
5. 각 검색 문서를 바탕으로 답변 후보와 0~5점 점수를 생성합니다.
6. 후보 답변과 출처·수정일을 전달해 유용하고 최근인 답변을 우선 선택하도록 요청합니다.
7. 최종 답변을 Streamlit 화면에 표시합니다.

문서별 점수는 LLM이 자체 생성하는 선택 신호입니다. 별도로 라벨링한 정답이나 실제 정확도 측정값과 구분합니다. `lastmod`가 없는 사이트맵에서는 원본 코드의 메타데이터 접근을 보완해야 할 수 있습니다.

## 코드 구조

```text
sitegpt-study/
├── Home.py
├── pages/
│   └── 04_SiteGPT.py
├── requirements.txt
└── README.md
```

| 구성 | 코드에서 하는 일 |
| --- | --- |
| Python·Streamlit | URL·질문 입력과 답변 표시 |
| SitemapLoader | 사이트맵에서 페이지 수집 |
| RecursiveCharacterTextSplitter | 토큰 기준 문서 분할 |
| OpenAIEmbeddings·FAISS | 임베딩과 검색 |
| ChatOpenAI·ChatPromptTemplate | 문서별 답변 후보 생성과 최종 답변 선택 |
| RunnableLambda·RunnablePassthrough | 검색·후보 생성·선택 단계 연결 |

## 실행 안내

Python 3.10의 가상환경과 OpenAI API 키를 사용합니다. 의존성은 원본의 2023년 버전 목록에서 이 예제에 필요한 항목을 추렸습니다. 현재 API 모델 접근 권한과 실행 환경에 따라 조정이 필요할 수 있습니다.

```bash
git clone https://github.com/kennyHyunSeokCho/sitegpt-study.git
cd sitegpt-study
python3.10 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
export OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
streamlit run Home.py
```

왼쪽 메뉴에서 SiteGPT를 열고 수집할 사이트의 사이트맵 XML URL을 입력한 다음 질문합니다. 페이지 수집과 모델 API 호출이 발생하므로, 직접 이용할 수 있는 사이트를 대상으로 실행합니다.

## 확인한 범위

이번 정리에서는 원본 파일의 일치 여부와 Python 구문, 문서별 답변·출처 전달 및 최종 선택 로직을 외부 API 호출 없이 점검했습니다. 현재 모델 API를 이용한 실제 사이트 크롤링·질의응답의 통합 실행은 추가 확인이 필요합니다.

관련 문서·회의 분석 실습은 [langchain_2023](https://github.com/kennyHyunSeokCho/langchain_2023)에 정리되어 있습니다.

