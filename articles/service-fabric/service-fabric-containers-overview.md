---
title: Panoramica di Service Fabric e contenitori | Documentazione Microsoft
description: Panoramica di Service Fabric e dell'uso dei contenitori per la distribuzione di applicazioni di microservizi. Questo articolo offre una panoramica di come possono essere usati i contenitori e delle funzionalità disponibili in Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: msfussell
ms.openlocfilehash: 6715142be7f40955861afa634bf6e2472c9f7294
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005776"
---
# <a name="service-fabric-and-containers"></a>Service Fabric e contenitori

## <a name="introduction"></a>Introduzione

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la disposizione in pacchetti, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.

Service Fabric è un [agente di orchestrazione dei contenitori](service-fabric-cluster-resource-manager-introduction.md) per la distribuzione di microservizi in un cluster di computer. Service Fabric trae vantaggio dalle lezioni apprese negli anni eseguendo servizi Microsoft su larga scala.

I microservizi possono essere distribuiti in molti modi, ad esempio usando i [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) o [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) oppure distribuendo [un codice a propria scelta](service-fabric-guest-executables-introduction.md). Se invece si vuole semplicemente [distribuire e gestire i contenitori](service-fabric-containers-overview.md), anche Service Fabric è un'ottima scelta.

Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. I processi offrono la massima velocità di attivazione e l'utilizzo della massima densità delle risorse in un cluster. Service Fabric può anche distribuire servizi in immagini contenitore. È inoltre possibile combinare servizi in processi e servizi in contenitori nella stessa applicazione.

Per iniziare subito a usare i contenitori in Service Fabric, provare una guida introduttiva, un'esercitazione o un esempio:  

