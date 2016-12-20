---
title: Service Fabric e distribuzione di contenitori in Linux | Documentazione Microsoft
description: "Service Fabric e uso di contenitori Docker per la distribuzione di applicazioni di microservizi. Questo articolo illustra le funzionalità offerte da Service Fabric per i contenitori e la modalità di distribuzione di un immagine contenitore Docker in un cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Distribuire un contenitore Docker in Service Fabric
> [!div class="op_single_selector"]
> * [Distribuire un contenitore Windows](service-fabric-deploy-container.md)
> * [Distribuire un contenitore Docker](service-fabric-deploy-container-linux.md)
>
>

Questo articolo descrive la creazione di servizi in contenitori Docker su Linux.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, denominati servizi in contenitori.

Le funzionalità includono:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse
* Autenticazione nel repository
* Mapping tra porta del contenitore e porta dell'host
* Individuazione e comunicazione da contenitore a contenitore
* Possibilità di configurare e impostare variabili di ambiente

## <a name="packaging-a-docker-container-with-yeoman"></a>Creazione del pacchetto di un contenitore Docker con yeoman
Quando si crea il pacchetto di un contenitore in Linux, si può scegliere di usare un modello yeoman oppure di [creare manualmente il pacchetto dell'applicazione](service-fabric-deploy-container.md#manually).

Un'applicazione di Service Fabric può contenere uno o più contenitori, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione dell'applicazione e l'aggiunta di un'immagine contenitore. È possibile usare Yeoman per creare una nuova applicazione con un singolo contenitore Docker denominato *SimpleContainerApp*. È possibile aggiungere altri servizi in un secondo momento modificando i file manifesto generati.

## <a name="create-the-application"></a>Creare l'applicazione
1. In un terminale digitare **yo azuresfguest**.
2. Per il framework scegliere **contenitore**.
3. Assegnare un nome dell'applicazione, ad esempio SimpleContainerApp.
4. Fornire l'URL per l'immagine contenitore da un repository DockerHub. Il formato sarà [repository]/[nome immagine].

![Generatore Yeoman di Service Fabric per i contenitori][sf-yeoman]

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Azure.

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```
2. Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```
3. Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://localhost:19080/Explorer. Sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X.
4. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.
5. Usare lo script di disinstallazione fornito nel modello per eliminare l'istanza dell'applicazione e annullare la registrazione del tipo di applicazione.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Panoramica di Service Fabric e contenitori](service-fabric-containers-overview.md)
* [Uso dell'interfaccia della riga di comando di Azure per interagire con un cluster di Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


