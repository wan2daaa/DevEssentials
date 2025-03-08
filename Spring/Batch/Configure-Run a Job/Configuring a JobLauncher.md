이 섹션에서는 `JobLauncher`를 직접 구성하는 방법에 대해 설명합니다.

- `EnableBatchProcessing`을 사용하면 `JobRegistry`가 제공됩니다. 
- `JobLauncher` 인터페이스의 가장 기본적인 implementaion은 <font color="#9bbb59">`TaskExecutorJobLauncher`</font>입니다. 
- 이 인터페이스의 유일한 필수 종속성(*required dependency*)은 (실행을 가져오는 데 필요한) `JobRepository`입니다.

The following example shows a `TaskExecutorJobLauncher` in Java:
```java
...
@Bean
public JobLauncher jobLauncher() throws Exception {
	TaskExecutorJobLauncher jobLauncher = new TaskExecutorJobLauncher();
	jobLauncher.setJobRepository(jobRepository);
	jobLauncher.afterPropertiesSet();
	return jobLauncher;
}
...
```

JobExecution이 획득(*obtained*)되면 다음 이미지와 같이 [[Spring/Batch/Domain Language of Batch/1. Job|Job]]의 실행 메서드로 전달되어 궁극적으로 caller에게 `JobExecution`을 반환합니다:
![[assets/Pasted image 20241215233808.png]]
이 시퀀스는 간단하며, 스케줄러에서 실행할 때 잘 작동합니다.
- 반환 값은 `JobExecution`에 `ExitStatus.FINISHED` or `FAILED`

그러나 HTTP 요청에서 실행하려고 할 때 문제가 발생합니다. 
이 시나리오에서는 실행을 **비동기적**으로 수행하여 `TaskExecutorJobLauncher`가 caller에게 즉시 반환되도록 해야 합니다. 
이는 배치 작업과 같이 `오래 실행되는 프로세스에 필요한 시간 동안 HTTP 요청을 열어 두는 것은 좋은 방법이 아니기 때문`입니다. 다음 이미지는 예시 시퀀스를 보여줍니다:
![[assets/Pasted image 20241215233942.png]]
`TaskExecuter`를 구성하여 이 시나리오를 허용하도록 `TaskExecutorJobLauncher`를 구성할 수 있습니다.

다음 Java 예제에서는 즉시 반환하도록 TaskExecutorJobLauncher를 구성합니다:
```java
@Bean
public JobLauncher jobLauncher() {
	TaskExecutorJobLauncher jobLauncher = new TaskExecutorJobLauncher();
	jobLauncher.setJobRepository(jobRepository());
	jobLauncher.setTaskExecutor(new SimpleAsyncTaskExecutor());
	jobLauncher.afterPropertiesSet();
	return jobLauncher;
}
```
스프링 `TaskExecutor` 인터페이스의 모든 구현을 사용하여, 작업이 비동기적으로 실행되는 방식을 제어할 수 있습니다.
