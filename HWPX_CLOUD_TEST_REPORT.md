# HWP·HWPX 클라우드 실행 조사 및 시험 보고
기준일: 2026-11-10 (UTC)

## 결론
GitHub Actions의 Linux 클라우드에서 Python과 HWPX 처리 엔진을 설치하고 실행했다. 합성 시험 문서의 구조 검사는 통과했다. 실제 교재 원본은 처리하지 않았으며, 한컴 렌더링은 설치된 한컴이 없어 실패했다. Codex Cloud 환경 템플릿 생성과 현재 ChatGPT에 대한 플러그인 등록은 완료하지 않았다.

## 실제 구성
- 실행 위치: GitHub-hosted Ubuntu 클라우드. 사용자 PC에서 문서를 편집하지 않음.
- Python: 3.12.16.
- python-hwpx: 6.11.0.
- python-hwpx-automation: 7.4.1 (oracle, hwp, mcp extra).
- lxml: 6.1.3; MCP SDK: 1.28.1.
- 저장된 실행 정의: [.github/workflows/hwpx-cloud-smoke.yml](.github/workflows/hwpx-cloud-smoke.yml).
- 일반 저장소 토큰 권한: contents:read. 원본 교재, 인증키, 학생 자료를 업로드하지 않음.
- GitHub main의 선언과 PyPI 배포본의 MCP 의존성 값이 달라 실제 설치 로그를 버전 근거로 사용함.