[Guida introduttiva: Distribuire un'applicazione contenitore Linux in Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Guida introduttiva: Distribuire un'applicazione contenitore Windows in Service Fabric](service-fabric-quickstart-containers.md)  
[Distribuire un'app .NET esistente in un contenitore](service-fabric-host-app-in-a-container.md)  
[Esempi di contenitori di Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Informazioni sui contenitori

I contenitori risolvono il problema relativo all'esecuzione affidabile delle applicazioni in diversi ambienti di elaborazione fornendo un ambiente immutabile in cui eseguire un'applicazione. I contenitori eseguono il wrapping di un'applicazione e di tutte le relative dipendenze, ad esempio le librerie e i file di configurazione, in una "scatola" isolata contenente tutto il necessario per eseguire il software all'interno del contenitore. Indipendentemente dalla posizione del contenitore, l'applicazione presente all'interno ha sempre tutto ciò che serve per essere eseguita, ad esempio le versioni corrette delle librerie dipendenti, i file di configurazione ed eventuali altri dati necessari.

I contenitori vengono eseguiti direttamente sul kernel e hanno una vista isolata del file system e delle altre risorse. Un'applicazione all'interno di un contenitore non ha alcuna conoscenza in merito ad altri processi o applicazioni all'esterno del contenitore. Ogni applicazione, con il relativo runtime e le relative dipendenze e librerie di sistema, viene eseguita all'interno di un contenitore con accesso privato completo alla specifica vista isolata del sistema operativo. Oltre alla disponibilità di tutte le dipendenze necessarie per l'esecuzione dell'applicazione in diversi ambienti di elaborazione, la sicurezza e l'isolamento delle risorse sono vantaggi importanti offerti dall'uso dei contenitori con Service Fabric, che altrimenti esegue i servizi in un processo.

Rispetto alle macchine virtuali, i contenitori offrono i vantaggi seguenti:

* **Dimensioni ridotte**: i contenitori usano uno spazio di archiviazione singolo e sovrappongono i diversi livelli di versioni e aggiornamenti per aumentare l'efficienza.
* **Velocità**: i contenitori non devono avviare un intero sistema operativo, pertanto possono essere avviati in modo molto più rapido, normalmente in pochi secondi.
* **Portabilità**: l'immagine di un'applicazione in contenitore può essere trasferita per essere eseguita nel cloud, in locale, all'interno di macchine virtuali o direttamente in computer fisici.
* **Governance delle risorse**: è possibile limitare le risorse fisiche utilizzabili da un contenitore nell'host.

### <a name="container-types-and-supported-environments"></a>Tipi di contenitori e ambienti supportati

Service Fabric supporta i contenitori sia in Linux che in Windows e in Windows supporta la modalità di isolamento di Hyper-V.

#### <a name="docker-containers-on-linux"></a>Contenitori Docker in Linux

Docker fornisce le API per creare e gestire contenitori su contenitori del kernel Linux. Docker Hub è un repository centrale per archiviare e recuperare immagini contenitore.
Per un'esercitazione basata su Linux, vedere [Creare la prima applicazione contenitore di Service Fabric in Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Contenitori Windows Server

Windows Server 2016 offre due diversi tipi di contenitori che si differenziano per livello di isolamento. I contenitori Windows Server e i contenitori Docker sono simili perché usufruiscono entrambi dell'isolamento dello spazio dei nomi e del file system, ma condividono il kernel con l'host in cui vengono eseguiti. In Linux, questo isolamento viene tradizionalmente fornito con cgroup e spazi dei nomi e i contenitori Windows Server presentano un comportamento simile.

I contenitori Windows con il supporto per Hyper-V offrono un grado superiore di isolamento e sicurezza perché nessun contenitore condivide il kernel del sistema operativo con altri contenitori o con l'host. Con questo livello superiore di isolamento di sicurezza, i contenitori abilitati per Hyper-V sono destinati a scenari multi-tenant potenzialmente ostili.
Per un'esercitazione basata su Windows, vedere [Creare la prima applicazione contenitore di Service Fabric in Windows](service-fabric-get-started-containers.md).

La figura seguente illustra i diversi tipi di livelli di virtualizzazione e isolamento disponibili.
![Piattaforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenari per l'uso di contenitori

Ecco alcuni esempi tipici dei casi in cui un contenitore rappresenta una buona scelta:

* **Modalità lift-and-shift IIS**: è possibile includere un'app [ASP.NET MVC](https://www.asp.net/mvc) esistente in un contenitore anziché eseguirne la migrazione ad ASP.NET Core. Queste app ASP.NET MVC dipendono da IIS (Internet Information Services). È possibile creare pacchetti delle applicazioni in immagini contenitore dall'immagine IIS creata in precedenza e distribuirle con Service Fabric. Per informazioni sulla creazione di contenitori Windows, vedere [Immagini contenitore in Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server).

* **Combinazione di contenitori e microservizi di Service Fabric**: usare un'immagine contenitore esistente per parte dell'applicazione. È ad esempio possibile usare il [contenitore NGINX](https://hub.docker.com/_/nginx/) per il front-end Web dell'applicazione e i servizi con stato per le operazioni di calcolo back-end più intensive.

* **Riduzione dell'impatto dei servizi che influiscono negativamente**: è possibile usare la funzionalità di governance delle risorse dei contenitori per limitare le risorse usate da un servizio in un host. Se sono presenti servizi che potrebbero usare un elevato numero di risorse e quindi influire sulle prestazioni degli altri (ad esempio, un'operazione come una query a esecuzione prolungata), può essere opportuno inserire tali servizi in contenitori con governance delle risorse.

## <a name="service-fabric-support-for-containers"></a>Supporto di Service Fabric per i contenitori

Service Fabric supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016, nonché la modalità di isolamento di Hyper-V. 

Service Fabric offre un [modello applicativo](service-fabric-application-model.md) in cui un contenitore rappresenta un host applicazioni in cui sono inserite più repliche dei servizi. Service Fabric supporta anche un [scenario eseguibile da guest](service-fabric-guest-executables-introduction.md) in cui non si usano i modelli di programmazione predefiniti di Service Fabric ma si assembla un'applicazione esistente, scritta con qualsiasi linguaggio o framework, all'interno di un contenitore. Questo scenario è il caso d'uso comune per i contenitori.

È anche possibile eseguire i [servizi di Service Fabric all'interno di un contenitore](service-fabric-services-inside-containers.md). Il supporto dell'esecuzione dei servizi di Service Fabric all'interno dei contenitori è attualmente limitato.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, ad esempio:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse, tra cui l'impostazione dei valori delle risorse per impostazione predefinita nei cluster di Azure.
* Autenticazione nel repository.
* Mapping tra porta del contenitore e porta dell'host.
* Individuazione e comunicazione da contenitore a contenitore.
* Possibilità di configurare e impostare variabili di ambiente.
* Possibilità di impostare le credenziali di sicurezza nel contenitore.
* Scelta tra modalità di rete diverse per i contenitori.

Per una panoramica completa del supporto dei contenitori in Azure, ad esempio come creare un cluster Kubernetes con il servizio Kubernetes di Azure, come creare un registro Docker privato in Registro contenitori di Azure e altro ancora, vedere [Azure per contenitori](https://docs.microsoft.com/azure/containers/).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato il supporto offerto da Service Fabric per l'esecuzione di contenitori. Nei passaggi successivi verranno presentati esempi relativi alle singole funzionalità per mostrarne la modalità d'uso.

[Creare la prima applicazione contenitore di Service Fabric in Linux](service-fabric-get-started-containers-linux.md)  
[Creare la prima applicazione contenitore di Service Fabric in Windows](service-fabric-get-started-containers.md)  
[Altre informazioni sui contenitori Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png