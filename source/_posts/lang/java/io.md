---
title: Exception & IO
date: 2023-09-09 00:00:00
tags: java
categories: lang
---

---

# Java 异常

```java
public class TestException {
	public static void main(String[] args) {
        System.out.println("start");
        try {
            int num = 1 / 0;  // -> JVM 自动创建一个异常类, 并停止 try 中的代码, 把异常对象提交给 JRE
        } catch (Exception e) {  // 捕获异常
            e.printStackTrace();  // 做出处理
        } finally {
            System.out.println("必须执行");
        }
        System.out.println("end");
    }
}
```

## 异常分类

- Throwable: 所有异常的根类

  - Error: 不用管, 处理不了

  - Exception

    - RuntimeException (UncheckedException): 运行时异常 (编译器查不出来的异常)

      - NullPointerException
      - ClassCastException: 强制转化时出现的异常
      - ArrayIndexOutOfBoundsException
      - NumberFormatException: Integer.parseInt("123acb");

    - CheckedException: 编译器就会查出来的异常 (通不过编译), 必须用 try catch 包围

      ```java
      File f = new File("D:/tmp.txt");
      try {
          f.createNewFile();  // 这个方法中有 throws IOException, 因此使用时, 必须用 try catch 包围
      } catch (IOException e) {
          
      }
      
      ```

## 异常的处理

**使用 try ... catch**

**声明式处理**

只抛出异常, 不处理

```java
pulic class Test {
	public void f() throws Exception {
		...
	}
}
```

谁调用, 谁处理 相当于抛给别人处理

## 自定义异常

extends Exception



# IO 流 (IO Stream)

Input (读) / Output (写)

## 流 (Stream)

一连串连续动态的数据集合

输入流 / 输出流

## 四大 IO 抽象类

字节输入输出流

- InputStream
- OutputStream

字符输入输出流

- Reader
- Writer

**InputStream 字节流**

抽象类 派生类不同的子类 输入数据单位为 byte (8 bits)

方法:

- int read() 0 - 255 之间 -1 表示未读出
- void close()

**OutputStream 字节流**

抽象类 派生类不同的子类 输出数据单位为 byte (8 bits)

方法

- void write()
- void close()

**Reader 字符流**

抽象类 派生类不同的子类 输入数据单位为字符 (Unicode)

- int read() 0-65535 之间 Unicode 值 -1 未读出
- close()

**Writer 字符流**

抽象类 派生类不同的子类 输出数据单位为字符

方法

- void write()
- void close()

## 其它分类

FileInputStream / FileOutputStream 节点流

ByteArrayInputStream / ByteArrayOutputStream 节点流

BufferedReader / BufferedWirter

**示例**

```java
class Draft {
    public void draft() {
        FileInputStream fis = null;  // 要在 try 的外面声明 FileInputStream 因为 finally 中要关闭, 不能写在 try 内部
        try {
            fis = new FileInputStream("D:\\workspace\\java-learning\\src\\main\\resources\\draft.txt");  // 有 CheckedException
            StringBuilder sb = new StringBuilder();
            int ch;
            while ((ch = fis.read()) != -1) {
                sb.append((char) ch);
            }
            System.out.println(sb);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

## File 类

createNewFile()

delete()

exists()

getAbsolutePath()

getPath()

getName()

isFile()

length()

isHidden()

isDirectory()

mkdir()

getParentFile()

list()

listFiles()

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        File file = new File(PATH + "/draft_1.txt");
        try {
            System.out.println(file.createNewFile());
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        File file = new File(PATH + "/a");
        System.out.println(file.mkdir());
        File file1 = new File(PATH + "/b/c");  // 多级目录
        System.out.println(file1.mkdirs());
    }
}
```

## 文件字节流

