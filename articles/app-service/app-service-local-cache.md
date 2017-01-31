---
title: Panoramica della cache locale del servizio app di Azure | Documentazione Microsoft
description: "Questo articolo descrive come abilitare, ridimensionare ed eseguire query sullo stato della funzionalità relativa alla cache locale del servizio app di Azure"
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
editor: 
tags: optional
keywords: 
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 09ec6d1aae5dc893e92b7c4ca1c30a251d02443d


---
# <a name="azure-app-service-local-cache-overview"></a>Panoramica della cache locale del servizio app di Azure
Il contenuto delle app Web di Azure viene memorizzato nell'archiviazione di Azure e presentato in modo permanente come condivisione del contenuto. Questa progettazione è concepita per consentire l'interazione con un'ampia gamma di app e ha gli attributi seguenti:  

* Il contenuto è condiviso tra più istanze di macchina virtuale (VM) dell'app Web.
* Il contenuto è permanente e può essere modificato dalle app Web in esecuzione.
* I file di log e file dei dati di diagnostica sono disponibili nella stessa cartella del contenuto condivisa.
* La pubblicazione di nuovo contenuto aggiorna direttamente la cartella del contenuto. È possibile visualizzare immediatamente il contenuto stesso tramite il sito Web di Gestione controllo servizi e l'app Web in esecuzione. Alcune tecnologie, ad esempio ASP.NET, in genere eseguono il riavvio di un'app Web quando vengono apportate modifiche ai file per ottenere il contenuto più recente.

Mentre molte app Web usano una o tutte queste funzionalità, alcune richiedono solo un archivio del contenuto di sola lettura ad alte prestazioni da cui possono essere eseguite con disponibilità elevata. Queste applicazioni possono trarre vantaggio da un'istanza di VM di una cache locale specifica.

La funzionalità cache locale del servizio app di Azure offre una visualizzazione di ruolo Web del contenuto. Il contenuto è costituito da una cache di scrittura con rimozione del contenuto di archiviazione creata in modo asincrono all'avvio del sito. Quando la cache è pronta, nel sito viene impostata l'esecuzione nel contenuto memorizzato nella cache. Le app Web eseguite nella cache locale possono sfruttare i vantaggi seguenti:

* Non sono soggette a latenze durante l'accesso al contenuto nell'archiviazione di Azure.
* Non risentono degli aggiornamenti pianificati o dei tempi di inattività non pianificati e altre interruzioni dell'archiviazione di Azure che si verificano nei server che forniscono la condivisione del contenuto.
* Risentono di un minor numero di riavvii delle app a seguito di modifiche alla condivisione dell'archiviazione.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Modalità di modifica del comportamento del servizio app da parte della cache locale
* La cache locale è una copia delle cartelle /site e /siteextensions dell'app Web. Viene creata nell'istanza di VM locale all'avvio dell'app web. Le dimensioni della cache locale di ogni app Web sono limitate a 300 MB per impostazione predefinita, ma possono essere aumentate fino a 2 GB.
* La cache locale è di lettura/scrittura. Le eventuali modifiche vengono tuttavia rimosse quando l'app Web sposta le macchine virtuali o viene riavviata. È consigliabile non usare la cache locale per le app che archiviano dati cruciali nell'archivio del contenuto.
* Le app Web possono continuare a scrivere file di log e dati di diagnostica come avviene attualmente. File di log e dati vengono tuttavia archiviati in locale nella VM, quindi vengono copiati periodicamente nell'archivio del contenuto condiviso. La copia nell'archivio del contenuto condiviso è una soluzione basata sul principio del "massimo sforzo". I writeback potrebbero infatti andare persi a seguito di un arresto anomalo improvviso dell'istanza di una VM.
* La struttura delle cartelle LogFiles e Data delle app Web che usano la cache locale è stata modificata. Le cartelle LogFiles e Data della risorsa di archiviazione includono ora sottocartelle che seguono il modello di denominazione "identificatore univoco" + timestamp. Ogni sottocartella corrisponde a un'istanza di VM in cui l'app Web è o era in esecuzione.  
* Le modifiche di pubblicazione nell'app Web con uno dei meccanismi di pubblicazione saranno pubblicate nell'archivio del contenuto condiviso. Si tratta di un comportamento previsto da progettazione finalizzato a rendere permanente il contenuto pubblicato. Per aggiornare la cache locale dell'app Web, è necessario riavviarla. Se questa può sembrare una misura eccessiva, vedere le informazioni più avanti in questo articolo per rendere il ciclo di vita più lineare.
* D:\Home punterà alla cache locale. D:\local continuerà a puntare all'archivio temporaneo specifico della macchina virtuale.
* La visualizzazione del contenuto predefinita del sito SCM continuerà a essere quella dell'archivio del contenuto condiviso.

