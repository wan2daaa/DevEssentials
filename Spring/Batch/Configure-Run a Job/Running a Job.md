

배치 작업을 시작하려면 최소한 두 가지,
	즉 <font color="#9bbb59">시작할 `Job`</font>과 <font color="#9bbb59">`JobLauncher`</font>가 필요합니다.
 두 가지 모두 동일한 컨텍스트에 포함될 수도 있거나, 다른 컨텍스트에 포함될 수도 있습니다.
 
**CLI에서 작업을 시작하면** 각 작업에 대해 **새 JVM이 인스턴스화**됩니다.
-> 따라서 모든 [[Spring/Batch/Domain Language of Batch/1. Job|Job]]은 고유한 `JobLauncher`가 있습니다. 

그러나 **HttpRequest**의 범위 내에 있는 웹 컨테이너 내에서 실행하는 경우, 
-> 일반적으로 <font color="#9bbb59">여러 요청이 작업을 시작하기 위해 호출하는 하나의 JobLauncher</font>(비동기 작업 시작용으로 구성됨)가 있습니다.


## Running Jobs from the Command Line

작업을 시작하는 스크립트는 JVM을 시작해야 하므로, 기본 entry point 역할을 하는 메인 메서드가 있는 클래스가 있어야 합니다. 
Spring Batch는 이러한 목적에 부합하는 구현체인 `CommandLineJobRunner`를 제공합니다. 
<font color="#9bbb59">이것은 애플리케이션을 부트스트랩(실행)하는 한 가지 방법일 뿐이라는 점에 유의하세요.</font> 

Java 프로세스를 시작하는 방법은 여러 가지가 있으며, 이 클래스를 최종적인 것으로 간주해서는 안 됩니다.

CommandLineJobRunner는 네 가지 작업을 수행합니다:
- 적절한 ApplicationContext를 로드합니다. 
- cli **arguments**를 -> `JobParameters`로 parse 합니다. 
- arguments를 기반으로 적절한 작업을 찾습니다. 
- ApplicationContext에 제공된 `JobLauncher`를 사용하여 [[Spring/Batch/Domain Language of Batch/1. Job|Job]]을 실행합니다.

이러한 모든 작업은 전달된 arguments만으로 수행됩니다. 다음 표에서는 required arguments를 설명합니다:


