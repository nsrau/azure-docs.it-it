---
title: Gestire le dipendenze JAR - Azure HDInsightManage JAR dependencies - Azure HDInsight
description: In questo articolo vengono illustrate le procedure consigliate per la gestione delle dipendenze di Java Archive (JAR) per le applicazioni HDInsight.This article discusses best practices for managing Java Archive (JAR) dependencies for HDInsight applications.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135733"
---
# <a name="jar-dependency-management-best-practices"></a>Best practice per la gestione delle dipendenze JAR

I componenti installati nei cluster HDInsight hanno dipendenze da librerie di terze parti. Di solito, una versione specifica di moduli comuni come Guava fa riferimento a questi componenti incorporati. Quando si invia un'applicazione con le relative dipendenze, può causare un conflitto tra versioni diverse dello stesso modulo. Se la versione del componente a cui si fa riferimento prima nel percorso di classe, i componenti incorporati possono generare eccezioni a causa dell'incompatibilità di versione. Tuttavia, se i componenti incorporati inseriscono prima le dipendenze nel `NoSuchMethod`percorso di classe, l'applicazione potrebbe generare errori come .

Per evitare conflitti di versione, prendere in considerazione l'ombreggiatura delle dipendenze dell'applicazione.

## <a name="what-does-package-shading-mean"></a>Cosa significa ombreggiatura pacchetto?
L'ombreggiatura consente di includere e rinominare le dipendenze. Riposiziona le classi e riscrive il bytecode e le risorse interessate per creare una copia privata delle dipendenze.

## <a name="how-to-shade-a-package"></a>Come ombreggiare un pacchetto?

### <a name="use-uber-jar"></a>Usa uber-jar
Uber-jar è un singolo file jar che contiene sia il file jar dell'applicazione che le relative dipendenze. Le dipendenze in Uber-jar non sono ombreggiate per impostazione predefinita. In alcuni casi, ciò può introdurre conflitti di versione se altri componenti o applicazioni fanno riferimento a una versione diversa di tali librerie. Per evitare questo problema, è possibile creare un file Uber-Jar con alcune (o tutte) le dipendenze ombreggiate.

### <a name="shade-package-using-maven"></a>Pacchetto Ombreggiatura con Maven
Maven può compilare applicazioni scritte sia in Java che in Scala. Maven-shade-plugin può aiutarti a creare facilmente un uber-jar ombreggiato.

L'esempio seguente `pom.xml` mostra un file che è stato aggiornato per ombreggiare un pacchetto utilizzando maven-shade-plugin.  La sezione `<relocation>…</relocation>` XML sposta `com.google.guava` le `com.google.shaded.guava` classi dal pacchetto al pacchetto spostando le voci del file JAR corrispondenti e riscrivendo il bytecode interessato.

Dopo `pom.xml`aver cambiato `mvn package` , è possibile eseguire per costruire il uber-jar ombreggiato.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Pacchetto Ombra con SBT
SBT è anche uno strumento di compilazione per Scala e Java. SBT non ha un plugin ombra come maven-shade-plugin. È possibile `build.sbt` modificare il file in colli ombreggiati. 

Ad esempio, `com.google.guava`per ombreggiare , è `build.sbt` possibile aggiungere il comando seguente al file:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Quindi è `sbt clean` possibile `sbt assembly` eseguire e per costruire il file jar ombreggiato. 

## <a name="next-steps"></a>Passaggi successivi

* [Usare gli strumenti IntelliJ di HDInsightUse HDInsight IntelliJ Tools](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Creare un'applicazione Scala Maven per Spark in IntelliJCreate a Scala Maven application for Spark in IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
