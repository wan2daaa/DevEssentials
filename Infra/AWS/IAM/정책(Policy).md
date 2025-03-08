- AWS 리소스에 대한 액세스 권한을 정의한 것
- 사용자, 그룹, 역할에 정책을 연결하여 사용 
- JSON 문서 형식으로 이루어짐 
- 정책이 명시되지 않는 경우, **기본적으로 모든 요청이 거부(Deny)**
```json
{
	"Statement": [{
		"Effect" : "Allow or Deny",
		"Action" : "정책이 허용하거나 거부하는 작업 목록",
		"Resource" : "작업이 적용되는 리소스",
		"Condition": { //정책이 적용되는 세부 조건(Optional)
			"condition" : {
				"key" : "value"
			}
		}
	}]
}
```

