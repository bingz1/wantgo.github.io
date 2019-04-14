---
title: AES和DES的比较
tags: 加密,解密
---
1.最近在调研APP端和服务端加密功能,需要用到对称加密算法,常用的对称加密算法有DES,3DES,AES
  AES是美国国家标准技术研究所NIST旨在取代DES 的新一代的加密标准
<style>
.article-entry table {
    width: 50%;
}
</style>

| 名称   | 秘钥长度       | 运算速度|安全性  |资源消耗  |
| ----- |:-------------:| ------:|-----:|-----:|
| DES   | 56位           | 较快   |低    |中     |
| 3DES  | 112位或168位    | 慢    |中    |高     |
| AES   | 128,192,156位  |  快    |高   |低      |
 所以 推荐使用AES加密算法

2.DES代码段

``` java
public class DES {

    private static Logger LOGGER = LoggerFactory.getLogger(DES.class);

    /**
     * 加密
     * @param datasource String
     * @param password   String
     * @return byte[]
     */
    public static String encrypt(String datasource, String password) {
        byte[] result = datasource.getBytes();
        SecureRandom random = new SecureRandom();
        try {
            DESKeySpec desKey = new DESKeySpec(password.getBytes());
            //创建一个密匙工厂，然后用它把DESKeySpec转换成
            SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
            SecretKey securekey = keyFactory.generateSecret(desKey);
            //Cipher对象实际完成加密操作
            Cipher cipher = Cipher.getInstance("DES");
            //用密匙初始化Cipher对象
            cipher.init(Cipher.ENCRYPT_MODE, securekey, random);
            //现在，获取数据并加密
            //正式执行加密操作
            return new BASE64Encoder().encode(cipher.doFinal(result));

        } catch (Throwable e) {
            if (LOGGER.isErrorEnabled()) {
                LOGGER.error("DES加密异常:{}", datasource, e);
            }
        }
        return null;
    }

    /**
     * 解密
     *
     * @param datasource String
     * @param password   String
     * @return byte[]
     */
    public static String decrypt(String datasource, String password) {
        // DES算法要求有一个可信任的随机数源
        SecureRandom random = new SecureRandom();
        try {
            // 创建一个DESKeySpec对象
            DESKeySpec desKey = new DESKeySpec(password.getBytes());
            // 创建一个密匙工厂
            SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
            // 将DESKeySpec对象转换成SecretKey对象
            SecretKey securekey = keyFactory.generateSecret(desKey);
            // Cipher对象实际完成解密操作
            Cipher cipher = Cipher.getInstance("DES");
            // 用密匙初始化Cipher对象
            cipher.init(Cipher.DECRYPT_MODE, securekey, random);
            // 真正开始解密操作
            return new String(cipher.doFinal(new BASE64Decoder().decodeBuffer(datasource)));
        } catch (Exception e) {
            if (LOGGER.isErrorEnabled()) {
                LOGGER.error("DES解密异常:{}", datasource, e);
            }
        }
        return null;
    }
}
```

AES 代码段
```java
public class AES {

    private static Logger LOGGER = LoggerFactory.getLogger(DES.class);

    /**
     * 加密
     * @param datasource 需要加密的内容
     * @param password  加密密码
     * @return
     */
    public static String encrypt(String datasource, String password) {

        try {
            KeyGenerator kgen = KeyGenerator.getInstance("AES");
            SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG" );
            secureRandom.setSeed(password.getBytes());
            kgen.init(128, secureRandom);
            SecretKey secretKey = kgen.generateKey();
            byte[] enCodeFormat = secretKey.getEncoded();
            SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
            Cipher cipher = Cipher.getInstance("AES");// 创建密码器
            byte[] byteContent = datasource.getBytes("utf-8");
            cipher.init(Cipher.ENCRYPT_MODE, key);// 初始化
            byte[] result = cipher.doFinal(byteContent);
            return new BASE64Encoder().encode(result); // 加密
        } catch (Exception e) {
            if (LOGGER.isErrorEnabled()) {
                LOGGER.error("AES加密异常:{}", datasource, e);
            }
        }
        return null;
    }

    /**解密
     * @param datasource  待解密内容
     * @param password 解密密钥
     * @return
     */
    public static String decrypt(String datasource, String password) {
        try {
            KeyGenerator kgen = KeyGenerator.getInstance("AES");
            SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG" );
            secureRandom.setSeed(password.getBytes());
            kgen.init(128, secureRandom);
            SecretKey secretKey = kgen.generateKey();
            byte[] enCodeFormat = secretKey.getEncoded();
            SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
            Cipher cipher = Cipher.getInstance("AES");// 创建密码器
            cipher.init(Cipher.DECRYPT_MODE, key);// 初始化
            byte[] result = cipher.doFinal(new BASE64Decoder().decodeBuffer(datasource));
            return new String(result); // 加密
        } catch (Exception e) {
            if (LOGGER.isErrorEnabled()) {
                LOGGER.error("AES解密异常:{}", datasource, e);
            }
        }
        return null;
    }
}
```
3. 速度比较
测试代码
```
public class AesTest {

    //待加密内容
    String str = "中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试中文测试";
    //密码，长度要是8的倍数
    String password = "9588028820109132570743325311898426347857298773549468758875018579537757772163084478873699447306034466200616411960574122434059469100235892702736860872901247123456";

    @Test
    public void test() throws Exception {

        Long begin = System.currentTimeMillis();
        for (int i=0;i<100000;i++){
            DES.decrypt(DES.encrypt(str,password),password);
        }
        Long end = System.currentTimeMillis();
        System.out.println("DES时间:"+(end-begin));

        Long beginAES = System.currentTimeMillis();
        for (int i=0;i<100000;i++){
            AES.decrypt(AES.encrypt(str,password),password);
        }
        Long endAES = System.currentTimeMillis();
        System.out.println("AES时间:"+(endAES-beginAES));

    }
}
```
测试结果
```
DES时间:41969
AES时间:24278
```

AES速度比DES速度更快一点
