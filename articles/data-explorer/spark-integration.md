---
title: Connettore di Esplora dati di Azure per Apache Spark
description: Questo articolo illustra come usare il connettore di Esplora dati di Azure per Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825189"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Connettore di Esplora dati di Azure per Apache Spark

Con Esplora dati di Azure e Apache Spark è possibile compilare applicazioni scalabili e veloci destinate a scenari basati sui dati, ad esempio Machine Learning, Extract-Transform-Load (ETL) e Log Analytics.

## <a name="prerequisites"></a>Prerequisiti

Per usare il connettore, l'applicazione deve avere:

* Java 1.8 SDK
* Maven 3.x
* Spark versione 2.3.2 o successiva

## <a name="link-to-data-explorer"></a>Eseguire il collegamento a Esplora dati di Azure

Per le applicazioni Scala e Java che usano le definizioni di progetto Maven, collegare l'applicazione con l'elemento seguente:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

In Maven, collegare con l'elemento seguente:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Comandi di compilazione

Per compilare file con estensione jar ed eseguire tutti i test:

```
mvn clean package
```

Per compilare file con estensione jar, eseguire tutti i test e installare i file con estensione jar nel repository Maven locale:

```
mvn clean install
```