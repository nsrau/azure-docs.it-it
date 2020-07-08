---
title: Distribuire il modello di soluzione del Consorzio di prova Ethereum in Azure
description: Usare la soluzione Ethereum di verifica dell'autorità di certificazione per distribuire e configurare una rete Ethereum per il Consorzio multimembro in Azure
ms.date: 06/04/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 127aa860fe0c80f4d12a373c00ad2f53447c3497
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85210117"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Distribuire il modello di soluzione del Consorzio di prova Ethereum in Azure

È possibile usare [il modello di soluzione Azure Ethereum Proof-of-Authority Consortium Preview](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) per distribuire, configurare e gestire una rete Ethereum di prova del Consorzio di più membri con una conoscenza minima di Azure e Ethereum.

Il modello di soluzione può essere usato da ogni membro del Consorzio per eseguire il provisioning di un footprint di rete blockchain usando Microsoft Azure servizi di calcolo, rete e archiviazione. Il footprint di rete di ogni membro del Consorzio è costituito da un set di nodi validator con carico bilanciato con cui un'applicazione o un utente può interagire per inviare transazioni Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Scegliere una soluzione Azure blockchain

Prima di scegliere di usare il modello di soluzione del Consorzio di prova Ethereum, confrontare lo scenario con i casi d'uso comuni delle opzioni di Azure blockchain disponibili.

Opzione | Modello di servizio | Caso d'uso comune
-------|---------------|-----------------
Modelli di soluzioni | IaaS | I modelli di soluzione sono Azure Resource Manager modelli che è possibile usare per eseguire il provisioning di una topologia di rete blockchain completamente configurato I modelli distribuiscono e configurano Microsoft Azure servizi di calcolo, rete e archiviazione per un determinato tipo di rete blockchain.
[Servizio Azure Blockchain](../service/overview.md) | PaaS | Azure blockchain Service Preview semplifica la formazione, la gestione e la governance delle reti blockchain del Consorzio. Usa il servizio Azure blockchain per le soluzioni che richiedono PaaS, la gestione del consorzio o la privacy dei contratti e delle transazioni.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS e PaaS | L'anteprima di Azure Blockchain Workbench è una raccolta di servizi e funzionalità di Azure che consentono di creare e distribuire applicazioni blockchain per condividere dati e processi di business con altre organizzazioni. Usare Azure blockchain Workbench per la realizzazione di prototipi di una soluzione blockchain o di un modello di prova dell'applicazione blockchain.

## <a name="solution-architecture"></a>Architettura della soluzione

Usando il modello di soluzione Ethereum, è possibile distribuire una singola o più aree basate su più membri Ethereum di rete per enti di prova.

![Architettura di distribuzione](./media/ethereum-poa-deployment/deployment-architecture.png)

Ogni distribuzione di un membro di un consorzio include:

* Macchine virtuali per l'esecuzione dei validator PoA
* Azure Load Balancer per la distribuzione di richieste DApp RPC, peering e governance
* Azure Key Vault per proteggere le identità dei validator
* Archiviazione di Azure per ospitare le informazioni di rete persistenti e coordinare il leasing
* Monitoraggio di Azure per l'aggregazione dei log e delle statistiche sulle prestazioni
* Gateway di rete virtuale (facoltativo) per consentire le connessioni VPN tra reti virtuali private

