---
title: Configurare app Java di Windows-servizio app Azure | Microsoft Docs
description: Informazioni su come configurare le app Java da eseguire nelle istanze di Windows predefinite nel servizio app Azure.
keywords: servizio app di Azure, app Web, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498524"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurare un'app Java Windows per il servizio app Azure

App Azure servizio consente agli sviluppatori Java di creare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Windows completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano nel servizio app. Se non si è mai usato app Azure servizio, è consigliabile leggere prima di tutto la [Guida introduttiva per Java](app-service-web-get-started-java.md) . Domande generali sull'uso del servizio app non specifiche per lo sviluppo Java sono disponibili nelle [domande frequenti su Windows del servizio app](faq-configuration-and-management.md).

> [!NOTE]
> Le informazioni cercate non sono disponibili? Per informazioni sulla distribuzione e la protezione dell'app Java, vedere le [domande frequenti su Windows OSS](faq-configuration-and-management.md) o la [Guida alla configurazione di Java Linux](containers/configure-language-java.md) .

## <a name="configuring-tomcat"></a>Configurazione di Tomcat

Per modificare Tomcat `server.xml` o altri file di configurazione, prendere nota della versione principale di Tomcat nel portale.

1. Trovare la home directory di Tomcat per la versione eseguendo il `env` comando. Cercare la variabile di ambiente che inizia con `AZURE_TOMCAT`e corrisponde alla versione principale. Ad esempio, `AZURE_TOMCAT85_HOME` punta alla directory Tomcat per Tomcat 8,5.
1. Dopo aver identificato la home directory di Tomcat per la versione in uso, copiare la directory `D:\home`di configurazione in. Se `AZURE_TOMCAT85_HOME` , ad esempio, il valore di `D:\Program Files (x86)\apache-tomcat-8.5.37`è, il nuovo percorso della directory copiata sarà `D:\home\apache-tomcat-8.5.37`.

Riavviare infine il servizio app. Le distribuzioni devono essere `D:\home\site\wwwroot\webapps` inserite come prima.

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti delle versioni principali verranno forniti tramite nuove opzioni di runtime nel servizio app Azure per Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto del prodotto per l' [Azul Zulu del JDK supportato da Azure](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft quando si sviluppa per azure o [Azure stack](https://azure.microsoft.com/overview/azure-stack/) con un [piano di supporto di Azure completo](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento descrive il supporto di Java Runtime per app Azure servizio in Windows.

- Per altre informazioni sull'hosting di applicazioni Web con app Azure servizio, vedere [Panoramica del servizio app](overview.md).
- Per informazioni sullo sviluppo di Java in Azure, vedere [Azure per Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
