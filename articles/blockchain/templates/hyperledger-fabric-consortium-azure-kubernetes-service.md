---
title: Consorzio Hyperledger Fabric nel servizio Azure Kubernetes (AKS)Hyperledger Fabric consortium on Azure Kubernetes Service (AKS)
description: Come distribuire e configurare la rete del consorzio Hyperledger Fabric nel servizio Azure KubernetesHow to deploy and configure Hyperledger Fabric consortium network on Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476441"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Consorzio Hyperledger Fabric nel servizio Azure Kubernetes (AKS)Hyperledger Fabric consortium on Azure Kubernetes Service (AKS)

È possibile usare il modello di consorzio Hyperledger Fabric (HLF) in Azure Kubernetes Service (AKS) per distribuire e configurare una rete di consorzio Hyperledger Fabric in Azure.You can use the Hyperledger Fabric (HLF) on Azure Kubernetes Service (AKS) template to deploy and configure a Hyperledger Fabric consortium network on Azure.

Dopo avere letto l'articolo, si sarà in grado di:

- Ottenere una conoscenza di lavoro di Hyperledger Fabric e dei vari componenti che formano gli elementi costitutivi della rete blockchain Hyperledger Fabric.
- Informazioni su come distribuire e configurare un consorzio di infrastruttura Hyperledger nel servizio Kubernetes di Azure per gli scenari di produzione.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architettura di Hyperledger Fabric Consortium

Per creare una rete Hyperledger Fabric in Azure, è necessario distribuire il servizio di ordinazione e l'organizzazione con nodi peer. I diversi componenti fondamentali creati come parte della distribuzione del modello sono:

- **Nodi orderer**: Nodo responsabile dell'ordine delle transazioni nella contabilità generale. Insieme ad altri nodi, i nodi ordinati formano il servizio di ordinamento della rete Hyperledger Fabric.

- **Nodi peer**: Un nodo che ospita principalmente i registri e i contratti intelligenti, questi elementi fondamentali della rete.

- **Autorità di certificazione di infrastruttura:** la CA dell'infrastruttura è l'autorità di certificazione (CA) per Hyperledger Fabric. La CA di infrastruttura consente di inizializzare e avviare il processo del server che ospita l'autorità di certificazione. Consente di gestire identità e certificati. Ogni cluster AKS distribuito come parte del modello avrà un pod CA di infrastruttura per impostazione predefinita.

- **CouchDB o LevelDB**: il database dello stato mondiale per i nodi peer può essere archiviato in LevelDB o CouchDB. LevelDB è il database di stato predefinito incorporato nel nodo peer e archivia i dati del chaincode come semplici coppie chiave-valore e supporta solo query chiave, intervallo di chiavi e chiavi composte. CouchDB è un database di stato alternativo facoltativo che supporta le query avanzate quando i valori dei dati chaincode sono modellati come JSON.

Il modello durante la distribuzione crea varie risorse di Azure nella sottoscrizione. Le diverse risorse di Azure distribuite sono:The different Azure resources deployed are:

- **Cluster AKS:** cluster di Azure Kubernetes configurato in base ai parametri di input forniti dal cliente. Il cluster AKS dispone di vari pod configurati per l'esecuzione dei componenti di rete di Hyperledger Fabric. I diversi contenitori creati sono:

  - **Strumenti di**infrastruttura : Lo strumento di infrastruttura è responsabile della configurazione dei componenti di Hyperledger Fabric.
  - **Orderer/peer pods**: I nodi della rete HLF.
  - **Proxy**: un pod proxy NGNIX attraverso il quale le applicazioni client possono interfacciarsi con il cluster AKS.
  - **Fabric CA**: Il pod che esegue la CA dell'infrastruttura.
- **PostgreSQL**: Viene distribuita un'istanza di PostgreSQL per mantenere le identità della CA dell'infrastruttura.

- **Insieme di credenziali delle**chiavi di Azure: viene distribuita un'istanza dell'insieme di credenziali delle chiavi per salvare le credenziali della CA di infrastruttura e i certificati radice forniti dal cliente, usati in caso di tentativi di distribuzione del modello, per gestire i meccanismi del modello.
- **Disco gestito di Azure:** il disco gestito di Azure è per l'archivio permanente per il database dello stato mondiale del registro e del nodo peer.
- **IP pubblico:** endpoint IP pubblico del cluster AKS distribuito per l'interfacciamento con il cluster.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Configurazione della rete Hyperledger Fabric Blockchain

Per iniziare, è necessaria una sottoscrizione di Azure in grado di supportare la distribuzione di più macchine virtuali e account di archiviazione standard. Se non si ha una sottoscrizione di Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/).

