---
title: Distribuire il Consorzio di infrastruttura iperledger nel servizio Azure Kubernetes
description: Come distribuire e configurare una rete del Consorzio di infrastruttura iperledger nel servizio Azure Kubernetes
ms.date: 08/06/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 081c7a10ee091f573e8f999c94588ef85c784f74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651567"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Distribuire il Consorzio di infrastruttura iperledger nel servizio Azure Kubernetes

È possibile usare l'infrastruttura di iperledger nel modello di servizio Azure Kubernetes per distribuire e configurare una rete del Consorzio di infrastruttura iperledger in Azure.

Dopo avere letto l'articolo, si sarà in grado di:

- Avere una conoscenza operativa dell'infrastruttura di iperledger e dei componenti che formano i blocchi predefiniti di una rete blockchain dell'infrastruttura iperledger.
- Informazioni su come distribuire e configurare una rete del Consorzio di infrastruttura iperledger nel servizio Azure Kubernetes per gli scenari di produzione.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Scegliere una soluzione Azure blockchain

Prima di scegliere di usare un modello di soluzione, confrontare lo scenario con i casi d'uso comuni delle opzioni disponibili di Azure blockchain:

Opzione | Modello di servizio | Caso d'uso comune
-------|---------------|-----------------
Modelli di soluzioni | IaaS | I modelli di soluzione sono Azure Resource Manager modelli che è possibile usare per eseguire il provisioning di una topologia di rete blockchain completamente configurato. I modelli distribuiscono e configurano Microsoft Azure servizi di calcolo, rete e archiviazione per un tipo di rete blockchain. I modelli di soluzione sono forniti senza un contratto di servizio. Utilizzare [Microsoft Q&una pagina per il](/answers/topics/azure-blockchain-workbench.html) supporto.
[Servizio Azure Blockchain](../service/overview.md) | PaaS | Azure blockchain Service Preview semplifica la formazione, la gestione e la governance delle reti blockchain del Consorzio. Usa il servizio Azure blockchain per le soluzioni che richiedono PaaS, la gestione del consorzio o la privacy dei contratti e delle transazioni.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | Azure blockchain Workbench Preview è una raccolta di servizi e funzionalità di Azure che consentono di creare e distribuire applicazioni blockchain per condividere i dati e i processi aziendali con altre organizzazioni. Usare Azure blockchain Workbench per la progettazione di una soluzione blockchain o un modello di prova per un'applicazione blockchain. Azure blockchain Workbench è disponibile senza un contratto di servizio. Utilizzare [Microsoft Q&una pagina per il](/answers/topics/azure-blockchain-workbench.html) supporto.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architettura del Consorzio di infrastruttura iperledger

Per creare una rete dell'infrastruttura iperledger in Azure, è necessario distribuire un servizio di ordinamento e un'organizzazione con nodi peer. Usando l'infrastruttura di iperledger nel modello di soluzione del servizio Kubernetes di Azure, è possibile creare nodi di ordine o nodi peer. È necessario distribuire il modello per ogni nodo che si desidera creare.

I componenti fondamentali creati come parte della distribuzione del modello sono:

- **Nodi di ordinamento**: nodo responsabile dell'ordinamento delle transazioni nel Ledger. Insieme ad altri nodi, i nodi ordinati formano il servizio di ordinamento della rete dell'infrastruttura dell'iperledger.

- **Nodi peer**: nodo che ospita principalmente i registri e i contratti intelligenti, che sono elementi fondamentali della rete.

- **CA infrastruttura**: l'autorità di certificazione (CA) per l'infrastruttura dell'iperledger. La CA infrastruttura consente di inizializzare e avviare un processo server che ospita l'autorità di certificazione. Consente di gestire le identità e i certificati. Per impostazione predefinita, ogni cluster AKS distribuito come parte del modello avrà un pod CA di infrastruttura.

- **CouchDB o LevelDB**: database di stato globale per i nodi peer. LevelDB è il database di stato predefinito incorporato nel nodo peer. Archivia i dati chaincode come semplici coppie chiave/valore e supporta solo query chiave, intervallo di chiavi e chiave composta. CouchDB è un database di stato alternativo facoltativo che supporta query avanzate quando i valori dei dati chaincode sono modellati come JSON.

