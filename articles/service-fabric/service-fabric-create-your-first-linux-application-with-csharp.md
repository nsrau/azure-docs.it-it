---
title: Creare la prima app di microservizi di Azure in Linux con C# | Documentazione Microsoft
description: Creare e distribuire un'applicazione di Service Fabric con C#
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 4baf144cc28eeff0ab8f8b60e837f8a2bad903af
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
<a id="create-your-first-azure-service-fabric-application" class="xliff"></a>

# Creare la prima applicazione di Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. In questa esercitazione verrà esaminata la creazione di un'applicazione per Linux e la compilazione di un servizio con C# (.NET Core).

<a id="prerequisites" class="xliff"></a>

## Prerequisiti
Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo di Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X è possibile [configurare un ambiente con un solo computer Linux in una macchina virtuale usando Vagrant](service-fabric-get-started-mac.md).

Sarà anche possibile configurare l'[interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md) (consigliato) o l'[interfaccia della riga di comando di XPlat](service-fabric-azure-cli.md) per distribuire l'applicazione.

<a id="create-the-application" class="xliff"></a>

## Creazione dell'applicazione
Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione del primo servizio e l'aggiunta dei successivi. Verrà usato Yeoman per creare un'applicazione con un solo servizio.

1. In un terminale, digitare il comando seguente per iniziare a creare lo scaffolding: `yo azuresfcsharp`
2. Assegnare un nome all'applicazione.
3. Scegliere il tipo del primo servizio e assegnargli un nome. Ai fini di questa esercitazione viene scelto un servizio Reliable Actor.

   ![Generatore Yeoman di Service Fabric per C#][sf-yeoman]

> [!NOTE]
> Per altre informazioni sulle opzioni, vedere [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md).
>
>

<a id="build-the-application" class="xliff"></a>

## Compilare l'applicazione.
I modelli Yeoman di Service Fabric includono uno script di compilazione che è possibile usare per compilare l'app dal terminale (dopo il passaggio alla cartella dell'applicazione).

  ```sh
 cd myapp
 ./build.sh
  ```

<a id="deploy-the-application" class="xliff"></a>

## Distribuire l'applicazione

Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale.

<a id="using-xplat-cli" class="xliff"></a>

### Uso dell'interfaccia della riga di comando di XPlat

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. Eseguire lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

<a id="using-azure-cli-20" class="xliff"></a>

### Tramite l'interfaccia della riga di comando di Azure 2.0

La distribuzione dell'applicazione compilata è uguale a quella di qualsiasi altra applicazione di Service Fabric. Per istruzioni dettagliate, vedere la documentazione sulla [gestione di un'applicazione di Service Fabric con l'interfaccia della riga di comando di Azure](service-fabric-application-lifecycle-azure-cli-2-0.md).

I parametri per questi comandi si trovano nei manifesti generati nel pacchetto dell'applicazione.

Dopo la distribuzione dell'applicazione, aprire un browser e passare a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) all'indirizzo [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Espandere quindi il nodo **Applicazioni**, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

<a id="start-the-test-client-and-perform-a-failover" class="xliff"></a>

## Avviare il client di test ed eseguire un failover
I progetti Actor non eseguono alcuna operazione in modo indipendente. Richiedono un altro servizio o client per l'invio dei messaggi. Il modello Actor include un semplice script di test che è possibile usare per interagire con il servizio Actor.

1. Eseguire lo script tramite l'utilità delle espressioni di controllo per visualizzare l'output del servizio Actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. In Service Fabric Explorer individuare il nodo che ospita la replica primaria del servizio Actor. Nello screenshot seguente si tratta del nodo 3.

    ![Ricerca della replica primaria in Service Fabric Explorer][sfx-primary]
3. Fare clic sul nodo trovato nel passaggio precedente, quindi selezionare **Disattiva (riavvio)** dal menu Azioni. Questa operazione consente di riavviare un nodo nel cluster locale forzando il failover in una replica secondaria in esecuzione in un altro nodo. Durante l'operazione, prestare attenzione all'output del client di test e notare che l'incremento del contatore prosegue nonostante il failover.

<a id="adding-more-services-to-an-existing-application" class="xliff"></a>

## Aggiunta di altri servizi a un'applicazione esistente

Per aggiungere un altro servizio a un'applicazione già creata mediante `yo`, seguire questa procedura: 
1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfcsharp:AddService`

<a id="migrating-from-projectjson-to-csproj" class="xliff"></a>

## Migrazione da project.json a .csproj
1. L'esecuzione di "dotnet migrate" nella directory radice del progetto consentirà la migrazione di tutti i file project.json al formato csproj.
2. Aggiornare di conseguenza i riferimenti al progetto ai file in formato csproj nei file di progetto.
3. Aggiornare i nomi dei file di progetto ai file in formato csproj in build.sh.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi
* [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Uso dell'interfaccia della riga di comando di Azure per interagire con un cluster di Service Fabric](service-fabric-azure-cli.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

<a id="related-articles" class="xliff"></a>

## Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introduzione all'interfaccia della riga di comando di XPlat per Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

