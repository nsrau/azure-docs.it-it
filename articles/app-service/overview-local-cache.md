---
title: Panoramica della cache locale - Servizio app di Azure | Microsoft Docs
description: Questo articolo descrive come abilitare, ridimensionare ed eseguire query sullo stato della funzionalità relativa alla cache locale del servizio app di Azure
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4dffa7dcafe4aabe3e8dcb56d4f5084d0c6ef821
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819674"
---
# <a name="azure-app-service-local-cache-overview"></a>Panoramica della cache locale del servizio app di Azure

> [!NOTE]
> La cache locale non è supportata nelle app per le funzioni o nelle app del servizio app in contenitori, come ad esempio nel [servizio app in Linux](containers/app-service-linux-intro.md).


Il contenuto del Servizio app di Azure viene memorizzato in Archiviazione di Azure e presentato in modo permanente come condivisione del contenuto. Questa progettazione è concepita per consentire l'interazione con un'ampia gamma di app e ha gli attributi seguenti:  

* Il contenuto è condiviso tra più istanze di macchina virtuale (VM) dell'app.
* Il contenuto è permanente e può essere modificato dalle app in esecuzione.
* I file di log e file dei dati di diagnostica sono disponibili nella stessa cartella del contenuto condivisa.
* La pubblicazione di nuovo contenuto aggiorna direttamente la cartella del contenuto. È possibile visualizzare immediatamente lo stesso contenuto tramite il sito Web SCM e l'app in esecuzione, in genere alcune tecnologie, ad esempio ASP.NET, avviano il riavvio di un'app in alcune modifiche dei file per ottenere il contenuto più recente.

Mentre molte app usano una o tutte queste funzionalità, alcune richiedono solo un archivio del contenuto di sola lettura ad alte prestazioni da cui poterle eseguire con disponibilità elevata. Queste applicazioni possono trarre vantaggio da un'istanza di VM di una cache locale specifica.

La funzionalità cache locale del servizio app di Azure offre una visualizzazione di ruolo Web del contenuto. Il contenuto è costituito da una cache di scrittura con rimozione del contenuto di archiviazione creata in modo asincrono all'avvio del sito. Quando la cache è pronta, nel sito viene impostata l'esecuzione nel contenuto memorizzato nella cache. Le app eseguite nella cache locale possono sfruttare i vantaggi seguenti:

