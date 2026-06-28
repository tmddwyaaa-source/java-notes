# 上传文件接口和oos租借

---

## 上传文件接口

### controller

```java
@RestController
public class FileUploadController {

    @PostMapping("/upload")//文件上传接口
    public Result<String> upload(MultipartFile file) throws IOException {
        String originalFilename = file.getOriginalFilename();//将文件先存在本地磁盘里
        String filename = UUID.randomUUID().toString()+originalFilename.substring(originalFilename.lastIndexOf("."));
        //通过UUID的方法获得随机数添加到图片截取的最后一个.后面东西(也就是文件后缀名)，来实现每次名称都不一样，保证上传文件不会别覆盖
        file.transferTo(new File("C:\\Users\\user\\Desktop\\files\\"+filename));//上传文件的路径名称
        return Result.success("url已上传");
        //String dome = UUID.randomUUID().toString()+originalFilename.substring()
    }

}
```

由于是上传文件，没用数据库内容，所以没有Service和Mapper

---

## 租借oos，使用阿里云免费试用oos

记下密钥，服务器区域等等信息

使用官方的调用手册

获得示例代码

修改关键参数如 密钥 oos项目名称等

基础test类dome结果如下
```java
package com.ch.itheima;

import com.aliyun.sdk.service.oss2.OSSClient;
import com.aliyun.sdk.service.oss2.credentials.CredentialsProvider;
import com.aliyun.sdk.service.oss2.credentials.StaticCredentialsProvider;
import com.aliyun.sdk.service.oss2.models.PutObjectRequest;
import com.aliyun.sdk.service.oss2.models.PutObjectResult;
import com.aliyun.sdk.service.oss2.transport.BinaryData;

import java.io.File;
import java.io.FileInputStream;

/**
 * OSS SDK v2 测试类：第一次尝试「上传本地文件」到阿里云 OSS。
 * <p>
 * 使用方式：填好下面【你来改】的配置 → 右键 Run main → 去 OSS 控制台看文件是否出现。
 * <p>
 * 注意：此类在 test 目录，仅用于学习验证，不是项目正式上传入口。
 */
public class Demo {

    public static void main(String[] args) {
        // ===================== 【你来改】OSS 连接配置 =====================

        // RAM 用户的 AccessKey ID（阿里云控制台 → AccessKey 管理）
        String accessKeyId = "LTAI5t9tNBMht81eksjdf5tUt";

        // RAM 用户的 AccessKey Secret
        String accessKeySecret = "FesfuenscCfOVkc8tE6ZJMmMmyJ6F3G";

        // Bucket 外网 Endpoint，形如 https://oss-cn-beijing.aliyuncs.com
        // 在 OSS 控制台 → Bucket 列表 → 点进你的 Bucket → 概览 → 外网 Endpoint
        String endpoint = "oss-cn-shanghai.aliyuncs.com";

        // Bucket 所在地域，只填地域 id，如 cn-beijing（不要带 https）
        String region = "cn-shanghai";

        // Bucket 名称（不是域名，只是桶名）
        String bucket = "big-event-dome";

        // 上传到 OSS 后的对象名（相当于桶里的文件路径，可带目录，如 images/test.png）
        String objectKey = "001.png";

        // 本地要上传的文件绝对路径（Windows 示例：C:\\Users\\user\\Desktop\\test.png）
        String localFilePath = "C:\\Users\\user\\Desktop\\files\\001.png";

        // ===================== 以下一般不用改 =====================

        // 1. 检查本地文件是否存在
        File localFile = new File(localFilePath);
        if (!localFile.exists()) {
            System.err.println("本地文件不存在，请检查 localFilePath：" + localFilePath);
            return;
        }

        // 2. 创建凭证提供者（测试类里用静态凭证；正式项目建议环境变量或配置文件）
        CredentialsProvider credentialsProvider =
                new StaticCredentialsProvider(accessKeyId, accessKeySecret);

        // 3. 构建 OSS 客户端（try-with-resources 用完自动关闭，不用手动 shutdown）
        try (OSSClient client = OSSClient.newBuilder()
                .credentialsProvider(credentialsProvider)
                .region(region)
                .endpoint(endpoint)
                .build()) {

            System.out.println("开始上传...");
            System.out.println("本地文件：" + localFile.getAbsolutePath());
            System.out.println("目标 Bucket：" + bucket);
            System.out.println("目标 ObjectKey：" + objectKey);

            // 4. 读取本地文件并上传（v2 使用 BinaryData.fromStream，需传入文件大小）
            try (FileInputStream inputStream = new FileInputStream(localFile)) {
                PutObjectResult result = client.putObject(
                        PutObjectRequest.newBuilder()
                                .bucket(bucket)
                                .key(objectKey)
                                .body(BinaryData.fromStream(inputStream, localFile.length()))
                                .build()
                );

                // 5. 打印结果
                System.out.println("上传成功！");
                System.out.printf("HTTP 状态码: %d%n", result.statusCode());
                System.out.printf("Request ID: %s%n", result.requestId());
                System.out.printf("ETag: %s%n", result.eTag());

                // 6. 拼接公网访问地址（Bucket 需开启公共读，或使用自定义域名；仅作参考）
                // endpoint 去掉 https:// 后形如 oss-cn-beijing.aliyuncs.com
                String host = endpoint.replace("https://", "").replace("http://", "");
                String fileUrl = "https://" + bucket + "." + host + "/" + objectKey;
                System.out.println("文件访问地址（若 Bucket 私有则浏览器打不开，属正常）：");
                System.out.println(fileUrl);
            }

        } catch (Exception e) {
            System.err.println("上传失败，请检查：AccessKey、Endpoint、Region、Bucket 名、RAM 权限");
            System.err.println("错误信息：");
            e.printStackTrace();
        }
    }
}

```


