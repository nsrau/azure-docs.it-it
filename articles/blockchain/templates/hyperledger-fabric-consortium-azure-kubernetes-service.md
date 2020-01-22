---
title: Iperledger Fabric Consortium in Azure Kubernetes Service (AKS)
description: Come distribuire e configurare la rete dell'infrastruttura iperledger in Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 59e13b671f68c29271227d481b41562256d66fd6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289646"
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

- **Azure Key Vault**: è stata distribuita un'istanza dell'insieme di credenziali delle chiavi per salvare le credenziali CA dell'infrastruttura e i certificati radice forniti dal cliente, usati in caso di tentativi di distribuzione del modello, in modo da gestire i meccanismi del modello.
- **Disco gestito**di Azure: il disco gestito di Azure è per l'archivio persistente per i database di stato globale e del nodo peer.
- **IP pubblico**: un endpoint IP pubblico del cluster AKS distribuito per l'interazione con il cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configurazione di rete blockchain dell'infrastruttura iperledger

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Configurare l'infrastruttura iperledger blockchain Network attenendosi alla procedura seguente:

- [Distribuire l'organizzazione di ordini/peer](#deploy-the-ordererpeer-organization)
- [Crea il Consorzio](#build-the-consortium)
- [Eseguire operazioni HLF Native](#run-native-hlf-operations)

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
    - **Nome organizzazione**: il nome dell'organizzazione dell'infrastruttura, necessario per varie operazioni del piano dati.
    - **Infrastruttura di rete componente**: scegliere ordinazione servizio o nodi peer in base al componente di rete blockchain che si desidera configurare.
    - **Numero di nodi** : di seguito sono riportati i due tipi di nodi:
        - Servizio di ordinamento: selezionare il numero di nodi per fornire la tolleranza di errore alla rete. Il numero di nodi dell'ordine supportato è solo 3, 5 e 7.
        - Nodi peer: è possibile scegliere i nodi 1-10 in base ai requisiti.
    - **Database di stato globale del nodo peer**: scegliere tra LevelDB e CoucbDB. Questo campo viene visualizzato quando l'utente sceglie il nodo peer nell'elenco a discesa componente rete infrastruttura.
    - **Nome utente infrastruttura**: immettere il nome utente usato per l'autenticazione della CA dell'infrastruttura.
    - **Password CA infrastruttura**: immettere la password per l'autenticazione dell'autorità di certificazione dell'infrastruttura.
    - **Conferma password**: confermare la password dell'infrastruttura della CA.
    - **Certificati**: se si vuole usare i propri certificati radice per inizializzare l'autorità di certificazione dell'infrastruttura, scegliere Carica certificato radice per l'opzione CA infrastruttura. in caso contrario, per impostazione predefinita l'autorità di certificazione crea certificati autofirmati.
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

Per compilare il post di blockchain Consortium per la distribuzione del servizio di ordinamento e dei nodi peer, è necessario eseguire i passaggi seguenti in sequenza. **Compilare** lo script di rete (byn.sh), che consente di configurare il Consorzio, creare il canale e installare chaincode.

> [!NOTE]
> Lo script di compilazione della rete (byn) fornito è strettamente da usare per gli scenari demo/devtest. Per la configurazione del livello di produzione è consigliabile usare le API HLF native.

Tutti i comandi per eseguire lo script byn possono essere eseguiti tramite l'interfaccia della riga di comando (CLI) di Azure bash. È possibile accedere alla versione Web di Azure shell tramite ![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) nell'angolo superiore destro della portale di Azure. Al prompt dei comandi digitare bash e immettere per passare all'interfaccia della riga di comando di bash.

Per ulteriori informazioni, vedere [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Infrastruttura di iperledger nel modello di servizio Kubernetes di Azure](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Scaricare byn.sh e il file Fabric-admin. yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Impostare le variabili di ambiente seguenti nell'interfaccia della riga di comando di Azure bash shell**:

Impostare le informazioni sul canale e l'organizzazione dell'ordine

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Impostare le informazioni sull'organizzazione peer

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Creare una condivisione file di Azure per condividere vari certificati pubblici tra le organizzazioni peer e orderer.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Comandi di gestione canali**

Per creare un nuovo canale, passare al cluster del servizio contenitore di ordini e al comando problema.

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Comandi del Consorzio di gestione**

Eseguire i comandi seguenti nell'ordine indicato per aggiungere un'organizzazione peer in un canale e un consorzio.

1. Passare a peer Organization AKS cluster e caricare il relativo servizio membro (MSP) in una risorsa di archiviazione file di Azure.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Andare al cluster dell'organizzazione del servizio contenitore di ordini e aggiungere l'organizzazione peer nel canale e nel Consorzio.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Tornare all'organizzazione peer ed eseguire il comando per aggiungere nodi peer nel canale.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Analogamente, per aggiungere altre organizzazioni peer nel canale, aggiornare le variabili di ambiente del servizio contenitore di un peer in base all'organizzazione peer richiesta ed eseguire i passaggi da 1 a 3.

**Comandi di gestione Chaincode**

Eseguire il comando seguente per eseguire l'operazione correlata a chaincode. Questi comandi eseguono tutte le operazioni in un chaincode demo. Questa demo chaincode ha due variabili "a" e "b". Quando si crea un'istanza di chaincode, "a" viene inizializzato con 1000 e "b" inizializzato con 2000. A ogni chiamata del chaincode, 10 unità vengono trasferite da "a" a "b". L'operazione di query su chaincode Mostra lo stato globale della variabile "a".

Eseguire i comandi seguenti eseguiti nel cluster AKS dell'organizzazione peer.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Comandi dell'operazione Chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Eseguire operazioni HLF Native

Per aiutare i clienti a iniziare a eseguire comandi nativi di iperledger sulla rete HLF su AKS. Viene fornita l'applicazione di esempio che usa Fabric NodeJS SDK per eseguire le operazioni di HLF. I comandi vengono forniti per creare una nuova identità utente e installare il proprio chaincode.

### <a name="before-you-begin"></a>Prima di iniziare

Seguire i comandi seguenti per la configurazione iniziale dell'applicazione:

- Scaricare i file dell'applicazione
- Genera profilo di connessione e profilo di amministrazione
- Importa identità utente amministratore

Dopo aver completato l'installazione iniziale, è possibile usare l'SDK per ottenere le operazioni seguenti:

- Generazione identità utente
- Operazioni Chaincode

I comandi sopra indicati possono essere eseguiti da Azure Cloud Shell.

### <a name="download-application-files"></a>Scaricare i file dell'applicazione

Il primo programma di installazione per l'applicazione in esecuzione consiste nel scaricare tutti i file dell'applicazione in una cartella.

**Creare la cartella dell'app e immettere nella cartella**:

```bash
mkdir app
cd app
```
Eseguire il comando seguente per scaricare tutti i file e i pacchetti necessari:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Questo comando richiede tempo per caricare tutti i pacchetti. Una volta completata l'esecuzione del comando, è possibile visualizzare una cartella `node_modules` nella directory corrente. Tutti i pacchetti necessari vengono caricati nella cartella `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Genera profilo di connessione e profilo di amministrazione

Creare `profile` directory all'interno della cartella `app`

```bash
cd app
mkdir ./profile
```
Impostare queste variabili di ambiente in Azure cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Eseguire il comando seguente per generare il profilo di connessione e il profilo di amministrazione dell'organizzazione

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Verrà creato un profilo di connessione e un `profile` di amministrazione dell'organizzazione nella cartella del profilo con il nome `<orgname>-ccp.json` e `<orgname>-admin.json` rispettivamente.

Analogamente, generare un profilo di connessione e un profilo di amministrazione per ogni ordinatore e organizzazione peer.


### <a name="import-admin-user-identity"></a>Importa identità utente amministratore

L'ultimo passaggio consiste nell'importare l'identità utente amministratore dell'organizzazione nel portafogli.

```bash
npm run importAdmin -- -o <orgName>

```
Il comando precedente esegue importAdmin. js per importare l'identità dell'utente amministratore nel portafoglio. Lo script legge l'identità amministratore dal profilo di amministrazione `<orgname>-admin.json` e lo importa nel portafogli per l'esecuzione di operazioni HLF.

Gli script usano file system Wallet per archiviare le identità. Viene creato un portafoglio in base al percorso specificato nel campo ". Wallet" nel profilo di connessione. Per impostazione predefinita, il campo ". Wallet" viene inizializzato con `<orgname>`, il che significa che nella directory corrente viene creata una cartella denominata `<orgname>` per archiviare le identità. Se si vuole creare un portafoglio in un altro percorso, modificare il campo ". Wallet" nel profilo di connessione prima di eseguire la registrazione dell'utente amministratore e qualsiasi altra operazione HLF.

Analogamente, importare l'identità utente amministratore per ogni organizzazione.

Per ulteriori informazioni sugli argomenti passati nel comando, vedere la guida del comando.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generazione identità utente

Eseguire i comandi seguenti nell'ordine indicato per generare nuove identità utente per l'organizzazione HLF.

> [!NOTE]
> Prima di iniziare con i passaggi di generazione dell'identità utente, assicurarsi che sia stata eseguita la configurazione iniziale dell'applicazione.

Impostare le variabili di ambiente in Azure cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrare e registrare un nuovo utente

Per registrare e registrare un nuovo utente, eseguire il comando seguente che esegue registerUser. js. Salva l'identità dell'utente generata nel portafogli.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> L'identità dell'utente amministratore viene utilizzata per eseguire il comando Register per il nuovo utente. Di conseguenza, è obbligatorio avere l'identità dell'utente amministratore nel portafoglio prima di eseguire questo comando. In caso contrario, questo comando avrà esito negativo.

Per ulteriori informazioni sugli argomenti passati nel comando, vedere la guida del comando

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operazioni Chaincode


> [!NOTE]
> Prima di iniziare con qualsiasi operazione chaincode, verificare che sia stata eseguita la configurazione iniziale dell'applicazione.

Impostare le variabili di ambiente specifiche di chaincode in Azure cloud Shell:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

È possibile eseguire le operazioni di chaincode seguenti:

- Installare chaincode
- Creare un'istanza di chaincode
- Richiama chaincode
- Chaincode query

### <a name="install-chaincode"></a>Installare chaincode

Eseguire il comando seguente per installare chaincode nell'organizzazione peer.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Il chaincode verrà installato in tutti i nodi peer del set di organizzazioni in `ORGNAME` variabile di ambiente. Se nel canale sono presenti due o più organizzazioni peer e si desidera installare chaincode su tutti i computer, eseguire i comandi separatamente per ogni organizzazione peer.

Attenersi alla procedura seguente:

- Impostare `ORGNAME` su `<peerOrg1Name>` ed emettere `installCC` comando.
- Impostare `ORGNAME` su `<peerOrg2Name>` ed emettere `installCC` comando.

  Eseguirlo per ogni organizzazione peer.

Per altri dettagli sugli argomenti passati nel comando, vedere la guida del comando.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Creare un'istanza di chaincode

Eseguire il comando seguente per creare un'istanza di chaincode nel peer.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Passare il nome della funzione di creazione di istanze e l'elenco delimitato da virgole degli argomenti in `<instantiateFunc>` e `<instantiateFuncArgs>` rispettivamente. Ad esempio, in [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), per creare un'istanza del set di chaincode `<instantiateFunc>` `"Init"` e `<instantiateFuncArgs>` a una stringa vuota `""`.

> [!NOTE]
> Eseguire il comando per una sola volta da una qualsiasi organizzazione peer nel canale.
> Una volta che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Viene quindi creata un'istanza di chaincode in tutti i nodi peer di tutte le organizzazioni peer nel canale.

Per ulteriori informazioni sugli argomenti passati nel comando, vedere la guida del comando

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Richiama chaincode

Eseguire il comando seguente per richiamare la funzione chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Passare il nome della funzione di richiamo e l'elenco delimitato da virgole degli argomenti in `<invokeFunction>` e `<invokeFuncArgs>` rispettivamente. Continuando con l'esempio fabcar chaincode, per richiamare la funzione initLedger set `<invokeFunction>` `"initLedger"` e `<invokeFuncArgs>` per `""`.

> [!NOTE]
> Eseguire il comando per una sola volta da una qualsiasi organizzazione peer nel canale.
> Una volta che la transazione è stata inviata correttamente all'ordinatore, l'ordinatore distribuisce questa transazione a tutte le organizzazioni peer nel canale. Di conseguenza, lo stato globale viene aggiornato in tutti i nodi peer di tutte le organizzazioni peer nel canale.

Per ulteriori informazioni sugli argomenti passati nel comando, vedere la guida del comando

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode query

Eseguire il comando seguente per eseguire una query su chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Passare il nome della funzione di query e l'elenco delimitato da virgole degli argomenti in `<queryFunction>` e `<queryFuncArgs>` rispettivamente. Anche in questo caso, prendendo `fabcar` chaincode come riferimento, per eseguire una query su tutte le automobili del set di Stati del mondo `<queryFunction>` `"queryAllCars"` e `<queryArgs>' to `"" ".

Per ulteriori informazioni sugli argomenti passati nel comando, vedere la guida del comando

```bash
npm run queryCC -- -h

```