* Non sono soggette a latenze durante l'accesso al contenuto nell'archiviazione di Azure.
* Non risentono degli aggiornamenti pianificati o dei tempi di inattività non pianificati e altre interruzioni dell'archiviazione di Azure che si verificano nei server che forniscono la condivisione del contenuto.
* Risentono di un minor numero di riavvii delle app a seguito di modifiche alla condivisione dell'archiviazione.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Modalità di modifica del comportamento del servizio app da parte della cache locale
* _D:\home_ punta alla cache locale, creata nell'istanza della macchina virtuale all'avvio dell'app. _D:\local_ continua a puntare all'archivio temporaneo specifico della macchina virtuale.
* La cache locale contiene una copia eseguita una sola volta delle cartelle _/site_ e _/siteextensions_ dell'archivio del contenuto condiviso rispettivamente in _D:\home\site_ e _D:\home\ siteextensions_. I file vengono copiati nella cache locale all'avvio dell'app. La dimensione delle due cartelle per ogni app è limitata a 300 MB per impostazione predefinita, ma può essere aumentata fino a 2 GB.
* La cache locale è di lettura/scrittura. Le eventuali modifiche vengono tuttavia rimosse quando l'app sposta le macchine virtuali o viene riavviata. Non usare la cache locale per le app che archiviano dati mission-critical nell'archivio del contenuto.
* _D:\home\LogFiles_ e _D:\home\Data_ contengono i file di log e i dati delle app. Le due sottocartelle vengono archiviate in locale nell'istanza della macchina virtuale e vengono copiate periodicamente nell'archivio del contenuto condiviso. Le app possono salvare in modo permanente i dati e i file di log, scrivendoli in queste cartelle. Tuttavia, la copia nell'archivio del contenuto condiviso è di tipo massimo sforzo e quindi i dati e i file di log potrebbero andare persi a seguito di un arresto anomalo improvviso del sistema dell'istanza di una macchina virtuale.
* La copia di tipo massimo sforzo influisce sul [flusso di registrazione](troubleshoot-diagnostic-logs.md#stream-logs). Si può verificare un ritardo massimo di un minuto per i log inviati nel flusso.
* Nell'archivio del contenuto condiviso è stata modificata la struttura delle cartelle _LogFiles_ e _Data_ delle app che usano la cache locale. Le cartelle includono ora sottocartelle che seguono il modello di denominazione "identificatore univoco" + timestamp. Ogni sottocartella corrisponde a un'istanza di macchina virtuale in cui l'app è o era in esecuzione.
* Le altre cartelle _D:\home_ rimangono nella cache locale e non vengono copiate nell'archivio del contenuto condiviso.
* La distribuzione di app con un qualsiasi metodo supportato implica la pubblicazione direttamente nell'archivio durevole del contenuto condiviso. Per aggiornare le cartelle _D:\home\site_ e _D:\home\siteextensions_ nella cache locale, è necessario riavviare l'app. vedere le informazioni più avanti in questo articolo per rendere il ciclo di vita più lineare.
* La visualizzazione del contenuto predefinita del sito SCM continua a essere quella dell'archivio del contenuto condiviso.

## <a name="enable-local-cache-in-app-service"></a>Abilitare la cache locale nel servizio app
La cache locale viene configurata mediante una combinazione di impostazioni delle app riservate. Queste impostazioni delle app possono essere configurate usando i metodi seguenti:

* [Azure portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurare la cache locale tramite il portale di Azure
<a name="Configure-Local-Cache-Portal"></a>

La cache locale viene abilitata per ogni app Web con questa impostazione dell'app: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Impostazioni delle app nel portale di Azure: cache locale](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurare la cache locale tramite Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Modificare l'impostazione delle dimensioni nella cache locale
Per impostazione predefinita, le dimensioni della cache locale sono pari a **1 GB**. Sono incluse le cartelle /site e /siteextensions copiate dall'archivio del contenuto, nonché eventuali log e cartelle di dati creati in locale. Per aumentare questo limite, usare l'impostazione dell'app `WEBSITE_LOCAL_CACHE_SIZEINMB`. È possibile aumentare le dimensioni fino a **2 GB** (2000 MB) per ogni app.

## <a name="best-practices-for-using-app-service-local-cache"></a>Procedure consigliate per l'uso della cache locale del servizio app
È consigliabile usare la cache locale insieme alla funzionalità degli [ambienti di gestione temporanea](../app-service/deploy-staging-slots.md) .

* Aggiungere l'impostazione dell'app *permanente* `WEBSITE_LOCAL_CACHE_OPTION` con il valore `Always` nello slot di **produzione**. Se si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, aggiungerla anch'essa come impostazione permanente nello slot di produzione.
* Creare uno slot di **gestione temporanea** e pubblicare in questo slot. Se si sfruttano i vantaggi della cache locale per lo slot di produzione, in genere non si imposta l'uso della cache locale nello slot di gestione temporanea per abilitare un ciclo di vita di compilazione-distribuzione-test lineare per la gestione temporanea.
* Testare il sito nello slot di gestione temporanea.  
* Al termine, eseguire un' [operazione di scambio](../app-service/deploy-staging-slots.md#Swap) tra lo slot di gestione temporanea e lo slot di produzione.  
* Le impostazioni permanenti includono il nome e sono permanenti in uno slot. Di conseguenza, quando lo slot di staging viene scambiato con lo slot di produzione, eredita le impostazioni dell'app della cache locale. Il nuovo slot di produzione scambiato verrà eseguito nella cache locale dopo alcuni minuti e verrà preparato durante la fase di preparazione dello slot successiva allo scambio. Quando lo scambio di slot è stato completato, lo slot di produzione viene eseguito nella cache locale.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Come è possibile stabilire se la cache locale si applica all'app?
Se l'app richiede un archivio del contenuto a prestazioni elevate e affidabile, non usa l'archivio del contenuto per scrivere dati critici in fase di esecuzione e ha una dimensione totale inferiore a 2 GB, è possibile usare la cache locale. Per determinare le dimensioni totali delle cartelle /site e /siteextensions è possibile usare l'estensione del sito relativa all'uso del disco delle app Web di Azure.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Come è possibile verificare se il sito usa la cache locale?
Se viene usata la funzionalità della cache locale con gli ambienti di staging, l'operazione di scambio non verrà completata fino a quando la cache locale non viene preparata. Per verificare se il sito viene eseguito nella cache locale, è possibile controllare la variabile di ambiente del processo di lavoro `WEBSITE_LOCALCACHE_READY`. Per accedere alle variabili di ambiente del processo di lavoro in più istanze, seguire le istruzioni disponibili nella pagina relativa alla [variabile di ambiente del processo di lavoro](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) .  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Sono state appena pubblicate nuove modifiche, ma sembra che non siano presenti nell'app. Perché?
Se l'app usa la cache locale, è necessario riavviare il sito per visualizzare le ultime modifiche. Se non si vogliono pubblicare le modifiche in un sito di produzione, vedere le opzioni dello slot nella sezione precedente relativa alle procedure consigliate.

### <a name="where-are-my-logs"></a>Dove si trovano i log?
Se si usa la cache locale, i log e le cartelle di dati hanno un aspetto leggermente diverso. Tuttavia, la struttura delle sottocartelle rimane la stessa, eccetto il fatto che le sottocartelle sono annidate in una sottocartella con il formato "identificatore VM univoco" + timestamp.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>È stata abilitata la cache locale ma l'app viene riavviata comunque. Perché? Pensavo che la cache locale fosse utile in caso di riavvii frequenti dell'app.
La cache locale consente di evitare i riavvii dell'app legati all'archiviazione. L'app può comunque essere ancora soggetta a riavvii durante gli aggiornamenti pianificati dell'infrastruttura della VM. In generale, il numero di riavvii dell'app che si verificano con la cache locale abilitata dovrebbe essere inferiore.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>La cache locale impedisce la copia delle directory in un'unità locale più veloce?
Nell'ambito del passaggio che copia il contenuto di archiviazione, qualsiasi cartella denominata repository viene esclusa. Questa soluzione è utile negli scenari in cui il contenuto del sito può contenere un repository di controllo di origine che potrebbe non essere necessario nel funzionamento quotidiano dell'app. 