FileInputStream / FileOutputStream (图像, 视频, 文本)

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream(PATH + "/pic.jpg");
            fos = new FileOutputStream(PATH + "/pic_copy.jpg");
            int t = 0;
            while ( (t = fis.read()) != -1 ) fos.write(t);  // 写
            fos.flush();  // 将数据从内存搞到磁盘中
        } catch (Exception e) {e.printStackTrace();}
        finally {  // 输入输出流都要关闭
            try {
                if (fis != null) fis.close();
                if (fos != null) fos.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

**缓冲区提高读写效率 用内存空间换效率**

通过指定一个固定长度 ($2^n$) 的字节数组作为缓冲区, 提高 io 效率, 相当于一次拿一个buffer

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream(PATH + "/pic.jpg");
            fos = new FileOutputStream(PATH + "/pic_copy.jpg");
            byte[] buffer = new byte[1024];  // 缓冲区
            int t = 0;
            // read() 返回值: the total number of bytes read into the buffer, or -1 if there is no more data because the end of the file has been reached.
            while ( (t = fis.read(buffer)) != -1 ) fos.write(buffer, 0, t);  // 写
            fos.flush();  // 将数据从内存搞到磁盘中
        } catch (Exception e) {e.printStackTrace();}
        finally {  // 输入输出流都要关闭
            try {
                if (fis != null) fis.close();
                if (fos != null) fos.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

通过输入流的 available() 返回当前文件的预估长度, 文件过大时, 对内存占用较大

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        FileInputStream fis = null;
        FileOutputStream fos = null;
        try {
            fis = new FileInputStream(PATH + "/pic.jpg");
            fos = new FileOutputStream(PATH + "/pic_copy.jpg");
            byte[] buffer = new byte[fis.available()];  // 缓冲区 长度被 available() 方法预估 相当于一次全拿
            fis.read(buffer);
            fos.write(buffer);
            fos.flush();  // 将数据从内存搞到磁盘中
        } catch (Exception e) {e.printStackTrace();}
        finally {  // 输入输出流都要关闭
            try {
                if (fis != null) fis.close();
                if (fos != null) fos.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

**缓冲流提高效率**

缓冲流本身没有读写功能, 它们在别的流上加上缓冲以提高效率 (把别的流包装起来)

BufferedInputStream / BufferedOutputStream

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        FileInputStream fis = null;
        BufferedInputStream bis = null;

        FileOutputStream fos = null;
        BufferedOutputStream bos = null;
        try {
            fis = new FileInputStream(PATH + "/pic.jpg");  // 先
            bis = new BufferedInputStream(fis);  // 流的包装  // 后

            fos = new FileOutputStream(PATH + "/pic_copy.jpg");  // 先
            bos = new BufferedOutputStream(fos);  // 后
			
            // 缓冲流中的 buffer 数组默认长度 8192 = 2^13
            int t;
            while ((t = bis.read()) != -1) bos.write(t);

            bos.flush();  // 将数据从内存搞到磁盘中
        } catch (Exception e) {e.printStackTrace();}
        finally {  // 输入输出流都要关闭
            try {
                // 当同时有节点流和处理流, 注意关闭顺序: 后开的, 先关闭
                if (bis != null) bis.close();
                if (fis != null) fis.close();

                if (bos != null) bos.close();
                if (fos != null) fos.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

## 文件拷贝工具类

```java
class FileUtils {
    public static void copyFile(String src, String des) {
        FileInputStream fis = null;
        BufferedInputStream bis = null;

        FileOutputStream fos = null;
        BufferedOutputStream bos = null;

        try {
            fis = new FileInputStream(src);
            bis = new BufferedInputStream(fis);

            fos = new FileOutputStream(des);
            bos = new BufferedOutputStream(fos);

            int temp = 0;
            while ((temp = bis.read()) != -1) bos.write(temp);
            bos.flush();
        } catch (Exception e) {e.printStackTrace();}
        finally {
            try {
                if (bis != null) bis.close();
                if (fis != null) fis.close();

                if (bos != null) bos.close();
                if (fos != null) fos.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

## 文件字符流

字节流针对字节操作, 字符流针对字符操作, 专门针对字符文件

```java
class Draft {
    private final String PATH = "D:/workspace/java-learning/src/main/resources";

    public void draft() {
        FileReader fr = null;
        FileWriter fw = null;
        try {
            fr = new FileReader(PATH + "/draft.txt");
            fw = new FileWriter(PATH + "/draft_copy.txt", true);  // 允许追加
            int temp;
            while ((temp = fr.read()) != -1) fw.write(temp);  // 一次读一个字符  返回 int Unicode 值
            fw.flush();
        } catch (Exception e) {e.printStackTrace();}
        finally {
            try {
                if (fr != null) fr.close();
                if (fw != null) fw.close();
            } catch (Exception e) {e.printStackTrace();}
        }
    }
}
```

