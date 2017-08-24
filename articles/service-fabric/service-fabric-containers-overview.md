---
title: Panoramica di Service Fabric e contenitori | Documentazione Microsoft
description: "Panoramica di Service Fabric e dell'uso dei contenitori per la distribuzione di applicazioni di microservizi. Questo articolo offre una panoramica di come possono essere usati i contenitori e delle funzionalità disponibili in Service Fabric."
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: f770b6181a99d24ea6a6e945d505da914e1b6128
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="service-fabric-and-containers"></a>Service Fabric e contenitori
> [!NOTE]
> Questa funzionalità è disponibile in anteprima per Linux.  La distribuzione di contenitori a un cluster di Service Fabric in Windows 10 non è ancora supportata (presto disponibile). 
>   

## <a name="introduction"></a>Introduzione
Azure Service Fabric è un [agente di orchestrazione](service-fabric-cluster-resource-manager-introduction.md) dei servizi in un cluster di computer, con anni di uso e ottimizzazione alle spalle nei servizi Microsoft con scalabilità estremamente elevata. I servizi possono essere sviluppati in molti modi: dall'uso dei [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) alla distribuzione di [eseguibili guest](service-fabric-deploy-existing-app.md). Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. I processi offrono la massima velocità di attivazione e l'utilizzo della massima densità delle risorse in un cluster. Service Fabric può anche distribuire servizi in immagini contenitore. Un aspetto importante è che è possibile combinare servizi in processi e servizi in contenitori nella stessa applicazione. 

## <a name="containers-and-service-fabric-roadmap"></a>Guida di orientamento per contenitori e Service Fabric
Per le versioni future sono previsti molti miglioramenti all'orchestrazione dei contenitori con Service Fabric. I miglioramenti includono funzionalità di rete avanzate quali supporto delle reti virtuali in un'applicazione, funzionalità di protezione, diagnostica migliorata e supporto per gli strumenti. Service Fabric consente di creare applicazioni che combinano contenitori inclusi nel codice esistente (ad esempio, applicazioni MVC IIS) con servizi sviluppati usando i modelli di programmazione di Service Fabric.  È possibile eseguire diverse applicazioni di questo tipo in un singolo cluster. 

## <a name="what-are-containers"></a>Informazioni sui contenitori
I contenitori sono componenti incapsulati distribuibili singolarmente che vengono eseguiti come istanze isolate sullo stesso kernel per sfruttare i meccanismi di virtualizzazione offerti da un sistema operativo. Pertanto ogni applicazione, il relativo runtime e le relative dipendenze e librerie di sistema vengono eseguiti all'interno di un contenitore con accesso privato completo alla vista isolata specifica del contenitore dei costrutti del sistema operativo. Insieme alla portabilità, questo grado di sicurezza e isolamento delle risorse è il principale vantaggio associato all'uso di contenitori con Service Fabric, che in alternativa esegue i servizi in processi.

I contenitori sono una tecnologia di virtualizzazione che virtualizza il sistema operativo sottostante rispetto alle applicazioni. I contenitori offrono un ambiente immutabile per eseguire le applicazioni con un grado variabile di isolamento. Vengono eseguiti direttamente sul kernel e hanno una vista isolata del file system e delle altre risorse. Rispetto alle macchine virtuali, i contenitori offrono i vantaggi seguenti:

* **Dimensioni ridotte**: i contenitori usano uno spazio di archiviazione singolo e sovrappongono i diversi livelli di versioni e aggiornamenti per aumentare l'efficienza.
* **Velocità**: i contenitori non devono avviare un intero sistema operativo, pertanto il loro avvio è molto più rapido e richiede in genere pochi secondi.
* **Portabilità**: l'immagine di un'applicazione in contenitore può essere trasferita per essere eseguita nel cloud, in locale, all'interno di macchine virtuali o direttamente in computer fisici.
* **Governance delle risorse**: è possibile limitare le risorse fisiche utilizzabili da un contenitore nell'host.

## <a name="container-types"></a>Tipi di contenitori
Service Fabric supporta i contenitori sia in Linux sia in Windows e in Windows supporta anche la modalità di isolamento di Hyper-V. 

### <a name="docker-containers-on-linux"></a>Contenitori Docker in Linux
Docker fornisce le API di alto livello per creare e gestire contenitori su contenitori del kernel Linux. Docker Hub è un repository centrale per archiviare e recuperare immagini contenitore.
Per un'esercitazione, vedere [Distribuire un contenitore Docker in Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Contenitori Windows Server
Windows Server 2016 fornisce due diversi tipi di contenitori che si differenziano per il livello di isolamento offerto. I contenitori Windows Server e i contenitori Docker sono simili perché entrambi usufruiscono dell'isolamento dello spazio dei nomi e del file system, ma condividono il kernel con l'host in cui vengono eseguiti. In Linux questo isolamento viene tradizionalmente garantito con `cgroups` e `namespaces` e i contenitori Windows Server presentano un comportamento simile.

