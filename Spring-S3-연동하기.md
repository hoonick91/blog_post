---
title: Spring S3 연동하기
date: 2018-12-07 16:49:58
tags:
categories: spring framework
---



## pom.xml 

```xml
<!-- https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk -->
<dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-java-sdk</artifactId>
    <version>1.9.2</version>
</dependency>
```



## FileUploadController.java

```java
@Controller
public class NewFileUploadController {

    private static final Logger logger = LoggerFactory.getLogger(NewFileUploadController.class);

    S3Util s3 = new S3Util();
    String bucketName = "bucketName";

    @ResponseBody
    @RequestMapping(value = "/upload", method = RequestMethod.POST, produces = "text/plain;charset=UTF-8")
    public String uploadImage(MultipartFile file) throws Exception {
        
        String uploadpath = "assetList";

        ResponseEntity<String> img_path = new ResponseEntity<>(
                UploadFileUtils.uploadFile(uploadpath, file.getOriginalFilename(), file.getBytes()),
                HttpStatus.CREATED);

        String certificatePath = (String) img_path.getBody();
        return certificatePath;

    }

}
```



## S3Utill .java

```java
public class S3Util {
    private String accessKey = "";
    private String secretKey = "";
    private AmazonS3 conn;

    // connection 맺는 생성자
    public S3Util() {
        AWSCredentials credentials = new BasicAWSCredentials(accessKey, secretKey);
        ClientConfiguration clientConfig = new ClientConfiguration();
        clientConfig.setProtocol(Protocol.HTTP);
        this.conn = new AmazonS3Client(credentials, clientConfig);
        conn.setEndpoint("s3.ap-northeast-2.amazonaws.com");
    }

    // fileUpload 메서드
    public void fileUpload(String bucketName, String fileName, byte[] fileData) throws FileNotFoundException {
        String filePath = (fileName).replace(File.separatorChar, '/');
        ObjectMetadata metadata = new ObjectMetadata();

        metadata.setContentLength(fileData.length);
        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(fileData);

        conn.putObject(bucketName, filePath, byteArrayInputStream, metadata);
    }

}

```



## UploadFileUtils.java

```java
public class UploadFileUtils {
    private static final Logger logger = LoggerFactory.getLogger(UploadFileUtils.class);

    public static String uploadFile(String uploadPath, String savedName, String originalName, byte[] byteData) throws Exception {

        S3Util s3 = new S3Util();
        String bucketName = "bucketName";

        String uploadedFileName = null;
        uploadedFileName = savedName.replace(File.separatorChar, '/');

        s3.fileUpload(bucketName, uploadPath+"/"+uploadedFileName, byteData);

        logger.info(uploadedFileName);

        return uploadedFileName;
    }
}

```



## FileUploadController.java

```java
@Controller
public class NewFileUploadController {

    S3Util s3 = new S3Util();
    String bucketName = "hybridbucket";

    @ResponseBody
    @RequestMapping(value = "/fileUpload", method = RequestMethod.POST)
    public String uploadAjaxCertificate(MultipartFile file) throws Exception {
        
        String uploadpath = "user_image";
        ResponseEntity<String> img_path = new ResponseEntity<>(
                UploadFileUtils.uploadFile(uploadpath, file.getOriginalFilename(), file.getBytes()),
                HttpStatus.CREATED);

        String certificatePath = (String) img_path.getBody();
        logger.info(certificatePath);

        return certificatePath;
    }

}
```





## 출처

[https://shj7242.github.io/2017/12/28/Spring34/](https://shj7242.github.io/2017/12/28/Spring34/)

