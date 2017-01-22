---
title: "Novità di Azure Toolkit for IntelliJ | Documentazione Microsoft"
description: "Sono disponibili informazioni sulle funzionalità più recenti del Toolkit di Azure per IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 46ed791f-df59-416a-809e-f52345ad973c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm;asirveda;martinsawicki
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: a2006dbcf0d63ef38651a0859dc654d531fd875a


---
# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Novità del Toolkit di Azure per IntelliJ
## <a name="azure-toolkit-for-intellij-releases"></a>Toolkit di Azure per le versioni di IntelliJ
Questo articolo include informazioni sulle diverse versioni e sugli aggiornamenti più recenti del Toolkit di Azure per Eclipse.

> [!NOTE]
> Esiste anche un Toolkit di Azure per l’IDE di Eclipse. Per ulteriori informazioni, vedere [Toolkit di Azure per Eclipse].
> 
> 

### <a name="august-26-2016"></a>26 agosto 2016
La versione del Toolkit di Azure per IntelliJ rilasciata ad agosto 2016 include i miglioramenti seguenti:

* **Distribuzioni di JDK personalizzate**. Azure Toolkit per IntelliJ supporta ora la specifica e la distribuzione di una versione di JDK arbitraria nel contenitore di app Web di Azure:
  * Oltre alla versione JDK fornita da Azure, è possibile scegliere tra un'ampia gamma di versioni di Zulu OpenJDK rese disponibili in Azure da Azul Systems.
  * È anche possibile specificare una distribuzione di JDK personalizzata se viene caricata come file ZIP nell'account di archiviazione.
* **Miglioramenti alla visualizzazione di Azure Explorer**:
  * Supporto per la gestione di Macchine virtuali usando il nuovo modello di Azure Resource Manager: è possibile elencare, creare ed eliminare le macchine virtuali basate su Resource Manager senza uscire dall'ambiente IDE.
  * Supporto per la gestione di BLOB di account di archiviazione tramite Azure Resource Manager, che integra le funzionalità esistenti per la gestione di account di archiviazione "classici".
* **Microsoft JDBC Driver 6.0 per SQL Server**. Questo aggiornamento include il driver JDBC più aggiornato per Microsoft SQL Server (versione 6.0), incluso ora come libreria che è possibile aggiungere facilmente ai progetti Java, sostituendo in tal modo la versione precedente.

### <a name="june-29-2016"></a>29 giugno 2016
La versione del Toolkit di Azure per IntelliJ rilasciata a giugno 2016 include i miglioramenti seguenti:

* **Requisito Java 8**. Per il Toolkit Azure per IntelliJ è ora necessario Java 8. Sebbene questo requisito sia fondamentale per il toolkit, le applicazioni possono continuare a usare versioni di Java supportate da Azure.
* **Supporto per i JDK di Java più recenti**. Le versioni più recenti dei JDK di Java sono ora supportate dal Toolkit di Azure per IntelliJ.
* **Supporto per Azure SDK versione 2.9.1**. La versione più recente di Azure SDK è ora il prerequisito minimo per il Toolkit di Azure per IntelliJ.
* **Campioni integrati**. Il Toolkit di Azure per IntelliJ presenta adesso numerose applicazioni campione per fornire supporto agli sviluppatori nella fase iniziale.
* **Integrazione dello strumento HDInsight**. Gli strumenti HDInsight di Azure sono adesso contenuti nel Toolkit di Azure per IntelliJ. Per altre informazioni, vedere l'articolo relativo al [plug-in degli strumenti HDInsight per IntelliJ].
* **Debug remoto delle app Web Java**. Il Toolkit di Azure per IntelliJ supporta adesso il debug remoto delle App Web Java nel servizio app di Azure.

### <a name="april-12-2016"></a>12 aprile 2016
La versione del Toolkit di Azure per IntelliJ rilasciata ad aprile 2016 include i miglioramenti seguenti:

* **Supporto per Azure SDK versione 2.9.0**. La versione più recente di Azure SDK è ora il prerequisito minimo per il Toolkit di Azure per IntelliJ.
* **Vari miglioramenti di usabilità, velocità di risposta e prestazioni relativamente al supporto di app Web di Azure**. Prestazioni ottimizzate relativamente alla comunicazione tra il Toolkit e il risultato di Azure in un'interfaccia utente più reattiva.
* **Possibilità di eliminare un contenitore di applicazioni Web in Azure da IntelliJ**. Il Toolkit di Azure per IntelliJ consente ora di eliminare un contenitore Web di Azure esistente senza uscire da IntelliJ.

## <a name="see-also"></a>Vedere anche
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Creare un'app Web Hello World per Azure in Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Creare un'App Web Hello World per Azure in IntelliJ]
  * *Novità del Toolkit di Azure per IntelliJ (questo articolo)*

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ./azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[plug-in degli strumenti HDInsight per IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md



<!--HONumber=Jan17_HO1-->


