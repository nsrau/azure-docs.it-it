---
title: Panoramica di Service Fabric e contenitori | Microsoft Docs
description: Panoramica di Service Fabric e dell'uso dei contenitori per la distribuzione di applicazioni di microservizi. Questo articolo offre una panoramica di come possono essere usati i contenitori e delle funzionalità disponibili in Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-service-fabric-and-containers"></a>Anteprima: Service Fabric e contenitori
> [!NOTE]
> Questa funzionalità è in anteprima per Linux e non è attualmente disponibile in Windows Server. Sarà disponibile in anteprima per Windows Server nella prossima versione di Service Fabric dopo la disponibilità generale di Windows Server 2016 e sarà quindi supportata nella versione successiva.
> 
> 

## <a name="introduction"></a>Introduzione
Service Fabric è un [agente di orchestrazione](service-fabric-cluster-resource-manager-introduction.md) dei servizi in un cluster di computer. I servizi possono essere distribuiti in molti modi, che includono dall'uso dei [modelli di programmazione di Service Fabric](service-fabric-choose-framework.md) alla distribuzione di [eseguibili guest](service-fabric-deploy-existing-app.md). Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. I processi offrono la massima velocità di attivazione e l'utilizzo della massima densità delle risorse nel cluster. Service Fabric può anche distribuire i servizi in immagini contenitore e, ancora più importante, consente combinare servizi in processi e servizi in contenitori nella stessa applicazione. È possibile usufruire del meglio di entrambe le tipologie in base allo scenario specifico.

## <a name="what-are-containers?"></a>Informazioni sui contenitori
I contenitori sono componenti incapsulati distribuibili singolarmente che vengono eseguiti come istanze isolate sullo stesso kernel, sfruttando la virtualizzazione a livello di sistema operativo. In questo modo, ogni applicazione, il relativo runtime e le relative dipendenze e librerie di sistema vengono eseguiti all'interno di un contenitore con accesso privato completo a una specifica vista isolata dei costrutti del sistema operativo. Insieme alla portabilità, questo grado di sicurezza e isolamento delle risorse è il principale vantaggio associato all'uso di contenitori con Service Fabric, che in alternativa esegue i servizi in processi. 

I contenitori sono una tecnologia di virtualizzazione che virtualizza il sistema operativo sottostante rispetto alle applicazioni. I contenitori offrono un ambiente immutabile per eseguire le applicazioni con un grado variabile di isolamento. Vengono eseguiti direttamente sul kernel e hanno una vista isolata del file system e delle altre risorse. Rispetto alle VM, i contenitori offrono i vantaggi seguenti:

1. **Dimensioni ridotte**. I contenitori usano un unico spazio di archiviazione e differenziali di dimensioni inferiori per ogni livello, garantendo così una maggiore efficienza.
2. **Tempi di avvio ridotti**. I contenitori non devono avviare un sistema operativo e possono quindi essere attivi e disponibili molto più rapidamente rispetto alle VM, in genere in alcuni secondi.
3. **Portabilità**. L'immagine di un'applicazione in contenitore può essere trasferita per essere eseguita nel cloud, in locale, all'interno di VM o direttamente su computer fisici.
4. **Governance delle risorse**. È possibile limitare le risorse fisiche utilizzabili da un contenitore nell'host.

## <a name="container-types"></a>Tipi di contenitori
Service Fabric supporta i tipi di contenitori riportati di seguito.

### <a name="docker-containers-on-linux"></a>Contenitori Docker in Linux
Docker fornisce API di livello superiore per creare e gestire contenitori su contenitori del kernel Linux e offre l'hub Docker come repository centrale per l'archiviazione e il recupero delle immagini contenitore. 

### <a name="windows-server-containers"></a>Contenitori Windows Server
Windows Server 2016 fornisce due diversi tipi di contenitori che si differenziano per il livello di isolamento offerto. I contenitori Windows Server sono simili ai contenitori Docker per l'isolamento dello spazio dei nomi e del file system, ma condividono il kernel con l'host in cui vengono eseguiti. In Linux, questo isolamento viene tradizionalmente fornito con cgroup e spazi dei nomi e i contenitori Windows Server presentano un comportamento simile. 

