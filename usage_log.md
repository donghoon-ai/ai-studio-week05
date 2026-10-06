# AI 사용 내역
20231451 신동훈

---
## 과제 1

**1. DB_URL 항목 env파일에 포함 여부**

<사용 프롬프트>
>현재 실습으로 .env파일 만드는 실습 중인데 DB_URL도 env파일에 들어갈 항목이니? URL에 비밀번호도 포함되어있는거야?

<채택 답변>

>네, DB_URL도 .env에 넣는 게 맞습니다. 그리고 URL에 비밀번호가 포함될 수 있습니다. 

<수정 내역>
>.env 파일에 DB_URL항목도 추가하여 DB주소를 추가하였다. 

**2. 비밀번호 마스킹 코드 질문**

<사용 프롬프트>
```python
  masked_password = info.password.apply(lambda p: "*" * len(p))
```
<채택 답변>

>아닙니다. .apply()는 pandas에 있는 메서드라 문자열에 실행하면 에러가 발생합니다. 문자열에 함수를 적용하는게 아니라 문자열 자체로 마스킹 문자열을 만들어야 합니다. 여러값에 각각 함수를 적용할 때 .apply 함수를 사용하는 것이므로 함수를 적용할 필요 없이 바로 "*" * len(info.password)로 작성하면 됩니다. 그리고 비밀번호를 문자열의 길이만큼 마스킹하면 비밀번호의 길이 정보를 나타내므로 길이와 상관없이 고정 문자열을 쓰는 것이 더 좋습니다.

<수정 내역>
```python
masked_password = "*****"
으로 비밀번호를 고정된 문자열로 마스킹하도록 수정하였다. 
```

**3. API키 마스킹 코드 질문**

<사용 프롬프트>
```python
masked_api_key = "*****"  # FIXED: API 키가 로그에 안남도록 마스킹
으로 api키값을 마스킹을 했고 그다음으로 headers 딕셔너리에서 api키값의 value만 마스킹된 masked api_key값으로 변경된 masked_headers 딕셔러니를 새롭게 만들고 싶은데 어떻게 코드를 작성하면 될까 코드를 작성해줘? 
```

<채택 답변>
```python
masked_headers = {k: (masked_api_key if k == "x-api-key" else v) for k, v in headers.items()}
```

<수정 내역>
>위의 AI가 작성해준 코드로 수정하여 API키값이 로그에 찍혀나오는 문제를 마스킹하여 출력하는 것으로 해결했다. 위 코드는 for 반복문으로 기존 딕셔너리의 (key:value)를 k와 v 변수로 받아 if 문의 조건식에 따라 새로운 딕셔너리를 구성하는 코드이다. if문으로 key값이 x-api-key이면 value값으로 masked_api_key을 주고 아니면 기존의 headers 딕셔너리의 key:value 쌍을 추가하는 식으로 새 딕셔너리를 구성한다.

**4. load_dotenv() 코드 검토 요청**

<사용 프롬프트>
```python
load_dotenv()  # .env 파일을 읽어 환경 변수 로드
API_KEY = os.getenv("API_KEY")
DB_URL = os.getenv("DB_URL")

if API_KEY is None or DB_URL is None:
    raise RuntimeError("환경 변수 API_KEY 또는 DB_URL이 설정되지 않았습니다. .env 파일을 확인하세요.")

print("키 로드 성공 (앞 8자리):", API_KEY[:8]+"...")
print("DB URL 로드 성공 (앞 20자리):", DB_URL[:20]+"...")

.env파일로 key값들을 분리하고 환경변수로 읽어오는 코드를 작성했는데 제대로 작성되었는지 검토해줘
```
<채택 답변>
```python
is None 검사는 빈 값을 통과시킵니다.
.env에 API_KEY=처럼 값이 비어 있으면 os.getenv는 None이 아니라 ""를 돌려줘서 검사를 통과합니다. if not API_KEY or not DB_URL:로 바꾸면 둘 다 잡습니다.
```
<수정 내역>
```python
if not API_KEY or not DB_URL:
    raise RuntimeError("환경 변수 API_KEY 또는 DB_URL이 설정되지 않았습니다. .env 파일을 확인하세요.")

로 수정하여 기존 코드의 key값이 빈칸 ""으로 인식되는 문제를 해결하였다.
```
