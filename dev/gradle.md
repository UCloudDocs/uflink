

# 基于gradle开发指南

如果基于gradle进行应用开发，需要在build.gradle文件中加入如下配置：

    buildscript {
        repositories {
            jcenter() // this applies only to the Gradle 'Shadow' plugin
        }
        dependencies {
            classpath 'com.github.jengelman.gradle.plugins:shadow:2.0.4'
        }
    }
    
    plugins {
        id 'java'
        id 'application'
        // shadow plugin to produce fat JARs
        id 'com.github.johnrengelman.shadow' version '2.0.4'
    }
    
    
    // artifact properties
    group = 'org.myorg.quickstart'
    version = '0.1-SNAPSHOT'
    mainClassName = 'org.myorg.quickstart.StreamingJob'
    description = """Flink Quickstart Job"""
    
    ext {
        javaVersion = '1.8'
        flinkVersion = '1.6.4'
        scalaBinaryVersion = '2.11'
        slf4jVersion = '1.7.7'
        log4jVersion = '1.2.17'
    }
    
    
    sourceCompatibility = javaVersion
    targetCompatibility = javaVersion
    tasks.withType(JavaCompile) {
        options.encoding = 'UTF-8'
    }
    
    applicationDefaultJvmArgs = ["-Dlog4j.configuration=log4j.properties"]
    
    task wrapper(type: Wrapper) {
        gradleVersion = '3.1'
    }
    
    // declare where to find the dependencies of your project
    repositories {
        mavenCentral()
        maven { url "https://repository.apache.org/content/repositories/snapshots/" }
    }
    
    // NOTE: We cannot use "compileOnly" or "shadow" configurations since then we could not run code
    // in the IDE or with "gradle run". We also cannot exclude transitive dependencies from the
    // shadowJar yet (see https://github.com/johnrengelman/shadow/issues/159).
    // -> Explicitly define the // libraries we want to be included in the "flinkShadowJar" configuration!
    configurations {
        flinkShadowJar // dependencies which go into the shadowJar
    
        // always exclude these (also from transitive dependencies) since they are provided by Flink
        flinkShadowJar.exclude group: 'org.apache.flink', module: 'force-shading'
        flinkShadowJar.exclude group: 'com.google.code.findbugs', module: 'jsr305'
        flinkShadowJar.exclude group: 'org.slf4j'
        flinkShadowJar.exclude group: 'log4j'
    }
    
    // declare the dependencies for your production and test code
    dependencies {
        // --------------------------------------------------------------
        // Compile-time dependencies that should NOT be part of the
        // shadow jar and are provided in the lib folder of Flink
        // --------------------------------------------------------------
        compile "org.apache.flink:flink-java:${flinkVersion}"
        compile "org.apache.flink:flink-streaming-java_${scalaBinaryVersion}:${flinkVersion}"
    
        // --------------------------------------------------------------
        // Dependencies that should be part of the shadow jar, e.g.
        // connectors. These must be in the flinkShadowJar configuration!
        // --------------------------------------------------------------
        //flinkShadowJar "org.apache.flink:flink-connector-kafka-0.11_${scalaBinaryVersion}:${flinkVersion}"
    
        compile "log4j:log4j:${log4jVersion}"
        compile "org.slf4j:slf4j-log4j12:${slf4jVersion}"
    
        // Add test dependencies here.
        // testCompile "junit:junit:4.12"
    }
    
    // make compileOnly dependencies available for tests:
    sourceSets {
        main.compileClasspath += configurations.flinkShadowJar
        main.runtimeClasspath += configurations.flinkShadowJar
    
        test.compileClasspath += configurations.flinkShadowJar
        test.runtimeClasspath += configurations.flinkShadowJar
    
        javadoc.classpath += configurations.flinkShadowJar
    }
    
    run.classpath = sourceSets.main.runtimeClasspath
    
    jar {
        manifest {
            attributes 'Built-By': System.getProperty('user.name'),
                    'Build-Jdk': System.getProperty('java.version')
        }
    }
    
    shadowJar {
        configurations = [project.configurations.flinkShadowJar]
    }

> 注解：注意修改mainClassName的值，确保其符合您的应用。应用开发完成后，可以直接直接运行main方法，在本地进行基本的测试。如果已经完成开发测试，则直接运行shadowJar即可。