Il modello nella distribuzione avvia varie risorse di Azure nella sottoscrizione. Le risorse di Azure distribuite sono:

- **Cluster AKS**: cluster del servizio Azure Kubernetes configurato in base ai parametri di input forniti dal cliente. Il cluster AKS dispone di diversi pod configurati per l'esecuzione dei componenti di rete dell'infrastruttura iperledger. I pod creati sono:

  - **Strumenti di infrastruttura**: strumenti responsabili della configurazione dei componenti dell'infrastruttura iperledger.
  - **Orderer/Pod peer**: nodi della rete dell'infrastruttura iperledger.
  - **Proxy**: un pod proxy ngnix tramite il quale le applicazioni client possono comunicare con il cluster AKS.
  - **CA infrastruttura**: il pod che esegue l'autorità di certificazione dell'infrastruttura.
- **PostgreSQL**: istanza di database che gestisce le identità della CA dell'infrastruttura.

- **Key Vault**: istanza del servizio Azure Key Vault distribuito per salvare le credenziali CA dell'infrastruttura e i certificati radice forniti dal cliente. L'insieme di credenziali viene usato in caso di tentativi di distribuzione del modello, per gestire i meccanismi del modello.
- **Disco gestito**: istanza del servizio Managed Disks di Azure che fornisce un archivio permanente per il Ledger e per il database di stato globale del nodo peer.
- **IP pubblico**: endpoint del cluster AKS distribuito per la comunicazione con il cluster.

## <a name="deploy-the-orderer-and-peer-organization"></a>Distribuire l'ordinatore e l'organizzazione peer

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Per iniziare a distribuire i componenti di rete dell'infrastruttura iperledger, passare alla [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**  >  **blockchain**, quindi cercare **infrastruttura iperledger nel servizio Kubernetes di Azure (anteprima)**.

