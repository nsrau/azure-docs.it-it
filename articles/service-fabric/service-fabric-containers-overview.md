---
title: Panoramica di Service Fabric e contenitori | Documentazione Microsoft
description: "Panoramica di Service Fabric e dell&quot;uso dei contenitori per la distribuzione di applicazioni di microservizi. Questo articolo offre una panoramica di come possono essere usati i contenitori e delle funzionalità disponibili in Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: b8334d0ca0d1460edad9b0ef399e9b3428b1ef8d
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="service-fabric-and-containers"></a>Service Fabric e contenitori
> [!NOTE]
> Questa funzionalità è disponibile in anteprima per Linux. 
>   

## <a name="introduction"></a>Introduzione
Azure Service Fabric è un [agente di orchestrazione](service-fabric-cluster-resource-manager-introduction.md) dei servizi in un cluster di computer, con anni di uso e ottimizzazione alle spalle nei servizi Microsoft con scalabilità estremamente elevata. I servizi possono essere sviluppati in molti modi: dall'uso dei [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) alla distribuzione di [eseguibili guest](service-fabric-deploy-existing-app.md). Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. I processi offrono la massima velocità di attivazione e l'utilizzo della massima densità delle risorse in un cluster. Service Fabric può anche distribuire servizi in immagini contenitore. Un aspetto importante è che è possibile combinare servizi in processi e servizi in contenitori nella stessa applicazione. 

## <a name="containers-and-service-fabric-roadmap"></a>Guida di orientamento per contenitori e Service Fabric
Nelle prossime versioni, Service Fabric continuerà ad aggiungere un supporto esteso ai contenitori sia su Windows che su Linux. Ci saranno miglioramenti a rete, vincoli di risorse, sicurezza, diagnostica, driver di volumi e strumenti, in particolar modo a Visual Studio. Così, si avrà un'esperienza di prima classe per l'uso delle immagini di contenitore per distribuire i servizi. Ciò garantisce la scelta di usare i contenitori per assemblare il codice esistente, ad esempio applicazioni MVC IIS, o i modelli di programmazione di Service Fabric. Inoltre, poiché Service Fabric considera contenitori e modelli allo stesso modo, è possibile usarli entrambi nelle applicazioni, offrendo così la massima flessibilità per la distribuzione del codice. È possibile usufruire del meglio di entrambe le tipologie in base allo scenario specifico.

## <a name="what-are-containers"></a>Informazioni sui contenitori
I contenitori sono componenti incapsulati distribuibili singolarmente che vengono eseguiti come istanze isolate sullo stesso kernel per sfruttare i meccanismi di virtualizzazione offerti da un sistema operativo. In questo modo, ogni applicazione, il relativo runtime e le relative dipendenze e librerie di sistema vengono eseguiti all'interno di un contenitore con accesso privato completo alla vista isolata specifica del contenitore dei costrutti del sistema operativo. Insieme alla portabilità, questo grado di sicurezza e isolamento delle risorse è il principale vantaggio associato all'uso di contenitori con Service Fabric, che in alternativa esegue i servizi in processi.

I contenitori sono una tecnologia di virtualizzazione che virtualizza il sistema operativo sottostante rispetto alle applicazioni. I contenitori offrono un ambiente immutabile per eseguire le applicazioni con un grado variabile di isolamento. Vengono eseguiti direttamente sul kernel e hanno una vista isolata del file system e delle altre risorse. Rispetto alle macchine virtuali, i contenitori offrono i vantaggi seguenti:

* **Dimensioni ridotte**: i contenitori usano un unico spazio di archiviazione e differenziali di dimensioni inferiori per ogni livello, garantendo così una maggiore efficienza.
* **Tempi di avvio ridotti**: i contenitori non devono avviare un sistema operativo e possono quindi essere attivi e disponibili molto più rapidamente rispetto alle macchine virtuali, in genere in alcuni secondi.
* **Portabilità**: l'immagine di un'applicazione in contenitore può essere trasferita per essere eseguita nel cloud, in locale, all'interno di macchine virtuali o direttamente in computer fisici.
* **Governance delle risorse**: è possibile limitare le risorse fisiche utilizzabili da un contenitore nell'host.

## <a name="container-types"></a>Tipi di contenitori
Service Fabric supporta i tipi di contenitori seguenti.

### <a name="docker-containers-on-linux"></a>Contenitori Docker in Linux
Docker fornisce le API di alto livello per creare e gestire contenitori su contenitori del kernel Linux. Docker Hub è un repository centrale per archiviare e recuperare immagini contenitore.

