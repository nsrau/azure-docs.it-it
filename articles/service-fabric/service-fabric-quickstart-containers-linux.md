---
title: Creare un'applicazione contenitore di Azure Service Fabric in Linux | Microsoft Docs
description: In questa guida introduttiva viene creata la prima applicazione contenitore Linux in Azure Service Fabric.  Compilare un'immagine Docker con l'applicazione, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 65f048d67ef5f250691700a382e781814c57e8a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Guida introduttiva: Distribuire un'applicazione contenitore Linux di Azure Service Fabric in Azure
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. 

Questa guida introduttiva illustra come distribuire contenitori Linux in un cluster di Service Fabric. Al termine, si avrà un'applicazione di voto costituita da un front-end Web Python e un back-end Redis in esecuzione in un cluster di Service Fabric. Verrà anche illustrato come effettuare il failover di un'applicazione e come ridimensionare un'applicazione del cluster.

![Pagina Web dell'app Voting][quickstartpic]

In questa guida introduttiva usare l'ambiente Bash in Azure Cloud Shell per eseguire i comandi dell'interfaccia della riga di comando di Service Fabric. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se è la prima volta che si esegue Cloud Shell, viene chiesto di configurare la condivisione file `clouddrive`. È possibile accettare le impostazioni predefinite o collegare una condivisione file esistente. Per altre informazioni, vedere [Configurare una condivisione file `clouddrive`](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share).

## <a name="get-the-application-package"></a>Ottenere il pacchetto dell'applicazione
Per distribuire contenitori in Service Fabric, è necessario un set di file manifesto che descrivono i singoli contenitori e l'applicazione (definizione di applicazione).

In Cloud Shell usare git per clonare una copia della definizione di applicazione, quindi passare alla directory `Voting` nel clone. 

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric
Per distribuire l'applicazione in Azure, è necessario un cluster di Service Fabric per eseguire l'applicazione. I cluster di entità offrono un modo semplice per creare rapidamente un cluster di Service Fabric. I cluster di entità sono cluster di Service Fabric gratuiti disponibili per un periodo di tempo limitato, ospitati in Azure ed eseguiti dal team di Service Fabric. È possibile usare i cluster di entità per distribuire le applicazioni e ottenere informazioni sulla piattaforma. Il cluster usa un solo certificato autofirmato per la sicurezza da nodo a nodo e da client a nodo.

Eseguire l'accesso e aggiungere un [cluster Linux](http://aka.ms/tryservicefabric). Scaricare il certificato PFX nel computer facendo clic sul collegamento **PFX**. Fare clic sul collegamento **ReadMe** (Leggimi) per trovare la password del certificato e le istruzioni per configurare diversi ambienti per usare il certificato. Tenere aperte entrambe le pagine **Welcome** (Benvenuto) e **ReadMe** (Leggimi) perché si useranno alcune delle istruzioni nei passaggi seguenti. 

> [!Note]
> È disponibile un numero limitato di cluster di entità ogni ora. Se viene restituito un errore quando si prova a registrarsi a un cluster di entità, è possibile attendere e riprovare in seguito oppure seguire i passaggi descritti in [Creare un cluster di Service Fabric in Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) per creare un cluster nella propria sottoscrizione. 
> 
>Se si crea il cluster, tenere presente che il servizio front-end Web è configurato per l'ascolto del traffico in ingresso sulla porta 80. Assicurarsi che tale porta sia aperta nel cluster. Se si usa un cluster di entità, questa porta è aperta.
>

## <a name="configure-your-environment"></a>Configurare l'ambiente
Service Fabric fornisce numerosi strumenti che è possibile usare per gestire un cluster e le applicazioni:

- Service Fabric Explorer, uno strumento basato su browser.
- Interfaccia della riga di comando di Service Fabric, la cui esecuzione si basa sull'interfaccia della riga di comando di Azure 2.0.
- Comandi di PowerShell. 

In questa guida introduttiva si usano l'interfaccia della riga di comando di Service Fabric in Cloud Shell e Service Fabric Explorer. Le sezioni seguenti illustrano come installare il certificato necessario per connettersi al cluster sicuro con questi strumenti.

### <a name="configure-certificate-for-the-service-fabric-cli"></a>Configurare il certificato per l'interfaccia della riga di comando di Service Fabric
Per usare l'interfaccia della riga di comando in Cloud Shell, è necessario caricare il file PFX del certificato in Cloud Shell e quindi usarlo per creare un file PEM.

1. Per caricare il certificato nella directory di lavoro corrente in Cloud Shell, trascinare il file PFX del certificato dalla cartella in cui è stato scaricato nel computer e rilasciarlo nella finestra di Cloud Shell.  

2. Per convertire il file PFX in un file PEM, usare il comando seguente. Per i cluster di entità, è possibile copiare un comando specifico nel file PFX e la password dalle istruzioni della pagina **ReadMe** (Leggimi).

    ```bash
    openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
    ``` 

### <a name="configure-certificate-for-service-fabric-explorer"></a>Configurare il certificato per Service Fabric Explorer
Per usare Service Fabric Explorer, è necessario importare il file PFX del certificato scaricato dal sito Web del cluster di entità nell'archivio certificati (Windows o Mac) oppure nel browser stesso (Ubuntu). È necessaria la password della chiave privata PFX, che è possibile ottenere dalla pagina **ReadMe** (Leggimi).

Usare il metodo preferito per importare il certificato nel sistema. Ad esempio: 

- In Windows: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nell'archivio personale, `Certificates - Current User\Personal\Certificates`. In alternativa, è possibile usare il comando di PowerShell nelle istruzioni di **ReadMe** (Leggimi).
- In Mac: fare doppio clic sul file PFX e seguire i prompt per installare il certificato nel keychain.
- In Ubuntu: Mozilla Firefox è il browser predefinito in Ubuntu 16.04. Per importare il certificato in Firefox, fare clic sul pulsante di menu nell'angolo in alto a destra del browser, quindi fare clic su **Opzioni**. Nella pagina **Preferenze** usare la casella di ricerca per cercare "certificati". Fare clic su **Mostra certificati**, selezionare la scheda **Certificati personali**, fare clic su **Importa** e seguire i prompt per importare il certificato.
 
   ![Installare il certificato in Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png) 

## <a name="deploy-the-service-fabric-application"></a>Distribuire l'applicazione Service Fabric 
1. In Cloud Shell connettersi al cluster di Service Fabric in Azure con l'interfaccia della riga di comando. L'endpoint è l'endpoint di gestione del cluster. Nella sezione precedente è stato creato il file PEM. Per i cluster di entità, è possibile copiare un comando specifico nel file PEM e l'endpoint di gestione dalle istruzioni della pagina **ReadMe** (Leggimi).

    ```bash
    sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
    ```

2. Usare lo script di installazione per copiare la definizione di applicazione di voto nel cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

2. Aprire un Web browser e passare all'endpoint Service Fabric Explorer per il cluster. L'endpoint ha il formato seguente: https://\<url-cluster-service-fabric-azure>:19080/Explorer, ad esempio `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. </br>Per i cluster di entità, è possibile trovare l'endpoint Service Fabric Explorer per il cluster nella pagina **Welcome** (Benvenuto). 

3. Espandere il nodo **Applicazioni**, in cui sarà ora presente una voce per il tipo dell'applicazione di voto e l'istanza creata.

    ![Service Fabric Explorer][sfx]

3. Per connettersi al contenitore in esecuzione, aprire un Web browser e passare all'URL del cluster, ad esempio `http://linh1x87d1d.westus.cloudapp.azure.com:80`. Nel browser verrà visualizzata l'applicazione di voto.

    ![Pagina Web dell'app Voting][quickstartpic]


> [!NOTE]
> È anche possibile distribuire le applicazioni di Service Fabric con Docker Compose. È ad esempio possibile usare il comando seguente per distribuire e installare l'applicazione nel cluster usando Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Effettuare il failover di un contenitore in un cluster
Service Fabric garantisce lo spostamento automatico delle istanze di contenitore in altri nodi del cluster in caso di errore. È anche possibile svuotare manualmente un nodo per i contenitori e passare quindi normalmente ad altri nodi del cluster. Service Fabric offre diversi modi per ridimensionare i servizi. Nei passaggi seguenti si usa Service Fabric Explorer.

Per effettuare il failover del contenitore front-end, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID). Si noti il nome del nodo nella visualizzazione struttura ad albero, che indica i nodi in cui il contenitore è attualmente in esecuzione, ad esempio `_nodetype_4`.
3. Espandere il nodo **Nodi** nella visualizzazione albero. Fare clic sui puntini di sospensione (...) accanto al nodo che esegue il contenitore.
4. Scegliere **Riavvia** per riavviare il nodo e confermare l'azione di riavvio. Il riavvio causa il failover del contenitore in un altro nodo del cluster.

    ![Visualizzazione del nodo in Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi di Service Fabric possono essere facilmente ridimensionati in un cluster per supportarne il carico. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster.

Per scalare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/Voting/azurevotefront** nella visualizzazione albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Avvio del ridimensionamento dei servizi in Service Fabric Explorer][containersquickstartscale]

  Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/Voting/azurevotefront** nella visualizzazione struttura ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Fine del ridimensionamento dei servizi in Service Fabric Explorer][containersquickstartscaledone]

    Saranno ora presenti due istanze del servizio. Nella visualizzazione albero si può verificare in quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione, sono state raddoppiate le risorse disponibili per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="clean-up-resources"></a>Pulire le risorse
