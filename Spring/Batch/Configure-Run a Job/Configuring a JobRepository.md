Ref: [[Spring/Batch/Domain Language of Batch/3. JobRepository|JobRepository]]

**<span style="background:rgba(240, 200, 0, 0.2)">`JobRepository`를 *customize* 방법을 설명</span>**

앞서 설명한 것처럼 `JobRepository`는 `JobExecution` 및 `StepExecution`과 같은 Spring Batch 내의 다양한 persisted 도메인 객체의 기본 CRUD 작업에 사용됩니다. 
- `JobLauncher`, `Job` 및 `Step`과 같은 많은 주요 기능에 필요합니다.

`@EnableBatchProcessing`을 사용하는 경우, `JobRepository`가 제공됩니다. 
- `JobRepository`의 Configuration options은 다음 예시와 같이 `@EnableBatchProcessing` 어노테이션의 속성을 통해 지정할 수 있습니다:
```java
@Configuration
@EnableBatchProcessing(
		dataSourceRef = "batchDataSource",
		transactionManagerRef = "batchTransactionManager",
		tablePrefix = "BATCH_",
		maxVarCharLength = 1000,
		isolationLevelForCreate = "SERIALIZABLE")
public class MyJobConfiguration {

   // job definition

}
```
- 여기에 나열된 구성 옵션은 모두 *optional*


## Transaction Configuration for the JobRepository

- 네임스페이스 또는 제공된 `FactoryBean`을 사용하는 경우, 리포지토리를 중심으로 transaction advice가 자동으로 생성됩니다.
- 이는 실패 후, 재시작에 필요한 상태를 포함한 **배치 메타데이터가 올바르게 유지되도록** 하기 위한 것입니다.
- repository method가 non transactional인 경우, 프레임워크의 동작이 제대로 정의되지 않습니다. 
- `create*` 메서드 속성의 격리 수준은 작업이 시작될 때 **두 프로세스가 동시에 동일한 작업을 시작하려고 시도하는 경우** **한 프로세스만 성공하도록 하기 위해** 별도로 지정됩니다. 
- `create*`메서드의 기본 격리 수준은 `SERIALIZABLE`로 매우 공격적인 수준입니다. 
- `READ_COMMITTED`도 일반적으로 똑같이 잘 작동합니다. 
- **두 프로세스가 이런 식으로 충돌할 가능성이 없는 경우** -> `READ_UNCOMMITTED`를 사용해도 괜찮습니다. 

- 그러나 `create*` 메서드 호출은 매우 짧기 때문에, 데이터베이스 플랫폼이 지원하는 한 `SERIALIZED`로 인해 문제가 발생할 가능성은 거의 없습니다. 
- 그래도, 이 설정을 재정의할 수 있습니다.
```java
@Configuration
@EnableBatchProcessing(isolationLevelForCreate = "ISOLATION_REPEATABLE_READ")
public class MyJobConfiguration {

   // job definition

}
```

- namespace를 사용하지 않는 경우에는 **AOP를 사용하여 리포지토리의 트랜잭션 동작도 구성해야 합니다**.
```java
@Bean
public TransactionProxyFactoryBean baseProxy() {
	TransactionProxyFactoryBean transactionProxyFactoryBean = new TransactionProxyFactoryBean();
	Properties transactionAttributes = new Properties();
	transactionAttributes.setProperty("*", "PROPAGATION_REQUIRED");
	transactionProxyFactoryBean.setTransactionAttributes(transactionAttributes);
	transactionProxyFactoryBean.setTarget(jobRepository());
	transactionProxyFactoryBean.setTransactionManager(transactionManager());
	return transactionProxyFactoryBean;
}
```

## Changing the Table Prefix

- [[Spring/Batch/Domain Language of Batch/3. JobRepository|JobRepository]]의 또 다른 수정 가능한 속성은 **메타데이터 테이블의 테이블 접두사(*prefix*)**입니다. 
- 기본적으로 테이블 접두사는 모두 *BATCH_*로 시작됩니다.
- `BATCH_JOB_EXECUTION`과 `BATCH_STEP_EXECUTION`이 두 가지 예입니다. 
- 그러나 이 prefix를 수정해야 하는 잠재적인 이유가 있습니다. 
- 테이블 이름 앞에 스키마 이름을 추가해야 하거나, 동일한 스키마 내에 둘 이상의 메타데이터 테이블 세트가 필요한 경우 테이블 접두사를 변경해야 합니다.

```java
@Configuration
@EnableBatchProcessing(tablePrefix = "SYSTEM.TEST_")
public class MyJobConfiguration {

   // job definition

}
```

앞의 변경 사항을 고려할 때 메타데이터 테이블에 대한 모든 쿼리에는 **SYSTEM.TEST_**가 앞에 붙습니다. BATCH_JOB_EXECUTION은 SYSTEM.TEST_JOB_EXECUTION로 변경됩니다.

## Non-standard Database Types in a Repository (리포지토리의 비표준 데이터베이스 유형)

- TODO. 현재 필요가 없어 추후 추가예정 

					