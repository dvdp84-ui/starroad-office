# status.json 갱신 절차 (루틴용)

1. 아래 각 봇에 대해 cursor `ReadTranscript`를 `{"in":"<id>","limit":60}`, `write_to_file:true`로 호출한다.
   - 비서실장 66aa6b26-71f1-41f6-8df4-e9374b29d207 · 루틴관리과장 b785d8bb-2524-4627-be3a-94cd0e263e66
   - 투자사업부 2caee292-2e95-4563-91e5-c209c9d20d94 · 주식초고수 2b2eb211-cf50-452e-81b3-ab4db332c101 · 바이매매봇 0e9fecc1-2385-4959-bbd2-ae580448ffd4
   - 앱개발사업부 e10e080e-8373-4295-b779-27ea19824aec · 홍보사업부 b61a7948-a185-4cfa-8b4a-c36525c7cec8
   - 굿즈사업부 f157be1f-e8c3-4fa3-982b-19184d93dc81 · 리서치사업부 2199cb95-1bb0-48a3-b2e4-c233efacac02
   - 루미뚱이 총괄기획자 8cde2bbf-b841-45f4-af08-87ef7d262782 · 그래픽 a05b2c3c-648b-460c-aabc-e737ebf039aa · 공간 a38bb583-6823-4611-98c3-354cb71eafa5 · 홍보 fe9bcb8b-b66b-4ecb-96d6-39002395abbe
   - id 모름(항상 미가동/기록 없음): 이것저것, 에릭슈미츠, Proto Bot, Researchy — id를 알게 되면 여기에 추가.
2. 마지막 활동 시각: 트랜스크립트에는 메시지별 시각이 없으므로, 파일에서 마지막 `<timestamp>...(UTC+9)</timestamp>` 를 쓴다:
   `grep -oE '[A-Za-z]+day, [A-Z][a-z]{2} [0-9]+, 2026, [0-9:]+ [AP]M' FILE | tail -1`
3. 요약: 마지막 SendToUser / SendToAgent 내용을 한 줄(30자 안팎) 한국어로. 지어내지 말 것.
4. 상태 규칙 (위에서부터 먼저 맞는 것):
   - 기록 없음/읽기 실패 → 미가동 (summary "기록 없음", lastActive null)
   - 마지막 활동이 1시간 이내 → 작업중
   - 마지막 메시지가 사용자 입력·키·결정 없이는 진행 불가라고 보고 → 막힘
   - 오늘(KST) 아침 보고를 비서실장/사장님께 보냈고 그 뒤 활동 없음 → 보고완료
   - 그 외 → 대기
   (페이지는 작업중인데 lastActive가 1시간 넘으면 자동으로 대기로 표시함)
5. status.json 형식: `{"updatedAt":"<ISO+09:00>","bots":{"<index.html STAFF 이름>":{"id","status","lastActive","summary"}}}` — 키는 index.html의 STAFF 이름과 정확히 같아야 함.
6. `python3 -m json.tool status.json` 로 검증 → `git add status.json && git commit -m "status 갱신" && git push`.
7. 확인: `curl -s -o /dev/null -w '%{http_code}' https://dvdp84-ui.github.io/starroad-office/status.json` 가 200이고 updatedAt이 새 값인지 (Pages 반영 1~2분).
