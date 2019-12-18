---
layout: article
title: Tìm hiểu về Java Native Interface
tags: [jni, java]
---

## 1. Tìm hiểu JNI
### 1.1. Giới thiệu
**Java Native Interface** (JNI) là một framework cho phép một chương trình Java chạy trên JVM có thể gọi các hàm/chương trình viết bằng ngôn ngữ native (ngôn ngữ dành cho hệ thống đó như C/C++, Assembly…) hoặc được gọi từ các chương trình native đó.

Chúng ta biết môi trường Java là một môi trường lập trình bao gồm 2 thứ: máy ảo Java (JVM – Java Virtual Machine) và các thư viện API. Khi dịch thì Java sẽ được dịch sang mã máy có thể chạy được trên JVM.

Một khái niệm khác là môi trường máy chủ, tức là hệ điều hành được cài trên máy tính đó, môi trường này cũng có các API riêng, tập lệnh CPU riêng, ngôn ngữ lập trình riêng (thường là C/C++, Assembly).

Môi trường Java được cài nằm trên môi trường máy chủ, ứng với mỗi hệ điều hành (môi trường máy chủ riêng) mà có một môi trường Java riêng, chẳng hạn trên Windows và Solaris thì môi trường Java này là Java Runtime Environment (JRE).
### 1.2. Kiểu dữ liệu
Các kiểu dữ liệu tham số ở phía native cũng tương tự với kiểu dữ liệu của ngôn ngữ đó.

Đối với các kiểu dữ liệu dạng primitive như int, float, char…v.v thì khi chuyển sang JNI cũng gần như là tương tự nhau, chỉ cần thêm kí tự 'j' vào trước tên kiểu dữ liệu, như int trong Java thì chuyển sang jint trong JNI, float thành jfloat.

![JNI data type](/assets/images/jni-data-type.png)

### 1.3. Callback
### 1.4. Object
### 1.5. Exeption
### 1.6. Reflection
## 2. Note
### 2.1 Convert const char to jstring
```java
jstring makeAString(JNIEnv *e, char *str) {
    jbyteArray bytes = 0;
    int len = strlen(str);
    bytes = e->NewByteArray(len);

    jclass string_cls = e->FindClass("java/lang/String");
    jmethodID string_constructor = e->GetMethodID(string_cls, "<init>", "([B)V");

    if(bytes != NULL) {
        e->SetByteArrayRegion(bytes, 0, len, (jbyte *)str);
        jstring result = (jstring)e->NewObject(string_cls, string_constructor, bytes);
        return result;
    }
    return NULL;
}
```

```java
jstring stdj2(JNIEnv *AEnv, const char *strDes) {
    if (strcmp(strDes, "") == 0) {
        return NULL;
    }
    jstring output = AEnv->NewStringUTF(strDes);
    return output;
}
```
```java
void j2std(JNIEnv *AEnv, jstring strSrc, 
std::string &strDes) {
    if (!strSrc) {
        strDes.clear();
        return;
    }
    strDes.clear();
    const char *s = AEnv->GetStringUTFChars(strSrc, NULL);
    strDes = s;
    AEnv->ReleaseStringUTFChars(strSrc, s);
}
```
### 2.2. Convert byteArray to unsigned char*
```java
unsigned char* as_unsigned_char_array(JNIEnv* env, jbyteArray array) {
    int len = env->GetArrayLength (array);
    unsigned char* buf = new unsigned char[len+1];
    memset(buf,'\0',len+1);
    env->GetByteArrayRegion (array, 0, len, reinterpret_cast<jbyte*>(buf));
    return buf;
}
```
Và convert unsigned char* sang jbyteArray
```java
jbyteArray as_byte_array(JNIEnv* env, unsigned char* buf, int len) {
    jbyteArray array = env->NewByteArray (len);
    env->SetByteArrayRegion (array, 0, len, reinterpret_cast<jbyte*>(buf));
    return array;
}
```
### 2.3. 
