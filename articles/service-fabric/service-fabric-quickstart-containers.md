---
title: Creare un’app per un contenitore Windows su Service Fabric in Azure | Microsoft Docs
description: In questa guida introduttiva viene creata la prima applicazione contenitore Windows in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 081b2be82b15c36566e8eb9fe4af0037804d0e7e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951196"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Avvio rapido: Distribuire i contenitori Windows in Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.

Per eseguire un'applicazione esistente in un contenitore Windows in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questa guida introduttiva illustra come distribuire un'immagine del contenitore Docker predefinita in un'applicazione di Service Fabric. Al termine, saranno in esecuzione Nano Server per Windows Server 2016 e un contenitore IIS. Questa guida introduttiva descrive la distribuzione di un contenitore Windows. Vedere [questa guida introduttiva](service-fabric-quickstart-containers-linux.md) per distribuire un contenitore Linux.

![Pagina Web predefinita di IIS][iis-default]

In questa guida introduttiva si apprende come:

* Creare un pacchetto di un contenitore di un'immagine Docker
* Configurare la comunicazione
* Compilare l'applicazione di Service Fabric e creare il pacchetto
* Distribuire l'applicazione del contenitore in Azure

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. È possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un computer di sviluppo che esegue:
  * Visual Studio 2015 o Visual Studio 2017.
  * [SDK e strumenti di Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Creare un pacchetto di un contenitore di un'immagine Docker con Visual Studio

L'SDK e gli strumenti di Service Fabric offrono un modello di servizio che permette di distribuire un contenitore in un cluster di Service Fabric.

Avviare Visual Studio come "Amministratore".  Selezionare **File** > **Nuovo** > **Progetto**.

Selezionare l'**applicazione di Service Fabric**, denominarla "MyFirstContainer" e fare clic su **OK**.

Selezionare **Contenitore** dai modelli **Applicazioni e contenitori ospitati**.

In **Nome immagine** immettere "microsoft/iis:nanoserver", l'[immagine di base di IIS e Nano Server per Windows Server](https://hub.docker.com/r/microsoft/iis/).

Configurare il mapping dalla porta all'host del contenitore in modo che per le richieste in ingresso per il servizio sulla porta 80 venga eseguito il mapping alla porta 80 del contenitore.  Impostare la **Porta del contenitore** su "80" e impostare **Porta host** su "80".  

Assegnare al servizio il nome "MyContainerService" e fare clic su **OK**.

![Finestra di dialogo Nuovo servizio][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Specificare la build del sistema operativo per l'immagine del contenitore
I contenitori compilati con una versione specifica di Windows Server potrebbero non supportare l'esecuzione in un host con una versione diversa di Windows Server. Ad esempio, i contenitori compilati con Windows Server 1709 non possono essere eseguiti negli host che eseguono Windows Server versione 2016. Per altre informazioni, vedere [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) (Compatibilità tra il sistema operativo del contenitore di Windows Server e il sistema operativo dell'host). 

Con la versione 6.1 del runtime di Service Fabric e le versioni più recenti, è possibile specificare più immagini del sistema operativo per ogni contenitore e contrassegnare ognuna con la versione della build del sistema operativo in cui deve essere distribuita. Ciò consente di assicurarsi che l'applicazione venga eseguita in host che eseguono versioni diverse del sistema operativo Windows. Per altre informazioni, vedere [Specificare immagini del contenitore specifiche per la build del sistema operativo](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Microsoft pubblica immagini diverse per le versioni di IIS compilate per versioni differenti di Windows Server. Per assicurarsi che Service Fabric distribuisca un contenitore compatibile con la versione di Windows Server in esecuzione nei nodi del cluster in cui si distribuisce l'applicazione, aggiungere le righe seguenti righe al file *ApplicationManifest.xml*. La versione della build per Windows Server 2016 è 14393 e la versione della build per Windows Server versione 1709 è 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Il manifesto del servizio continua a specificare solo un'immagine per nanoserver, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Creare un cluster

Per distribuire l'applicazione in un cluster in Azure, è possibile aggiungere un party cluster. I cluster di entità sono cluster Service Fabric gratuiti e disponibili per un periodo di tempo limitato ospitati in Azure e gestiti dal team di Service Fabric, in cui chiunque può distribuire applicazioni e ottenere informazioni sulla piattaforma.  Il cluster usa un solo certificato autofirmato per la sicurezza da nodo a nodo e da client a nodo. I cluster di entità supportano i contenitori. Se si decide di configurare e usare il proprio cluster, questo deve essere in esecuzione in uno SKU che supporti i contenitori, come Windows Server 2016 Datacenter con contenitori.

Eseguire l'accesso e [aggiungere un cluster Windows](http://aka.ms/tryservicefabric). Scaricare il certificato PFX nel computer facendo clic sul collegamento **PFX**. Fare clic sul collegamento **How to connect to a secure Party cluster?** (Come connettersi a un cluster di entità sicuro?) e copiare la password del certificato. Il certificato, la password del certificato e il valore di **Endpoint connessione** vengono usati nei passaggi seguenti.

![Certificato PFX ed endpoint connessione](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> È disponibile un numero limitato di cluster di entità ogni ora. Se viene restituito un errore quando si tenta di connettersi a un cluster di entità, è possibile attendere e riprovare in seguito, oppure seguire i passaggi descritti nell'esercitazione sulla [distribuzione di un'app .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) per creare un cluster di Service Fabric nella propria sottoscrizione di Azure e distribuirvi l'applicazione. Il cluster creato tramite Visual Studio supporta i contenitori. Dopo avere distribuito e verificato l'applicazione nel proprio cluster, è possibile passare direttamente a [Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric](#complete-example-service-fabric-application-and-service-manifests) in questa guida introduttiva.
>

In un computer Windows installare il certificato PFX nell'archivio certificati *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Ricordare l'identificazione personale per il passaggio seguente.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Distribuire l'applicazione in Azure usando Visual Studio

Ora che l'applicazione è pronta, è possibile distribuirla in un cluster direttamente da Visual Studio.

Fare clic con il pulsante destro del mouse su **MyFirstContainer** in Esplora soluzioni e scegliere **Pubblica**. Verrà visualizzata la finestra di dialogo Pubblica.

Copiare l'**endpoint della connessione** dalla pagina del party cluster nel campo **Endpoint connessione**. Ad esempio, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Fare clic su **Parametri di connessione avanzati** e verificare le informazioni sui parametri di connessione.  I valori di *FindValue* e *ServerCertThumbprint* devono corrispondere all'identificazione personale del certificato installato nel passaggio precedente.

![Finestra di dialogo Pubblica](./media/service-fabric-quickstart-containers/publish-app.png)

Fare clic su **Pubblica**.

Ogni applicazione nel cluster deve avere un nome univoco.  Un party cluster è tuttavia un ambiente pubblico condiviso e potrebbe verificarsi un conflitto con un'applicazione esistente.  Se è presente un conflitto di nomi, ridenominare il progetto di Visual Studio e distribuirlo di nuovo.

Aprire un browser e passare all'**endpoint connessione** specificato nella pagina del party cluster. Facoltativamente, è possibile anteporre l'identificatore di schema, `http://`, e accodare la porta, `:80`, all'URL. Ad esempio, http://zwin7fh14scd.westus.cloudapp.azure.com:80. Verrà visualizzata la pagina Web predefinita di IIS: ![Pagina Web predefinita di IIS][iis-default]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

* Creare un pacchetto di un contenitore di un'immagine Docker
* Configurare la comunicazione
* Compilare l'applicazione di Service Fabric e creare il pacchetto
* Distribuire l'applicazione del contenitore in Azure

Per altre informazioni sull'uso di contenitori Windows in Service Fabric, continuare con l'esercitazione sulle app contenitore Windows.

> [!div class="nextstepaction"]
> [Creare un'app contenitore Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
