# 🏛️ Architecture Decision Records (ADR)
- Architecture Decision Record (ADR)는 아키텍처적으로 중요한 의사결정과 그 배경을 문서화한 기록입니다.
- 목적
  - **투명성**: 의사결정 과정과 근거를 명확히 기록
  - **이력 관리**: 시간에 따른 결정의 변화 추적
  - **지식 공유**: 팀원 간의 컨텍스트 공유
  - **책임감**: 결정에 대한 명확한 소유권


> **아키텍처 의사결정 기록 및 관리**  
> 중요한 기술적 결정을 투명하고 체계적으로 문서화

## 🔄 ADR 작성 시점

### ✅ ADR을 작성해야 하는 경우
```
아키텍처 영향:
□ 새로운 기술 스택 도입
□ 아키텍처 패턴 변경
□ 데이터베이스 선택
□ 외부 서비스 연동

비용/리스크 영향:
□ 성능에 중대한 영향
□ 보안 정책 변경
□ 확장성 결정
□ 운영 복잡도 증가

팀 영향:
□ 개발 프로세스 변경
□ 도구 체인 변경
□ 코딩 표준 수립
□ 배포 전략 변경
```

### ❌ ADR이 불필요한 경우
```
□ 일상적인 버그 수정
□ 단순한 기능 추가
□ UI 디자인 변경
□ 임시적인 해결책
```

## 📝 ADR 작성 형식

### 🏗️ 기본 구조
```markdown
# ADR-XXX: [결정 제목]

## 상태 (Status)
[제안됨/승인됨/거부됨/폐기됨/대체됨]

## 컨텍스트 (Context)
[의사결정이 필요한 상황과 배경]

## 결정 (Decision)
[내린 결정과 선택한 옵션]

## 결과 (Consequences)
[결정으로 인한 결과와 영향]
```

### 넘버링 규칙
```
ADR-001: 첫 번째 결정
ADR-002: 두 번째 결정
...
ADR-XXX: N번째 결정

형식: ADR-[3자리 숫자]: [간단한 제목]
```

### 파일구조
```
adr/
├── README.md                    # 이 파일
├── adr-template.md             # ADR 작성 템플릿
├── ADR-001-frontend-framework.md
├── ADR-002-state-management.md
├── ADR-003-database-choice.md
└── index.md                    # ADR 목록 및 현황
```

### 상태관리
```
제안됨 (Proposed):
- 아직 결정되지 않은 상태
- 검토 중인 상태

승인됨 (Accepted):
- 팀에서 승인된 결정
- 현재 적용 중인 상태

거부됨 (Rejected):
- 검토 후 채택하지 않기로 한 결정
- 거부 사유 명시

폐기됨 (Deprecated):
- 더 이상 유효하지 않은 결정
- 대체 ADR 명시

대체됨 (Superseded):
- 새로운 결정으로 대체됨
- 후속 ADR 링크 필수
```

### 검토 체크리스트
```
내용 완성도:
□ 컨텍스트가 명확히 설명되었는가?
□ 모든 옵션이 공정하게 평가되었는가?
□ 결정 근거가 충분한가?
□ 예상되는 결과가 구체적인가?

문서 품질:
□ 제목이 명확한가?
□ 구조가 일관성 있는가?
□ 오타나 문법 오류가 없는가?
□ 관련 링크가 유효한가?

실행 가능성:
□ 구현 계획이 현실적인가?
□ 필요한 리소스가 고려되었는가?
□ 위험 요소가 식별되었는가?
□ 모니터링 방법이 정의되었는가?
```

### 📋 ADR 목록 (index.md)
```markdown
# ADR 목록

| 번호 | 제목 | 상태 | 날짜 | 결정자 |
|------|------|------|------|--------|
| [ADR-001](./ADR-001-frontend-framework.md) | 프론트엔드 프레임워크 선택 | 승인됨 | 2024-01-15 | 개발팀 |
| [ADR-002](./ADR-002-state-management.md) | 상태 관리 라이브러리 | 승인됨 | 2024-01-20 | 프론트팀 |
| [ADR-003](./ADR-003-database-choice.md) | 데이터베이스 선택 | 승인됨 | 2024-01-25 | 백엔드팀 |
```

## 🔧 도구 및 자동화

### 📱 ADR 생성 스크립트
```bash
#!/bin/bash
# adr-new.sh - 새로운 ADR 생성 스크립트

ADR_DIR="./adr"
TEMPLATE="$ADR_DIR/adr-template.md"

# 다음 ADR 번호 계산
LAST_NUM=$(ls $ADR_DIR/ADR-*.md 2>/dev/null | \
           sed 's/.*ADR-\([0-9]*\)-.*/\1/' | \
           sort -n | tail -1)
NEXT_NUM=$(printf "%03d" $((${LAST_NUM:-0} + 1)))

# 제목 입력받기
echo "ADR 제목을 입력하세요:"
read TITLE

# 파일명 생성
FILENAME="ADR-${NEXT_NUM}-$(echo $TITLE | tr ' ' '-' | tr '[:upper:]' '[:lower:]').md"

# 템플릿에서 새 ADR 생성
sed "s/ADR-XXX/ADR-${NEXT_NUM}/g; s/\[결정 제목\]/$TITLE/g" \
    $TEMPLATE > "$ADR_DIR/$FILENAME"

echo "새 ADR이 생성되었습니다: $FILENAME"
```

### 🔍 ADR 검증 스크립트
```bash
#!/bin/bash
# adr-validate.sh - ADR 형식 검증

for adr in adr/ADR-*.md; do
  echo "Validating $adr..."
  
  # 필수 섹션 확인
  grep -q "## 상태" "$adr" || echo "  ❌ 상태 섹션 누락"
  grep -q "## 컨텍스트" "$adr" || echo "  ❌ 컨텍스트 섹션 누락"
  grep -q "## 결정" "$adr" || echo "  ❌ 결정 섹션 누락"
  grep -q "## 결과" "$adr" || echo "  ❌ 결과 섹션 누락"
  
  echo "  ✅ $adr 검증 완료"
done
```