1. Usare lo script di disinstallazione (uninstall.sh) incluso nel modello per eliminare l'istanza dell'applicazione dal cluster e annullare la registrazione del tipo di applicazione. La pulizia dell'istanza con questo script richiede tempo ed è quindi consigliabile non eseguire lo script di installazione immediatamente dopo questo script. È possibile usare Service Fabric Explorer per determinare quando è stata rimossa l'istanza ed è stata annullata la registrazione del tipo di applicazione. 

    ```bash
    ./uninstall.sh
    ```

2. Se non è necessario eseguire altre operazioni con il cluster, è possibile rimuovere il certificato dall'archivio certificati. Ad esempio: 
   - In Windows: usare lo [snap-in di MMC Certificati](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Assicurarsi di selezionare **My user account** (Account utente personale) quando si aggiunge lo snap-in. Passare a `Certificates - Current User\Personal\Certificates` e rimuovere il certificato.
   - In Mac: usare l'app Keychain.
   - In Ubuntu: seguire la procedura usata per visualizzare i certificati e rimuovere il certificato.

3. Se non si vuole continuare a usare Azure Cloud Shell, è possibile eliminare l'account di archiviazione associato per evitare di incorrere in spese. Chiudere la sessione di Cloud Shell. Nel portale di Azure fare clic sull'account di archiviazione associato a Cloud Shell, quindi fare clic su **Elimina** nella parte superiore della pagina e rispondere ai prompt.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata distribuita un'applicazione contenitore Linux in un cluster di Service Fabric in Azure, è stato effettuato il failover nell'applicazione ed è stata ridimensionata l'applicazione nel cluster. Per altre informazioni sull'uso di contenitori Linux in Service Fabric, continuare con l'esercitazione sulle app contenitore Linux.

> [!div class="nextstepaction"]
> [Creare un'app contenitore Linux](./service-fabric-tutorial-create-container-images.md)


[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