## 실행 근거
1. [첫 실행](https://github.com/somaking1/EM-managing/actions/runs/42853757436): 성공.
2. [스키마 검사·증거 보관 추가 후 최종 실행](https://github.com/somaking1/EM-managing/actions/runs/42854058761): 성공.
3. 최종 코드 커밋: 4dfceb6cef0785e0e7ff7113f3c79831754e57cf.
4. [시험 결과 ZIP](https://github.com/somaking1/EM-managing/actions/runs/42854058761/artifacts/13780759569).
   - 파일 5개: synthetic-test.hwpx, synthetic-test.hwp, synthetic-roundtrip.hwpx, report.json, requirements-tested.txt.
   - 19,310 bytes.
   - SHA-256: b641ddab5e0aaff3dce6b72446ee6fbb6c924cc4df353e2f2b3c56076f942deb.
   - GitHub 보관 만료: 2026-12-10. 실행 정의는 저장소에 계속 남아 있으며 재실행으로 증거를 다시 생성할 수 있음.

## 시험 결과
시험 데이터는 새로 만든 짧은 문항 2개, 편집 가능한 수식 2개, 사각형 1개다. 사용자가 올린 교재의 내용이나 정확도를 대신 검증하는 시험이 아니다.

| 항목 | 결과 | 확인 범위 |
|---|---|---|
| Python·의존 패키지 설치 | 통과 | pip check 충돌 없음 |
| HWPX 저장·다시 열기 | 통과 | ZIP 무결성, XML, 패키지·문서 검증 |
| 본문 32pt | 통과 | charPr height=3200 저장 확인 |
| 편집 가능한 수식 32pt | 통과 | 수식 스크립트 2개와 baseUnit=3200 확인 |
| 세로 용지 | 통과 | 시험용 A4 페이지 폭<높이 확인. 실제 교재 판형은 별도 적용 필요 |
| 명시적인 쪽넘김 | 통과 | 두 번째 문항의 pageBreak=1 확인 |
| 도형 글자처럼 취급 | 통과 | 사각형 pos treatAsChar=1 확인 |
| HWP 바이너리 왕복 변환 | 통과 | 합성 HWP를 다시 읽어 본문·수식·사각형 확인 |
| HWP 변환 누락 | 시험 문서 0건 | conversion_report의 unconverted와 dropped가 비어 있음 |
| 한컴 PDF 출력 | 실패 | 종료 코드 3, hancom-unavailable |
| 실제 표시되는 쪽 수·잘림 | 미검수 | 구조 검사로 대신할 수 없음 |
| 사용자가 올린 원본의 문항 분리 | 미실시 | 원본을 공개 저장소에 올리지 않음 |
| 현재 대화의 MCP 연결 | 미실시 | MCP 패키지 설치와 ChatGPT 도구 연결은 별개 |

전체 작업의 초록색 성공 표시는 구조 시험 성공이다. 렌더 실패를 예상된 환경 제약으로 따로 기록하도록 작성했으며, PDF 검수까지 통과했다는 뜻이 아니다.

## GitHub 후보 비교
| 후보 | 검토 내용 | 판단 |
|---|---|---|
| [airmang/python-hwpx](https://github.com/airmang/python-hwpx) | 한컴 없이 Linux에서 HWP5·HWPX 읽기·쓰기, 수식·도형 구조 편집, 변환 손실 보고 | 우선 채택. 이번 설치·합성 시험 통과 |
| [airmang/hwpx-plugins](https://github.com/airmang/hwpx-plugins) | 같은 개발자가 유지하는 에이전트 지침·도구 연결. 실제 편집은 python-hwpx/automation 엔진 | 향후 Codex 환경에 연결할 1순위. 현재 대화에 등록했다고 주장하지 않음 |
| [ratiertm/hwpx-skill](https://github.com/ratiertm/hwpx-skill) | HWPX 생성·편집 CLI, 수식·도형, SVG/PNG 미리보기 | 대안. 이번 설치·실행은 하지 않았으며 원본 HWP 보존은 별도 확인 필요 |
| [neolord0/hwp2hwpx](https://github.com/neolord0/hwp2hwpx) | Java 기반 HWP→HWPX 변환 | HWP 읽기 실패 시 비교 변환 후보. 이번 실행은 하지 않음 |

플러그인 문서의 소개 버전과 실제 엔진 릴리스가 같지 않을 수 있다. README의 숫자만 믿지 않고 배포본 설치 결과와 호환성을 확인해야 한다. 이번에는 core 6.11.0/automation 7.4.1을 고정했다.

## 막힌 부분과 해결 경로
### 1. Codex Cloud 저장 환경
현재 대화의 환경 목록 요청은 Codex app tool request failed 오류를 반환했다. 웹 설정 경로는 로그아웃 화면이었다. 이것만으로 사용자 계정에서 Codex Cloud를 쓸 수 없다고 판단할 수는 없다.

공식 경로는 ChatGPT 설정 → Codex Cloud → Environments → Create environment, 또는 새 작업의 Work in → Cloud → Select environment → Create environment이다. 로그인된 화면에서 환경을 생성하고 이 저장소를 선택한 뒤 시험에 사용한 패키지를 설치하고 저장/게시한다. 새 작업은 저장된 설정으로 독립 실행 공간을 만들므로, 실행 중인 VM 자체와 결과 파일을 영구 보관하는 기능과는 구분해야 한다.

### 2. 실제 교재 원본 접근
현재 저장소는 public이다. 합성 시험 코드만 저장했다. 실제 원본 제작은 원본에 접근 가능한 비공개 Codex Cloud 작업 공간에서 진행하거나, 별도의 비공개 저장소와 안전한 파일 전달 경로를 마련한 뒤 진행한다. 원본을 공개 저장소·공개 로그에 넣는 방식은 사용하지 않았다.

### 3. 한컴 렌더링
Linux 작업자는 한컴 렌더 엔진이 없어 render-pdf가 hancom-unavailable로 종료되었다. python-hwpx와 자동화 패키지를 다시 설치해도 한컴 프로그램이 생기지는 않는다.

화면 제어 없이 최종 쪽 수를 검수하려면 한컴이 설치되고 COM 자동화가 가능한 Windows 클라우드 작업자를 별도로 연결하는 방안이 있다. 한컴 설치·라이선스·연결 환경이 실제로 마련되어야 하며, 이번에 구성하지 않았다. Linux 미리보기·쪽 수 추정은 보조 검사로만 사용할 수 있다.

### 4. 원본 HWP에서 변환 손실이 발견될 때
conversion_report에 미지원·누락 개체가 있으면 해당 문항의 완성 판정을 보류한다. Java hwp2hwpx 변환 결과와 비교하거나, 한컴이 있는 클라우드에서 원본을 HWPX로 변환한 뒤 XML 편집을 이어가는 경로를 검토한다. 텍스트 추출 후 문서를 다시 쓰는 방식은 수식·도형 보존 요구를 충족하는지 별도 확인이 필요하다.

## 다시 실행하는 방법
저장소 → Actions → HWPX cloud smoke test → Run workflow → main → Run workflow.
실행 결과의 Retain synthetic test evidence 단계와 Artifacts를 확인한다.

새 실행마다 작업용 VM은 새로 생기고 패키지를 설치한다. 저장되는 것은 실행 정의와 코드다. 이번에 만든 것은 GitHub 클라우드 시험 구성이고, Codex Cloud의 저장된 환경 목록에 등록된 템플릿은 아니다.

## 출처
- https://learn.chatgpt.com/docs/environments/cloud-environments
- https://docs.github.com/en/actions/tutorials/build-and-test-code/python
- https://github.com/airmang/python-hwpx/blob/main/README.md
- https://github.com/airmang/python-hwpx/releases/tag/v6.11.0
- https://github.com/airmang/python-hwpx-automation/blob/main/docs/render-pdf.md
- https://github.com/airmang/hwpx-plugins/blob/main/README.md
- https://github.com/ratiertm/hwpx-skill/blob/main/README.md
- https://github.com/neolord0/hwp2hwpx
