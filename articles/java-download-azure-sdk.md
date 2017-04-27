---
title: Download di Azure SDK per Java | Documentazione Microsoft
description: Informazioni su come scaricare Azure SDK per Java, con codice di esempio fornito per i progetti Maven.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Download di Azure SDK per Java
Questo articolo contiene le istruzioni per scaricare e installare le librerie di gestione di Azure per Java.

> [!NOTE]
> Le librerie di gestione di Azure per Java vengono distribuite con la [licenza Apache versione 2.0][license].
>

## <a name="azure-libraries-for-java---manual-download"></a>Librerie di Azure per Java - Download manuale
Per installare manualmente le librerie di gestione di Azure per Java, fare clic su <http://go.microsoft.com/fwlink/?LinkId=690320> per scaricare un file ZIP che contiene tutte le librerie e tutte le dipendenze.

Dopo aver scaricato il file zip sul computer, estrarre il contenuto e usare una delle opzioni seguenti per aggiungere i file JAR al progetto:

* Importare i file JAR nel progetto Java in Eclipse o IntelliJ.
* Configurare i percorsi di compilazione per progetti Java in Eclipse o IntelliJ per includere il percorso dei file JAR.

> [!NOTE]
> Per dettagli sulla licenza e altre informazioni, vedere i file license.txt e ThirdPartyNotices.txt nell'archivio ZIP.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Librerie di gestione di Azure per Java - Compilazione con Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Passaggio 1: configurare il progetto per usare Maven per la compilazione
Per creare in Eclipse progetti Maven che usano le librerie di gestione di Azure per Java, seguire le istruzioni nell'articolo [Getting Started with Azure Management Libraries for Java][maven-getting-started] (Introduzione alle librerie di gestione di Azure per Java).

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Passaggio 2 - configurare le impostazioni di Maven con le dipendenze necessarie
Dopo aver configurato il progetto per l'uso di Maven per la compilazione, è possibile aggiungere le dipendenze necessarie al file pom.xml usando una sintassi simile a quella dell'esempio seguente. Notare che non è necessario aggiungere tutte le dipendenze elencate nel seguente esempio; è sufficiente aggiungere le dipendenze specifiche richieste dal progetto.

> [!NOTE]
> All'interno di ogni elemento `<version>` nell'esempio seguente, sostituire i segnaposto "n.n.n" di questo esempio con numeri di versione validi, che possono essere ottenuti dall'[archivio delle librerie di Azure su Maven].
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Creare un'app Web Hello World per Azure in Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Creare un'App Web Hello World per Azure in IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]

Per altre informazioni su come usare Azure con Java, visitare il [Centro per sviluppatori Java di Azure].

> [!NOTE]
> Per informazioni dettagliate sull'impostazione dei percorsi di compilazione in Eclipse, vedere l’articolo [Java Build Path] sul sito Web Eclipse.
>

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[archivio delle librerie di Azure su Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