I contenitori Windows Hyper-V offrono un grado superiore di isolamento e sicurezza perché i singoli contenitori non condividono il kernel del sistema operativo con altri contenitori o con l'host. Con questo livello superiore di isolamento di sicurezza, i contenitori Hyper-V sono destinati a scenari multi-tenant ostili.
Per un'esercitazione, vedere [Distribuire un contenitore Windows in Service Fabric](service-fabric-get-started-containers.md).

La figura seguente illustra i diversi tipi di livelli di virtualizzazione e isolamento disponibili nel sistema operativo.
![Piattaforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenari per l'uso di contenitori
Ecco alcuni esempi tipici dei casi in cui un contenitore rappresenta una buona scelta:

* **Trasferimento da IIS**: se sono disponibili app [ASP.NET MVC](https://www.asp.net/mvc) che si vuole continuare a usare, anziché eseguirne la migrazione ad ASP.NET Core è possibile inserirle in un contenitore. Queste app ASP.NET MVC dipendono da IIS (Internet Information Services). È possibile creare pacchetti delle applicazioni in immagini contenitore dall'immagine IIS creata in precedenza e distribuirle con Service Fabric. Per informazioni sulla creazione delle immagini IIS, vedere [Immagini contenitore in Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) .
* **Combinazione di contenitori e microservizi di Service Fabric**: usare un'immagine contenitore esistente per parte dell'applicazione. È ad esempio possibile usare il [contenitore NGINX](https://hub.docker.com/_/nginx/) per il front-end Web dell'applicazione e i servizi con stato per le operazioni di calcolo back-end più intensive.
* **Riduzione dell'impatto dei servizi che influiscono negativamente**: è possibile usare la funzionalità di governance delle risorse dei contenitori per limitare le risorse usate da un servizio in un host. Se sono presenti servizi che potrebbero usare un elevato numero di risorse e quindi influire sulle prestazioni degli altri (ad esempio, un'operazione come una query a esecuzione prolungata), può essere opportuno inserire tali servizi in contenitori con governance delle risorse.

## <a name="service-fabric-support-for-containers"></a>Supporto di Service Fabric per i contenitori
Service Fabric attualmente supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016, nonché la modalità di isolamento di Hyper-V. 

Nel [modello applicativo](service-fabric-application-model.md)di Service Fabric, un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi. Service Fabric può eseguire qualsiasi contenitore e lo scenario è simile allo [scenario degli eseguibili guest](service-fabric-deploy-existing-app.md), in cui il pacchetto di un'applicazione esistente viene creato all'interno di un contenitore. Questo scenario è il caso d'uso comune per i contenitori. Gli esempi includono l'esecuzione di un'applicazione scritta in qualsiasi linguaggio o framework, ma non con i modelli di programmazione incorporati di Service Fabric.

È anche possibile eseguire i servizi Service Fabric all'interno dei contenitori. Il supporto dell'esecuzione dei servizi di Service Fabric all'interno dei contenitori è attualmente limitato, ma verrà esteso nelle versioni future.

* **Servizi Reliable senza stato all'interno dei contenitori**: i servizi Reliable senza stato che usano i modelli di programmazione Reliable Services sono supportati solo su Linux. Il supporto per i servizi Reliable senza stato nei contenitori Windows è previsto per una versione futura.
* **Servizi con stato all'interno di contenitori**: questi servizi usano il modello di programmazione Reliable Actors o Reliable Services. Il supporto per l'esecuzione di servizi con stato nei contenitori verrà aggiunto in una versione futura.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, Service Fabric offre le funzionalità seguenti per i servizi nei contenitori:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse.
* Autenticazione nel repository.
* Mapping tra porta del contenitore e porta dell'host.
* Individuazione e comunicazione da contenitore a contenitore.
* Possibilità di configurare e impostare variabili di ambiente.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i contenitori, la funzione di agente di orchestrazione dei contenitori svolta da Service Fabric e le funzionalità offerte da Service Fabric per supportare i contenitori. Come passaggio successivo verranno esaminati esempi delle singole funzionalità per mostrarne le modalità d'uso.

[Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-get-started-containers.md)

[Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-get-started-containers-linux.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