## <a name="enable-local-cache-in-app-service"></a>Abilitare la cache locale nel servizio app
La cache locale viene configurata mediante una combinazione di impostazioni delle app riservate. Queste impostazioni delle app possono essere configurate usando i metodi seguenti:

* [Portale di Azure](#Configure-Local-Cache-Portal)
* [Gestione risorse di Azure](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurare la cache locale tramite il portale di Azure
<a name="Configure-Local-Cache-Portal"></a>

La cache locale viene abilitata per ogni app Web con questa impostazione dell'app: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Impostazioni delle app nel portale di Azure: cache locale](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurare la cache locale tramite Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
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
Per impostazione predefinita le dimensioni della cache locale corrispondono a **300 MB**. Sono incluse le cartelle /site e /siteextensions copiate dall'archivio del contenuto, nonché eventuali log e cartelle di dati creati in locale. Per aumentare questo limite, usare l'impostazione dell'app `WEBSITE_LOCAL_CACHE_SIZEINMB`. È possibile aumentare le dimensioni fino a **2 GB** (2000 MB) per ogni app Web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Procedure consigliate per l'uso della cache locale del servizio app
È consigliabile usare la cache locale insieme alla funzionalità degli [ambienti di gestione temporanea](../app-service-web/web-sites-staged-publishing.md) .

* Aggiungere l'impostazione dell'app *permanente* `WEBSITE_LOCAL_CACHE_OPTION` con il valore `Always` nello slot di **produzione**. Se si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, aggiungerla anch'essa come impostazione permanente nello slot di produzione.
* Creare uno slot di **gestione temporanea** e pubblicare in questo slot. Se si sfruttano i vantaggi della cache locale per lo slot di produzione, in genere non si imposta l'uso della cache locale nello slot di gestione temporanea per abilitare un ciclo di vita di compilazione-distribuzione-test lineare per la gestione temporanea.
* Testare il sito nello slot di gestione temporanea.  
* Al termine, eseguire un' [operazione di scambio](../app-service-web/web-sites-staged-publishing.md#Swap) tra lo slot di gestione temporanea e lo slot di produzione.  
* Le impostazioni permanenti includono il nome e sono permanenti in uno slot. Di conseguenza, quando lo slot di gestione temporanea viene scambiato in slot di produzione, erediterà le impostazioni dell'app della cache locale. Il nuovo slot di produzione scambiato verrà eseguito nella cache locale dopo alcuni minuti e verrà preparato durante la fase di preparazione dello slot successiva allo scambio. Quando lo scambio di slot è stato completato, lo slot di produzione verrà quindi eseguito nella cache locale.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Come è possibile stabilire se la cache locale si applica all'app Web?
Se l'app Web richiede un archivio del contenuto a prestazioni elevate e affidabile e non usa l'archivio del contenuto per scrivere dati critici in fase di esecuzione e ha una dimensione totale inferiore a 2 GB, è possibile usare la cache locale. Per determinare le dimensioni totale delle cartelle /site e /siteextensions è possibile usare l'estensione del sito relativa all'utilizzo del disco delle app Web di Azure.  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Come è possibile verificare se il sito usa la cache locale?
Se viene usata la funzionalità della cache locale con gli ambienti di gestione temporanea, l'operazione di scambio non verrà completata fino a quando la cache locale non viene preparata. Per verificare se il sito viene eseguito nella cache locale, è possibile controllare la variabile di ambiente del processo di lavoro `WEBSITE_LOCALCACHE_READY`. Per accedere alle variabili di ambiente del processo di lavoro in più istanze, seguire le istruzioni disponibili nella pagina relativa alla [variabile di ambiente del processo di lavoro](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) .  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Sono state appena pubblicate nuove modifiche, ma sembra che non siano presenti nell'app Web. Perché?
Se l'app Web usa la cache locale, è necessario riavviare il sito per visualizzare le ultime modifiche. Se non si vogliono pubblicare le modifiche in un sito di produzione, vedere le opzioni dello slot nella sezione precedente relativa alle procedure consigliate.

### <a name="where-are-my-logs"></a>Dove si trovano i log?
Se si usa la cache locale, i log e le cartelle di dati hanno un aspetto leggermente diverso. Tuttavia, la struttura delle sottocartelle rimane la stessa, eccetto il fatto che le sottocartelle sono annidate in una sottocartella con il formato "identificatore VM univoco" + timestamp.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>È stata abilitata la cache locale ma l'app Web viene ancora riavviata. Perché? Pensavo che la cache locale fosse utile in caso di riavvii frequenti dell'app.
La cache locale consente di evitare i riavvii dell'app Web correlati all'archiviazione. L'app Web può comunque essere ancora soggetta a riavvii durante gli aggiornamenti pianificati dell'infrastruttura della VM. In generale, il numero di riavvii dell'app che si verificano con la cache locale abilitata dovrebbe essere inferiore.



<!--HONumber=Dec16_HO3-->