I contenitori Windows Hyper-V offrono un grado superiore di isolamento e sicurezza perché i singoli contenitori non condividono il kernel del sistema operativo tra loro o con l'host. Con questo livello superiore di isolamento di sicurezza, i contenitori Hyper-V sono destinati soprattutto a scenari multi-tenant ostili.

La figura seguente illustra i diversi tipi di livelli di virtualizzazione e isolamento disponibili nel sistema operativo.
![Piattaforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenari per l'uso di contenitori
Alcuni esempi tipici dei casi in cui è opportuno usare contenitori includono:

1. **Trasferimento da IIS**. Se sono disponibili app ASP.NET MVC che si vuole continuare a usare, anziché eseguirne la migrazione ad ASP.NET Core è possibile inserirle in un contenitore. Queste app ASP.NET MVC dipendono da IIS. È possibile creare pacchetti delle app nelle immagini contenitore dall'immagine IIS creata in precedenza e distribuire le app con Service Fabric. Per informazioni sulla creazione delle immagini IIS, vedere [Immagini contenitore in Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) .
2. **Combinazione di contenitori e microservizi di Service Fabric**. Usare un'immagine contenitore esistente per parte dell'applicazione. È ad esempio possibile usare il [contenitore NGINX](https://hub.docker.com/_/nginx/) per il front-end Web dell'applicazione e i servizi con stato creati con Reliable Services per il calcolo back-end più intensivo. Questo scenario include ad esempio le applicazioni di gioco.
3. **Riduzione dell'impatto dei servizi che influiscono negativamente**. La funzionalità di governance delle risorse dei contenitori consente di limitare le risorse usate da un servizio in un host. Se sono presenti servizi che potrebbero utilizzare un elevato numero di risorse e quindi influire sulle prestazioni degli altri (ad esempio, un'operazione come una query a esecuzione prolungata), può essere opportuno inserire tali servizi in contenitori con governance delle risorse.

## <a name="service-fabric-support-for-containers"></a>Supporto di Service Fabric per i contenitori
Service Fabric attualmente supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016. Il supporto per i contenitori Hyper-V verrà aggiunto in una versione futura. 

Nel [modello applicativo](service-fabric-application-model.md)di Service Fabric, un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi. Per i contenitori sono supportati gli scenari riportati di seguito.

1. **Contenitori guest**: sono identici allo [scenario degli eseguibili guest](service-fabric-deploy-existing-app.md) in cui è possibile distribuire applicazioni esistenti in un contenitore. Ad esempio, codice nodejs, javascript o di qualsiasi tipo (file EXE).
2. **Servizi senza stato all'interno di contenitori**: si tratta di servizi senza stato che usano i modelli di programmazione Reliable Services e Reliable Actors. Questo scenario è attualmente supportato solo in Linux. Il supporto per servizi senza stato in contenitori Windows verrà aggiunto in una versione futura.
3. **Servizi con stato all'interno di contenitori**: si tratta di servizi con stato che usano il modello di programmazione Reliable Actors in Linux. Reliable Services non è attualmente supportato in Linux.  Il supporto per servizi con stato in contenitori Windows verrà aggiunto in una versione futura.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, denominati servizi in contenitori. Le funzionalità includono:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse
* Autenticazione nel repository
* Mapping tra porta del contenitore e porta dell'host
* Individuazione e comunicazione da contenitore a contenitore
* Possibilità di configurare e impostare variabili di ambiente

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i contenitori, la funzione di agente di orchestrazione dei contenitori svolta da Service Fabric e le funzionalità offerte da Service Fabric per supportare i contenitori. Come passaggio successivo verranno esaminati esempi delle singole funzionalità per mostrarne le modalità d'uso. 

[Distribuire un contenitore in Service Fabric](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png




<!--HONumber=Oct16_HO2-->