Per impostazione predefinita, gli endpoint RPC e peering sono accessibili tramite IP pubblico per abilitare la connettività semplificata tra le sottoscrizioni e i cloud. Per i controlli di accesso a livello di applicazione, è possibile usare i [contratti di autorizzazione della parità](https://wiki.parity.io/Permissioning). Le reti distribuite dietro le VPN, che sfruttano i gateway VNet per la connettività tra sottoscrizioni sono supportate. Poiché le distribuzioni VPN e VNet sono più complesse, è consigliabile iniziare con un modello IP pubblico durante la prototipazione di una soluzione.

I contenitori Docker vengono usati per l'affidabilità e la modularità. Azure Container Registry viene usato per ospitare e gestire le immagini con versione come parte di ogni distribuzione. Le immagini del contenitore sono costituite da:

* Agente di orchestrazione: genera identità e contratti di governance. Archivia le identità in un archivio identità.
* Parità client-lease Identity dall'archivio identità. Individua e si connette ai peer.
* Agente EthStats: raccoglie i log e le statistiche locali tramite RPC e inserisce le informazioni in monitoraggio di Azure.
* Governance DApp-interfaccia Web per l'interazione con i contratti di governance.

### <a name="validator-nodes"></a>Nodi validator

Nel protocollo Proof-of-Authority, i nodi di convalida prendono il posto dei tradizionali nodi di mining. Ogni validator ha un'identità Ethereum univoca che consente di partecipare al processo di creazione dei blocchi. Ogni membro del consorzio può effettuare il provisioning di due o più nodi di convalida in cinque aree, per la ridondanza geografica. I nodi di convalida comunicano con altri nodi di convalida per convergere sullo stato del libro mastro distribuito sottostante. Per garantire una partecipazione equa alla rete, a ogni membro del Consorzio non è consentito l'utilizzo di più validator rispetto al primo membro della rete. Se, ad esempio, il primo membro distribuisce tre validator, ogni membro può avere un massimo di tre validator.

### <a name="identity-store"></a>Archivio di identità

Un archivio di identità viene distribuito nella sottoscrizione di ogni membro che mantiene in modo sicuro le identità Ethereum generate. Per ogni validator, il contenitore dell'orchestrazione genera una chiave privata Ethereum e la archivia in Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Distribuire la rete Ethereum Consortium

In questa procedura dettagliata si presuppone che si stia creando una rete Ethereum Consortium multiparte. Il flusso seguente è un esempio di distribuzione a più parti:

1. Tre membri che generano ognuno un account Ethereum usando MetaMask
1. *Il membro A* distribuisce il POA Ethereum, fornendo il proprio indirizzo pubblico Ethereum
1. Il *membro A* fornisce l'URL del consorzio al *membro B* e al *membro C*
1. Il *membro B* e il *membro C* distribuiscono Ethereum PoA, fornendo l'indirizzo pubblico di Ethereum e l'URL del consorzio del *membro A*
1. Il *membro A* vota il *membro B* come amministratore
1. Il *membro A* e il *membro B* votano entrambi il *membro C* come amministratore

Le sezioni successive illustrano come configurare il footprint del primo membro nella rete.

### <a name="create-resource"></a>Crea risorsa

Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa** nell'angolo superiore sinistro.

Selezionare **Blockchain**il  >  **Consorzio di prova blockchain Ethereum (anteprima)**.

### <a name="basics"></a>Nozioni di base

In **nozioni di base**specificare i valori per i parametri standard per qualsiasi distribuzione.

![Nozioni di base](./media/ethereum-poa-deployment/basic-blade.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Crea una nuova rete o Unisciti alla rete esistente | È possibile creare una nuova rete Consortium o partecipare a una rete Consortium esistente. Per l'aggiunta a una rete esistente sono necessari parametri aggiuntivi. | Create new
Indirizzo di posta elettronica | Si riceverà una notifica di posta elettronica al termine della distribuzione con le informazioni sulla distribuzione. | Un indirizzo di posta elettronica valido
Nome utente macchina virtuale | Nome utente amministratore di ogni macchina virtuale distribuita | 1-64 caratteri alfanumerici
Tipo di autenticazione | Metodo per l'autenticazione per la macchina virtuale. | Password
Password | Password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. Tutte le macchine virtuali hanno inizialmente la stessa password. È possibile modificare la password dopo il provisioning. | 12-72 caratteri 
Subscription | La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio |
Gruppo di risorse| Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio. | myResourceGroup
Location | La regione di Azure per gruppo di risorse. | Stati Uniti occidentali 2

Selezionare **OK**.

### <a name="deployment-regions"></a>Regioni di distribuzione

In *aree di distribuzione*specificare il numero di aree e località per ciascuna. È possibile distribuire in un massimo di cinque aree. La prima area deve corrispondere alla posizione del gruppo di risorse dalla sezione *nozioni di base* . Per le reti di sviluppo o di test, è possibile usare una singola area per ogni membro. Per la produzione, distribuire in due o più aree per la disponibilità elevata.

![aree di distribuzione](./media/ethereum-poa-deployment/deployment-regions.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Numero di regioni|Numero di aree per la distribuzione della rete di consorzio| 2
Prima regione | Prima regione per la distribuzione della rete di consorzio | Stati Uniti occidentali 2
Seconda regione | Seconda area per la distribuzione della rete Consortium. Le aree aggiuntive sono visibili quando il numero di aree è maggiore o uguale a due. | Stati Uniti orientali 2

Selezionare **OK**.

### <a name="network-size-and-performance"></a>Dimensioni e prestazioni della rete

In *dimensioni e prestazioni della rete*specificare gli input per la dimensione della rete Consortium. Le dimensioni di archiviazione del nodo validator determinano la dimensione potenziale del blockchain. Le dimensioni possono essere modificate dopo la distribuzione.

![Dimensioni e prestazioni della rete](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Number of load balanced validator nodes (Numero di nodi di convalida con carico bilanciato) | Numero di nodi validator di cui eseguire il provisioning come parte della rete. | 2
Validator node storage performance (Prestazioni di archiviazione dei nodi di convalida) | Tipo di disco gestito per ogni nodo validator distribuito. Per informazioni dettagliate sui prezzi, vedere [prezzi di archiviazione](https://azure.microsoft.com/pricing/details/managed-disks/) | SSD Standard
Validator node virtual machine size (Dimensioni della macchina virtuale dei nodi convalida) | Dimensioni della macchina virtuale usata per i nodi di convalida. Per informazioni dettagliate sui prezzi, vedere [prezzi di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | D2 Standard v3

La macchina virtuale e il livello di archiviazione influiscono sulle prestazioni della rete.  Usare la tabella seguente per facilitare la scelta dell'efficienza dei costi:

SKU della macchina virtuale|Livello di archiviazione|Prezzo|Velocità effettiva|Latenza
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
Consortium Member ID (ID membro del consorzio) | ID associato a ogni membro che partecipa alla rete Consortium. Viene usato per configurare gli spazi degli indirizzi IP per evitare conflitti. Per una rete privata, l'ID membro deve essere univoco tra organizzazioni diverse nella stessa rete.  È necessario un ID membro univoco anche quando la stessa organizzazione esegue la distribuzione in più regioni. Prendere nota del valore di questo parametro poiché è necessario condividerlo con altri membri di join per assicurarsi che non esistano conflitti. L'intervallo valido è compreso tra 0 e 255. | 0
ID rete | L'ID di rete per la rete di consorzio Ethereum in fase di realizzazione. Ogni rete Ethereum ha il proprio ID di rete, di cui 1 è l'ID della rete pubblica. L'intervallo valido è compreso tra 5 e 999.999.999 | 10101010
Admin Ethereum Address (Indirizzo Ethereum amministratore) | Indirizzo dell'account Ethereum usato per partecipare alla governance del PoA. È possibile usare metamask per generare un indirizzo Ethereum. |
Opzioni avanzate | Opzioni avanzate per le impostazioni di Ethereum | Abilitare
Eseguire la distribuzione tramite IP pubblico | Se si seleziona VNet privato, la rete viene distribuita dietro un gateway VNet e rimuove l'accesso al peering. Per i VNet privati, tutti i membri devono usare un gateway VNet per la compatibilità della connessione. | IP pubblico
Limite di gas di blocco | Limite di gas di blocco iniziale della rete. | 50 milioni
Block Reseal Period (sec) (Periodo nuovo sigillo blocco (sec)) | Frequenza con cui verranno creati blocchi vuoti in assenza di transazioni in rete. Una frequenza maggiore determina una finalità più rapida, ma costi di archiviazione maggiori. | 15
Contratto di autorizzazione transazione | Bytecode per il contratto di concessione delle autorizzazioni per le transazioni. Limita la distribuzione e l'esecuzione di Smart contract a un elenco di account Ethereum consentito. |

Selezionare **OK**.

### <a name="monitoring"></a>Monitoraggio

Il monitoraggio consente di configurare una risorsa di log per la rete. L'agente di monitoraggio raccoglie e rileva le metriche e i log utili dalla rete, consentendo di controllare rapidamente l'integrità della rete o i problemi di debug.

![Monitoraggio di Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parametro | Descrizione | Valore di esempio
----------|-------------|--------------
Monitoraggio | Opzione per abilitare il monitoraggio | Abilitare
Connettersi ai log di monitoraggio di Azure esistenti | Opzione per creare una nuova istanza di log di monitoraggio di Azure o aggiungere un'istanza esistente | Create new
Location | Area in cui è distribuita la nuova istanza | Stati Uniti orientali
ID dell'area di lavoro di log Analytics esistente (Connetti a log di monitoraggio di Azure esistenti = join esistente)|ID area di lavoro dell'istanza dei log di monitoraggio di Azure esistente||ND
Chiave primaria di log Analytics esistente (Connetti a log di monitoraggio di Azure esistenti = join esistente)|Chiave primaria usata per connettersi all'istanza di log di monitoraggio di Azure esistente||ND

Selezionare **OK**.

### <a name="summary"></a>Summary

Fare clic sul riepilogo per esaminare gli input specificati ed eseguire la convalida pre-distribuzione di base. Prima di distribuire, è possibile scaricare il modello e i parametri.

Selezionare **Crea** per distribuire.

Se la distribuzione include gateway VNet, la distribuzione può richiedere da 45 a 50 minuti.

## <a name="deployment-output"></a>Output di distribuzione

Una volta completata la distribuzione, è possibile accedere ai parametri necessari usando il portale di Azure.

### <a name="confirmation-email"></a>Messaggio di posta elettronica di conferma

Se si specifica un indirizzo di posta elettronica ([sezione Nozioni di base](#basics)), viene inviato un messaggio di posta elettronica che include le informazioni sulla distribuzione e i collegamenti a questa documentazione.

![Messaggio di posta elettronica distribuzione](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portale

Dopo che la distribuzione è stata completata e che è stato eseguito il provisioning di tutte le risorse, è possibile visualizzare i parametri di output nel gruppo di risorse.

1. Passare al gruppo di risorse nel portale.
1. Selezionare **panoramica > distribuzioni**.

    ![Panoramica del gruppo di risorse](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selezionare la distribuzione di **Microsoft-Azure-blockchain. Azure-blockchain-ether-...** .
1. Selezionare la sezione **output** .

    ![Output distribuzione](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Ampliamento del consorzio

Per espandere il consorzio, è prima di tutto necessario connettere la rete fisica. Se si esegue la distribuzione dietro una VPN, vedere la sezione [connessione del gateway VNet](#connecting-vnet-gateways) configurare la connessione di rete come parte della distribuzione del nuovo membro. Al termine della distribuzione, usare il [DApp di governance](#governance-dapp) per diventare un amministratore di rete.

### <a name="new-member-deployment"></a>Distribuzione di un nuovo membro

Condividere le informazioni seguenti con il membro aggiunto. Le informazioni sono disponibili nel messaggio di posta elettronica di post-distribuzione o nell'output della distribuzione del portale.

* URL di dati di consorzio
* Il numero di nodi distribuiti
* ID risorsa del gateway di rete virtuale (se si usa la VPN)

Quando si distribuisce la propria presenza di rete, il membro di distribuzione deve usare lo stesso modello di soluzione Ethereum di verifica dell'autorità di certificazione:

* Selezionare **Join Existing** (Aggiungi esistente)
* Scegliere lo stesso numero di nodi validator del resto dei membri della rete per garantire una rappresentazione equa
* USA lo stesso indirizzo Ethereum amministratore
* Usare l' *URL dei dati Consortium* fornito nelle *impostazioni di Ethereum*
* Se il resto della rete è dietro una VPN, selezionare **VNet privato** nella sezione Avanzate.

### <a name="connecting-vnet-gateways"></a>Connessione di gateway di rete virtuale

Questa sezione è necessaria solo se è stata eseguita la distribuzione usando un VNet privato. È possibile ignorare questa sezione se si usano indirizzi IP pubblici.

Per una rete privata, i diversi membri sono connessi tramite connessioni gateway VNet. Prima che un membro possa accedere alla rete e vedere il traffico delle transazioni, un membro esistente deve eseguire una configurazione finale sul gateway VPN per accettare la connessione. I nodi Ethereum del membro di join non vengono eseguiti fino a quando non viene stabilita una connessione. Per ridurre le probabilità di un singolo punto di errore, creare connessioni di rete ridondanti nel Consorzio.

Dopo aver distribuito il nuovo membro, il membro esistente deve completare la connessione bidirezionale configurando una connessione gateway di rete virtuale al nuovo membro. Per i membri esistenti è necessario:

* Il gateway VNet ResourceID del membro che si connette. Vedere [output della distribuzione](#deployment-output).
* Chiave di connessione condivisa.

Il membro esistente deve eseguire lo script di PowerShell seguente per completare la connessione. È possibile usare Azure Cloud Shell posizionati nella barra di spostamento in alto a destra nel portale.

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

È possibile individuare il portale di monitoraggio di Azure seguendo il collegamento nel messaggio di posta elettronica di distribuzione o individuando il parametro nell'output della distribuzione [OMS_PORTAL_URL].

Il portale visualizza prima di tutto le statistiche di rete generali e la panoramica dei nodi.

![Categorie di monitoraggio](./media/ethereum-poa-deployment/monitor-categories.png)

La selezione di **Panoramica del nodo** Mostra le statistiche dell'infrastruttura per nodo.

![Statistiche del nodo](./media/ethereum-poa-deployment/node-stats.png)

Selezionando **statistiche di rete** vengono visualizzate le statistiche di rete Ethereum.

![Statistiche di rete](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Query Kusto di esempio

È possibile eseguire una query sui log di monitoraggio per esaminare gli errori o gli avvisi della soglia di installazione. Le query seguenti sono esempi che è possibile eseguire nello strumento di *Ricerca log* :

I blocchi di elenco segnalati da più di una query di convalida possono essere utili per trovare i fork a catena.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Ottiene il conteggio medio dei peer per un nodo validator specificato, mediato in bucket di 5 minuti.

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

Per impostazione predefinita, l'accesso alle porte SSH è negato in base a una regola del gruppo sicurezza di rete per motivi di sicurezza. Per accedere alle istanze di macchine virtuali nella rete PoA, è necessario modificare la regola di sicurezza seguente per *consentire*.

1. Passare alla sezione **Panoramica** del gruppo di risorse distribuito nella portale di Azure.

    ![Panoramica SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selezionare il **gruppo di sicurezza di rete** per l'area della macchina virtuale a cui si vuole accedere.

    ![Gruppo di sicurezza di rete SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selezionare la regola **Consenti-SSH** .

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. Modifica **azione** da **consentire**

    ![Abilitare Consenti per SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selezionare **Salva**. L'applicazione delle modifiche può richiedere alcuni minuti.

È possibile connettersi in remoto alle macchine virtuali per i nodi validator tramite SSH con il nome utente amministratore e la password/chiave SSH specificati. Il comando SSH per accedere al primo nodo validator è elencato nell'output della distribuzione del modello. Ad esempio:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Per ottenere nodi di transazione aggiuntivi, incrementare di uno il numero di porta.

Se è stato distribuito in più di un'area, impostare il comando sul nome DNS o sull'indirizzo IP del servizio di bilanciamento del carico in tale area. Per trovare il nome DNS o l'indirizzo IP delle altre aree, trovare la risorsa con la convenzione di denominazione ** \* \* \* \* \* -lbpip- \# reg** e visualizzare il nome DNS e le proprietà dell'indirizzo IP.

## <a name="azure-traffic-manager-load-balancing"></a>Bilanciamento del carico di Gestione traffico di Azure

Gestione traffico di Azure consente di ridurre i tempi di inattività e di migliorare la velocità di risposta della rete PoA grazie al routing del traffico in ingresso tra più distribuzioni in aree diverse. I controlli di integrità predefiniti e il reindirizzamento automatico consentono di garantire la disponibilità elevata degli endpoint RPC e del DApp di governance. Questa funzionalità è utile se la distribuzione è stata eseguita in più aree e si è pronti per la produzione.

Usare gestione traffico per migliorare la disponibilità di rete PoA con failover automatico. È anche possibile usare gestione traffico per aumentare la velocità di risposta delle reti indirizzando gli utenti finali alla località di Azure con latenza di rete più bassa.

Se si decide di creare un profilo di Gestione traffico, è possibile usare il nome DNS del profilo di accesso alla rete. Dopo che gli altri membri del consorzio sono stati aggiunti alla rete, Gestione traffico può essere usato anche per bilanciare il carico tra i validator distribuiti.

### <a name="creating-a-traffic-manager-profile"></a>Creazione di un profilo di Gestione traffico

1. Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa** nell'angolo superiore sinistro.
1. Cercare **il profilo di gestione traffico**.

    ![Ricerca di gestione traffico di Azure](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Assegnare al profilo un nome univoco e selezionare il gruppo di risorse usato per la distribuzione del PoA.

1. Selezionare **Crea** per distribuire.

    ![Creazione di gestione traffico](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Una volta distribuita, selezionare l'istanza nel gruppo di risorse. Il nome DNS per accedere a gestione traffico si trova nella scheda Panoramica.

    ![Individuare il nome DNS di Gestione traffico](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Scegliere la scheda **endpoint** e selezionare il pulsante **Aggiungi** .
1. Fornire un nome univoco per l'endpoint.
1. Per **tipo di risorsa di destinazione**scegliere **indirizzo IP pubblico**.
1. Scegliere l'indirizzo IP pubblico del servizio di bilanciamento del carico della prima area.

    ![Routing di Gestione traffico](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Ripetere la procedura per ogni area nella rete distribuita. Una volta che gli endpoint sono nello stato **abilitato** , vengono caricati automaticamente e l'area è bilanciata al nome DNS di gestione traffico. È ora possibile usare questo nome DNS al posto del parametro [CONSORTIUM_DATA_URL] in altri passaggi dell'articolo.

## <a name="data-api"></a>API Dati

Ogni membro del consorzio ospita le informazioni necessarie per consentire agli altri utenti di connettersi alla rete. Per consentire la facilità di connettività, ogni membro ospita un set di informazioni di connessione nell'endpoint dell'API dati.

Il membro esistente fornisce la [CONSORTIUM_DATA_URL] prima della distribuzione del membro. Durante la distribuzione, un membro aggiunto recupererà le informazioni dall'interfaccia JSON nell'endpoint seguente:

`<CONSORTIUM_DATA_URL>/networkinfo`

La risposta contiene informazioni utili per l'aggiunta di membri (blocco Genesis, ABI del contratto del set di validator, bootnodes) e informazioni utili per il membro esistente (indirizzi validator). È possibile usare questa standardizzazione per estendere il Consorzio tra i provider di servizi cloud. Questa API restituisce una risposta in formato JSON con la struttura seguente:

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

Il fulcro di Proof-of-Authority è la governance decentralizzata. Poiché la verifica dell'autorità si basa su un elenco consentito di autorità di rete per garantire l'integrità della rete, è importante fornire un meccanismo equo per apportare modifiche a questo elenco di autorizzazioni. Ogni distribuzione viene fornita con un set di Smart-Contracts e del portale per la governance on-Chain di questo elenco consentito. Dopo che una modifica proposta raggiunge un voto di maggioranza da parte dei membri del consorzio, la modifica viene applicata. Il voto consente di aggiungere o compromettere i partecipanti a nuovi consensuali per essere rimossi in modo trasparente che incoraggi una rete onesta.

Il DApp di governance è un set di [contratti intelligenti](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) pre-distribuiti e di un'applicazione Web che vengono usati per gestire le autorità nella rete. Le autorità sono suddivise in identità amministrative e nodi validator.
Gli amministratori hanno la possibilità di delegare la partecipazione al consenso a un set di nodi validator. Gli amministratori possono anche votare altri amministratori all'interno o all'esterno della rete.

![App decentralizzata per la governance](./media/ethereum-poa-deployment/governance-dapp.png)

* **Governance decentralizzata:** Le modifiche alle autorità di rete vengono gestite tramite il voto a catena da parte degli amministratori selezionati.
* **Delega validator:** Le autorità possono gestire i nodi validator impostati in ogni distribuzione di PoA.
* **Cronologia modifiche controllabile:** Ogni modifica viene registrata in blockchain, garantendo trasparenza e controllabilità.

### <a name="getting-started-with-governance"></a>Introduzione alla governance

Per eseguire qualsiasi tipo di transazione tramite il DApp di governance, è necessario usare un portafoglio di Ethereum. L'approccio più semplice consiste nell'usare un portafoglio nel browser, ad esempio [metamask](https://metamask.io); Tuttavia, poiché questi contratti intelligenti vengono distribuiti in rete, è anche possibile automatizzare le interazioni con il contratto di governance.

Dopo aver installato MetaMask, passare all'app decentralizzata per la governance nel browser.  È possibile individuare l'URL tramite portale di Azure nell'output della distribuzione.  Se non è installato un portafoglio nel browser, non sarà possibile eseguire alcuna azione. Tuttavia, è possibile visualizzare lo stato dell'amministratore.  

### <a name="becoming-an-admin"></a>Come diventare un amministratore

Se si è il primo membro distribuito in rete, si diventa automaticamente un amministratore e i nodi di parità vengono elencati come validator. Se si sta aderendo alla rete, è necessario votare come amministratore per la maggior parte (superiore al 50%) del set di amministratori esistente. Se si sceglie di non diventare un amministratore, i nodi ancora sincronizzano e convalidano il blockchain; Tuttavia, non partecipano al processo di creazione dei blocchi. Per avviare il processo di voto per diventare amministratore, selezionare **nomina** e immettere l'indirizzo e l'alias del Ethereum.

![Nominate (Nomina)](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Candidati

Selezionando la scheda **candidati** viene visualizzato il set corrente di amministratori candidati.  Una volta che un candidato raggiunge un voto di maggioranza dagli amministratori correnti, il candidato viene promosso a amministratore.  Per votare un candidato, selezionare la riga e selezionare **vota in**. Se si cambia idea a un voto, selezionare il candidato e selezionare Annulla **Voto**.

![Candidati](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Amministratori

La scheda **Admins** Mostra il set di amministratori corrente e offre la possibilità di votare.  Quando un amministratore perde più del 50% di supporto, viene rimosso come amministratore sulla rete. Tutti i nodi validator di cui l'amministratore è proprietario perdono lo stato del validator e diventano nodi di transazione nella rete. Un amministratore può essere rimosso per diversi motivi. Tuttavia, spetta al Consorzio accettare in anticipo un criterio.

![Amministratori](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Validator

Selezionando la scheda **validator** vengono visualizzati i nodi di parità distribuiti correnti per l'istanza di e il relativo stato corrente (tipo di nodo). Ogni membro del Consorzio dispone di un set di validator diverso in questo elenco, perché questa vista rappresenta il membro del Consorzio distribuito corrente. Se l'istanza viene appena distribuita e non sono stati aggiunti i validator, è possibile scegliere di **aggiungere validator**. L'aggiunta di validator sceglie automaticamente un set di nodi di parità con bilanciamento del carico di area e li assegna al set di validator. Se sono stati distribuiti più nodi rispetto alla capacità consentita, i nodi rimanenti diventano nodi di transazione nella rete.

L'indirizzo di ogni validator viene assegnato automaticamente tramite l'[archivio identità](#identity-store) in Azure.  Se un nodo diventa inattivo, cede la propria identità, consentendo a un altro nodo nella distribuzione di sostituirlo. Questo processo garantisce la disponibilità elevata della partecipazione al consenso.

![Validator](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nome del consorzio

Qualsiasi amministratore può aggiornare il nome del Consorzio.  Selezionare l'icona a forma di ingranaggio in alto a sinistra per aggiornare il nome del Consorzio.

### <a name="account-menu"></a>Menu Account

In alto a destra, è l'alias dell'account Ethereum e Identicon.  Se si è un amministratore, si ha la possibilità di aggiornare l'alias.

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Sviluppo Ethereum<a id="tutorials"></a>

Per compilare, distribuire e testare i contratti intelligenti, di seguito sono riportate alcune opzioni che è possibile prendere in considerazione per lo sviluppo di Ethereum:
* [Suite tartufo](https://www.trufflesuite.com/docs/truffle/overview) -ambiente di sviluppo Ethereum basato su client
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) -ambiente di sviluppo Ethereum basato su browser e locale

### <a name="compile-deploy-and-execute-smart-contract"></a>Compilazione, distribuzione ed esecuzione di Smart Contract

Nell'esempio seguente viene creato un semplice contratto Smart. Si usa il tartufo per compilare e distribuire il contratto intelligente nella rete blockchain. Una volta distribuita, si chiama una funzione di contratto intelligente tramite una transazione.

#### <a name="prerequisites"></a>Prerequisiti

* Installare [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python è necessario per tartufo e Web3. Selezionare l'opzione Installa per includere Python nel percorso.
* Installare tartufo v 5.0.5 `npm install -g truffle@v5.0.5` . Truffle richiede l'installazione di diversi strumenti, tra cui [Node.js](https://nodejs.org) e [Git](https://git-scm.com/). Per altre informazioni, vedere la [documentazione di tartufo](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Creare il progetto Truffle

Prima di poter compilare e distribuire un contratto intelligente, è necessario creare un progetto tartufo.

1. Aprire una shell o un prompt dei comandi.
1. Creare una cartella denominata `HelloWorld`.
1. Passare alla nuova `HelloWorld` cartella.
1. Inizializzare un nuovo progetto tartufo usando il comando `truffle init` .

    ![Creare un nuovo progetto tartufo](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Aggiungere un contratto intelligente

Creare contratti intelligenti nella sottodirectory **contratti** del progetto tartufo.

1. Creare un file nell'oggetto denominato `postBox.sol` nella sottodirectory **Contracts** del progetto tartuf.
1. Aggiungere il codice di solidità seguente a **postBox. Sol**.

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

### <a name="deploy-smart-contract-using-truffle"></a>Distribuire un contratto intelligente usando tartufo

I progetti tartufo contengono un file di configurazione per i dettagli della connessione di rete blockchain. Modificare il file di configurazione per includere le informazioni di connessione per la rete.

> [!WARNING]
> Non inviare mai la chiave privata Ethereum tramite la rete. Assicurarsi che ogni transazione venga prima firmata in locale e che la transazione firmata venga inviata in rete.

1. È necessaria la frase mnemonico per l' [account amministratore di Ethereum usato durante la distribuzione della rete blockchain](#ethereum-settings). Se è stato usato metamask per creare l'account, è possibile recuperare il tasto di scelta da metamask. Selezionare l'icona dell'account amministratore nella parte superiore destra dell'estensione della metamaschera e selezionare **impostazioni > sicurezza & Privacy > rivelare le parole di inizializzazione**.
1. Sostituire il contenuto di `truffle-config.js` nel progetto tartufo con il contenuto seguente. Sostituire l'endpoint segnaposto e i valori del tasto di scelta.

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

1. Poiché si usa il provider del portafoglio di tartufo HD, installare il modulo nel progetto usando il comando `npm install truffle-hdwallet-provider --save` .

Il tartufo usa gli script di migrazione per distribuire i contratti intelligenti in una rete blockchain. Per distribuire il nuovo contratto intelligente è necessario uno script di migrazione.

1. Aggiungere una nuova migrazione per distribuire il nuovo contratto. Creare `2_deploy_contracts.js` un file nella sottodirectory **Migrations** del progetto tartufo.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Eseguire la distribuzione nella rete PoA usando il comando di migrazione a tartufo. Al prompt dei comandi nella directory del progetto tartufo eseguire:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Chiamare una funzione di contratto intelligente

Ora che il contratto intelligente è stato distribuito, è possibile inviare una transazione per chiamare una funzione.

1. Nella directory del progetto tartufo creare un nuovo file denominato `sendtransaction.js` .
1. Aggiungere il seguente contenuto a **sendtransaction.js**.

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

1. Eseguire lo script usando il comando tartuf Execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Esegui script per chiamare la funzione tramite transazione](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Supporto WebAssembly (WASM)

Il supporto WebAssembly è già abilitato nelle nuove reti PoA distribuite. Consente lo sviluppo di smart contract in qualsiasi linguaggio esegua il transpile in Web-Assembly (Rust, C, C++). Per altre informazioni, vedere [Panoramica della parità di webassembly](https://wiki.parity.io/WebAssembly-Home) ed [esercitazione di parità Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Domande frequenti

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Si noterà che nella rete sono presenti molte transazioni che non sono state inviate. Da dove provengono?

Non è sicuro sbloccare l'[API personale](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). I bot sono in ascolto degli account Ethereum sbloccati e provano a svuotare i fondi. Il bot presuppone che questi account contengano vero ether e che provino a trasferire il saldo per primi. Non abilitare l'API personale nella rete. È invece possibile pre-firmare le transazioni manualmente usando un portafoglio come metamask o a livello di codice.

### <a name="how-to-ssh-onto-a-vm"></a>Come si stabilisce una connessione SSH a una VM?

La porta SSH non è esposta per motivi di sicurezza. Seguire [questa guida per abilitare la porta SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Come si configura un membro di controllo o i nodi delle transazioni?

I nodi di transazione sono un set di client di parità con peering con la rete ma che non partecipano al consenso. Questi nodi possono tuttavia essere usati per inviare transazioni Ethereum e leggere lo stato dello smart contract. Questo meccanismo può essere usato per garantire la controllabilità ai membri del Consorzio non di autorità nella rete. A tale scopo, seguire la procedura descritta in [Growing the Consortium](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Perché le transazioni MetaMask richiedono molto tempo?

Per garantire che le transazioni vengano ricevute nell'ordine corretto, ogni transazione Ethereum è dotata di un nonce incrementale. Se è stato usato un account in metamask in una rete diversa, è necessario reimpostare il valore nonce. Fare clic sull'icona delle impostazioni (tre barre), impostazioni, Reimposta account. La cronologia delle transazioni verrà cancellata e sarà possibile inviare di nuovo la transazione.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>È necessario specificare la tariffa gas in MetaMask?

L'etere non serve ad alcuna finalità in Proof-of-Authority Consortium. Di conseguenza, non è necessario specificare il costo del gas quando si inviano transazioni in metamask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Cosa devo fare se la distribuzione ha esito negativo a causa del provisioning non riuscito di Azure OMS?

Monitoraggio è una funzionalità facoltativa. In rari casi in cui la distribuzione non riesce a causa dell'impossibilità di eseguire correttamente il provisioning della risorsa di monitoraggio di Azure, è possibile ridistribuire senza monitoraggio di Azure.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Le distribuzioni di IP pubblici sono compatibili con le distribuzioni di reti private?

No. Il peering richiede una comunicazione bidirezionale, quindi l'intera rete deve essere pubblica o privata.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Qual è la velocità effettiva delle transazioni prevista di Proof-of-Authority?

La velocità effettiva delle transazioni è strettamente dipendente dai tipi di transazioni e dalla topologia di rete. Con l'utilizzo di transazioni semplici è stato effettuato un benchmark di una media di 400 transazioni al secondo con una rete distribuita in più aree.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Come posso eseguire la sottoscrizione a eventi smart contract?

Ethereum Proof-of-Authority supporta ora i socket Web.  Controllare l'output della distribuzione per individuare l'URL e la porta del socket Web.

## <a name="support-and-feedback"></a>Supporto, commenti e suggerimenti

Per notizie su Azure Blockchain, visitare il [blog di Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) per rimanere sempre aggiornati sull'offerta di servizi blockchain e per informazioni dal team tecnico di Azure Blockchain.

Per inviare un feedback sul prodotto o richiedere nuove funzionalità, pubblicare o votare un'idea tramite il [forum di feedback su Azure per Blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Supporto della community

È possibile interagire con i tecnici Microsoft e con gli esperti della community di Azure Blockchain.

* [Microsoft Q&una pagina di domande per il servizio Azure blockchain](https://docs.microsoft.com/answers/topics/azure-blockchain-workbench.html). Il supporto tecnico per i modelli blockchain è limitato ai problemi di distribuzione.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Passaggi successivi

Per altre soluzioni Azure blockchain, vedere la [documentazione di Azure blockchain](https://docs.microsoft.com/azure/blockchain/).
