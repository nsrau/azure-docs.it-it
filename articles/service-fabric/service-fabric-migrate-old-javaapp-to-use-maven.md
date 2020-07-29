---
title: Eseguire la migrazione da Java SDK a Maven
description: Aggiornare le applicazioni Java meno recenti che usano Service Fabric Java SDK per recuperare le dipendenze Java di Service Fabric da Maven. Al termine di questa configurazione, sarà possibile eseguire la compilazione delle applicazioni Java meno recenti.
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: f65194cde25deac8c03d6ddc3a7c0b928aaca171
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327311"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Aggiornare un'applicazione Java di Service Fabric precedente per recuperare le librerie Java da Maven
Service Fabric i file binari Java sono stati spostati da Service Fabric SDK Java all'hosting di Maven. È possibile usare **mavencentral** per recuperare le dipendenze Service Fabric Java più recenti. Questa guida consentirà di aggiornare le applicazioni Java esistenti create per il Service Fabric Java SDK usando il modello di attende o Eclipse per essere compatibile con la compilazione basata su Maven.

## <a name="prerequisites"></a>Prerequisiti

1. Per prima cosa, disinstallare Java SDK esistente.

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. Installare l'interfaccia della riga di comando di Service Fabric più recente seguendo la procedura descritta [qui](service-fabric-cli.md).

3. Per compilare e usare le applicazioni Java di Service Fabric, assicurarsi che JDK 1,8 e Gradle siano installati. Se non sono ancora installati, è possibile eseguire questi comandi per installare JDK 1.8 (openjdk-8-jdk) e Gradle:

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. Aggiornare gli script di installazione/disinstallazione dell'applicazione per usare la nuova interfaccia della riga di comando di Service Fabric seguendo la procedura descritta [qui](service-fabric-application-lifecycle-sfctl.md). Per informazioni di riferimento, vedere gli [esempi](https://github.com/Azure-Samples/service-fabric-java-getting-started) introduttivi.

>[!TIP]
> Dopo la disinstallazione di Service Fabric Java SDK, Yeoman non funzionerà. Seguire i prerequisiti descritti [qui](service-fabric-create-your-first-linux-application-with-java.md) per consentire il funzionamento del generatore di modelli Yeoman di Service Fabric per Java.

## <a name="service-fabric-java-libraries-on-maven"></a>Librerie Java di Service Fabric in Maven

Le librerie Java di Service Fabric sono ospitate in Maven. È possibile aggiungere le dipendenze nel file ``pom.xml`` o ``build.gradle`` dei progetti per usare le librerie Java di Service Fabric da **mavenCentral**.

### <a name="actors"></a>Actor

Supporto di Service Fabric Reliable Actors per l'applicazione.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Servizi

Supporto di servizi senza stato di Service Fabric per l'applicazione.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>Altro

#### <a name="transport"></a>Trasporto

Supporto del livello trasporto per l'applicazione Java di Service Fabric. Non è necessario aggiungere esplicitamente questa dipendenza alle applicazioni Reliable Actors o Services, a meno che non si esegua la programmazione al livello trasporto.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Supporto di Fabric

Supporto a livello di sistema per Service Fabric, che comunica con il runtime nativo di Service Fabric. Non è necessario aggiungere esplicitamente questa dipendenza alle applicazioni Reliable Actors o Services. Viene recuperata automaticamente da Maven quando si includono le altre dipendenze riportate sopra.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>Migrazione di un servizio senza stato di Service Fabric

Per poter compilare il servizio Java senza stato di Service Fabric esistente usando le dipendenze di Service Fabric recuperate da Maven, è necessario aggiornare il file ``build.gradle`` all'interno del servizio. La versione precedente si presenta come segue:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

A questo punto, per recuperare le dipendenze da Maven, il **aggiornato** ``build.gradle`` avrebbe le parti corrispondenti come segue:

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

Per un'idea generale di come si presenterà lo script di compilazione per un servizio Java senza stato di Service Fabric, vedere uno qualsiasi degli esempi introduttivi, ad esempio il file [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle) per l'esempio EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Migrazione di un servizio Service Fabric Actors

Per poter compilare l'applicazione Java di Service Fabric Actors esistente usando le dipendenze di Service Fabric recuperate da Maven, è necessario aggiornare il file ``build.gradle`` all'interno del pacchetto dell'interfaccia e nel pacchetto del servizio. Se si ha un pacchetto TestClient, è necessario aggiornare anche quello. Per il servizio Actor ``Myactor`` sarà quindi necessario eseguire l'aggiornamento nelle posizioni seguenti:

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Aggiornamento dello script di compilazione per il progetto dell'interfaccia

La versione precedente si presenta come segue:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

A questo punto, per recuperare le dipendenze da Maven, il **aggiornato** ``build.gradle`` avrebbe le parti corrispondenti come segue:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Aggiornamento dello script di compilazione per il progetto Actor

La versione precedente si presenta come segue:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

A questo punto, per recuperare le dipendenze da Maven, il **aggiornato** ``build.gradle`` avrebbe le parti corrispondenti come segue:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Aggiornamento dello script di compilazione per il progetto del client di test

Le modifiche sono simili a quelle illustrate nella sezione precedente per il progetto Actor. La versione precedente dello script Gradle si presenta come segue:

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

A questo punto, per recuperare le dipendenze da Maven, il **aggiornato** ``build.gradle`` avrebbe le parti corrispondenti come segue:

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse](service-fabric-get-started-eclipse.md)
* [Interagire con un cluster di Service Fabric usando l'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
