---
layout: post
title:  "Base64 Encode / Decode via AES"
date:   2015-12-17
banner_image: 
tags: [java, security]
comments: true
---

AES 128비트 암호화 알고리즘을 통한 Base64 인코딩/디코딩

<!--more-->

Java에서는 JCE (Java Cryptography Extension) 를 통해 암호화에 필요한 클래스를 제공하고 있습니다.

AES 알고리즘은 128, 192, 256bit 를 지원하지만, 기본으로 설치되는 JDK 를 통해 AES 알고리즘을 사용하는 경우

정책상 128bit 밖에 사용 할 수 없습니다. 
(기본 제공하는 각 암호화 알고리즘의 Limit 키사이즈는 아래 링크 참고)

128bit 를 사용하지 않는경우 아래와 같은 Exception 이 발생합니다.

java.security.InvalidKeyException: Illegal key size or default parameters

192, 256 bit 방식을 사용하기 위해선 별도로 JCE Unlimited Strength Jurisdiction Policy Files 을

오라클 홈페이지에서 다운받아 사용해야 합니다.

JCE에 포함된 javax.crypto 패키지는 암호화에 필요한 클래스를 제공하고 있습니다.

해당 패키지에서 제공하는 클래스를 통해 데이터를 암호화/복호화 할 수 있습니다.

또한, 부가적으로 아래 코드에서 javax.xml.bind 패키지의 DatatypeConverter 클래스를 사용한 이유는

JAXB 에서 XML & Java 간 데이터를 표현할때 필요한 데이터를 변환해주는 클래스를 사용하여

Base64 로 인코딩/디코딩 해주는 메서드를 사용 하였습니다.

{% highlight java %}
//keySize에 따른 AES 암호화 키 생성
public static String generateBase64Key(int keySize) throws Exception {     
     
    KeyGenerator generator = KeyGenerator.getInstance("AES");
    generator.init(keySize);
    Key secretKey = generator.generateKey();
     
    return DatatypeConverter.printBase64Binary(secretKey.getEncoded());
}
 
//문자열을 key를 통해 암호화 하고 base64 로 인코딩
public static String encryptAES128(byte[] key, String message) throws Exception {
     
    SecretKeySpec skeySpec = new SecretKeySpec(key, "AES");
    Cipher cipher = Cipher.getInstance("AES");
    cipher.init(Cipher.ENCRYPT_MODE, skeySpec);
    byte[] encrypted = cipher.doFinal(message.getBytes()); //encrypt
     
    String encodeString = DatatypeConverter.printBase64Binary(encrypted); //Converts an array of bytes into a string.
 
    return encodeString;
}
 
//base64로 인코딩된 key 를 통해 문자열 base64 인코딩
public static String encryptAES128(String base64Key, String message) throws Exception {
    return encryptAES128(base64DecodeKey(base64Key), message);
}
 
//key 를 통해 문자열 base64 디코딩
public static String decryptAES128(byte[] keys, String encrypted)
        throws NoSuchAlgorithmException, NoSuchPaddingException,
        IllegalBlockSizeException, BadPaddingException, InvalidKeyException {
     
    SecretKeySpec keySpec = new SecretKeySpec(keys, "AES");
 
    Cipher cipher = Cipher.getInstance("AES");
    cipher.init(Cipher.DECRYPT_MODE, keySpec);
 
    byte[] decodeBytes = DatatypeConverter.parseBase64Binary(encrypted); //Converts the string argument into an array of bytes.
    byte[] decryptBytes = cipher.doFinal(decodeBytes); //decrypt
     
    return new String(decryptBytes);   
}
 
//base64로 인코딩된 key 를 통해 문자열 base64 디코딩
public static String decryptAES128(String base64Key, String encrypted)
        throws NoSuchAlgorithmException, NoSuchPaddingException,
        IllegalBlockSizeException, BadPaddingException, InvalidKeyException {
             
    return decryptAES128(base64DecodeKey(base64Key), encrypted);   
}
 
//base64로 인코딩된 key를 byte 배열로 디코딩
public static byte[] base64DecodeKey(String base64Key) {
    return DatatypeConverter.parseBase64Binary(base64Key);     
}
 
//byte배열의 키를 base64로 인코딩
public static String base64EncodeKey(byte[] key) {
    return DatatypeConverter.printBase64Binary(key);
}
{% endhighlight %}

{% include center_image_caption.html imageurl="/assets/150325/aes.png" caption="AES 를 통한 문자열 암호화/복호화" %}

마지막으로, 128bit 보다 강력한 256bit 를 사용하시길 권장합니다.

[SampleMotions.zip](/assets/150325/SecuritySample.zip) 