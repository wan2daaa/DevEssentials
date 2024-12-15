- Spring Batch 2.2.0부터는 xml과 동일한 Java Configuration을 사용하여 배치 작업을 구성할 수 있습니다. 
- Java 기반 구성을 위한 세 가지 구성 요소, 즉`@EnableBatchProcessing` 어노테이션과 두 개의 빌더가 있습니다.

- `@EnableBatchProcessing` 어노테이션은 Spring의 다른 `@Enable*` 어노테이션과 유사하게 작동합니다. 
- 이 경우 `@EnableBatchProcessing`은 배치 작업을 빌드하기 위한 기본 Configuration을 제공합니다. 
- 이 기본 Configuration 내에서 autowired 할 수 있는 **여러 beans과 함께 `StepScope` 및 `JobScope`의 인스턴스가 생성**됩니다:
	- `JobRepository`: a bean named `jobRepository`
	- `JobLauncher`: a bean named `jobLauncher`
	- `JobRegistry`: a bean named `jobRegistry`
	- `JobExplorer`: a bean named `jobExplorer`
	- `JobOperator`: a bean named `jobOperator`

- default implementation은 앞의 목록에 언급된 *bean*을 제공하며, *context* 내에서 `Datasource` 및 `PlatformTransactionManager`를 빈으로 제공해야 합니다. 
- `Datasource`와 `PlatformTransactionManager`는 ->**`JobRepository` 및 `JobExplorer` 인스턴스에서 사용됩니다.**
- 기본적으로`Datasource`는 dataSource, TransactionManager는 transactionManager라는 이름의 `Datasource`가 사용됩니다. 
- `@EnableBatchProcessing` 어노테이션의 속성을 사용하여 이러한 `bean`을 customize할 수 있습니다. 

- 다음 예는 custom data source and transaction manager를 제공하는 방법을 보여줍니다:
```java
@Configuration
@EnableBatchProcessing(dataSourceRef = "batchDataSource", transactionManagerRef = "batchTransactionManager")
public class MyJobConfiguration {

	@Bean
	public DataSource batchDataSource() {
		return new EmbeddedDatabaseBuilder().setType(EmbeddedDatabaseType.HSQL)
				.addScript("/org/springframework/batch/core/schema-hsqldb.sql")
				.generateUniqueName(true).build();
	}

	@Bean
	public JdbcTransactionManager batchTransactionManager(DataSource dataSource) {
		return new JdbcTransactionManager(dataSource);
	}

	@Bean
	public Job job(JobRepository jobRepository) {
		return new JobBuilder("myJob", jobRepository)
				//define job flow as needed
				.build();
	}

}
```

하나의 Configuration class에만 `@EnableBatchProcessing` 어노테이션이 있어야 합니다. 어노테이션이 있는 클래스가 있으면 앞서 설명한 모든 Configuration이 완료된 것입니다.


`DefaultBatchConfiguration` 를 상속받으면, `@EnableBatchProcessing` 어노테이션 과 똑같이 작동하고(bean 등록), 조금더 상세하게 세팅을 수정할 수 있다.
```java
@Configuration
class MyJobConfiguration extends DefaultBatchConfiguration {

	@Bean
	public Job job(JobRepository jobRepository) {
		return new JobBuilder("job", jobRepository)
				// define job flow as needed
				.build();
	}

}
```

