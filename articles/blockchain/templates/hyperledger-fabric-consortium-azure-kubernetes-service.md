---
title: Iperledger Fabric Consortium in Azure Kubernetes Service (AKS)
description: Come distribuire e configurare la rete dell'infrastruttura iperledger in Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: da4ec99f1b9d73ab67a2312094feaa1a89aee394
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980227"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Iperledger Fabric Consortium in Azure Kubernetes Service (AKS)

È possibile usare il modello di infrastruttura iperledger (HLF) in Azure Kubernetes Service (AKS) per distribuire e configurare una rete del Consorzio di infrastruttura iperledger in Azure.

Dopo avere letto l'articolo, si sarà in grado di:

- Acquisire familiarità con l'infrastruttura iperledger e i vari componenti che costituiscono i blocchi predefiniti della rete blockchain dell'infrastruttura iperledger.
- Informazioni su come distribuire e configurare un consorzio di infrastruttura iperledger nel servizio Azure Kubernetes per gli scenari di produzione.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architettura del Consorzio di infrastruttura iperledger

Per compilare la rete dell'infrastruttura iperledger in Azure, è necessario distribuire il servizio di ordinamento e l'organizzazione con nodi peer. I diversi componenti fondamentali creati come parte della distribuzione del modello sono:

- **Nodi di ordinamento**: nodo responsabile dell'ordinamento delle transazioni nel Ledger. Insieme ad altri nodi, i nodi ordinati formano il servizio di ordinamento della rete dell'infrastruttura dell'iperledger.

- **Nodi peer**: nodo che ospita principalmente Ledger e contratti intelligenti, questi elementi fondamentali della rete.

- **CA infrastruttura**: l'autorità di certificazione dell'infrastruttura è l'autorità di certificazione (CA) per l'infrastruttura dell'iperledger. La CA infrastruttura consente di inizializzare e avviare il processo server che ospita l'autorità di certificazione. Consente di gestire le identità e i certificati. Per impostazione predefinita, ogni cluster AKS distribuito come parte del modello avrà un pod CA di infrastruttura.

- **CouchDB o LevelDB**: il database di stato globale per i nodi peer può essere archiviato in LevelDB o CouchDB. LevelDB è il database di stato predefinito incorporato nel nodo peer e archivia i dati chaincode come semplici coppie chiave-valore e supporta solo query chiave, intervallo di chiavi e chiave composta. CouchDB è un database di stato alternativo facoltativo che supporta query avanzate quando i valori dei dati chaincode sono modellati come JSON.

Il modello nella distribuzione avvia varie risorse di Azure nella sottoscrizione. Le diverse risorse di Azure distribuite sono:

- **Cluster AKS**: cluster Azure Kubernetes configurato in base ai parametri di input forniti dal cliente. Il cluster AKS dispone di diversi pod configurati per l'esecuzione dei componenti di rete dell'infrastruttura iperledger. I diversi pod creati sono:

  - **Strumenti di infrastruttura**: lo strumento infrastruttura è responsabile della configurazione dei componenti dell'infrastruttura iperledger.
  - **Orderer/Pod peer**: i nodi della rete HLF.
  - **Proxy**: un pod proxy ngnix tramite il quale le applicazioni client possono interfacciarsi con il cluster AKS.
  - **CA infrastruttura**: il pod che esegue l'autorità di certificazione dell'infrastruttura.
- **PostgreSQL**: viene distribuita un'istanza di PostgreSQL per gestire le identità della CA dell'infrastruttura.

- **Azure Key Vault**: è stata distribuita un'istanza di Key Vault per salvare le credenziali CA dell'infrastruttura e i certificati radice forniti dal cliente, usati in caso di tentativi di distribuzione del modello, per gestire i meccanismi del modello.
- **Disco gestito**di Azure: il disco gestito di Azure è per l'archivio persistente per i database di stato globale e del nodo peer.
- **IP pubblico**: un endpoint IP pubblico del cluster AKS distribuito per l'interazione con il cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configurazione di rete blockchain dell'infrastruttura iperledger

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Configurare l'infrastruttura iperledger blockchain Network attenendosi alla procedura seguente:

