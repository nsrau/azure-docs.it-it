<properties
   pageTitle="Creare la prima applicazione di Service Fabric in Linux con Java | Microsoft Azure"
   description="Creare e distribuire un'applicazione di Service Fabric con Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>  


# Creare la prima applicazione di Azure Service Fabric

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. In questa esercitazione verrà esaminata la creazione di un'applicazione per Linux e la compilazione di un servizio con Java.

## Prerequisiti

Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo di Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X è possibile [configurare un ambiente con un solo computer Linux in una macchina virtuale usando Vagrant](service-fabric-get-started-mac.md).

## Creazione dell'applicazione

Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione del primo servizio e l'aggiunta dei successivi. Verrà usato Yeoman per creare una nuova applicazione con un solo servizio.

1. In un terminale digitare **yo azuresfjava**.

2. Assegnare un nome all'applicazione.

3. Scegliere il tipo del primo servizio e assegnargli un nome. Ai fini di questa esercitazione verrà scelto un servizio Reliable Actor.

  ![Generatore Yeoman di Service Fabric per Java][sf-yeoman]  

>[AZURE.NOTE] Per altre informazioni sulle opzioni, vedere [Panoramica dei modelli di programmazione di Service Fabric](service-fabric-choose-framework.md).

## Compilare l'applicazione.

I modelli Yeoman di Service Fabric includono uno script di compilazione per [Gradle](https://gradle.org/), che è possibile usare per compilare l'applicazione dal terminale.

  ```bash
  gradle
  ```

## Distribuire l'applicazione

Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Azure.

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    cd myapp
    ./install.sh
    ```

3. Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://localhost:19080/Explorer. Sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X.

4. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

## Avviare il client di test ed eseguire un failover

I progetti Actor non eseguono alcuna operazione in modo indipendente. Richiedono un altro servizio o client per l'invio dei messaggi. Il modello Actor include un semplice script di test che è possibile usare per interagire con il servizio Actor.

1. Eseguire lo script tramite l'utilità delle espressioni di controllo per visualizzare l'output del servizio Actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. In Service Fabric Explorer individuare il nodo che ospita la replica primaria del servizio Actor. Nello screenshot seguente si tratta del nodo 3.

    ![Ricerca della replica primaria in Service Fabric Explorer][sfx-primary]  

3. Fare clic sul nodo trovato nel passaggio precedente, quindi selezionare **Disattiva (riavvio)** dal menu Azioni. Con questa operazione verrà riavviato uno dei cinque nodi nel cluster locale e forzato il failover in una delle repliche secondarie in esecuzione in un altro nodo. Durante l'operazione, prestare attenzione all'output dal client di test e notare che l'incremento del contatore prosegue nonostante il failover.

## Compilare e distribuire un'applicazione con il plug-in Eclipse Neon

Se è installato il plug-in di Service Fabric per Eclipse Neon, è possibile usarlo per creare, compilare e distribuire le applicazioni di Service Fabric compilate con Java.

### Creazione dell'applicazione

Il plug-in di Service Fabric è disponibile mediante l'estendibilità di Eclipse.

1. In Eclipse scegliere **File > Other > Service Fabric** (File > Altro > Service Fabric). Verrà visualizzato un set di opzioni, inclusi attori e contenitori.

    ![Modelli di Service Fabric in Eclipse][sf-eclipse-templates]  

2. In questo caso scegliere Servizio senza stato.

3. Verrà chiesto di confermare l'uso della prospettiva Service Fabric, in modo da ottimizzare Eclipse per l'uso con progetti di Service Fabric. Scegliere 'Yes' (Sì).

### Distribuire l'applicazione

I modelli di Service Fabric includono un set di attività Gradle, attivabili tramite Eclipse, per la compilazione e distribuzione delle applicazioni.

1. Scegliere **Run > Run Configurations** (Esegui > Esegui configurazioni).

2. Espandere **Gradle Project** (Progetto Gradle) e scegliere **ServiceFabricDeployer**.

3. Fare clic su **Run**.

L'app verrà compilata e distribuita dopo alcuni istanti. È possibile monitorarne lo stato da Service Fabric Explorer.

## Passaggi successivi

- [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!-- Images -->  
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->