## 落实目标

将示例代码分装成工具类

落实到上传文件接口

### 工具类AliOssUtil

将固有变量封装成常量

将变量封装成方法参数由调用方提供

确定返回类型

```java
package com.ch.utils;

import com.aliyun.sdk.service.oss2.OSSClient;
import com.aliyun.sdk.service.oss2.credentials.CredentialsProvider;
import com.aliyun.sdk.service.oss2.credentials.StaticCredentialsProvider;
import com.aliyun.sdk.service.oss2.models.PutObjectRequest;
import com.aliyun.sdk.service.oss2.models.PutObjectResult;
import com.aliyun.sdk.service.oss2.transport.BinaryData;

import java.io.InputStream;

public class AliOssUtil {
    // RAM 用户的 AccessKey ID（阿里云控制台 → AccessKey 管理）
    private static final String ACCESS_KEY_ID = "LTAI5t9tNZbBMht281eT5tUt";

    // RAM 用户的 AccessKey Secret
    private static final String ACCESS_KEY_SECRET = "Feaf0CfOVkc8tEe4w6ZJMmMmyJ6F3G";

    // Bucket 外网 Endpoint，形如 https://oss-cn-beijing.aliyuncs.com
    private static final String ENDPOINT = "oss-cn-beijing.aliyuncs.com";

    // Bucket 所在地域，只填地域 id，如 cn-beijing（不要带 https）
    private static final String REGION = "cn-beijing";

    // Bucket 名称（不是域名，只是桶名）
    private static final String BUCKET_NAME = "big-event-dome";

    /**
     * 上传文件到 OSS
     *
     * @param objectKey   上传到 OSS 后的对象名（如 uuid.png）
     * @param inputStream 文件输入流（Controller 中传 file.getInputStream()）
     * @return 成功返回公网访问地址，失败返回 null
     */
    public static String uploadFile(String objectKey, InputStream inputStream) {
        if (inputStream == null) {
            return null;
        }

        CredentialsProvider credentialsProvider =
                new StaticCredentialsProvider(ACCESS_KEY_ID, ACCESS_KEY_SECRET);

        try (OSSClient client = OSSClient.newBuilder()
                .credentialsProvider(credentialsProvider)
                .region(REGION)
                .endpoint(ENDPOINT)
                .build()) {

            PutObjectResult result = client.putObject(
                    PutObjectRequest.newBuilder()
                            .bucket(BUCKET_NAME)
                            .key(objectKey)
                            .body(BinaryData.fromStream(inputStream))
                            .build()
            );

            System.out.println("上传成功！");
            System.out.printf("HTTP 状态码: %d%n", result.statusCode());
            System.out.printf("Request ID: %s%n", result.requestId());
            System.out.printf("ETag: %s%n", result.eTag());

            String host = ENDPOINT.replace("https://", "").replace("http://", "");
            return "https://" + BUCKET_NAME + "." + host + "/" + objectKey;

        } catch (Exception e) {
            System.err.println("上传失败，请检查：AccessKey、Endpoint、Region、Bucket 名、RAM 权限");
            e.printStackTrace();
            return null;
        }
    }
}

```

### Controller

```java
@RestController
public class FileUploadController {

    @PostMapping("/upload")//文件上传接口
    public Result<String> upload(MultipartFile file) throws IOException {
        String originalFilename = file.getOriginalFilename();//将文件先存在本地磁盘里
        String filename = UUID.randomUUID().toString()+originalFilename.substring(originalFilename.lastIndexOf("."));
        //通过UUID的方法获得随机数添加到图片截取的最后一个.后面东西(也就是文件后缀名)，来实现每次名称都不一样，保证上传文件不会别覆盖
        //file.transferTo(new File("C:\\Users\\user\\Desktop\\files\\"+filename));//上传文件的路径名称

        String url = AliOssUtil.uploadFile(filename,file.getInputStream());//使用封装的上传文件示例代码
        return Result.success(url);
        //String dome = UUID.randomUUID().toString()+originalFilename.substring()
    }

}
```