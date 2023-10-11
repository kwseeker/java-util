# Lombok+MapStruct类型转换失败

由于MapStruct转换过程中需要用到对象的`getter/setter`（相对于反射性能更好），而对象的getter/setter方法是由Lombok编译时自动生成的，出现此问题估计是Lombok的注解处理器没有优先执行（Annotation Processor）。

TODO：有空深入源码看看。

解决方法：告诉 Maven 在编译时，执行指定路径下的注解处理器，lombok需要放到mapstruct前面。

```xml
<build>
    <pluginManagement>
        <plugins>
            <!-- maven-compiler-plugin 插件，解决 Lombok + MapStruct 组合 -->
            <!-- https://stackoverflow.com/questions/33483697/re-run-spring-boot-configuration-annotation-processor-to-update-generated-metada -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.version}</version>
                <configuration>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                            <version>${spring-boot.version}</version>
                        </path>
                        <!-- Annotation Processor顺序执行，lombok需要放到mapstruct前面 -->
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                            <version>${lombok.version}</version>
                        </path>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${mapstruct.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

