# FashionRAG — 멀티모달 RAG 실습

CLIP 이미지 검색과 GPT 비전 모델로 만든 **패션 스타일링 어시스턴트**예요.
한국어로 질문하면 비슷한 패션 사진을 찾아서 보여 주고, 그 사진을 근거로 스타일링 조언을 해 줘요.

## 동작 흐름

```
한국어 질문
  → ① 번역 (한국어 → 영어)           gpt-4o-mini
  → ② 이미지 검색 (Retrieval)         CLIP 임베딩 + Chroma 벡터 DB
  → ③ 프롬프트 구성 (Augmentation)    검색된 이미지를 base64로 변환해 프롬프트에 추가
  → ④ 답변 생성 (Generation)          gpt-4o-mini 비전 모델
  → ⑤ 번역 (영어 → 한국어)
한국어 답변
```

## 배운 것

- **멀티모달 RAG**의 구조: Retrieval → Augmentation → Generation
- **CLIP**: 텍스트와 이미지를 같은 벡터 공간에 임베딩해서 텍스트로 이미지를 검색해요.
- **Chroma 벡터 DB**: 이미지를 저장(`add`)하고 검색(`query`)해요. distance가 작을수록 비슷해요.
- **LangChain LCEL**: `프롬프트 | 모델 | 파서`로 번역 체인과 비전 체인을 만들어요.
- **base64 data URL**: 이미지를 텍스트로 바꿔서 비전 모델 API 요청에 담아요.

## 사용한 기술

| 라이브러리 | 역할 |
|---|---|
| `datasets` | Hugging Face 데이터셋 로드 (`streaming=True`) |
| `open-clip-torch` | CLIP 임베딩 모델 |
| `chromadb` | 벡터 DB |
| `langchain-core`, `langchain-openai` | LLM 체인 구성, OpenAI 연결 |

데이터: [Fashionpedia](https://huggingface.co/datasets/detection-datasets/fashionpedia) (CC BY 4.0)

## 실행 방법

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

프로젝트 폴더에 `.env` 파일을 만들고 OpenAI API 키를 넣어요.

```
OPENAI_API_KEY=sk-...
```

`FashionRAG_멀티모달RAG_수업.ipynb`를 열고 위에서부터 셀을 차례로 실행해요.
이미지(`fashion_dataset/`)와 벡터 DB(`img_vdb/`)는 노트북을 실행하면 자동으로 만들어져요.