2. Immettere i dettagli del progetto nella scheda **nozioni di base** .

    ![Screenshot che mostra la scheda nozioni di base.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Immettere i dettagli seguenti:
    - **Sottoscrizione**: scegliere il nome della sottoscrizione in cui si vuole distribuire i componenti di rete dell'infrastruttura iperledger.
    - **Gruppo di risorse**: creare un nuovo gruppo di risorse o scegliere un gruppo di risorse vuoto esistente. Il gruppo di risorse conterrà tutte le risorse distribuite come parte del modello.
    - **Area**: scegliere l'area di Azure in cui si vuole distribuire il cluster del servizio Kubernetes di Azure per i componenti dell'infrastruttura iperledger. Il modello è disponibile in tutte le aree in cui è disponibile AKS. Scegliere un'area in cui la sottoscrizione non raggiunga il limite di quota della macchina virtuale (VM).
    - **Prefisso risorsa**: immettere un prefisso per la denominazione delle risorse distribuite. Deve avere una lunghezza inferiore a sei caratteri e la combinazione di caratteri deve includere sia numeri che lettere.
4. Selezionare la scheda **Impostazioni infrastruttura** per definire i componenti di rete dell'infrastruttura iperledger che verranno distribuiti.

    ![Screenshot che mostra la scheda Impostazioni infrastruttura.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Immettere i dettagli seguenti:
    - **Nome organizzazione**: immettere il nome dell'organizzazione dell'infrastruttura dell'iperledger, che è necessario per varie operazioni del piano dati. Il nome dell'organizzazione deve essere univoco per ogni distribuzione.
    - **Infrastruttura di rete componente**: scegliere **ordinazione servizio** o **nodi peer**, in base al componente di rete blockchain che si desidera configurare.
    - **Numero di nodi**: di seguito sono riportati i due tipi di nodi:
        - **Servizio di ordinamento**: selezionare il numero di nodi per fornire la tolleranza di errore alla rete. Il numero di nodi dell'ordine supportato è 3, 5 e 7.
        - **Nodi peer**: è possibile scegliere tra 1 e 10 nodi in base ai requisiti.
    - **Database di stato globale del nodo peer**: scegliere tra LevelDB e CouchDB. Questo campo viene visualizzato quando si scelgono i **nodi peer** nell'elenco a discesa **componente rete infrastruttura** .
    - **Nome utente CA infrastruttura**: immettere il nome utente usato per l'autenticazione dell'autorità di certificazione dell'infrastruttura.
    - **Password CA infrastruttura**: immettere la password per l'autenticazione dell'autorità di certificazione dell'infrastruttura.
    - **Conferma password**: confermare la password dell'infrastruttura della CA.
    - **Certificati**: se si vuole usare i propri certificati radice per inizializzare l'autorità di certificazione dell'infrastruttura, scegliere l'opzione **Carica certificato radice per la CA dell'infrastruttura** . In caso contrario, la CA dell'infrastruttura crea i certificati autofirmati per impostazione predefinita.
    - **Certificato radice**: caricare il certificato radice (chiave pubblica) con cui deve essere inizializzata l'autorità di certificazione dell'infrastruttura. Sono supportati i certificati del formato con estensione PEM. I certificati devono essere validi e in un fuso orario UTC.
    - **Chiave privata del certificato radice**: caricare la chiave privata del certificato radice. Se si dispone di un certificato con estensione PEM, che dispone di una chiave pubblica e privata combinata, caricarla anche qui.


6. Selezionare la scheda **Impostazioni cluster AKS** per definire la configurazione del cluster del servizio Kubernetes di Azure che rappresenta l'infrastruttura sottostante in cui verranno configurati i componenti di rete dell'infrastruttura iperledger.

    ![Screenshot che mostra la scheda Impostazioni cluster A K S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Immettere i dettagli seguenti:
    - **Nome del cluster Kubernetes**: se necessario, modificare il nome del cluster AKS. Questo campo viene prepopolato in base al prefisso di risorsa fornito.
    - **Kubernetes Version**: scegliere la versione di Kubernetes che verrà distribuita nel cluster. In base all'area selezionata nella scheda **nozioni di base** , le versioni supportate disponibili potrebbero cambiare.
    - **Prefisso DNS**: immettere un prefisso per il nome del Domain Name System (DNS) per il cluster AKS. Si userà DNS per connettersi all'API Kubernetes quando si gestiscono i contenitori dopo aver creato il cluster.
    - **Dimensioni del nodo**: per le dimensioni del nodo Kubernetes, è possibile scegliere dall'elenco delle unità di mantenimento delle scorte (SKU) delle macchine virtuali disponibili in Azure. Per prestazioni ottimali, si consiglia la versione standard DS3 V2.
    - **Numero di nodi: immettere**il numero di nodi Kubernetes da distribuire nel cluster. Si consiglia di mantenere questo numero di nodi uguale o superiore al numero di nodi dell'infrastruttura iperledger specificati nella scheda **Impostazioni infrastruttura** .
    - **ID client dell'entità servizio**: immettere l'ID client di un'entità servizio esistente o crearne uno nuovo. Per l'autenticazione AKS è necessaria un'entità servizio. Vedere i [passaggi per creare un'entità servizio](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Segreto client dell'entità servizio**: immettere il segreto client dell'entità servizio specificata nell'ID client per l'entità servizio.
    - **Conferma segreto client**: confermare il segreto client per l'entità servizio.
    - **Abilitare il monitoraggio dei contenitori**: scegliere di abilitare il monitoraggio di AKS, che consente di effettuare il push dei log AKS nell'area di lavoro log Analytics specificata.
    - **Log Analytics area di lavoro**: l'area di lavoro log Analytics verrà popolata con l'area di lavoro predefinita creata se il monitoraggio è abilitato.

8. Selezionare la scheda **revisione e creazione** . Questo passaggio attiva la convalida per i valori specificati.
9. Al termine della convalida, selezionare **Crea**.

    La distribuzione richiede in genere da 10 a 12 minuti. L'ora può variare a seconda delle dimensioni e del numero di nodi AKS specificati.
10. Al termine della distribuzione, viene inviata una notifica tramite le notifiche di Azure nell'angolo superiore destro.
11. Selezionare **Vai al gruppo di risorse** per controllare tutte le risorse create come parte della distribuzione del modello. Tutti i nomi delle risorse inizieranno con il prefisso specificato nella scheda **nozioni di base** .

## <a name="build-the-consortium"></a>Crea il Consorzio

Per compilare il Consorzio blockchain dopo aver distribuito il servizio di ordinamento e i nodi peer, eseguire i passaggi seguenti in sequenza. Lo script dell'infrastruttura iperledger di Azure (azhlf) consente di configurare il Consorzio, creare il canale ed eseguire operazioni chaincode.

> [!NOTE]
> Lo script di Azure iperledger Fabric (azhlf) è stato aggiornato per offrire altre funzionalità. Se si vuole fare riferimento allo script precedente, vedere il [file Leggimi in GitHub](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Questo script è compatibile con l'infrastruttura di iperledger in Azure Kubernetes Service Template versione 2.0.0 e versioni successive. Per verificare la versione della distribuzione, seguire la procedura descritta in [risoluzione dei problemi](#troubleshoot).

> [!NOTE]
> Lo script viene fornito per facilitare gli scenari di dimostrazione, sviluppo e test. Il canale e il Consorzio creati da questo script hanno criteri di infrastruttura di base per l'iperledger per semplificare gli scenari demo, sviluppo e test. Per la configurazione dell'ambiente di produzione, è consigliabile aggiornare i criteri di infrastruttura dell'iperledger del canale/Consorzio in linea con le esigenze di conformità dell'organizzazione usando le API dell'infrastruttura dell'iperledger nativo.


Tutti i comandi per eseguire lo script dell'infrastruttura iperledger di Azure possono essere eseguiti tramite l'interfaccia della riga di comando di Azure bash. È possibile accedere a Azure Cloud Shell tramite l' ![ opzione infrastruttura iperledger in Azure Kubernetes Service template nell' ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) angolo superiore destro della portale di Azure. Al prompt dei comandi digitare `bash` e premere INVIO per passare all'interfaccia della riga di comando di bash oppure selezionare **bash** dalla barra degli strumenti cloud Shell.

Per ulteriori informazioni, vedere [Azure cloud Shell](../../cloud-shell/overview.md) .

![Screenshot che mostra i comandi in Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Nell'immagine seguente viene illustrato il processo dettagliato per la creazione di un consorzio tra un'organizzazione di ordini e un'organizzazione peer. Le sezioni seguenti illustrano i comandi dettagliati per completare questi passaggi.

![Diagramma del processo per la creazione di un Consortium.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Al termine dell'installazione iniziale, usare l'applicazione client per ottenere le operazioni seguenti:  

- Gestione dei canali
- Gestione del consorzio
- Gestione Chaincode

### <a name="download-client-application-files"></a>Scaricare i file dell'applicazione client

Il primo programma di installazione prevede il download dei file dell'applicazione client. Eseguire i comandi seguenti per scaricare tutti i file e i pacchetti necessari:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Questi comandi clonano il codice dell'applicazione client dell'infrastruttura iperledger di Azure dal repository GitHub pubblico, quindi caricano tutti i pacchetti NPM dipendenti. Una volta completata l'esecuzione del comando, è possibile visualizzare una cartella node_modules nella directory corrente. Tutti i pacchetti necessari vengono caricati nella cartella node_modules.

### <a name="set-up-environment-variables"></a>Configurare le variabili di ambiente

Tutte le variabili di ambiente seguono la convenzione di denominazione delle risorse di Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Impostare le variabili di ambiente per il client dell'organizzazione dell'ordinatrice

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Impostare le variabili di ambiente per il client dell'organizzazione peer

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

In base al numero di organizzazioni peer nel Consorzio, potrebbe essere necessario ripetere i comandi peer e impostare la variabile di ambiente di conseguenza.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Impostare le variabili di ambiente per un account di archiviazione di Azure

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Usare i comandi seguenti per creare un account di archiviazione di Azure. Se si ha già un account di archiviazione di Azure, ignorare questo passaggio.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Usare i comandi seguenti per creare una condivisione file nell'account di archiviazione di Azure. Se si dispone già di una condivisione file, ignorare questo passaggio.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Usare i comandi seguenti per generare una stringa di connessione per una condivisione file di Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importare un profilo di connessione organizzazione, l'identità utente amministratore e MSP

Usare i comandi seguenti per recuperare il profilo di connessione dell'organizzazione, l'identità dell'utente amministratore e il provider di servizi gestiti (MSP) dal cluster del servizio Kubernetes di Azure e archiviare tali identità nell'archivio locale dell'applicazione client. Un esempio di archivio locale è la directory *azhlfTool/Stores* .

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

### <a name="create-a-channel"></a>Creare un canale

Dal client dell'organizzazione dell'ordine, utilizzare il comando seguente per creare un canale che contenga solo l'organizzazione dell'ordine.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Aggiungere un'organizzazione peer per la gestione del Consorzio

>[!NOTE]
> Prima di iniziare con qualsiasi operazione del Consorzio, assicurarsi che sia stata completata la configurazione iniziale dell'applicazione client.  

Per aggiungere un'organizzazione peer in un canale e un consorzio, eseguire i comandi seguenti nell'ordine indicato: 

1.  Dal client dell'organizzazione peer, caricare il MSP dell'organizzazione peer in archiviazione di Azure.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Dal client dell'organizzazione dell'ordine, scaricare il MSP dell'organizzazione peer dall'archiviazione di Azure. Eseguire quindi il comando per aggiungere l'organizzazione peer nel canale e nel Consorzio.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Dal client dell'organizzazione dell'ordine, caricare il profilo di connessione dell'ordine in archiviazione di Azure in modo che l'organizzazione peer possa connettersi ai nodi dell'ordine utilizzando questo profilo di connessione.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Dal client dell'organizzazione peer scaricare il profilo di connessione dell'ordine da archiviazione di Azure. Eseguire quindi il comando per aggiungere nodi peer nel canale.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Analogamente, per aggiungere altre organizzazioni peer nel canale, aggiornare le variabili di ambiente peer in base all'organizzazione peer richiesta e ripetere i passaggi da 1 a 4.

### <a name="set-anchor-peers"></a>Imposta peer di ancoraggio

Dal client dell'organizzazione peer eseguire il comando per impostare peer di ancoraggio per l'organizzazione peer nel canale specificato.

>[!NOTE]
> Prima di eseguire questo comando, assicurarsi che l'organizzazione peer venga aggiunta nel canale usando i comandi di gestione del Consorzio.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` elenco di nodi peer separati da spazi da impostare come peer di ancoraggio. Ad esempio:

  - Impostare `<anchorPeersList>` come `"peer1"` se si desidera impostare solo il nodo Peer1 come peer di ancoraggio.
  - Impostare `<anchorPeersList>` come `"peer1" "peer3"` se si desidera impostare i nodi Peer1 e Peer3 come peer di ancoraggio.

## <a name="chaincode-management-commands"></a>Comandi di gestione Chaincode

>[!NOTE]
> Prima di iniziare con qualsiasi operazione chaincode, verificare di aver eseguito la configurazione iniziale dell'applicazione client.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Impostare le variabili di ambiente specifiche di chaincode

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Installare chaincode  

Eseguire il comando seguente per installare chaincode nell'organizzazione peer.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Il comando installerà chaincode su tutti i nodi peer del set di organizzazioni peer nella `ORGNAME` variabile di ambiente. Se due o più organizzazioni peer si trovano nel canale e si vuole installare chaincode su tutti, eseguire questo comando separatamente per ogni organizzazione peer.  

Seguire questa procedura:  

1.  Impostare `ORGNAME` e `USER_IDENTITY` in base a `peerOrg1` ed eseguire il `./azhlf chaincode install` comando.  
2.  Impostare `ORGNAME` e `USER_IDENTITY` in base a `peerOrg2` ed eseguire il `./azhlf chaincode install` comando.  

### <a name="instantiate-chaincode"></a>Creare un'istanza di chaincode  

Dall'applicazione client peer eseguire il comando seguente per creare un'istanza di chaincode sul canale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Passare il nome della funzione di creazione di istanze e l'elenco di argomenti separati da spazi `<instantiateFunc>` rispettivamente in e `<instantiateFuncArgs>` . Ad esempio, per creare un'istanza di chaincode_example02. go chaincode, impostare `<instantiateFunc>` su `init` e `<instantiateFuncArgs>` su `"a" "2000" "b" "1000"` .

È anche possibile passare il file JSON di configurazione della raccolta usando il `--collections-config` flag. In alternativa, impostare gli argomenti temporanei utilizzando il `-t` flag durante la creazione di istanze di chaincode utilizzate per le transazioni private.

Ad esempio:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

La `<collectionConfigJSONFilePath>` parte è il percorso del file JSON che contiene le raccolte definite per la creazione di un'istanza di dati privati chaincode. È possibile trovare il file JSON di configurazione di una raccolta di esempio relativo alla directory *azhlfTool* nel percorso seguente: `./samples/chaincode/src/private_marbles/collections_config.json` .
Passare `<transientArgs>` come JSON valido in formato stringa. Escape per i caratteri speciali. ad esempio `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Eseguire il comando una sola volta da una qualsiasi organizzazione peer nel canale. Dopo che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Viene quindi creata un'istanza di Chaincode in tutti i nodi peer di tutte le organizzazioni peer nel canale.  

### <a name="invoke-chaincode"></a>Richiama chaincode  

Dal client dell'organizzazione peer eseguire il comando seguente per richiamare la funzione chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Passare il nome della funzione Invoke e l'elenco di argomenti separati da spazi  `<invokeFunction>`   rispettivamente in e  `<invokeFuncArgs>`   . Continuando con l'esempio chaincode_example02. go chaincode, per eseguire un'operazione Invoke, impostare  `<invokeFunction>`   su  `invoke`   e  `<invokeFuncArgs>`   su `"a" "b" "10"` .  

>[!NOTE]
> Eseguire il comando una sola volta da una qualsiasi organizzazione peer nel canale. Dopo che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Lo stato globale viene quindi aggiornato in tutti i nodi peer di tutte le organizzazioni peer nel canale.  


### <a name="query-chaincode"></a>Chaincode query  

Eseguire il comando seguente per eseguire una query su chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
I peer di verifica sono peer in cui chaincode è installato e viene chiamato per l'esecuzione di transazioni. È necessario impostare `<endorsingPeers>` in modo che contenga i nomi dei nodi peer dall'organizzazione peer corrente. Elenca i peer di verifica per una determinata combinazione di chaincode e canale separati da spazi. Ad esempio: `-p "peer1" "peer3"`.

Se si usa *azhlfTool* per installare chaincode, passare tutti i nomi dei nodi peer come valore all'argomento peer di verifica dell'autenticità. Chaincode viene installato in ogni nodo peer per l'organizzazione. 

Passare il nome della funzione di query e l'elenco di argomenti separati da spazi  `<queryFunction>`   rispettivamente in e  `<queryFuncArgs>`   . Riprendendo chaincode_example02. go chaincode come riferimento, per eseguire una query sul valore di "a" nello stato del mondo, impostare  `<queryFunction>`   su  `query` e  `<queryArgs>` su `"a"` .  

## <a name="troubleshoot"></a>Risolvere problemi

Eseguire i comandi seguenti per trovare la versione della distribuzione del modello.

Impostare le variabili di ambiente in base al gruppo di risorse in cui è stato distribuito il modello.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Eseguire il comando seguente per stampare la versione del modello.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Per rimanere sempre aggiornati sulle offerte del servizio blockchain e le informazioni del team di progettazione di Azure blockchain, visitare il [Blog di Azure blockchain](https://azure.microsoft.com/blog/topics/blockchain/).

Per inviare un feedback sul prodotto o richiedere nuove funzionalità, pubblicare o votare un'idea tramite il [forum di feedback su Azure per Blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Supporto della community

Interagisci con i tecnici Microsoft e gli esperti della community di Azure blockchain:

- [Microsoft Q&una pagina](/answers/topics/azure-blockchain-workbench.html) 
   
  Il supporto tecnico per i modelli blockchain è limitato ai problemi di distribuzione.
- [Microsoft Tech community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
