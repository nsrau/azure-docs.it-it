---
title: Distribuire il modello di soluzione del consorzio Ethereum Proof-of-Authority in AzureDeploy Ethereum Proof-of-Authority consortium solution template on Azure
description: Utilizzare la soluzione del consorzio Ethereum Proof-of-Authority per distribuire e configurare una rete Ethereum di un consorzio multimembro in AzureUse the Ethereum Proof-of-Authority consortium solution to deploy and configure a multi-member consortium Ethereum network on Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387500"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuire il modello di soluzione del consorzio Ethereum proof-of-authority in AzureDeploy Ethereum proof-of-authority consortium solution template on Azure

È possibile usare il modello di soluzione Azure di [anteprima del Consorzio Dic-of-Authority di Ethereum](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) per distribuire, configurare e gestire una rete Ethereum del consorzio con conoscenze di Azure ed Ethereum minime.

Il modello di soluzione può essere usato da ogni membro del consorzio per eseguire il provisioning di un footprint di rete blockchain usando i servizi di calcolo, rete e archiviazione di Microsoft Azure.The solution template can be used by each consortium member to provision a blockchain network footprint using Microsoft Azure compute, networking, and storage services. Il footprint di rete di ogni membro del consorzio è costituito da un set di nodi validator con cui un'applicazione o un utente può interagire per inviare transazioni Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Scegliere una soluzione Azure BlockchainChoose an Azure Blockchain solution

Prima di scegliere di usare il modello di soluzione del consorzio Ethereum proof-of-authority, confrontare lo scenario con i casi d'uso comuni delle opzioni Blockchain di Azure disponibili.

Opzione | Modello di servizio | Caso d'uso comune
-------|---------------|-----------------
Modelli di soluzioni | IaaS | I modelli di soluzione sono modelli di Azure Resource Manager che è possibile usare per eseguire il provisioning di una topologia di rete blockchain completamente configurata. I modelli distribuiscono e configurano i servizi di calcolo, rete e archiviazione di Microsoft Azure per un determinato tipo di rete blockchain.
[Servizio Azure Blockchain](../service/overview.md) | PaaS | Azure Blockchain Service Preview semplifica la formazione, la gestione e la governance delle reti blockchain del consorzio. Usa il servizio Blockchain di Azure per le soluzioni che richiedono PaaS, la gestione del consorzio o la privacy di contratti e transazioni.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | L'anteprima di Azure Blockchain Workbench è una raccolta di servizi e funzionalità di Azure che consentono di creare e distribuire applicazioni blockchain per condividere dati e processi di business con altre organizzazioni. Usa Azure Blockchain Workbench per la creazione di prototipi di una soluzione blockchain o di un concetto di prova dell'applicazione blockchain.

## <a name="solution-architecture"></a>Architettura della soluzione

Utilizzando il modello di soluzione Ethereum, è possibile distribuire una rete di consorzio Ethereum proof-of-authority basata su più o più aree.

![Architettura di distribuzione](./media/ethereum-poa-deployment/deployment-architecture.png)

Ogni distribuzione di un membro di un consorzio include:

* Macchine virtuali per l'esecuzione dei validator PoA
* Azure Load Balancer per la distribuzione di richieste RPC, peering e governance dAppAzure Load Balancer for distributing RPC, peering, and governance DApp requests
* Azure Key Vault per proteggere le identità dei validator
* Archiviazione di Azure per ospitare le informazioni di rete persistenti e coordinare il leasing
* Monitoraggio di Azure per l'aggregazione dei log e delle statistiche sulle prestazioni
* Gateway di rete virtuale (facoltativo) per consentire le connessioni VPN tra reti virtuali private