- [Distribuire l'organizzazione di ordini/peer](#deploy-the-ordererpeer-organization)
- [Crea il Consorzio](#build-the-consortium)

## <a name="deploy-the-ordererpeer-organization"></a>Distribuire l'organizzazione di ordini/peer

Per iniziare a usare la distribuzione dei componenti di rete HLF, passare alla [portale di Azure](https://portal.azure.com). Selezionare **Crea una risorsa > Blockchain** > cercare l' **infrastruttura dell'Iperledger nel servizio Kubernetes di Azure**.

1. Selezionare **Crea** per avviare la distribuzione del modello. Viene visualizzata l' **infrastruttura create iperledger nel servizio Kubernetes di Azure** .

    ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Immettere i dettagli del progetto nella pagina **nozioni di base** .

    ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Immettere i dettagli seguenti:
    - **Sottoscrizione**: scegliere il nome della sottoscrizione in cui si vuole distribuire i componenti di rete HLF.
    - **Gruppo di risorse**: creare un nuovo gruppo di risorse o scegliere un gruppo di risorse vuoto esistente. il gruppo di risorse conterrà tutte le risorse distribuite come parte del modello.
    - **Area**: scegliere l'area di Azure in cui si vuole distribuire il cluster Azure Kubernetes per i componenti di HLF. Il modello è disponibile in tutte le aree in cui è disponibile AKS assicurarsi di scegliere un'area in cui la sottoscrizione non raggiunga il limite di quota della macchina virtuale (VM).
    - **Prefisso risorsa**: prefisso per la denominazione delle risorse distribuite. Il prefisso della risorsa deve avere una lunghezza inferiore a sei caratteri e la combinazione di caratteri deve includere sia numeri che lettere.
4. Selezionare la scheda **Impostazioni infrastruttura** per definire i componenti di rete HLF che verranno distribuiti.

    ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Immettere i dettagli seguenti:
    - **Nome organizzazione**: il nome dell'organizzazione dell'infrastruttura, necessario per varie operazioni del piano dati. Il nome dell'organizzazione deve essere univoco per ogni distribuzione.
    - **Infrastruttura di rete componente**: scegliere ordinazione servizio o nodi peer in base al componente di rete blockchain che si desidera configurare.
    - **Numero di nodi** : di seguito sono riportati i due tipi di nodi:
        - Servizio di ordinamento: selezionare il numero di nodi per fornire la tolleranza di errore alla rete. Il numero di nodi dell'ordine supportato è solo 3, 5 e 7.
        - Nodi peer: è possibile scegliere i nodi 1-10 in base ai requisiti.
    - **Database di stato globale del nodo peer**: scegliere tra LevelDB e CoucbDB. Questo campo viene visualizzato quando l'utente sceglie il nodo peer nell'elenco a discesa componente rete infrastruttura.
    - **Nome utente infrastruttura**: immettere il nome utente usato per l'autenticazione della CA dell'infrastruttura.
    - **Password CA infrastruttura**: immettere la password per l'autenticazione dell'autorità di certificazione dell'infrastruttura.
    - **Conferma password**: confermare la password dell'infrastruttura della CA.
    - **Certificati**: se si vuole usare i propri certificati radice per inizializzare l'autorità di certificazione dell'infrastruttura, scegliere Carica certificato radice per l'opzione della CA dell'infrastruttura. in caso contrario, per impostazione predefinita, l'autorità di certificazione dell'infrastruttura crea certificati autofirmati.
    - **Certificato radice**: caricare il certificato radice (chiave pubblica) con cui deve essere inizializzata l'autorità di certificazione dell'infrastruttura. I certificati del formato PEM sono supportati, i certificati devono essere validi nel fuso orario UTC.
    - **Chiave privata del certificato radice**: caricare la chiave privata del certificato radice. Se si dispone di un certificato con estensione PEM, che include sia la chiave pubblica che quella privata, è possibile caricarla anche qui.


6. Selezionare la scheda **Impostazioni cluster AKS** per definire la configurazione del cluster Kubernetes di Azure che rappresenta l'infrastruttura sottostante in cui verranno impostati i componenti di rete dell'infrastruttura.

    ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Immettere i dettagli seguenti:
    - **Nome del cluster Kubernetes**: il nome del cluster AKS creato. Questo campo viene prepopolato in base al prefisso della risorsa fornito. se necessario, è possibile modificarlo.
    - **Kubernetes Version**: versione di Kubernetes che verrà distribuita nel cluster. In base all'area selezionata nella scheda **nozioni di base** , è possibile che vengano modificate le versioni supportate disponibili.
    - **Prefisso DNS**: prefisso del nome Domain Name System (DNS) per il cluster AKS. Si userà DNS per connettersi all'API Kubernetes quando si gestiscono i contenitori dopo la creazione del cluster.
    - **Dimensioni del nodo**: le dimensioni del nodo Kubernetes, è possibile scegliere dall'elenco di SKU (Stock Keeping Unit) delle macchine virtuali disponibili in Azure. Per prestazioni ottimali, si consiglia la versione standard DS3 V2.
    - **Node count**: conteggio del numero di nodi Kubernetes da distribuire nel cluster. Si consiglia di mantenere questo numero di nodi almeno uguale o superiore al numero di nodi HLF specificati nelle impostazioni dell'infrastruttura.
    - **ID client dell'entità servizio**: immettere l'ID client di un'entità servizio esistente o crearne uno nuovo, che è necessario per l'autenticazione AKS. Vedere la procedura per [creare un'entità servizio](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Segreto client dell'entità servizio**: immettere il segreto client dell'entità servizio specificata nell'ID client dell'entità servizio.
    - **Conferma segreto client**: confermare il segreto client specificato nel segreto client dell'entità servizio.
    - **Abilitare il monitoraggio del contenitore**: scegliere di abilitare il monitoraggio di AKS, che consente di effettuare il push dei log AKS nell'area di lavoro log Analytics specificata.
    - **Area**di lavoro log Analytics: l'area di lavoro di log Analytics verrà popolata con l'area di lavoro predefinita creata se il monitoraggio è abilitato.

8. Dopo aver fornito tutti i dettagli, selezionare **Verifica e crea** scheda. La verifica e la creazione attivano la convalida per i valori specificati.
9. Al termine della convalida, è possibile selezionare **Crea**.
La distribuzione richiede in genere 10-12 minuti. può variare a seconda delle dimensioni e del numero di nodi AKS specificati.
10. Al termine della distribuzione, si riceverà una notifica tramite le notifiche di Azure nell'angolo superiore destro.
11. Selezionare **Vai al gruppo di risorse** per controllare tutte le risorse create come parte della distribuzione del modello. Tutti i nomi delle risorse inizieranno con il prefisso specificato nell'impostazione di **base** .

## <a name="build-the-consortium"></a>Crea il Consorzio

Per compilare il post di blockchain Consortium per la distribuzione del servizio di ordinamento e dei nodi peer, è necessario eseguire i passaggi seguenti in sequenza. Script di Azure HLF (azhlf), che consente di configurare il Consorzio, creare il canale e le operazioni chaincode.

> [!NOTE]
> Nello script è presente un aggiornamento che consente di fornire una maggiore funzionalità con lo script di Azure HLF. Se si vuole fare riferimento allo script precedente, [vedere qui](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Questo script è compatibile con l'infrastruttura di iperledger in Azure Kubernetes Service Template versione 2.0.0 e versioni successive. Per verificare la versione della distribuzione, seguire la procedura descritta in [risoluzione dei problemi](#troubleshoot).

> [!NOTE]
> Lo script Azure HLF (azhlf) fornito è utile solo per gli scenari demo/DevTest. Il canale e il Consorzio creati da questo script hanno criteri HLF di base per semplificare lo scenario demo/DevTest. Per la configurazione dell'ambiente di produzione, è consigliabile aggiornare i criteri di canale/Consorzio HLF in linea con le esigenze di conformità dell'organizzazione usando le API HLF native.


Tutti i comandi per eseguire lo script di Azure HLF possono essere eseguiti tramite la riga di comando di Azure bash. Interfaccia (CLI). È possibile accedere alla versione Web di Azure shell tramite  ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) nell'angolo superiore destro della portale di Azure. Al prompt dei comandi digitare bash e immettere per passare all'interfaccia della riga di comando di bash.

Per ulteriori informazioni, vedere [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Nell'immagine seguente viene illustrato il processo dettagliato per la creazione di un consorzio tra un'organizzazione di ordini e un'organizzazione peer. I comandi dettagliati per eseguire questi passaggi vengono acquisiti nelle sezioni seguenti.

![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Seguire i comandi seguenti per la configurazione iniziale dell'applicazione client: 

1.  [Scaricare i file dell'applicazione client](#download-client-application-files)
2.  [Variabili di ambiente di installazione](#setup-environment-variables)
3.  [Importa profilo di connessione organizzazione, utente amministratore e MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Dopo aver completato l'installazione iniziale, è possibile usare l'applicazione client per ottenere le operazioni seguenti:  

- [Comandi di gestione canali](#channel-management-commands)
- [Comandi del Consorzio di gestione](#consortium-management-commands)
- [Comandi di gestione Chaincode](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Scaricare i file dell'applicazione client

Il primo programma di installazione prevede il download dei file dell'applicazione client. Eseguire il comando seguente per scaricare tutti i file e i pacchetti necessari:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Questi comandi clonano il codice dell'applicazione client Azure HLF dal repository GitHub pubblico, seguito dal caricamento di tutti i pacchetti NPM dipendenti. Una volta completata l'esecuzione del comando, è possibile visualizzare una cartella node_modules nella directory corrente. Tutti i pacchetti necessari vengono caricati nella cartella node_modules.


### <a name="setup-environment-variables"></a>Impostare le variabili di ambiente

> [!NOTE]
> Tutte le variabili di ambiente seguono la convenzione di denominazione delle risorse di Azure.


**Imposta sotto le variabili di ambiente per il client dell'organizzazione di orderer**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Imposta le variabili di ambiente seguenti per il client dell'organizzazione peer**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> In base al numero di org peer nel Consorzio, potrebbe essere necessario ripetere i comandi peer e impostare la variabile di ambiente di conseguenza.

**Impostare le variabili di ambiente seguenti per la configurazione dell'account di archiviazione di Azure**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Seguire questa procedura per la creazione dell'account di archiviazione di Azure. Se è già stato creato un account di archiviazione di Azure, ignorare questi passaggi

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Seguire questa procedura per la creazione di una condivisione file nell'account di archiviazione di Azure. Se è già stata creata una condivisione file, ignorare i passaggi seguenti.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Seguire i passaggi seguenti per la generazione della stringa di connessione alla condivisione file di Azure

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importa profilo di connessione organizzazione, identità utente amministratore e MSP

Eseguire i comandi seguenti per recuperare il profilo di connessione dell'organizzazione, l'identità dell'utente amministratore e l'MSP dal cluster Azure Kubernetes e archiviare tali identità nell'archivio locale dell'applicazione client, ad esempio nella directory "azhlfTool/Stores".

Per l'organizzazione dell'ordine:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

Per l'organizzazione peer:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Comandi di gestione canali

> [!NOTE]
> Prima di iniziare con qualsiasi operazione del canale, verificare che sia stata eseguita la configurazione iniziale dell'applicazione client.  

Di seguito sono riportati i due comandi di gestione del canale:

1. [Comando crea canale](#create-channel-command)
2. [Impostazione del comando Anchor peer (s)](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Comando crea canale

Dal client dell'organizzazione orderer, eseguire il comando per creare un nuovo canale. Questo comando creerà un canale con solo l'organizzazione dell'ordine.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Impostazione del comando Anchor peer (s)
Dal client dell'organizzazione peer, eseguire il comando seguente per impostare i peer di ancoraggio per l'organizzazione peer sul canale specificato.

>[!NOTE]
> Prima di eseguire questo comando, assicurarsi che l'organizzazione peer venga aggiunta nel canale usando i comandi di gestione del Consorzio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`elenco separato da spazi dei nodi peer da impostare come peer di ancoraggio. Ad esempio,

  - Impostare `<anchorPeersList>` come "peer1" Se si vuole impostare solo il nodo Peer1 come peer di ancoraggio.
  - Impostare `<anchorPeersList>` come "peer1" "Peer3" Se si desidera impostare il nodo Peer1 e Peer3 come peer di ancoraggio.

### <a name="consortium-management-commands"></a>Comandi del Consorzio di gestione

>[!NOTE]
> Prima di iniziare con qualsiasi operazione del Consorzio, verificare che sia stata eseguita la configurazione iniziale dell'applicazione client.  

Eseguire i comandi seguenti nell'ordine indicato per aggiungere un'organizzazione peer in un canale e un consorzio
1.  Dal client dell'organizzazione peer, caricare l'organizzazione peer MSP in archiviazione di Azure

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Dal client dell'organizzazione dell'ordine, scaricare l'organizzazione peer MSP da archiviazione di Azure e quindi eseguire il comando per aggiungere un'organizzazione peer nel canale/Consorzio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Dal client dell'organizzazione dell'ordine, caricare il profilo di connessione per l'ordine di archiviazione in archiviazione di Azure in modo che l'organizzazione peer possa connettersi ai nodi dell'ordine usando questo profilo di connessione

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Dal client dell'organizzazione peer, scaricare il profilo di connessione di orderer da archiviazione di Azure e quindi eseguire il comando per aggiungere nodi peer nel canale.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Analogamente, per aggiungere altre organizzazioni peer nel canale, aggiornare le variabili di ambiente peer in base all'organizzazione peer richiesta ed eseguire i passaggi da 1 a 4.


### <a name="chaincode-management-commands"></a>Comandi di gestione Chaincode

>[!NOTE]
> Prima di iniziare con qualsiasi operazione chaincode, verificare che sia stata eseguita la configurazione iniziale dell'applicazione client.  

**Imposta le variabili di ambiente specifiche di chaincode seguenti**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

È possibile eseguire le operazioni di chaincode seguenti:  

- [Installare chaincode](#install-chaincode)  
- [Creare un'istanza di chaincode](#instantiate-chaincode)  
- [Richiama chaincode](#invoke-chaincode)
- [Chaincode query](#query-chaincode)


### <a name="install-chaincode"></a>Installare chaincode  

Eseguire il comando seguente per installare chaincode nell'organizzazione peer.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Il chaincode verrà installato in tutti i nodi peer del set di organizzazioni peer nella variabile di ambiente NomeOrg. Se nel canale sono presenti due o più organizzazioni peer e si desidera installare chaincode su tutti questi elementi, eseguire questo comando separatamente per ogni organizzazione peer.  

Attenersi alla procedura seguente:  

1.  Impostare `ORGNAME` e `USER_IDENTITY` come per peerOrg1 e il `./azhlf chaincode install` comando issue.  
2.  Impostare `ORGNAME` e `USER_IDENTITY` come per peerOrg2 e il `./azhlf chaincode install` comando issue.  

### <a name="instantiate-chaincode"></a>Creare un'istanza di chaincode  

Dall'applicazione client peer eseguire il comando seguente per creare un'istanza di chaincode sul canale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Passare il nome della funzione di creazione di istanze e l'elenco `<instantiateFunc>` separato `<instantiateFuncArgs>` da spazi di argomenti rispettivamente in e. Ad esempio, in chaincode_example02. go chaincode, per creare un'istanza di chaincode `<instantiateFunc>` impostato `init`su `<instantiateFuncArgs>` e su "a" "2000" "b" "1000".

> [!NOTE]
> Eseguire il comando per una sola volta da una qualsiasi organizzazione peer nel canale. Una volta che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Viene quindi creata un'istanza di chaincode in tutti i nodi peer di tutte le organizzazioni peer nel canale.  


### <a name="invoke-chaincode"></a>Richiama chaincode  

Dal client dell'organizzazione peer eseguire il comando seguente per richiamare la funzione chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passare il nome della funzione di richiamo e l'elenco separato `<invokeFunction>` da `<invokeFuncArgs>` spazi di argomenti rispettivamente in e. Continuando con l'esempio chaincode_example02. go chaincode, per eseguire l'operazione `<invokeFunction>` Invoke `invoke` impostare `<invokeFuncArgs>` su e su "a" "b" "10".  

>[!NOTE]
> Eseguire il comando per una sola volta da una qualsiasi organizzazione peer nel canale. Una volta che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Di conseguenza, lo stato globale viene aggiornato in tutti i nodi peer di tutte le organizzazioni peer nel canale.  


### <a name="query-chaincode"></a>Chaincode query  

Eseguire il comando seguente per eseguire una query su chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Passare il nome della funzione di query e l'elenco separato `<queryFunction>` da `<queryFuncArgs>` spazi di argomenti rispettivamente in e. Anche in questo caso, prendendo chaincode_example02. go chaincode come riferimento, per eseguire una query sul valore di "a `<queryFunction>` " `query` nello `<queryArgs>` stato globale impostato su e su "a".  

## <a name="troubleshoot"></a>Risolvere problemi

**Per verificare la versione del modello in esecuzione**

Eseguire i comandi seguenti per trovare la versione della distribuzione del modello.

Impostare le variabili di ambiente in base al gruppo di risorse in cui è stato distribuito il modello.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Eseguire il comando seguente per stampare la versione del modello
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```