Per informazioni sulla procedura dettagliata, leggere [Distribuire un contenitore Docker in Service Fabric](service-fabric-deploy-container-linux.md).

### <a name="windows-server-containers"></a>Contenitori Windows Server
Windows Server 2016 fornisce due diversi tipi di contenitori che si differenziano per il livello di isolamento offerto. I contenitori Windows Server e i contenitori Docker sono simili perché entrambi usufruiscono dell'isolamento dello spazio dei nomi e del file system, ma condividono il kernel con l'host in cui vengono eseguiti. In Linux, questo isolamento viene tradizionalmente fornito con cgroup e spazi dei nomi e i contenitori Windows Server presentano un comportamento simile.

I contenitori Windows Hyper-V offrono un grado superiore di isolamento e sicurezza perché i singoli contenitori non condividono il kernel del sistema operativo con altri contenitori o con l'host. Con questo livello superiore di isolamento di sicurezza, i contenitori Hyper-V sono destinati a scenari multi-tenant ostili.

Per informazioni sulla procedura dettagliata, leggere [Distribuire un contenitore Windows in Service Fabric](service-fabric-deploy-container.md).

La figura seguente illustra i diversi tipi di livelli di virtualizzazione e isolamento disponibili nel sistema operativo.
![Piattaforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenari per l'uso di contenitori
Ecco alcuni esempi tipici dei casi in cui un contenitore rappresenta una buona scelta:

* **Trasferimento da IIS**: se sono disponibili app [ASP.NET MVC](https://www.asp.net/mvc) che si vuole continuare a usare, anziché eseguirne la migrazione ad ASP.NET Core è possibile inserirle in un contenitore. Queste app ASP.NET MVC dipendono da IIS (Internet Information Services). È possibile creare pacchetti delle app in immagini contenitore dall'immagine IIS creata in precedenza e distribuirle con Service Fabric. Per informazioni sulla creazione delle immagini IIS, vedere [Immagini contenitore in Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) .
* **Combinazione di contenitori e microservizi di Service Fabric**: usare un'immagine contenitore esistente per parte dell'applicazione. È ad esempio possibile usare il [contenitore NGINX](https://hub.docker.com/_/nginx/) per il front-end Web dell'applicazione e i servizi con stato per le operazioni di calcolo back-end più intensive.
* **Riduzione dell'impatto dei servizi che influiscono negativamente**: è possibile usare la funzionalità di governance delle risorse dei contenitori per limitare le risorse usate da un servizio in un host. Se sono presenti servizi che potrebbero usare un elevato numero di risorse e quindi influire sulle prestazioni degli altri (ad esempio, un'operazione come una query a esecuzione prolungata), può essere opportuno inserire tali servizi in contenitori con governance delle risorse.

## <a name="service-fabric-support-for-containers"></a>Supporto di Service Fabric per i contenitori
Service Fabric attualmente supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016. Il supporto per i contenitori Hyper-V verrà aggiunto in una versione futura.

Nel [modello applicativo](service-fabric-application-model.md)di Service Fabric, un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi. Per i contenitori sono supportati gli scenari riportati di seguito.

* **Contenitori guest**: questo scenario è identico allo [scenario degli eseguibili guest](service-fabric-deploy-existing-app.md) in cui è possibile distribuire applicazioni esistenti in un contenitore. Ad esempio, codice Node.js, JavaScript o di qualsiasi tipo (eseguibili).
* **Servizi senza stato all'interno di contenitori**: si tratta di servizi senza stato che usano i modelli di programmazione Reliable Services e Reliable Actors. Questo scenario è attualmente supportato solo in Linux. Il supporto per servizi senza stato in contenitori Windows verrà aggiunto in una versione futura.
* **Servizi con stato all'interno di contenitori**: si tratta di servizi con stato che usano il modello di programmazione Reliable Actors in Linux. Reliable Services non è attualmente supportato in Linux.  Il supporto per servizi con stato in contenitori Windows verrà aggiunto in una versione futura.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, denominati servizi in contenitori. Le funzionalità includono:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse.
* Autenticazione nel repository.
* Mapping tra porta del contenitore e porta dell'host.
* Individuazione e comunicazione da contenitore a contenitore.
* Possibilità di configurare e impostare variabili di ambiente.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i contenitori, la funzione di agente di orchestrazione dei contenitori svolta da Service Fabric e le funzionalità offerte da Service Fabric per supportare i contenitori. Come passaggio successivo verranno esaminati esempi delle singole funzionalità per mostrarne le modalità d'uso.

[Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-deploy-container.md)

[Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-deploy-container-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

