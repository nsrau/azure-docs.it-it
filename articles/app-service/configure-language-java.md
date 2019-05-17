---
title: Configurare Windows App Java - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare le app Java per l'esecuzione nelle istanze di Windows predefinito nel servizio App di Azure.
keywords: servizio app di Azure, app web, windows, oss, java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604180"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurare un Windows app Java per servizio App di Azure

Servizio App di Azure permette agli sviluppatori Java per creare, distribuire e ridimensionare le Tomcat o Java Standard Edition (SE) di pacchetti di applicazioni web in un servizio completamente gestito basato su Windows. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per sviluppatori Java usando nel servizio App. Se non si è mai usato Azure App Service, è consigliabile leggere il [Guida introduttiva a Java](app-service-web-get-started-java.md) prima. Risposta alle domande di carattere generale sull'uso del servizio App che non sono specifiche per lo sviluppo Java nel [domande frequenti su Windows del servizio App](faq-configuration-and-management.md).

> [!NOTE]
> Le informazioni cercate non sono disponibili? Vedere la [domande frequenti su sistemi operativi Windows](faq-configuration-and-management.md) o nella [Guida alla configurazione di Java Linux](containers/configure-language-java.md) per informazioni sulla distribuzione e protezione di app Java.

## <a name="configuring-tomcat"></a>Configurazione Tomcat

Per modificare di Tomcat `server.xml` o altri file di configurazione, innanzitutto prendere nota della versione principale di Tomcat nel portale.

1. Trovare la directory principale di Tomcat per la versione eseguendo il `env` comando. Cercare la variabile di ambiente che inizia con `AZURE_TOMCAT`e corrisponde alla versione principale. Ad esempio, `AZURE_TOMCAT85_HOME` punti alla directory di Tomcat per Tomcat 8.5.
1. Dopo aver identificato la home directory di Tomcat per la versione, copiare la directory di configurazione per `D:\home`. Ad esempio, se `AZURE_TOMCAT85_HOME` conteneva un valore di `D:\Program Files (x86)\apache-tomcat-8.5.37`, il percorso completo della directory di configurazione copiato sarebbe `D:\home\tomcat\conf`.

Riavviare infine il servizio app. Le distribuzioni devono passare a `D:\home\site\wwwroot\webapps` esattamente come in precedenza.

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti di versione principale verranno forniti tramite nuove opzioni di runtime in servizio App di Azure per Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto tecnico per il [supportate da Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft durante lo sviluppo per Azure o [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) con un [qualificato piano di supporto tecnico di Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento fornisce l'istruzione di Runtime Java di supporto per servizio App di Azure su Windows.

- Per altre informazioni sull'hosting di applicazioni web con servizio App di Azure, vedere [Panoramica del servizio App](overview.md).
- Per informazioni su Java sullo sviluppo di Azure, vedere [Azure per il centro per sviluppatori Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
