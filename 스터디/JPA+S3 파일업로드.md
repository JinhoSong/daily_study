1. [Access Key와 secret Key 받기](#Access-Key와-secret-Key-받기)
2. [S3 버킷](#S3-버킷)
3. [FileUpload](#FileUpLoad)
4. [테스트](#테스트)
5. [다음 할일](#다음 할일)

---

1. ### Access Key와 secretKey 받기

	1. **key 생성하기**

		1. aws 접속 후 IAM 서비스로 이동

			* **IAM 이란 암호나 엑세스 키를 공유하지 않고 AWS 계정의 리소스를 관리하고 사용할 수 있는 권한을 다른 사람에게 부여할 수 있는 기능을 제공한다**
			* 즉, 다른 사람이 나의 AWS에 접근이 가능해지고 내가 올린 파일을 다운로드 하는게 가능해진다.
			* **다른 말로 해커가 해당 키를 알아낼 경우 열심히 비트코인 채굴에 이용당할 수 있으니 key를 받고나서 절대 공개되지 않도록 한다!!**

		2. 사용자 이름과 Access에 체크한다.

			​	![image](C:\gitBlog\Daily_Study\springSecurity\97026871-ee29ec00-1594-11eb-93d1-d574aa329f96.png)

		3. 기존 정책 직접 연결 -> AmazonS3FullAccess를 선택

			![image](https://user-images.githubusercontent.com/52272332/97026985-1580b900-1595-11eb-957e-90617a4b6855.png)

		4. 5단계까지 이동 후 .csv 파일을 다운받는다.

			![image-20201024010848885](C:\Users\chosu\AppData\Roaming\Typora\typora-user-images\image-20201024010848885.png)

			* **해당 파일에 Access Key, secretKey가 모두 존재한다**

	2. **받은 키 설정**

		`application.properties`

		```properties
		cloud.aws.credentials.accessKey= 엑세스 키
		cloud.aws.credentials.secretKey= 시크릿 키
		cloud.aws.s3.bucket= 버킷 이름
		cloud.aws.region.static=ap-northeast-2
		cloud.aws.stack.auto=false
		```

		* **region.static**
			1. s3를 서비스할 REGION명을 작성
			2. `ap-northeast-2` -> 서울을 뜻한다
		* **stack.auto**
			1. Spring Cloud 실행 시 서버 구성을 자동화하는 이를 사용하지 않는다는 의미
			2. 안하면 Error 발생

2. ### S3 버킷

	* **파일을 받고 해당 파일 다운로드 URL을 제공해주는 버킷을 사용한다**

	1. AWS - S3에 접속 후 버킷 만들기 클릭

		![image](https://user-images.githubusercontent.com/52272332/97031113-be7de280-159a-11eb-98d1-954657686019.png)

	2. 권한 설정으로 이동 후 맨 뒤 모든 퍼블릭 엑세스 차단을 해제한다.

		![image](https://user-images.githubusercontent.com/52272332/97031255-eff6ae00-159a-11eb-82fd-460931913d3b.png)

		* **해제 안하면 나중에 다운로드 받으려고 눌렀을때 권한으로 인해 접근 불가하다**

3. ### FileUpLoad

	`build.gradle`

	```groovy
	project(':module-core') {
	    dependencies {
	        compile group: 'org.springframework.cloud', name: 'spring-cloud-aws', version: '2.2.1.RELEASE', ext: 'pom'
	        compile group: 'org.springframework.cloud', name: 'spring-cloud-aws-autoconfigure', version: '2.2.1.RELEASE'
	      // 해당 코드를 추가한다. 
	    }
	}
	```

	`Material.java`

	```java
	package com.predict.stock.material;
	
	import com.predict.stock.borad.Board;
	import lombok.Builder;
	import lombok.Getter;
	import lombok.NoArgsConstructor;
	
	import javax.persistence.*;
	
	@Entity
	@Getter
	@NoArgsConstructor
	public class Material {
	
	    @Id
	    @GeneratedValue
	    private Long id;
	
	    private String title;
	
	    private String filePath;
	
	    @Builder
	    public Material(Long id, String title, String filePath){
	        this.id = id;
	        this.title = title;
	        this.filePath = filePath;
	    }
	}
	```

	`MaterialRequestDto`

	```java
	package com.predict.stock.Dto.material;
	
	import com.predict.stock.material.Material;
	import lombok.*;
	
	@Getter
	@Setter
	@ToString
	@NoArgsConstructor
	public class MaterialRequestDto {
	    private Long id;
	    private String title;
	    private String filePath;
	
	    public Material toEntity(){
	        Material material = Material.builder()
	                .id(id)
	                .title(title)
	                .filePath(filePath)
	                .build();
	        return material;
	    }
	
	    @Builder
	    public MaterialRequestDto(Long id, String title, String filePath){
	        this.id = id;
	        this.title = title;
	        this.filePath = filePath;
	    }
	
	}
	
	```

	* **전달 받은 title과 filePath를 Entity에 전달하기 위한 DTO**

	`S3Service`

	```java
	package com.predict.stock.s3;
	
	import com.amazonaws.auth.AWSCredentials;
	import com.amazonaws.auth.AWSStaticCredentialsProvider;
	import com.amazonaws.auth.BasicAWSCredentials;
	import com.amazonaws.services.s3.AmazonS3;
	
	import com.amazonaws.services.s3.AmazonS3ClientBuilder;
	import com.amazonaws.services.s3.model.CannedAccessControlList;
	import com.amazonaws.services.s3.model.PutObjectRequest;
	import lombok.NoArgsConstructor;
	import org.springframework.beans.factory.annotation.Value;
	import org.springframework.stereotype.Service;
	import org.springframework.web.multipart.MultipartFile;
	
	import javax.annotation.PostConstruct;
	import java.io.IOException;
	
	@Service
	@NoArgsConstructor
	public class S3Service {
	
	    private AmazonS3 s3Client;
	
	    @Value("${cloud.aws.credentials.accessKey}")
	    private String accessKey;
	
	    @Value("${cloud.aws.credentials.secretKey}")
	    private String secretKey;
	
	    @Value("${cloud.aws.s3.bucket}")
	    private String bucket;
	
	    @Value("${cloud.aws.region.static}")
	    private String region;
	
	    @PostConstruct
	    public void setS3Client(){
	        AWSCredentials credentials = new BasicAWSCredentials(this.accessKey,this.secretKey);
	
	        s3Client = AmazonS3ClientBuilder.standard()
	                .withCredentials(new AWSStaticCredentialsProvider(credentials))
	                .withRegion(this.region)
	                .build();
	    }
	
	    public String upload(MultipartFile file) throws IOException{
	        String fileName = file.getOriginalFilename();
	
	        s3Client.putObject(new PutObjectRequest(bucket,fileName, file.getInputStream(), null)
	        .withCannedAcl(CannedAccessControlList.PublicRead));
	        return s3Client.getUrl(bucket,fileName).toString();
	    }
	}
	
	```

	* `@Value("${cloud.aws.credentials.accessKey}")`
		1. `application.properties`에 설정해둔 key값들을 가져와 넣어준다.
	* `@PostConstruct`
		1.  의존성 주입이 이루어진 후 초기화를 수행하는 메서드이다.
		2. 이 메서드는 다른 리소스에서 호출되지 않는다 해도 수행된다.
		3. **사용시 Bean이 초기화 됨과 동시에 의존성을 확인할 수 있다.**
		4. bean lifecycle에 한번만 수행되므로 bean이 여러번 초기화되는 것을 방지한다.
	* `s3Client.putObject`
		1. 업로드하기 위해 사용되는 함수
		2. `(bucket,fileName, file.getInputStream(), null)` 
			* `bucket`에 넣도록 하고
			* `fileName`저장한 이름을 준다
	* `return s3Client.getUrl(bucket,fileName).toString();`
		1. **DB에 저장한 데이터는 해당 파일을 다운 받을 URL이다**
		2. **해당 URL을 파일자체 대신 DB에 넣는다.**

	`MaterialService`

	```java
	package com.predict.stock.material;
	
	import com.predict.stock.Dto.material.MaterialRequestDto;
	import lombok.AllArgsConstructor;
	import org.springframework.stereotype.Service;
	
	@Service
	@AllArgsConstructor
	public class MaterialService {
	    private final MaterialRepository materialRepository;
	
	    public void saveMaterial(MaterialRequestDto materialRequestDto){
	            materialRepository.save(materialRequestDto.toEntity());
	   }
	
	}
	```

	`MaterialRepository`

	```java
	package com.predict.stock.material;
	
	import com.predict.stock.material.Material;
	import org.springframework.data.jpa.repository.JpaRepository;
	
	public interface MaterialRepository extends JpaRepository<Material, Long> {
	
	    Material findByFilePath(String filePath);
	}
	```

	`fileupload.html`

	```html
	<!DOCTYPE html>
	<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml">
	<head>
	    <meta charset="UTF-8">
	    <title>S3 파일 업로드 테스트</title>
	</head>
	<body>
	<h1>파일 업로드</h1> <hr>
	
	<form th:action="@{/fileupload}" method="post" enctype="multipart/form-data">
	    제목 : <input type="text" name="title"> <br>
	    파일 : <input type="file" name="file"> <br>
	    <button>등록하기</button>
	</form>
	
	</body>
	</html>
	```

4. ### 테스트

	![image](https://user-images.githubusercontent.com/52272332/97032156-50d2b600-159c-11eb-9e17-94e037e0c8d2.png)

	![image](https://user-images.githubusercontent.com/52272332/97032196-65af4980-159c-11eb-93f9-ec0766571966.png)

	![image](https://user-images.githubusercontent.com/52272332/97032236-719b0b80-159c-11eb-8971-531ce0b3de8d.png)

	* **성공~**

5. ### 다음 할일

	1. **Board와 Material의 OneToMany 매핑관계를 완성하기**

	2. **ajax 통신에서 Board와 Material를 동시 저장하는 방법 생각해보기**

	3. **attachment까지 추가해서 완벽한 관계 성공시키기......**

		