| `jobPath` | ApplicationContext를 생성하는 데 사용되는 XML 파일의 위치입니다. <br>이 파일에는 전체 `Job`을 실행하는 데 필요한 모든 것이 포함되어야 합니다. |
| --------- | ----------------------------------------------------------------------------------------------- |
| `jobName` | 실행할 작업의 이름입니다.                                                                                  |
위의 arguments는 path를 앞에, name을 뒤에 붙여 전달해야 합니다. 
이 이후의 모든 arguments는 [[Spring/Batch/Domain Language of Batch/1. Job#JobParameters|JobParameters]]로 간주되어 [[Spring/Batch/Domain Language of Batch/1. Job#JobParameters|JobParameters]] 객체로 변환되며, name=value 형식이어야 합니다.

다음 예는 Java에 정의된 [[Spring/Batch/Domain Language of Batch/1. Job|Job]]에 [[Spring/Batch/Domain Language of Batch/1. Job#JobParameters|JobParameters]] 전달된 날짜를 보여줍니다:
```bash
java CommandLineJobRunner io.spring.EndOfDayJobConfiguration endOfDay schedule.date=2007-05-05,java.time.LocalDate
```


**어떤 값이 identify 안하는 값인지 지정할 수 있다.**
```bash
java CommandLineJobRunner endOfDayJob.xml endOfDay \ 
schedule.date=2007-05-05,java.time.LocalDate,true \ 
vendor.id=123,java.lang.Long,false
```

대부분의 경우 manifest를 사용하여 `main` 클래스를 jar에 선언하고 싶을 것입니다. 
그러나 간단하게 하기 위해 클래스를 직접 사용했습니다. 
이 예제에서는 배치의 [[Spring/Batch/Domain Language of Batch/0. 기본 컨셉|도메인 언어]]에 있는 EndOfDay 예제를 사용합니다. 
- 첫 번째 argument는 [[Spring/Batch/Domain Language of Batch/1. Job|Job]]을 포함하는 Configuration 클래스에 대한 정규화된 클래스 이름인 io.spring.`EndOfDayJobConfiguration`입니다. 
- 두 번째 argument인 <font color="#9bbb59">endOfDay</font>는 Job name을 나타냅니다. 
- 마지막 argument인 <font color="#9bbb59">schedule.date</font>=2007-05-05,java.time.LocalDate는 java.time.LocalDate 유형의 [[Spring/Batch/Domain Language of Batch/1. Job#JobParameters|JobParameters]] 객체로 변환됩니다.
```java
@Configuration
@EnableBatchProcessing
public class EndOfDayJobConfiguration {

    @Bean
    public Job endOfDay(JobRepository jobRepository, Step step1) {
        return new JobBuilder("endOfDay", jobRepository)
    				.start(step1)
    				.build();
    }

    @Bean
    public Step step1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("step1", jobRepository)
    				.tasklet((contribution, chunkContext) -> null, transactionManager)
    				.build();
    }
}
```

### Exit Codes
CLI에서 배치 작업을 시작할 때, 엔터프라이즈 스케줄러를 사용하는 경우가 많습니다. 
대부분의 스케줄러는 상당히 단순하며 프로세스 수준에서만 작동합니다. 
-> 즉, 일부OS 프로세스(예: 호출하는 셸 스크립트)에 대해서만 알고 있습니다. 

이 시나리오에서 <font color="#9bbb59">`Job`의 성공 또는 실패</font>에 대해 스케줄러와 통신하는 유일한 방법은 == <font color="#9bbb59">`return codes`</font>를 통해서입니다. 
`return codes`는 프로세스가 실행 결과를 나타내기 위해 스케줄러에 반환하는 숫자입니다. 

가장 간단한 경우 
	- 0은 성공, 
	- 1은 실패입니다. 

그러나 "작업 A가 4를 반환하면 작업 B를 시작하고, 5를 반환하면 작업 C를 시작합니다."와 같이 더 복잡한 시나리오가 있을 수 있습니다. 
이러한 유형의 동작은 스케줄러 수준에서 구성되지만, 

특정 배치 작업에 대한 `exit code`의 숫자 표현을 반환하는 방법을 제공하는 Spring Batch와 같은 처리 프레임워크가 중요합니다.

Spring Batch에서는 5장에서 더 자세히 다루는 `ExitStatus` 내에 이 기능이 캡슐화되어 있습니다. 
`Exit Code`에 대해 논의하기 위해 알아야 할 유일한 중요한 것은 `ExitStatus`에는 프레임워크(또는 개발자가)가 <font color="#9bbb59">설정한 종료 코드 속성이 있으며</font> `JobLauncher`에서 반환된 `JobExecution`의 일부로 반환된다는 것입니다. 
CommandLineJobRunner는 ExitCodeMapper 인터페이스를 사용하여 이 문자열 값을 숫자로 변환합니다:
```java
public interface ExitCodeMapper {
    public int intValue(String exitCode);
}
```
`ExitCodeMapper`의 필수 계약은 문자열 종료 코드가 주어지면 **숫자 표현이 반환된다는 것**입니다.
`job runner`가 사용하는 default implementation은 
 - 완료 시 0, 
 - 일반 오류 시 1, 
 - 제공된 컨텍스트에서 `Job`을 찾을 수 없는 등의 모든 `job runner error` 시 2를 
 반환하는 `SimpleJvmExitCodeMapper`입니다. 
 
 위의 세 가지 값보다 더 복잡한 값이 필요한 경우, `ExitCodeMapper` 인터페이스의 사용자 정의 구현을 제공해야 합니다.
`CommandLineJobRunner`는 <font color="#9bbb59">`ApplicationContext`를 생성하는 클래스</font>이므로 
'함께 `@autowired`'할 수 없으므로 덮어써야 하는 값은 모두 `@autowired`해야 합니다.

즉, `BeanFactory` 내에서 `ExitCodeMapper`의 구현이 발견되면 -> 컨텍스트가 생성된 후 runner에 주입됩니다. 
자체 `ExitCodeMapper`를 제공하려면 구현을 루트 수준 빈으로 선언하고, 런처가 로드하는 ApplicationContext의 일부가 되도록 하기만 하면 됩니다.


## Running Jobs from within a Web Container
웹 컨테이너 내에서 작업 실행

지금까지 오프라인 처리(예: 배치 작업)는 앞서 설명한 대로 CLI에서 시작되었습니다. 
그러나 HttpRequest에서 실행하는 것이 더 나은 옵션인 경우가 많습니다. 
이러한 사용 사례에는 *reporting*, *ad-hoc(임시) job running*, *웹 애플리케이션 지원* 등이 포함됩니다. 
왜냐하면, 배치 작업은 (정의상) 오래 실행되기 때문에 가장 중요한 문제는 <font color="#9bbb59">작업을 비동기적으로 실행하는 것</font>입니다:
![[assets/Pasted image 20241216001546.png]]
이 경우 컨트롤러는 Spring MVC 컨트롤러입니다. 
컨트롤러는 비동기적으로 실행되도록 구성된 `JobLauncher`를 사용하여 `Job`을 실행하고 
-> 이 컨트롤러는 즉시 `JobExecution`을 반환합니다.

`Job`은 여전히 실행 중일 가능성이 높습니다. 
그러나 이 *nonblocking behavior*을 사용하면 컨트롤러가 즉시 반환할 수 있으며, 이는 HttpRequest를 처리할 때 필요합니다. 

다음 목록은 예시를 보여줍니다:
```java
@Controller
public class JobLauncherController {

    @Autowired
    JobLauncher jobLauncher;

    @Autowired
    Job job;

    @RequestMapping("/jobLauncher.html")
    public void handle() throws Exception{
        jobLauncher.run(job, new JobParameters());
    }
}
```