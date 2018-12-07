# 常见问题FAQ
#### Q1. 在IDE中运行Demo时，出现乱码情况怎么办？
A：Demo的构建方式为Gradle，文件编码是UTF-8。如果开发环境为Windows，那么需要设置IDE的参数。以Intellij IDEA为例，需要打开File->Other Settings->Default Settings->Gradle，在Gradle Vm Options中设置-Dfile.encoding=utf-8