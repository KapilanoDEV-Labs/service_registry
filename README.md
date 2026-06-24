The pom.xml has:

<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
		</dependency>

You need to add @EnableEurekaServer to the ServiceRegistryApplication.java.

To the quiz and questions service application.properties you need a name:

For example: spring.application.name=question-service // this will appear on Eureka server's wed page

Our application.properties

spring.application.name=service.registry
server.port=8761

eureka.instance.hostname=localhost
eureka.client.fetch-registry=false // Don't want to fetch the repository
eureka.client.register-with-eureka=false // Don't want to register with itself

You need to uncomment the netflix-eureka-client in each client MS's pom.xml.

You can create multiple instances of question or quiz MS and if configured they will
also appear on Eureka server.

The MS use Feign to find each other. The quiz MS can use RestTemplate to connect
to the question MS but you need to hardcode the IP and port. Instead Feign will connect
it to question.
On the quiz MS uncomment the spring-cloud-starter-openfeign in the pom.xml. Then create
create QuestionInterface.java:

@FeignClient("QUESTION-SERVICE")
public interface QuestionInterface {
@GetMapping("question/generate")
public ResponseEntity<List<Integer>> getQuestionsForQuiz(@RequestParam String categoryName, @RequestParam Integer numQuestions);

    @PostMapping("question/getQuestions")
    public ResponseEntity<List<QuestionWrapper>> getQuestionsFromId(@RequestBody List<Integer> questionIds);

    @PostMapping("question/getScore")
    public ResponseEntity<Integer> getScore(@RequestBody List<Response> responses);

}# service_registry
