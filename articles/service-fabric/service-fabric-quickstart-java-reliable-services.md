---
title: Creare un'applicazione Java di Azure Service Fabric | Microsoft Docs
description: In questa guida introduttiva viene creata un'applicazione Java per Azure usando un'applicazione Reliable Services Service Fabric di esempio.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 5ae6ba28ba448591d58cc3963f5df9a563997ab0
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959546"
---
# <a name="quickstart-deploy-a-java-service-fabric-reliable-services-application-to-azure"></a>Guida introduttiva: Distribuire un'applicazione Reliable Services di Service Fabric per Java in Azure
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori. 

In questa guida introduttiva viene illustrato come distribuire l'applicazione Java in Service Fabric tramite l'IDE Eclipse in un computer di sviluppo Linux. Al termine, sarà disponibile un'applicazione di voto con un front-end Web Java che salva i risultati delle votazioni in un servizio back-end con stato nel cluster.

![Screenshot dell'applicazione](./media/service-fabric-quickstart-java/votingapp.png)

In questa guida introduttiva si apprende come:

* Usare Eclipse come strumento per le applicazioni Java di Service Fabric
* Distribuire l'applicazione nel cluster locale 
* Distribuire l'applicazione in un cluster in Azure
* Scalare orizzontalmente l'applicazione in più nodi

## <a name="prerequisites"></a>prerequisiti
Per completare questa guida introduttiva:
1. [Installare Service Fabric SDK e l'interfaccia della riga di comando di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Installare Git](https://git-scm.com/)
3. [Installare Eclipse](https://www.eclipse.org/downloads/)
4. [Configurare l'ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), assicurandosi di seguire i passaggi facoltativi per installare il plug-in Eclipse 

## <a name="download-the-sample"></a>Scaricare l'esempio
In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
1. Avviare il cluster locale eseguendo il comando seguente:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    L'avvio del cluster locale può richiedere tempo. Per verificare se il cluster è in esecuzione, accedere a Service Fabric Explorer in **http://localhost:19080**. I cinque nodi integri indicano che il cluster locale è in esecuzione. 
    
    ![Cluster locale integro](./media/service-fabric-quickstart-java/localclusterup.png)

2. Aprire Eclipse.
3. Fare clic su File -> Open Projects from File System (Apri progetti da file system). 
4. Fare clic su Directory e scegliere la directory `Voting` nella cartella `service-fabric-java-quickstart` clonata da Github. Fare clic su Finish. 

    ![Finestra di dialogo Import (Importa) di Eclipse](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. A questo punto, il progetto `Voting` si trova nella finestra Package Explorer (Esplora pacchetti) di Eclipse. 
6. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Publish Application** (Pubblica applicazione) nell'elenco a discesa **Service Fabric**. Scegliere **PublishProfiles/Local.json** come profilo target e fare clic su Publish (Pubblica). 

    ![Finestra di dialogo Publish (Pubblica) locale](./media/service-fabric-quickstart-java/localjson.png)
    
7. Aprire il Web browser preferito e accedere all'applicazione tramite **http://localhost:8080**. 

    ![Front-end locale dell'applicazione](./media/service-fabric-quickstart-java/runninglocally.png)
    
È ora possibile aggiungere un set di opzioni per le votazioni e iniziare a raccogliere i voti. L'applicazione viene eseguita e archivia tutti i dati nel cluster di Service Fabric, senza che sia necessario un database separato.

## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurare il cluster di Azure Service Fabric
Per distribuire l'applicazione in un cluster di Azure, creare un cluster personale.

I cluster di entità sono cluster di Service Fabric gratuiti disponibili per un periodo di tempo limitato, ospitati in Azure ed eseguiti dal team di Service Fabric. È possibile usare i cluster di entità per distribuire le applicazioni e ottenere informazioni sulla piattaforma. Il cluster usa un solo certificato autofirmato per la sicurezza da nodo a nodo e da client a nodo.

Eseguire l'accesso e aggiungere un [cluster Linux](http://aka.ms/tryservicefabric). Scaricare il certificato PFX nel computer facendo clic sul collegamento **PFX**. Fare clic sul collegamento **ReadMe** (Leggimi) per trovare la password del certificato e le istruzioni per configurare diversi ambienti per usare il certificato. Tenere aperte entrambe le pagine **Welcome** (Benvenuto) e **ReadMe** (Leggimi) perché si useranno alcune delle istruzioni nei passaggi seguenti. 

> [!Note]
> È disponibile un numero limitato di cluster di entità ogni ora. Se viene restituito un errore quando si prova a registrarsi a un cluster di entità, è possibile attendere e riprovare in seguito oppure seguire i passaggi descritti in [Creare un cluster di Service Fabric in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) per creare un cluster nella propria sottoscrizione. 
>
> Il servizio Spring Boot è configurato per l'ascolto del traffico in ingresso sulla porta 8080. Assicurarsi che tale porta sia aperta nel cluster. Se si usa il cluster di entità, questa porta è aperta.
>

Service Fabric fornisce numerosi strumenti che è possibile usare per gestire un cluster e le applicazioni:

- Service Fabric Explorer, uno strumento basato su browser.
- Interfaccia della riga di comando di Service Fabric, la cui esecuzione si basa sull'interfaccia della riga di comando di Azure 2.0.
- Comandi di PowerShell. 

In questa guida introduttiva si usano l'interfaccia della riga di comando di Service Fabric e Service Fabric Explorer. 

Per usare l'interfaccia della riga di comando, è necessario creare un file PEM basato sul file PFX scaricato. Per convertire il file, usare il comando seguente. Per i cluster di entità, è possibile copiare un comando specifico nel file PFX dalle istruzioni della pagina **ReadMe** (Leggimi).

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

Per usare Service Fabric Explorer, è necessario importare il file PFX del certificato scaricato dal sito Web del cluster di entità nell'archivio certificati (Windows o Mac) oppure nel browser stesso (Ubuntu). È necessaria la password della chiave privata PFX, che è possibile ottenere dalla pagina **ReadMe** (Leggimi).

Usare il metodo preferito per importare il certificato nel sistema. Ad esempio: 

- In Windows: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nell'archivio personale, `Certificates - Current User\Personal\Certificates`. In alternativa, è possibile usare il comando di PowerShell nelle istruzioni di **ReadMe** (Leggimi).
- In Mac: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nel keychain.
- In Ubuntu: Mozilla Firefox è il browser predefinito in Ubuntu 16.04. Per importare il certificato in Firefox, fare clic sul pulsante di menu nell'angolo in alto a destra del browser, quindi fare clic su **Opzioni**. Nella pagina **Preferenze** usare la casella di ricerca per cercare "certificati". Fare clic su **Mostra certificati**, selezionare la scheda **Certificati personali**, fare clic su **Importa** e seguire i prompt per importare il certificato.
 
   ![Installare il certificato in Firefox](./media/service-fabric-quickstart-java/install-cert-firefox.png) 


### <a name="add-certificate-information-to-your-application"></a>Aggiungere le informazioni del certificato all'applicazione

L'identificazione personale del certificato deve essere aggiunta all'applicazione perché usa i modelli di programmazione di Service Fabric. 

1. L'identificazione personale del certificato sarà necessaria nel file `Voting/VotingApplication/ApplicationManifest.xml` durante l'esecuzione in un cluster sicuro. Eseguire il comando seguente per estrarre l'identificazione personale del certificato.

    ```bash
    openssl x509 -in [CERTIFICATE_PEM_FILE] -fingerprint -noout
    ```

2. Nel file `Voting/VotingApplication/ApplicationManifest.xml` aggiungere il frammento di codice seguente sotto il tag **ApplicationManifest**. Il valore **X509FindValue** deve essere l'identificazione personale dal passaggio precedente, senza punto e virgola. 

    ```xml
    <Certificates>
        <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
    </Certificates>   
    ```
    
### <a name="deploy-the-application-using-eclipse"></a>Distribuire l'applicazione tramite Eclipse
Ora che l'applicazione e il cluster sono pronti, è possibile procedere alla distribuzione in un cluster direttamente da Eclipse.

1. Aprire il file **Cloud.json** nella directory **PublishProfiles** e compilare i campi `ConnectionIPOrURL` e `ConnectionPort` in modo appropriato. Di seguito è illustrato un esempio: 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Publish Application** (Pubblica applicazione) nell'elenco a discesa **Service Fabric**. Scegliere **PublishProfiles/Cloud.json** come profilo target e fare clic su Publish (Pubblica). 

    ![Finestra di dialogo Publish (Pubblica)](./media/service-fabric-quickstart-java/cloudjson.png)

3. Aprire il Web browser e accedere all'applicazione tramite **http://\<ConnectionIPOrURL>:8080**. 

    ![Cloud front-end dell'applicazione](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi possono essere facilmente ridimensionati in un cluster per supportare le modifiche del carico sui servizi. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster. Sono disponibili diversi sistemi per garantire il ridimensionamento dei servizi. È ad esempio possibile usare gli script o i comandi dell'interfaccia della riga di comando di Service Fabric (sfctl). I passaggi seguenti usano Service Fabric Explorer.

Service Fabric Explorer è in esecuzione in tutti i cluster di Service Fabric ed è accessibile da un browser passando alla porta di gestione HTTP (19080) del cluster, ad esempio `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.

Per ridimensionare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Ridimensionamento dei servizi in Service Fabric Explorer](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Ridimensionamento dei servizi in Service Fabric Explorer completato](./media/service-fabric-quickstart-java/servicescaled.png)

    È ora possibile vedere che il servizio dispone di due istanze. Nella visualizzazione ad albero viene indicato su quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione, sono state raddoppiate le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:

* Usare Eclipse come strumento per le applicazioni Java di Service Fabric
* Distribuire le applicazioni Java nel cluster locale 
* Distribuire le applicazioni Java in un cluster in Azure
* Scalare orizzontalmente l'applicazione in più nodi

Per altre informazioni sull'uso di app Java in Service Fabric, continuare con l'esercitazione sulle app Java.

> [!div class="nextstepaction"]
> [Distribuire un'app Java](./service-fabric-tutorial-create-java-app.md)