Per impostazione predefinita, gli endpoint RPC e peering sono accessibili tramite IP pubblico per abilitare la connettività semplificata tra le sottoscrizioni e i cloud. Per i controlli di accesso a livello di applicazione, è possibile utilizzare [i contratti di autorizzazione di Parity.](https://wiki.parity.io/Permissioning) Sono supportate le reti distribuite dietro le VPN, che sfruttano i gateway VNet per la connettività tra sottoscrizioni. Poiché le distribuzioni VPN e VNet sono più complesse, è consigliabile iniziare con un modello IP pubblico durante la creazione di prototipi di una soluzione.

I contenitori Docker vengono utilizzati per l'affidabilità e la modularità. Il Registro di sistema del contenitore di Azure viene usato per ospitare e gestire le immagini con controllo delle versioni come parte di ogni distribuzione. Le immagini del contenitore sono costituite da:

* Orchestrator - Genera identità e contratti di governance. Archivia le identità in un archivio identità.
* Client di parità: consente di affittare l'identità dall'archivio identità. Individua e si connette ai colleghi.
* Agente EthStats: raccoglie i log e le statistiche locali tramite RPC e invia le informazioni in Monitoraggio di Azure.EthStats Agent - Collects local logs and stats via RPC and pushes information to Azure Monitor.
* Governance DApp - Interfaccia Web per l'interazione con i contratti governance.

### <a name="validator-nodes"></a>Nodi di convalida

Nel protocollo Proof-of-Authority, i nodi di convalida prendono il posto dei tradizionali nodi di mining. Ogni validatore ha un'identità Ethereum univoca che gli permette di partecipare al processo di creazione del blocco. Ogni membro del consorzio può effettuare il provisioning di due o più nodi di convalida in cinque aree, per la ridondanza geografica. I nodi di convalida comunicano con altri nodi di convalida per convergere sullo stato del libro mastro distribuito sottostante. Per garantire una partecipazione equa alla rete, a ciascun membro del consorzio è vietato l'utilizzo di un numero di validatori superiore a quello del primo membro della rete. Ad esempio, se il primo membro distribuisce tre validator, ogni membro può avere solo fino a tre validator.

### <a name="identity-store"></a>Archivio di identità

Un archivio identità viene distribuito nella sottoscrizione di ogni membro che contiene in modo sicuro le identità Ethereum generate. Per ogni validator, il contenitore dell'orchestrazione genera una chiave privata Ethereum e la archivia nell'insieme di credenziali delle chiavi di Azure.For each validator, the orchestration container generates an Ethereum private key and stores it in Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Distribuire la rete del consorzio Ethereum

In questa procedura, supponiamo che si sta creando una rete di consorzio Ethereum multi-parte. Il flusso seguente è un esempio di distribuzione con più parti:The following flow is an example of a multi-party deployment:

1. Tre membri che generano ognuno un account Ethereum usando MetaMask
1. *Il membro A* schiera Ethereum PoA, fornendo il loro indirizzo pubblico Ethereum
1. Il *membro A* fornisce l'URL del consorzio al *membro B* e al *membro C*
1. Il *membro B* e il *membro C* distribuiscono Ethereum PoA, fornendo l'indirizzo pubblico di Ethereum e l'URL del consorzio del *membro A*
1. Il *membro A* vota il *membro B* come amministratore
1. Il *membro A* e il *membro B* votano entrambi il *membro C* come amministratore

Nelle sezioni successive viene illustrato come configurare l'impronta del primo membro nella rete.

### <a name="create-resource"></a>Crea risorsa

Nel [portale](https://portal.azure.com)di Azure selezionare **Crea una risorsa** nell'angolo superiore sinistro.

Selezionare **Blockchain** > **Ethereum Proof-of-Authority Consortium (anteprima)**.

### <a name="basics"></a>Nozioni di base

In **Nozioni di base**specificare i valori per i parametri standard per qualsiasi distribuzione.

![Nozioni di base](./media/ethereum-poa-deployment/basic-blade.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Creare una nuova rete o partecipare a una rete esistente | È possibile creare una nuova rete di consorzio o partecipare a una rete di consorzio preesistente. L'aggiunta a una rete esistente richiede parametri aggiuntivi. | Creare un nuovo gruppo di risorse
Indirizzo di posta elettronica | Si riceve una notifica tramite posta elettronica al termine della distribuzione con informazioni sulla distribuzione. | Un indirizzo email valido
Nome utente macchina virtuale | Nome utente amministratore di ogni macchina virtuale distribuita | 1-64 caratteri alfanumerici
Tipo di autenticazione | Metodo per l'autenticazione per la macchina virtuale. | Password
Password | Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. Tutte le macchine virtuali hanno inizialmente la stessa password. È possibile modificare la password dopo il provisioning. | 12-72 caratteri 
Subscription | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio |
Gruppo di risorse| Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | myResourceGroup
Location | La regione di Azure per gruppo di risorse. | Stati Uniti occidentali 2

Selezionare **OK**.

### <a name="deployment-regions"></a>Regioni di distribuzione

In *Aree di distribuzione*specificare il numero di aree e posizioni per ognuna. È possibile eseguire la distribuzione in un massimo di cinque aree. La prima area deve corrispondere alla posizione del gruppo di risorse dalla sezione *Nozioni di base.* Per le reti di sviluppo o di test, è possibile usare una singola area per membro. Per la produzione, eseguire la distribuzione in due o più aree per garantire la disponibilità elevata.

![aree di distribuzione](./media/ethereum-poa-deployment/deployment-regions.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Numero di regioni|Numero di aree per la distribuzione della rete di consorzio| 2
Prima regione | Prima regione per la distribuzione della rete di consorzio | Stati Uniti occidentali 2
Seconda regione | Seconda regione per la distribuzione della rete del consorzio. Altre aree sono visibili quando il numero di regioni è pari o superiore a due. | Stati Uniti orientali 2

Selezionare **OK**.

### <a name="network-size-and-performance"></a>Dimensioni e prestazioni della rete

In *Dimensioni e prestazioni rete*specificare gli input per le dimensioni della rete del consorzio. La dimensione di archiviazione del nodo validator determina la dimensione potenziale della blockchain. La dimensione può essere modificata dopo la distribuzione.

![Dimensioni e prestazioni della rete](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Number of load balanced validator nodes (Numero di nodi di convalida con carico bilanciato) | Numero di nodi validator di cui eseguire il provisioning come parte della rete. | 2
Validator node storage performance (Prestazioni di archiviazione dei nodi di convalida) | Tipo di disco gestito per ognuno dei nodi validator distribuiti. Per informazioni dettagliate sui prezzi, vedi [Prezzi dello storage](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Validator node virtual machine size (Dimensioni della macchina virtuale dei nodi convalida) | Dimensioni della macchina virtuale usata per i nodi di convalida. Per informazioni dettagliate sui prezzi, vedere [Prezzi delle macchine virtualiFor](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) details on pricing, see virtual machine pricing | Standard D2 v3

La macchina virtuale e il livello di archiviazione influiscono sulle prestazioni della rete.  Utilizzare la tabella seguente per scegliere l'efficienza dei costi:

SKU della macchina virtuale|Livello di archiviazione|Price|Velocità effettiva|Latenza
---|---|---|---|---
F1|SSD Standard|low|low|high
D2_v3|SSD Standard|media|media|media
F16s|SSD Premium|high|high|low

Selezionare **OK**.

### <a name="ethereum-settings"></a>Impostazioni Ethereum

In *Impostazioni Ethereum*specificare le impostazioni di configurazione relative a Ethereum.

![Impostazioni Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Consortium Member ID (ID membro del consorzio) | ID associato a ciascun membro che partecipa alla rete del consorzio. Viene utilizzato per configurare gli spazi di indirizzi IP per evitare conflitti. Per una rete privata, l'ID membro deve essere univoco tra organizzazioni diverse nella stessa rete.  È necessario un ID membro univoco anche quando la stessa organizzazione esegue la distribuzione in più regioni. Prendere nota del valore di questo parametro poiché è necessario condividerlo con altri membri uniti per assicurarsi che non vi sia alcuna collisione. L'intervallo valido è compreso tra 0 e 255. | 0
ID rete | L'ID di rete per la rete di consorzio Ethereum in fase di realizzazione. Ogni rete Ethereum ha il proprio ID di rete, di cui 1 è l'ID della rete pubblica. L'intervallo valido è compreso tra 5 e 999.999.999 | 10101010
Admin Ethereum Address (Indirizzo Ethereum amministratore) | L'indirizzo dell'account Ethereum utilizzato per partecipare alla governance PoA. È possibile utilizzare MetaMask per generare un indirizzo Ethereum. |
Opzioni avanzate | Opzioni avanzate per le impostazioni di Ethereum | Abilitare
Eseguire la distribuzione tramite IP pubblicoDeploy using Public IP | Se è selezionata l'opzione Rete virtuale privata, la rete viene distribuita dietro un gateway della rete virtuale e rimuove l'accesso peering. Per la rete virtuale privata, tutti i membri devono utilizzare un gateway di rete virtuale affinché la connessione sia compatibile. | IP pubblico
Limite di gas di blocco | Limite iniziale del gas di blocco della rete. | 50000000
Block Reseal Period (sec) (Periodo nuovo sigillo blocco (sec)) | Frequenza con cui verranno creati blocchi vuoti in assenza di transazioni in rete. Una frequenza maggiore determina una finalità più rapida, ma costi di archiviazione maggiori. | 15
Contratto di autorizzazione delle transazioniTransaction Permission Contract | Bytecode per il contratto di concessione delle autorizzazioni per le transazioni. Limita la distribuzione e l'esecuzione di contratti intelligenti a un elenco consentito di account Ethereum. |

Selezionare **OK**.

### <a name="monitoring"></a>Monitoraggio

Il monitoraggio consente di configurare una risorsa di registro per la rete. L'agente di monitoraggio raccoglie ed espone metriche e log utili dalla rete, offrendo la possibilità di controllare rapidamente l'integrità della rete o i problemi di debug.

![Monitoraggio di Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Monitoraggio | Opzione per abilitare il monitoraggio | Abilitare
Connettersi ai log di Monitoraggio di Azure esistentiConnect to existing Azure Monitor logs | Opzione per creare una nuova istanza dei log di Monitoraggio di Azure o aggiungersi a un'istanza esistenteOption to create a new Azure Monitor logs instance or join an existing instance | Creare un nuovo gruppo di risorse
Location | Area in cui viene distribuita la nuova istanza | Stati Uniti orientali
ID area di lavoro di log analytics esistente (Connessione a log di Monitoraggio di Azure esistenti - Join esistente)Existing log analytics workspace ID (Connect to existing Azure Monitor logs - Join Existing)|ID area di lavoro dell'istanza di log di Monitoraggio di Azure esistente||ND
Chiave primaria di log analytics esistente (Connessione ai log di Monitoraggio di Azure esistenti e aggiunta a un'altra)|Chiave primaria usata per connettersi all'istanza di log di Monitoraggio di Azure esistenteThe primary key used to connect to the existing Azure Monitor logs instance||ND

Selezionare **OK**.

### <a name="summary"></a>Riepilogo

Fare clic sul riepilogo per esaminare gli input specificati ed eseguire la convalida pre-distribuzione di base. Prima della distribuzione, è possibile scaricare il modello e i parametri.

Selezionare **Crea** per la distribuzione.

Se la distribuzione include gateway di rete virtuale, la distribuzione può richiedere da 45 a 50 minuti.

## <a name="deployment-output"></a>Output di distribuzione

Al termine della distribuzione, è possibile accedere ai parametri necessari tramite il portale di Azure.Once the deployment has completed, you can access the necessary parameters using the Azure portal.

### <a name="confirmation-email"></a>Messaggio di posta elettronica di conferma

Se si specifica un indirizzo di posta elettronica[(Sezione Nozioni di base](#basics)), viene inviato un messaggio di posta elettronica che include le informazioni sulla distribuzione e i collegamenti a questa documentazione.

![Messaggio di posta elettronica distribuzione](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portale

Dopo aver completato correttamente la distribuzione e aver eseguito il provisioning di tutte le risorse, è possibile visualizzare i parametri di output nel gruppo di risorse.

1. Passare al gruppo di risorse nel portale.
1. Selezionare **Panoramica > distribuzioni**.

    ![Panoramica del gruppo di risorse](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selezionare la distribuzione **di microsoft-azure-blockchain.azure-blockchain-ether-....**
1. Selezionare la sezione **Uscite.**

    ![Output di distribuzione](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Ampliamento del consorzio

Per espandere il consorzio, è prima di tutto necessario connettere la rete fisica. Se si esegue la distribuzione dietro una VPN, vedere la sezione [Connessione del gateway della rete virtuale](#connecting-vnet-gateways) configurare la connessione di rete come parte della distribuzione del nuovo membro. Al termine della distribuzione, usare [Governance DApp](#governance-dapp) per diventare un amministratore di rete.

### <a name="new-member-deployment"></a>Distribuzione di un nuovo membro

Condividere le informazioni seguenti con il membro aggiunto. Le informazioni sono disponibili nella posta elettronica post-distribuzione o nell'output di distribuzione del portale.

* URL di dati di consorzio
* Il numero di nodi distribuiti
* ID risorsa del gateway di rete virtuale (se si usa la VPN)

Il membro che distribuisce deve utilizzare lo stesso modello di soluzione del consorzio Ethereum Proof-of-Authority quando distribuisce la propria presenza di rete utilizzando le indicazioni seguenti:

* Selezionare **Join Existing** (Aggiungi esistente)
* Scegliere lo stesso numero di nodi validator come gli altri membri della rete per garantire una rappresentazione equa
* Utilizzare lo stesso indirizzo Admin Ethereum
* Utilizzare l'URL dei dati del *consorzio* fornito nelle *impostazioni di Ethereum*
* Se il resto della rete è protetto da una VPN, selezionare **Rete virtuale privata** nella sezione avanzata

### <a name="connecting-vnet-gateways"></a>Connessione di gateway di rete virtuale

Questa sezione è necessaria solo se è stata distribuita usando una rete virtuale privata. È possibile ignorare questa sezione se si utilizzano indirizzi IP pubblici.

Per una rete privata, i diversi membri sono connessi tramite connessioni gateway VNet. Prima che un membro possa unirsi alla rete e visualizzare il traffico delle transazioni, un membro esistente deve eseguire una configurazione finale nel gateway VPN per accettare la connessione. I nodi Ethereum del membro di join non verranno eseguiti fino a quando non viene stabilita una connessione. Per ridurre le possibilità di un singolo punto di errore, creare connessioni di rete ridondanti nel consorzio.

Dopo aver distribuito il nuovo membro, il membro esistente deve completare la connessione bidirezionale configurando una connessione gateway di rete virtuale al nuovo membro. Il membro esistente ha bisogno di:

* Il gateway della rete virtuale ResourceID del membro di connessione. Vedere [output di distribuzione](#deployment-output).
* Chiave di connessione condivisa.

Il membro esistente deve eseguire lo script di PowerShell seguente per completare la connessione. È possibile usare Azure Cloud Shell nella barra di spostamento in alto a destra nel portale.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitoraggio del servizio

È possibile individuare il portale di Monitoraggio di Azure seguendo il collegamento nel messaggio di posta elettronica di distribuzione o individuando il parametro nell'output della distribuzione [OMS_PORTAL_URL].

Il portale visualizza prima di tutto le statistiche di rete generali e la panoramica dei nodi.

![Monitorare le categorie](./media/ethereum-poa-deployment/monitor-categories.png)

La selezione di **Nodi Panoramica** mostra le statistiche dell'infrastruttura per nodo.

![Statistiche dei nodi](./media/ethereum-poa-deployment/node-stats.png)

La selezione delle statistiche di **rete** mostra le statistiche di rete di Ethereum.

![Statistiche di rete](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

È possibile eseguire query sui log di monitoraggio per analizzare gli errori o gli avvisi di soglia di installazione. Le query seguenti sono esempi che è possibile eseguire nello strumento *Ricerca log:*

I blocchi di elenco segnalati da più query di convalida possono essere utili per trovare i fork a catena.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Ottenere il numero medio di peer per un nodo di validator specificato con una media superiore a bucket di 5 minuti.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Accesso SSH

Per impostazione predefinita, l'accesso alle porte SSH è negato in base a una regola del gruppo sicurezza di rete per motivi di sicurezza. Per accedere alle istanze della macchina virtuale nella rete PoA, è necessario modificare la seguente regola di sicurezza in *Consenti*.

1. Passare alla sezione **Panoramica** del gruppo di risorse distribuite nel portale di Azure.Go to the Overview section of the deployed resource group in the Azure portal.

    ![Panoramica SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selezionare il gruppo di sicurezza di **rete** per l'area della macchina virtuale a cui si vuole accedere.

    ![Gruppo di sicurezza di rete SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selezionare la regola **allow-ssh.**

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. Modifica **azione** per **consentire**

    ![Abilitare Consenti per SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selezionare **Salva**. L'applicazione delle modifiche potrebbe richiedere alcuni minuti.

È possibile connettersi in remoto alle macchine virtuali per i nodi validator tramite SSH con il nome utente e la chiave di amministratore forniti. Il comando SSH per accedere al primo nodo validator è elencato nell'output di distribuzione del modello. Ad esempio:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Per accedere anodi di transazione aggiuntivi, incrementare il numero di porta di uno.

Se è stata distribuita in più aree, modificare il comando con il nome DNS o l'indirizzo IP del servizio di bilanciamento del carico in tale area. Per trovare il nome DNS o l'indirizzo IP delle altre aree, trovare la risorsa con la convenzione ** \* \* \* \* \*di denominazione -lbpip-reg\# ** e visualizzarne il nome DNS e le proprietà dell'indirizzo IP.

## <a name="azure-traffic-manager-load-balancing"></a>Bilanciamento del carico di Gestione traffico di Azure

Gestione traffico di Azure consente di ridurre i tempi di inattività e di migliorare la velocità di risposta della rete PoA grazie al routing del traffico in ingresso tra più distribuzioni in aree diverse. I controlli di integrità predefiniti e il reindirizzamento automatico consentono di garantire la disponibilità elevata degli endpoint RPC e della Governance DApp. Questa funzionalità è utile se la distribuzione è stata eseguita in più aree e si è pronti per la produzione.

Utilizzare Gestione traffico per migliorare la disponibilità della rete PoA con failover automatico. È anche possibile usare Gestione traffico per aumentare la velocità di risposta delle reti instradando gli utenti finali alla posizione di Azure con latenza di rete più bassa.

Se si decide di creare un profilo di Gestione traffico, è possibile usare il nome DNS del profilo di accesso alla rete. Dopo che gli altri membri del consorzio sono stati aggiunti alla rete, Gestione traffico può essere usato anche per bilanciare il carico tra i validator distribuiti.

### <a name="creating-a-traffic-manager-profile"></a>Creazione di un profilo di Gestione traffico

1. Nel [portale](https://portal.azure.com)di Azure selezionare **Crea una risorsa** nell'angolo superiore sinistro.
1. Cercare **il profilo**di Gestione traffico .

    ![Cercare Gestione traffico di AzureSearch for Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Assegnare al profilo un nome univoco e selezionare il gruppo di risorse usato per la distribuzione PoA.

1. Selezionare **Crea** per la distribuzione.

    ![Creare Gestione trafficoCreate Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Una volta distribuita, selezionare l'istanza nel gruppo di risorse. Il nome DNS per accedere a Gestione traffico è disponibile nella scheda Panoramica.

    ![Individuare il nome DNS di Gestione traffico](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Scegliere la scheda **Endpoint** e selezionare il pulsante **Aggiungi.**
1. Fornire un nome univoco per l'endpoint.
1. Per **Tipo di risorsa di destinazione**scegliere Indirizzo IP **pubblico**.
1. Scegliere l'indirizzo IP pubblico del servizio di bilanciamento del carico della prima area.

    ![Routing di Gestione traffico](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Ripetere la procedura per ogni area nella rete distribuita. Quando gli endpoint sono nello stato **abilitato,** vengono automaticamente caricati e vengono bilanciati l'area in corrispondenza del nome DNS del gestore del traffico. È ora possibile utilizzare questo nome DNS al posto del parametro [CONSORTIUM_DATA_URL] in altri passaggi dell'articolo.

## <a name="data-api"></a>API Dati

Ogni membro del consorzio ospita le informazioni necessarie per consentire agli altri utenti di connettersi alla rete. Per abilitare la connettività, ogni membro ospita un set di informazioni di connessione nell'endpoint dell'API dei dati.

Il membro esistente fornisce il [CONSORTIUM_DATA_URL] prima della distribuzione del membro. Durante la distribuzione, un membro aggiunto recupererà le informazioni dall'interfaccia JSON nell'endpoint seguente:

`<CONSORTIUM_DATA_URL>/networkinfo`

La risposta contiene informazioni utili per l'unione di membri (blocco Genesis, validator Set contratto ABI, bootnodes) e informazioni utili per il membro esistente (indirizzi validator). È possibile utilizzare questa standardizzazione per estendere il consorzio tra i provider di cloud. Questa API restituisce una risposta in formato JSON con la struttura seguente:This API returns a JSON formatted response with the following structure:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>App decentralizzata per la governance

Il fulcro di Proof-of-Authority è la governance decentralizzata. Poiché la prova di autorità si basa su un elenco consentito di autorità di rete per mantenere la rete integra, è importante fornire un meccanismo equo per apportare modifiche a questo elenco di autorizzazioni. Ogni distribuzione viene fornita con un set di contratti intelligenti e un portale per la governance on-chain di questo elenco consentito. Dopo che una modifica proposta raggiunge un voto di maggioranza da parte dei membri del consorzio, la modifica viene applicata. Il voto consente di aggiungere o compromettere i partecipanti ai nuovi partecipanti in modo che vengano rimossi in modo trasparente che incoraggi una rete onesta.

La governance DApp è un insieme di contratti intelligenti pre-distribuiti e un'applicazione web che vengono [utilizzati](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) per governare le autorità sulla rete. Le autorità sono suddivise in identità di amministrazione e nodi validator.
Gli amministratori hanno il potere di delegare la partecipazione al consenso a un set di nodi validator. Gli amministratori possono anche votare altri amministratori all'interno o all'esterno della rete.

![App decentralizzata per la governance](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governance decentralizzata:** Le modifiche nelle autorità di rete vengono amministrate tramite il voto on-chain da parte di amministratori selezionati.
* **Delega validatore:** Le autorità possono gestire i nodi validator impostati in ogni distribuzione PoA.
* **Cronologia modifiche controllabili:** Ogni modifica viene registrata sulla blockchain fornendo trasparenza e verificabilità.

### <a name="getting-started-with-governance"></a>Introduzione alla governance

Per eseguire qualsiasi tipo di transazione attraverso la Governance DApp, è necessario utilizzare un portafoglio Ethereum. L'approccio più semplice consiste nell'utilizzare un portafoglio in-browser come [MetaMask](https://metamask.io); Tuttavia, poiché questi contratti intelligenti vengono distribuiti nella rete, è anche possibile automatizzare le interazioni al contratto Governance.

Dopo aver installato MetaMask, passare all'app decentralizzata per la governance nel browser.  È possibile individuare l'URL tramite il portale di Azure nell'output di distribuzione.  Se non si dispone di un portafoglio nel-browser installato non sarà in grado di eseguire alcuna azione; tuttavia, è possibile visualizzare lo stato dell'amministratore.  

### <a name="becoming-an-admin"></a>Come diventare un amministratore

Se sei il primo membro distribuito in rete, diventi automaticamente un amministratore e i nodi di parità vengono elencati come validatori. Se ti unisci alla rete, devi essere votato come amministratore a maggioranza (superiore al 50%) dell'amministratore esistente. Se scegli di non diventare un amministratore, i tuoi nodi sincronizzano e convalidano la blockchain; tuttavia, non partecipano al processo di creazione del blocco. Per iniziare il processo di votazione per diventare un amministratore, seleziona **Nomina** e inserisci il tuo indirizzo Ethereum e l'alias.

![Nominate (Nomina)](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidati

Selezionando la scheda Candidati viene visualizzato il set corrente di amministratori **candidati.**  Quando un candidato raggiunge un voto a maggioranza da parte degli amministratori correnti, il candidato viene promosso a un amministratore.  Per votare un candidato, selezionare la riga e scegliere **Vota in**. Se si cambia idea su un voto, selezionare il candidato e scegliere **Revoca voto**.

![Candidati](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Amministratori

La scheda **Amministratori** mostra il set corrente di amministratori e ti offre la possibilità di votare contro.  Una volta che un amministratore perde più del 50% di supporto, vengono rimossi come amministratore della rete. Tutti i nodi validator di proprietà dell'amministratore perdono lo stato di validator e diventano nodi di transazione nella rete. Un amministratore può essere rimosso per diversi motivi; tuttavia, spetta al consorzio concordare una politica in anticipo.

![Amministratori](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validator

Selezionando la scheda Validatori vengono **visualizzati** i nodi di parità distribuiti correnti per l'istanza e il relativo stato corrente (tipo di nodo). Ogni membro del consorzio ha un diverso set di validatori in questo elenco, poiché questa visualizzazione rappresenta l'attuale membro del consorzio distribuito. Se l'istanza è stata appena distribuita e non sono stati aggiunti i validator, si ottiene l'opzione **Aggiungi validatori**. L'aggiunta di validatori sceglie automaticamente un set di nodi di parità con bilanciamento regionale e li assegna al set di validator. Se sono stati distribuiti più nodi rispetto alla capacità consentita, i nodi rimanenti diventano nodi di transazione nella rete.

L'indirizzo di ogni validator viene assegnato automaticamente tramite l'[archivio identità](#identity-store) in Azure.  Se un nodo si arresta, rinuncia alla propria identità, consentendo a un altro nodo nella distribuzione di prendere il suo posto. Questo processo assicura che la vostra partecipazione al consenso sia altamente disponibile.

![Validator](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome del consorzio

Qualsiasi amministratore può aggiornare il nome del consorzio.  Selezionare l'icona a forma di ingranaggio in alto a sinistra per aggiornare il nome del consorzio.

### <a name="account-menu"></a>Menu Account

In alto a destra, c'è l'alias dell'account Ethereum e l'identicon.  Se sei un amministratore, hai la possibilità di aggiornare il tuo alias.

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Sviluppo Ethereum<a id="tutorials"></a>

Per compilare, distribuire e testare i contratti intelligenti, ecco alcune opzioni che è possibile prendere in considerazione per lo sviluppo di Ethereum:To compile, deploy, and test smart contracts, here are a few options you can consider for Ethereum development:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - Ambiente di sviluppo Ethereum basato su client
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Ambiente di sviluppo Ethereum locale e basato su browser

### <a name="compile-deploy-and-execute-smart-contract"></a>Compila, distribuisci ed esegui contratto intelligente

Nell'esempio seguente viene creato un contratto intelligente semplice. Si utilizza Tartufo per compilare e distribuire il contratto intelligente alla rete blockchain. Una volta distribuita, si chiama una funzione di contratto intelligente tramite una transazione.

#### <a name="prerequisites"></a>Prerequisiti

* Installare [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python è necessario per Tartufo e Web3. Selezionare l'opzione di installazione per includere Python nel percorso.
* Installare Tartufo v5.0.5 `npm install -g truffle@v5.0.5`. Truffle richiede l'installazione di diversi strumenti, tra cui [Node.js](https://nodejs.org) e [Git](https://git-scm.com/). Per ulteriori informazioni, vedere Documentazione sul [tartufo](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Creare il progetto Truffle

Prima di poter compilare e distribuire un contratto intelligente, è necessario creare un progetto Tartufo.

1. Aprire una shell o un prompt dei comandi.
1. Creare una cartella denominata `HelloWorld`.
1. Passare alla nuova `HelloWorld` cartella.
1. Inizializzare un nuovo progetto Tartufo utilizzando il comando `truffle init`.

    ![Creare un nuovo progetto Tartufo](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Aggiungere un contratto intelligente

Crea i tuoi contratti intelligenti nella sottodirectory dei **contratti** del tuo progetto Tartufo.

1. Creare un file `postBox.sol` nella directory denominata nella sottodirectory **contracts** del progetto Tartufo.
1. Aggiungere il seguente codice Solidity a **postBox.sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Distribuire il contratto intelligente con il tartufo

I progetti di tartufo contengono un file di configurazione per i dettagli di connessione di rete blockchain. Modificare il file di configurazione per includere le informazioni di connessione per la rete.

> [!WARNING]
> Non inviare mai la tua chiave privata Ethereum in rete. Assicurarsi che ogni transazione venga prima firmata in locale e che la transazione firmata venga inviata in rete.

1. Hai bisogno della frase mnemonica per [l'account amministratore Ethereum utilizzato durante la distribuzione della tua rete blockchain](#ethereum-settings). Se è stato utilizzato MetaMask per creare l'account, è possibile recuperare il tasto di scelta da MetaMask.If you used MetaMask to create the account, you can retrieve the mnemonic from MetaMask. Selezionare l'icona dell'account amministratore in alto a destra nell'estensione MetaMask e selezionare **Impostazioni > Sicurezza & Privacy > Reveal Seed Words**.
1. Sostituire il `truffle-config.js` contenuto del progetto Tartufo con il seguente contenuto. Sostituire il punto finale segnaposto e i valori mnemonici.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Dal momento che stiamo utilizzando il provider Truffle HD Wallet, installare il modulo nel vostro progetto utilizzando il comando `npm install truffle-hdwallet-provider --save`.

Il tartufo utilizza script di migrazione per distribuire contratti intelligenti in una rete blockchain. È necessario uno script di migrazione per distribuire il nuovo contratto intelligente.

1. Aggiungere una nuova migrazione per distribuire il nuovo contratto. Creare `2_deploy_contracts.js` un file nella sottodirectory **migrazioni** del progetto Tartufo.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Eseguire la distribuzione nella rete PoA utilizzando il comando Dimitufo. Al prompt dei comandi nella directory del progetto Truffle, eseguire:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Chiamare una funzione di contratto intelligenteCall a smart contract function

Ora che il contratto intelligente è distribuito, è possibile inviare una transazione per chiamare una funzione.

1. Nella directory del progetto Tartufo, `sendtransaction.js`creare un nuovo file denominato .
1. Aggiungere il contenuto seguente a **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Eseguire lo script utilizzando il comando Truffle execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Eseguire lo script per chiamare la funzione tramite transazioneExecute script to call function via transaction](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Supporto WebAssembly (WASM)

Il supporto WebAssembly è già abilitato nelle nuove reti PoA distribuite. Consente lo sviluppo di smart contract in qualsiasi linguaggio esegua il transpile in Web-Assembly (Rust, C, C++). Per ulteriori informazioni, vedere: [Parity Overview of WebAssembly](https://wiki.parity.io/WebAssembly-Home) and [Tutorial from Parity Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Domande frequenti

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Ho notato che ci sono molte transazioni sulla rete che non ho inviato. Da dove provengono?

Non è sicuro sbloccare l'[API personale](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). I bot sono in ascolto degli account Ethereum sbloccati e provano a svuotare i fondi. Il bot presuppone che questi account contengano vero ether e che provino a trasferire il saldo per primi. Non abilitare l'API personale nella rete. Prefirmare invece le transazioni manualmente utilizzando un portafoglio come MetaMask o a livello di codice.

### <a name="how-to-ssh-onto-a-vm"></a>Come si stabilisce una connessione SSH a una VM?

La porta SSH non è esposta per motivi di sicurezza. Seguire [questa guida per abilitare la porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Come si configura un membro di controllo o i nodi delle transazioni?

I nodi di transazione sono un set di client di parità sottoposti a peering con la rete ma che non partecipano al consenso. Questi nodi possono tuttavia essere usati per inviare transazioni Ethereum e leggere lo stato dello smart contract. Questo meccanismo funziona per fornire la verificabilità ai membri del consorzio non-autorità sulla rete. A tale scopo, seguire i passaggi in [Growing the Consortium](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Perché le transazioni MetaMask richiedono molto tempo?

Per garantire che le transazioni vengano ricevute nell'ordine corretto, ogni transazione Ethereum è dotata di un nonce incrementale. Se hai usato un account in MetaMask su una rete diversa, devi reimpostare il valore nonce. Fare clic sull'icona delle impostazioni (tre barre), Impostazioni, Reimposta account. La cronologia delle transazioni verrà cancellata e sarà possibile inviare di nuovo la transazione.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>È necessario specificare la tariffa gas in MetaMask?

L'etere non serve ad alcuna finalità in Proof-of-Authority Consortium. Pertanto, non è necessario specificare la tassa di gas quando si inviano le transazioni in MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Cosa devo fare se la distribuzione ha esito negativo a causa del provisioning non riuscito di Azure OMS?

Monitoraggio è una funzionalità facoltativa. In some rare cases where your deployment fails because of inability to successfully provision Azure Monitor resource, you can redeploy without Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Le distribuzioni di IP pubblici sono compatibili con le distribuzioni di reti private?

No. Il peering richiede una comunicazione bidirezionale, pertanto l'intera rete deve essere pubblica o privata.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual è la velocità effettiva delle transazioni prevista di Proof-of-Authority?

La velocità effettiva delle transazioni è strettamente dipendente dai tipi di transazioni e dalla topologia di rete. Con l'utilizzo di transazioni semplici è stato effettuato un benchmark di una media di 400 transazioni al secondo con una rete distribuita in più aree.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Come posso eseguire la sottoscrizione a eventi smart contract?

Ethereum Proof-of-Authority supporta ora i socket Web.  Controllare l'output della distribuzione per individuare l'URL e la porta del socket Web.

## <a name="next-steps"></a>Passaggi successivi

Per altre soluzioni Azure Blockchain, vedere la documentazione di Azure Blockchain.For more Azure Blockchain [solutions,](https://docs.microsoft.com/azure/blockchain/)see the Azure Blockchain documentation .
