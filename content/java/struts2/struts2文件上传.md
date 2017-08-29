# Struts2接收图片
Action接收file对象需要在自定义Action类定义三个属性，并且需要getter和setter方法。

1. File对象类型的属性，代表所上传的文件。
2. File名称是：FileName结尾的String类型的属性。
3. File类型：ContentType结尾的String类型的属性。

e.g:

```java
public class UserAction extends ActionSupport {
    private File upload;//上传文件对象
    private String uploadFileName;//上传文件的名称
    private String uploadContentType;//上传文件的类型

    public File getUpload() {
        return upload;
    }

    public void setUpload(File upload) {
        this.upload = upload;
    }

    public String getUploadFileName() {
        return uploadFileName;
    }

    public void setUploadFileName(String uploadFileName) {
        this.uploadFileName = uploadFileName;
    }

    public String getUploadContentType() {
        return uploadContentType;
    }

    public void setUploadContentType(String uploadContentType) {
        this.uploadContentType = uploadContentType;
    }
}
```