Impostare la rete Blockchain Hyperledger Fabric attenendosi alla seguente procedura:

- [Distribuire l'organizzazione orderer/peer](#deploy-the-ordererpeer-organization)
- [Costruire il consorzio](#build-the-consortium)
- [Eseguire operazioni HLF nativeRun native HLF operations](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Distribuire l'organizzazione orderer/peer

Per iniziare a usare la distribuzione dei componenti di rete HLF, passare al [portale di Azure.](https://portal.azure.com) Selezionare **Crea una risorsa > Blockchain** > cercare Infrastruttura **Hyperledger nel servizio Azure Kubernetes**.

1. Selezionare **crea** per avviare la distribuzione del modello. Viene visualizzata la finestra **crea infrastruttura Hyperledger nel servizio Azure Kubernetes.The Create Hyperledger Fabric on Azure Kubernetes Service** displays.

    ![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Immettere i dettagli del progetto nella pagina **Nozioni di base.**

    ![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Immettere i dettagli seguenti:
    - **Sottoscrizione**: scegliere il nome della sottoscrizione in cui distribuire i componenti di rete HLF.
    - **Gruppo**di risorse: creare un nuovo gruppo di risorse o scegliere un gruppo di risorse vuoto esistente, il gruppo di risorse conterrà tutte le risorse distribuite come parte del modello.
    - **Area:** scegliere l'area di Azure in cui si vuole distribuire il cluster Azure Kubernetes per i componenti HLF. Il modello è disponibile in tutte le aree in cui AKS è disponibile Assicurarsi di scegliere un'area in cui la sottoscrizione non raggiungono il limite di quota della macchina virtuale (VM).
    - **Prefisso risorsa**: Prefisso per la denominazione delle risorse distribuite. Il prefisso della risorsa deve avere una lunghezza inferiore a sei caratteri e la combinazione di caratteri deve includere sia numeri che lettere.
4. Selezionare la scheda **Impostazioni infrastruttura** per definire i componenti di rete HLF che verranno distribuiti.

    ![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Immettere i dettagli seguenti:
    - **Nome organizzazione**: il nome dell'organizzazione Fabric, necessario per varie operazioni del piano dati. Il nome dell'organizzazione deve essere univoco per ogni distribuzione. 
    - **Componente**di rete dell'infrastruttura : scegliere i nodi Servizio di ordinazione o Peer in base al componente di rete Blockchain che si desidera configurare.
    - **Numero di nodi** - I seguenti sono i due tipi di nodi:
        - Servizio di ordinazione: selezionare il numero di nodi per fornire la tolleranza di errore alla rete. Solo 3,5 e 7 sono il numero di nodi orderer supportati.
        - Nodi peer: è possibile scegliere da 1 a 10 nodi in base alle proprie esigenze.
    - **Database dello stato del mondo**del nodo peer: scegliere tra LevelDB e CoucbDB. Questo campo viene visualizzato quando l'utente sceglie il nodo peer nell'elenco a discesa Componente di rete dell'infrastruttura.
    - **Nome utente fabric**: Immettere il nome utente utilizzato per l'autenticazione della CA dell'infrastruttura.
    - **Password CA fabric**: Immettere la password per l'autenticazione della CA dell'infrastruttura.
    - **Conferma password**: Confermare la password della CA di infrastruttura.
    - **Certificati**: Se si desidera utilizzare i propri certificati radice per inizializzare la CA dell'infrastruttura, scegliere l'opzione Carica certificato radice per CA dell'infrastruttura, altrimenti per impostazione predefinita la CA dell'infrastruttura crea certificati autofirmati.
    - **Certificato radice:** caricare il certificato radice (chiave pubblica) con cui è necessario inizializzare la CA dell'infrastruttura. Sono supportati i certificati di formato .pem, i certificati devono essere validi nel fuso orario UTC.
    - **Chiave privata certificato radice**: Caricare la chiave privata del certificato radice. Se si dispone di un certificato .pem, che ha combinato sia la chiave pubblica che la chiave privata, caricarlo anche qui.


6. Selezionare la scheda **Impostazioni cluster AKS** per definire la configurazione del cluster Azure Kubernetes che è l'infrastruttura sottostante in cui verranno impostati i componenti di rete dell'infrastruttura.

    ![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Immettere i dettagli seguenti:
    - **Nome cluster Kubernetes**: nome del cluster AKS creato. Questo campo viene prepopolato in base al prefisso della risorsa fornito, è possibile modificare se necessario.
    - **Kubernetes versione**: La versione dei Kubernetes che verrà distribuito sul cluster. In base all'area selezionata nella scheda **Nozioni di base,** le versioni supportate disponibili potrebbero cambiare.
    - **Prefisso DNS**: Prefisso nome DNS (Domain Name System) per il cluster AKS. Utilizzerai DNS per connetterti all'API Kubernetes quando gestisci i contenitori dopo la creazione del cluster.
    - **Dimensione del nodo:** la dimensione del nodo Kubernetes, è possibile scegliere dall'elenco delle unità di conservazione (SKU) di VM Stock disponibili in Azure.Node size : The size of the Kubernetes node, you can choose from the list of VM Stock keeping unit (SKUs) available on Azure. Per prestazioni ottimali, si consiglia Standard DS3 v2.
    - **Conteggio nodi**: il conteggio del numero di nodi Kubernetes da distribuire nel cluster. È consigliabile mantenere questo conteggio dei nodi almeno uguale o superiore al numero di nodi HLF specificato nelle impostazioni dell'infrastruttura.
    - **ID client dell'entità servizio:** immettere l'ID client di un'entità servizio esistente o crearne una nuova, necessaria per l'autenticazione AKS. Vedere la procedura per [creare l'entità servizio](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Segreto client dell'entità servizio:** immettere il segreto client dell'entità servizio fornito nell'ID client dell'entità servizio.
    - **Conferma segreto client:** confermare il segreto client fornito nel segreto client dell'entità servizio.
    - **Abilita monitoraggio contenitore**: scegliere di abilitare il monitoraggio AKS, che consente ai log AKS di eseguire il push nell'area di lavoro di Log Analytics specificata.
    - **Area di lavoro di Log Analytics:** l'area di lavoro Analisi log verrà popolata con l'area di lavoro predefinita creata se il monitoraggio è abilitato.

8. Dopo aver fornito tutti i dettagli sopra riportati, seleziona **Revisione e crea** scheda. La revisione e la creazione attiva la convalida per i valori forniti.
9. Una volta superata la convalida, è possibile selezionare **create**.
La distribuzione richiede in genere 10-12 minuti, può variare a seconda delle dimensioni e del numero di nodi AKS specificati.
10. Dopo la distribuzione corretta, si riceve una notifica tramite le notifiche di Azure nell'angolo in alto a destra.
11. Selezionare **Vai al gruppo** di risorse per controllare tutte le risorse create nell'ambito della distribuzione del modello. Tutti i nomi delle risorse inizieranno con il prefisso fornito nell'impostazione **Nozioni di base.**

## <a name="build-the-consortium"></a>Costruire il consorzio

Per creare il consorzio blockchain post distribuzione del servizio di ordinazione e dei nodi peer, è necessario eseguire i passaggi seguenti in sequenza. **Crea il tuo** script di rete (byn.sh), che ti aiuta a configurare il consorzio, creare il canale e installare il codice di catena.

> [!NOTE]
> Crea lo script di rete (byn) fornito è strettamente da utilizzare per gli scenari demo/devtest. Per l'installazione del grado di produzione è consigliabile usare le API HLF native.

Tutti i comandi per eseguire lo script byn possono essere eseguiti tramite l'interfaccia della riga di comando (CLI) di Azure Bash. È possibile accedere alla versione Web della shell di Azure tramite ![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) nell'angolo in alto a destra del portale di Azure. Al prompt dei comandi, digitare bash e Enter per passare a bash CLI.

Per altre [informazioni,](https://docs.microsoft.com/azure/cloud-shell/overview) vedere Shell di Azure.See Azure shell for more information.

![Hyperledger Fabric on Azure Kubernetes Service Template](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Scarica byn.sh e il file fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Impostare le variabili di ambiente seguenti nella shell Bash dell'interfaccia della riga di comando di Azure:Set below**environment variables on Azure CLI Bash shell :

Impostare le informazioni sul canale e le informazioni sull'organizzazione degli ordinatori

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
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

Creare una condivisione File di Azure per condividere vari certificati pubblici tra organizzazioni peer e orderer.

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
**Comandi di gestione dei canali**

Vai all'organizzazione dell'ordinatore cluster AKS ed emettere il comando per creare un nuovo canale

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Comandi di gestione del Consorzio**

Eseguire i comandi seguenti nell'ordine specificato per aggiungere un'organizzazione peer in un canale e in un consorzio.

1. Passare a Cluster AKS dell'organizzazione peer e caricare il relativo servizio membro fornito (MSP) in un archivio file di Azure.Go to Peer Organization AKS Cluster and upload its Member Service Provide (MSP) on an Azure File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Passare al cluster AKS dell'organizzazione orderer e aggiungere l'organizzazione peer nel canale e nel consorzio.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Tornare all'organizzazione peer ed eseguire il comando per unire i nodi peer nel canale.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Analogamente, per aggiungere altre organizzazioni peer nel canale, aggiornare le variabili di ambiente AKS peer in base all'organizzazione peer richiesta ed eseguire i passaggi da 1 a 3.

**Comandi di gestione del codice a catena**

Eseguire il comando seguente per eseguire l'operazione relativa a chaincode. Questi comandi eseguono tutte le operazioni su un chaincode demo. Questo chaincode demo ha due variabili "a" e "b". Durante la creazione di un'istanza del codice a catena, "a" viene inizializzato con 1000 e "b" viene inizializzato con 2000. Ad ogni chiamata del codice a catena, 10 unità vengono trasferite da "a" a "b". L'operazione di query su chaincode mostra lo stato mondiale della variabile "a".

Eseguire i comandi seguenti eseguiti nel cluster AKS dell'organizzazione peer.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Comandi di funzionamento del codice a catenaChaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Eseguire operazioni HLF nativeRun native HLF operations

Per aiutare i clienti a iniziare con l'esecuzione di comandi nativi Hyperledger sulla rete HLF su AKS. Viene fornita l'applicazione di esempio che usa l'infrastruttura NodeJS SDK per eseguire le operazioni HLF. I comandi vengono forniti a Creare una nuova identità utente e installare il proprio codice a catena.

### <a name="before-you-begin"></a>Prima di iniziare

Seguire i seguenti comandi per la configurazione iniziale dell'applicazione:

- Scaricare i file dell'applicazione
- Generare il profilo di connessione e il profilo di amministratore
- Importare l'identità utente amministratoreImport admin user identity

Dopo aver completato la configurazione iniziale, è possibile utilizzare l'SDK per ottenere le seguenti operazioni:

- Generazione dell'identità utente
- Operazioni Chaincode

I comandi sopra menzionati possono essere eseguiti da Azure Cloud Shell.

### <a name="download-application-files"></a>Scaricare i file dell'applicazione

La prima configurazione per l'esecuzione dell'applicazione consiste nel scaricare tutti i file dell'applicazione in una cartella.

**Crea la cartella dell'app e inseriscila nella cartella:**

```bash
mkdir app
cd app
```
Eseguire il comando seguente per scaricare tutti i file e i pacchetti necessari:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
Questo comando richiede tempo per caricare tutti i pacchetti. Dopo l'esecuzione del comando, `node_modules` è possibile visualizzare una cartella nella directory corrente. Tutti i pacchetti necessari `node_modules` vengono caricati nella cartella.

### <a name="generate-connection-profile-and-admin-profile"></a>Generare il profilo di connessione e il profilo di amministratore

Crea `profile` directory `app` all'interno della cartella

```bash
cd app
mkdir ./profile
```
Impostare queste variabili di ambiente nella shell cloud di AzureSet these environment variables on Azure cloud shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Esegui sotto il comando per generare il profilo di connessione e il profilo amministratore dell'organizzazione

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Verrà creato il profilo `profile` di connessione e l'amministratore dell'organizzazione nella cartella del profilo con nome `<orgname>-ccp.json` e `<orgname>-admin.json` rispettivamente.

Analogamente, generare il profilo di connessione e il profilo di amministratore per ogni organizzazione di orderer e peer.


### <a name="import-admin-user-identity"></a>Importare l'identità utente amministratoreImport admin user identity

L'ultimo passaggio consiste nell'importare l'identità dell'utente amministratore dell'organizzazione nel portafoglio.

```bash
npm run importAdmin -- -o <orgName>

```
Il comando precedente esegue importAdmin.js per importare l'identità dell'utente admin nel portafoglio. Lo script legge l'identità `<orgname>-admin.json` dell'amministratore dal profilo di amministratore e la importa in portafoglio per l'esecuzione di operazioni HLF.

Gli script utilizzano portafoglio file system per memorizzare le identità. Crea un portafoglio in base al percorso specificato nel campo ".wallet" nel profilo di connessione. Per impostazione predefinita, il campo `<orgname>`".wallet" viene `<orgname>` inizializzato con , il che significa che una cartella denominata viene creata nella directory corrente per archiviare le identità. Se si desidera creare portafoglio in un altro percorso, modificare il campo ".wallet" nel profilo di connessione prima di eseguire registrare l'utente amministratore di registrazione e qualsiasi altra operazione HLF.

Analogamente, importare l'identità utente di amministrazione per ogni organizzazione.

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generazione dell'identità utente

Eseguire i comandi seguenti nell'ordine specificato per generare nuove identità utente per l'organizzazione HLF.

> [!NOTE]
> Prima di iniziare con i passaggi di generazione dell'identità utente, assicurarsi che venga eseguita la configurazione iniziale dell'applicazione.

Impostare le variabili di ambiente seguenti nella shell cloud di AzureSet below environment variables on azure cloud shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Registrare e registrare un nuovo utente

Per registrare e registrare il nuovo utente, eseguire il comando seguente che esegue registerUser.js.To register and enroll new user, execute the below command that executes registerUser.js. Salva l'identità dell'utente generato nel portafoglio.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> L'identità dell'utente amministratore viene utilizzata per emettere il comando register per il nuovo utente. Pertanto, è obbligatorio avere l'identità dell'utente amministratore nel portafoglio prima di eseguire questo comando. In caso contrario, questo comando avrà esito negativo.

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operazioni Chaincode


> [!NOTE]
> Prima di iniziare con qualsiasi operazione di catena di codici, assicurarsi che venga eseguita la configurazione iniziale dell'applicazione.

Set di seguito catena di variabili di ambiente specifiche nella shell del cloud di Azure:Set below below chaincode specific environment variables on Azure Cloud shell:

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

Le seguenti operazioni di chaincode possono essere eseguite:

- Installare chaincode
- Creare un'istanza del codice di catena
- Richiamare il codice di catenaInvoke chaincode
- Catena di query

### <a name="install-chaincode"></a>Installare chaincode

Eseguire il comando below per installare chaincode nell'organizzazione peer.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Verrà installato chaincode in tutti i nodi `ORGNAME` peer dell'organizzazione impostati nella variabile di ambiente. Se nel canale sono presenti due o più organizzazioni peer e si desidera installare chaincode in tutte le organizzazioni, eseguire i comandi separatamente per ogni organizzazione peer.

Attenersi alla seguente procedura:

- `ORGNAME` Impostare `<peerOrg1Name>` su `installCC` ed eseguire il comando.
- `ORGNAME` Impostare `<peerOrg2Name>` su `installCC` ed eseguire il comando.

  Eseguirlo per ogni organizzazione peer.

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Creare un'istanza del codice di catena

Eseguire il comando seguente per creare un'istanza di chaincode nel peer.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Passare il nome della funzione di creazione `<instantiateFunc>` di `<instantiateFuncArgs>` istanze e l'elenco separato da virgole di argomenti rispettivamente. Ad esempio, in [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), `<instantiateFunc>` per `"Init"` `<instantiateFuncArgs>` creare un'istanza del chaincode impostato su e su una stringa `""`vuota.

> [!NOTE]
> Eseguire il comando per una volta da qualsiasi organizzazione peer nel canale.
> Una volta inviata correttamente la transazione all'ordinatore, l'ordinatore distribuisce la transazione a tutte le organizzazioni peer nel canale. Di conseguenza, viene creata un'istanza del chaincode su tutti i nodi peer in tutte le organizzazioni peer nel canale.

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Richiamare il codice di catenaInvoke chaincode

Eseguire il comando seguente per richiamare la funzione chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Passare il nome della funzione invoke `<invokeFunction>` e `<invokeFuncArgs>` l'elenco separato da virgole di argomenti rispettivamente. Continuando con l'esempio fabcar chaincode, `<invokeFunction>` per `"initLedger"` `<invokeFuncArgs>` richiamare la funzione initLedger impostata su e su `""`.

> [!NOTE]
> Eseguire il comando per una volta da qualsiasi organizzazione peer nel canale.
> Una volta inviata correttamente la transazione all'ordinatore, l'ordinatore distribuisce la transazione a tutte le organizzazioni peer nel canale. Di conseguenza, lo stato mondiale viene aggiornato su tutti i nodi peer di tutte le organizzazioni peer nel canale.

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Catena di query

Eseguire il comando seguente per eseguire query chaincode:Execute below command to query chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Passare il nome della funzione di `<queryFunction>` query `<queryFuncArgs>` e l'elenco di argomenti separati da virgole rispettivamente. Anche in `fabcar` questo caso, prendendo chaincode come riferimento, `<queryFunction>` `"queryAllCars"` per `<queryArgs>` `""`interrogare tutte le auto nello stato del mondo impostato su e su .

Consultare la Guida del comando per ulteriori dettagli sugli argomenti passati nel comando

```bash
npm run queryCC -- -h

```
