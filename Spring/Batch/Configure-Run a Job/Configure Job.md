- `Job` 인터페이스의 구현은 여러 가지가 있습니다. 
- 그러나 이러한 구현체는 제공된 *builder*(Java 구성의 경우) 또는 XML 네임스페이스(XML 기반 구성의 경우) 뒤에 추상화되어 있습니다. 

```java
@Bean
public Job footballJob(JobRepository jobRepository) {
    return new JobBuilder("footballJob", jobRepository)
                     .start(playerLoad())
                     .next(gameLoad())
                     .next(playerSummarization())
                     .build();
}
```

- `Job`(그리고 일반적으로 그 안에 있는 모든 `Step`)에는 `JobRepository`가 필요합니다. 
- `JobRepository`의 Configuration은 [[Spring/Batch/Configure-Run a Job/Java Configuration|Java Configuration]]을 통해 처리됩니다.

- 위의 예시 코드에는 세 개의 `Step` 인스턴스로 구성된 `Job`을 보여줍니다. 
- `Job` 관련 빌더에는 병렬화(*parallelization*)(Split), 선언적 흐름 제어(*declarative flow control*)(Decision), 흐름 정의의 외부화(*externalization of flow definitions*)(Flow)에 도움이 되는 다른 요소도 포함할 수 있습니다.


## Restartability
- 배치 작업을 실행할 때 중요한 문제 중 하나는 **`Job`이 다시 시작될 때의 동작에 관한 것**입니다. 
- 특정 `JobInstance`에 대한 `JobExecution`이 이미 존재하는 경우 -> `Job`이 시작될 때 **재시작**(*restart*)으로 간주됩니다. 
- 이상적으로는 모든 작업이 중단된 지점에서 다시 시작될 수 있어야 하지만, 이것이 불가능한 시나리오가 있습니다. 
- 이 시나리오에서는 새 `JobInstance`가 생성되도록 하는 것은 전적으로 개발자의 몫입니다. 
- 작업을 다시 시작해서는 안 되지만, 항상 새 JobInstance의 일부로 실행해야 하는 경우 **restartable 속성을 false**로 설정할 수 있습니다.

```java
@Bean
public Job footballJob(JobRepository jobRepository) {
    return new JobBuilder("footballJob", jobRepository)
                     .preventRestart()
                     ...
                     .build();
}
```
- 다른 말로 표현하자면, **restartable을 false**로 설정하면 "이 `Job`은 재시작을 지원하지 않습니다"라는 뜻입니다. 
- 다시 시작할 수 없는 `Job`을 다시 시작하면, `JobRestartException`이 발생합니다.


## Intercepting Job Execution
- Job이 실행되는 동안, custom code를 실행할 수 있도록 **수명 주기에서 다양한 이벤트에 대한 알림을 받을 수 있다.**
- **SimpleJob은 적절한 시점에 `JobListener`를 호출**하여 이를 가능하게 합니다
```java
public interface JobExecutionListener {

    void beforeJob(JobExecution jobExecution);

    void afterJob(JobExecution jobExecution);
}
```

```java
@Bean
public Job footballJob(JobRepository jobRepository) {
    return new JobBuilder("footballJob", jobRepository)
                     .listener(sampleListener())
                     ...
                     .build();
}
```

- `afterJob` method는 <font color="#9bbb59">작업의 성공 또는 실패에 관계없이 호출된다</font>

- **성공 또는 실패를 확인해야 하는 경우** -> `JobExecution`에서 해당 정보를 얻을 수 있습니다
```java
public void afterJob(JobExecution jobExecution){
    if (jobExecution.getStatus() == BatchStatus.COMPLETED ) {
        //job success
    }
    else if (jobExecution.getStatus() == BatchStatus.FAILED) {
        //job failure
    }
}
```


이 인터페이스에 해당하는 주석은 다음과 같습니다:
- `@BeforeJob`
- `@AfterJob`

## JobParametersValidator
- `AbstractJob`의 subclass를 사용하는 `Job`은 런타임에 선택적으로 `Job Parameters`에 대한 validator를 선언할 수 있습니다. 
- 이 기능은 예를 들어 **`Job`이 모든 필수 매개변수로 시작되는지 확인해야 할 때 유용**합니다. 
- 간단한 필수 매개변수와 선택 매개변수의 조합을 제한하는 데 사용할 수 있는 `DefaultJobParametersValidator`가 있습니다. 
- 보다 복잡한 제약 조건의 경우 인터페이스를 직접 구현할 수 있습니다.
```java
@Bean
public Job job1(JobRepository jobRepository) {
    return new JobBuilder("job1", jobRepository)
                     .validator(parametersValidator())
                     ...
                     .build();
}
```


## Inheriting from a Parent Job
- 현재 필요하지 않아서 작성 안함 