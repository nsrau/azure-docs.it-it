---
title: Creare un'app per un contenitore Linux su Service Fabric in Azure | Microsoft Docs
description: In questa guida rapida, compilerai un'immagine Docker con l'applicazione, eseguirai il push dell'immagine in un registro contenitori e distribuirai il contenitore in un cluster di Service Fabric.
services: service-fabric
documentationcenter: linux
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/30/2019
ms.author: aljo,suhuruli
ms.custom: mvc
ms.openlocfilehash: 1487c9e3e57a2fe4dcdcab04aa81cd81315decfd
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302395"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Guida introduttiva: Distribuire contenitori Linux in Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili.

Questa guida introduttiva illustra come distribuire contenitori Linux in un cluster di Service Fabric in Azure. Al termine, si avrà un'applicazione di voto costituita da un front-end Web Python e un back-end Redis in esecuzione in un cluster di Service Fabric. Verrà anche illustrato come effettuare il failover di un'applicazione e come ridimensionare un'applicazione del cluster.

![Pagina Web dell'app Voting][quickstartpic]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

1. Se non si ha una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

2. Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. Installare [Service Fabric SDK e l'interfaccia della riga di comando](service-fabric-get-started-linux.md#installation-methods)

4. Installare [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Ottenere il pacchetto dell'applicazione

Per distribuire contenitori in Service Fabric, è necessario un set di file manifesto che descrivono i singoli contenitori e l'applicazione (definizione di applicazione).

In una console usare git per clonare una copia della definizione di applicazione, quindi passare alla directory `Voting` nel clone.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric

Per distribuire l'applicazione in Azure, è necessario un cluster di Service Fabric per eseguire l'applicazione. I comandi seguenti creano un cluster con cinque nodi in Azure.  Creano inoltre un certificato autofirmato, lo aggiungono a un insieme di credenziali delle chiavi e lo scaricano in locale. Il nuovo certificato viene usato per proteggere il cluster in fase di distribuzione e per l'autenticazione dei client.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Il servizio front-end Web è configurato per l'ascolto del traffico in ingresso sulla porta 80. Per impostazione predefinita, la porta 80 è aperta nelle VM del cluster e in Azure Load Balancer.
>

## <a name="configure-your-environment"></a>Configurare l'ambiente

Service Fabric fornisce numerosi strumenti che è possibile usare per gestire un cluster e le applicazioni:

- Service Fabric Explorer, uno strumento basato su browser.
- Interfaccia della riga di comando di Service Fabric, la cui esecuzione si basa sull'interfaccia della riga di comando di Azure. 
- Comandi di PowerShell.

In questa guida introduttiva si usano l'interfaccia della riga di comando di Service Fabric e Service Fabric Explorer, uno strumento basato sul Web. Per usare Service Fabric Explorer, è necessario importare il file PFX del certificato nel browser. Per impostazione predefinita il file PFX non prevede una password.

Mozilla Firefox è il browser predefinito in Ubuntu 16.04. Per importare il certificato in Firefox, fare clic sul pulsante di menu nell'angolo in alto a destra del browser, quindi fare clic su **Opzioni**. Nella pagina **Preferenze** usare la casella di ricerca per cercare "certificati". Fare clic su **Mostra certificati**, selezionare la scheda **Certificati personali**, fare clic su **Importa** e seguire i prompt per importare il certificato.

   ![Installare il certificato in Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Distribuire l'applicazione Service Fabric

1. Connettersi al cluster di Service Fabric in Azure con l'interfaccia della riga di comando. L'endpoint è l'endpoint di gestione del cluster. Nella sezione precedente è stato creato il file PEM. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Usare lo script di installazione per copiare la definizione di applicazione di voto nel cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.  Il file del certificato PEM dovrebbe trovarsi nella stessa directory del file *install.sh*.

    ```bash
    ./install.sh
    ```

3. Aprire un Web browser e passare all'endpoint Service Fabric Explorer per il cluster. L'endpoint ha il formato seguente:  **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**, ad esempio `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Espandere il nodo **Applicazioni**, in cui sarà ora presente una voce per il tipo dell'applicazione di voto e l'istanza creata.

    ![Service Fabric Explorer][sfx]

5. Per connettersi al contenitore in esecuzione, aprire un Web browser e passare all'URL del cluster, ad esempio `http://containertestcluster.eastus.cloudapp.azure.com:80`. Nel browser verrà visualizzata l'applicazione di voto.

    ![Pagina Web dell'app Voting][quickstartpic]

> [!NOTE]
> È anche possibile distribuire le applicazioni di Service Fabric con Docker Compose. È ad esempio possibile usare il comando seguente per distribuire e installare l'applicazione nel cluster usando Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Effettuare il failover di un contenitore in un cluster

Service Fabric garantisce lo spostamento automatico delle istanze di contenitore in altri nodi del cluster in caso di errore. È anche possibile svuotare manualmente un nodo per i contenitori e passare quindi normalmente ad altri nodi del cluster. Service Fabric offre diversi modi per ridimensionare i servizi. Nei passaggi seguenti si usa Service Fabric Explorer.

Per effettuare il failover del contenitore front-end, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID). Si noti il nome del nodo nella visualizzazione struttura ad albero, che indica i nodi in cui il contenitore è attualmente in esecuzione, ad esempio `_nodetype_1`.
3. Espandere il nodo **Nodi** nella visualizzazione albero. Fare clic sui puntini di sospensione (...) accanto al nodo che esegue il contenitore.
4. Scegliere **Riavvia** per riavviare il nodo e confermare l'azione di riavvio. Il riavvio causa il failover del contenitore in un altro nodo del cluster.

    ![Visualizzazione del nodo in Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster

I servizi di Service Fabric possono essere facilmente ridimensionati in un cluster per supportarne il carico. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster.

Per scalare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/Voting/azurevotefront** nella visualizzazione albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Avvio del ridimensionamento dei servizi in Service Fabric Explorer][containersquickstartscale]

    Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Fine del ridimensionamento dei servizi in Service Fabric Explorer][containersquickstartscaledone]

    Saranno ora presenti due istanze del servizio. Nella visualizzazione albero si può verificare in quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione, sono state raddoppiate le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="clean-up-resources"></a>Pulire le risorse

Usare lo script di disinstallazione (uninstall.sh) incluso nel modello per eliminare l'istanza dell'applicazione dal cluster e annullare la registrazione del tipo di applicazione. La pulizia dell'istanza con questo script richiede tempo ed è quindi consigliabile non eseguire lo script di installazione immediatamente dopo questo script. È possibile usare Service Fabric Explorer per determinare quando è stata rimossa l'istanza ed è stata annullata la registrazione del tipo di applicazione.

```bash
./uninstall.sh
```

Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster. È possibile trovare l'ID della sottoscrizione accedendo al portale di Azure. Eliminare il gruppo di risorse e tutte le risorse del cluster con il comando [az group delete](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Se non è necessario eseguire altre operazioni con il cluster, è possibile rimuovere il certificato dall'archivio certificati. Ad esempio: 
- In Windows: usare lo [snap-in di MMC Certificati](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Assicurarsi di selezionare **My user account** (Account utente personale) quando si aggiunge lo snap-in. Passare a `Certificates - Current User\Personal\Certificates` e rimuovere il certificato.
- Su Mac: usare l'app Keychain.
- In Ubuntu: seguire la procedura usata per visualizzare i certificati e rimuovere il certificato.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita un'applicazione contenitore Linux in un cluster di Service Fabric in Azure, è stato effettuato il failover nell'applicazione ed è stata ridimensionata l'applicazione nel cluster. Per altre informazioni sull'uso di contenitori Linux in Service Fabric, continuare con l'esercitazione sulle app contenitore Linux.

> [!div class="nextstepaction"]
> [Creare un'app contenitore Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
