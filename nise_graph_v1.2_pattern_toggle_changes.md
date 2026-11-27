# v1.2 패턴 적용 토글 표시 조건 변경

- 수정 파일: `nise_graph_13types_v1.2.html`
- 관련 커밋: `099275a`
- 이 문서는 해당 커밋 중 패턴 적용 토글 관련 변경 두 곳을 정리합니다.

## 변경 목적

범례가 없는 단일 계열 그래프는 패턴 없는 기본 스타일을 사용합니다. 이 경우 ‘패턴 적용’ 토글을 눌러도 그래프가 바뀌지 않으므로, 해당 토글을 표시하지 않도록 수정했습니다.

## 1. 토글 표시 조건 추가

위치: `reviewSyncClueButtons(node)` 함수, 9994행 부근.

```javascript
// 범례 없이 단일 계열을 그리는 그래프는 기본 무패턴 스타일이므로 토글을 숨긴다.
const chart=echarts.getInstanceByDom(node);
const option=chart && chart.getOption();
const singleChart=section.querySelectorAll('.detail-chart,.sm-plot').length===1;
const hidePattern=!!(singleChart && option && option.series && option.series.length===1 &&
  !(option.legend && option.legend.length));

[patternBtn,sidePatternBtn].forEach(function(button){
  if(button) button.hidden=hidePattern;
});
```

아래 조건이 모두 충족되면 패턴 적용 토글의 `hidden` 속성을 켭니다.

1. 해당 `.spec-section` 안의 `.detail-chart` 또는 `.sm-plot` 요소가 총 1개입니다.
2. ECharts 옵션의 `series`가 1개입니다.
3. ECharts 옵션에 `legend`가 없거나 빈 배열입니다.

상단 패턴 버튼(`patternBtn`)과 우측 패널 패턴 버튼(`sidePatternBtn`)에 같은 조건을 적용합니다. 그래프 유형 전환으로 조건을 충족하지 않게 되면 `hidden` 속성을 해제해 토글을 다시 표시합니다.

## 2. 숨김 CSS 추가

위치: 2264행.

```css
.review-chart-actions button[hidden],
.ts-chart-sidepanel button[data-kind="patterns"][hidden]{
  display:none!important
}
```

기존 버튼의 `display:inline-flex` 스타일보다 숨김 처리가 우선하도록 `display:none!important`를 적용했습니다.

## 확인 결과

브라우저에서 ⑦ 전학 경험 유무 및 시기 화면의 그래프 유형을 전환하며 확인했습니다.

| 그래프 유형 | 확인 결과 |
|---|---|
| 총 수(명) 기준 연도 비교 | 범례 없는 단일 계열이므로 패턴 적용 토글이 숨겨짐 |
| 그룹 세로막대 그래프 | 여러 계열의 범례와 함께 패턴 적용 토글이 다시 표시됨 |
| 총 수(명) 기준으로 재전환 | 패턴 적용 토글이 다시 숨겨짐 |

※ 행 번호는 커밋 `099275a` 기준이며, 이후 코드 변경에 따라 달라질 수 있습니다.
