---
title: Modello di soluzione Ethereum Proof-of-Work Consortium
description: Il modello di soluzione Etherereum Proof-of-Work Consortium consente di distribuire e configurare una rete Ethereum di consorzio multi-membro
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: fa58ecf4607efc1d212e40b98d199756d4b987f8
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231798"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Modello di soluzione Ethereum Proof-of-Work Consortium

Il modello di soluzione Ethereum Proof-of-Work Consortium è stato progettato per rendere più facile e veloce la distribuzione e la configurazione di una rete Ethereum di consorzio multi-membro con conoscenze minime su Azure ed Ethereum.

Con un numero limitato di input utente e una distribuzione tramite un singolo clic attraverso il portale Azure, ogni membro può eseguire il provisioning del proprio footprint di rete, utilizzando i servizi di calcolo, di networking e di storage di Microsoft Azure in tutto il mondo. Il footprint di rete di ogni membro è costituito da un insieme di nodi di transazione con carico bilanciato con cui un'applicazione o un utente può interagire per inviare transazioni, un insieme di nodi di mining per registrare le transazioni e un gateway VPN. Un successivo passaggio di connessione consente di collegare i gateway per creare una rete blockchain multi-membro completamente configurata.

## <a name="about-blockchain"></a>Informazioni sulle blockchain

Per gli utenti che hanno familiarità con la community blockchain, il rilascio di questa soluzione è una grande opportunità per conoscere la tecnologia in modo facile e configurabile su Azure. Tuttavia, per iniziare, è consigliabile distribuire la più semplice topologia di rete Ethereum autonoma con questa procedura guidata, prima di costruire reti di consorzio composte da più membri.

### <a name="mining-node-details"></a>Dettagli nodo di mining

I nodi che eseguono il mining delle transazioni sono separati dai nodi che accettano transazioni per garantire che le due azioni non siano in concorrenza per le stesse risorse.

Un membro del consorzio può eseguire il provisioning di un massimo di cinque regioni contenenti uno o più nodi di mining, con il supporto di un disco gestito. Uno o più nodi nella regione sono configurati come nodi di avvio per supportare l'individuazione dinamica dei nodi nella rete. I nodi di mining comunicano con altri nodi di mining per convergere sullo stato del libro mastro distribuito sottostante. Non è necessario che l'applicazione sia a conoscenza o comunichi con questi nodi. Concentrandosi sulle reti private, i nodi di mining sono isolati dal traffico Internet pubblico in entrata per aggiungere un secondo livello di protezione. Il traffico in uscita è consentito, ma non per la porta di individuazione Ethereum.

Tutti i nodi hanno una versione stabile del client Go Ethereum (Geth) e sono configurati per essere nodi di mining. Se non è stato fornito un blocco originario personalizzato, tutti i nodi utilizzano lo stesso indirizzo Ethereum e la stessa coppia di chiavi protetta da password dell'account Ethereum. La passphrase Ethereum fornita viene utilizzata per generare l'account predefinito (coinbase) per ogni nodo di mining. Come nodi di minig, raccolgono i corrispettivi che vengono aggiunti a questo account.

Il numero di nodi di mining per membro del consorzio dipende dalla dimensione complessiva della rete desiderata e dalla quantità di potenza hash dedicata a ciascun membro. Più grande è la rete, più nodi devono essere compromessi per ottenere un vantaggio sleale. Il modello supporta fino a 15 nodi di mining per regione di cui è stato eseguito il provisioning utilizzando il set di scalabilità di macchine virtuali.

### <a name="transaction-node-details"></a>Dettagli nodo di transazione

Un membro del consorzio dispone anche una serie di nodi di transazione con bilanciamento del carico. Questi nodi sono raggiungibili dall'esterno della rete virtuale, in modo che le applicazioni possono utilizzare questi nodi per inviare transazioni o eseguire contratti intelligenti all'interno della rete blockchain. Tutti i nodi hanno una versione stabile del client Go Ethereum (Geth) e sono configurati per mantenere una copia completa del libro mastro distribuito. Se non viene fornito un blocco originale personalizzato, questi nodi utilizzano lo stesso account Ethereum, protetto dalla password dell'account Ethereum.

I nodi di transazione sono a carico bilanciato e hanno una disponibilità impostata in modo da mantenere un'elevata disponibilità. Il modello supporta fino a cinque nodi di transizione di cui è stato eseguito il provisioning utilizzando il set di scalabilità di macchine virtuali.

### <a name="log-analytics-details"></a>Dettagli di Log Analytics

Ogni distribuzione crea anche una nuova istanza di Log Analytics o può aggiungersi a un'istanza esistente. Ciò consente il monitoraggio di varie metriche delle prestazioni di ogni macchina virtuale che compone la rete distribuita.

## <a name="deployment-architecture"></a>Architettura di distribuzione

### <a name="description"></a>Descrizione

Questo modello di soluzione consente di distribuire una rete di consorzio Ethereum multi-membro basata su una singola regione o su più regioni. La rete virtuale di ogni regione è collegata a un'altra regione in una topologia a catena utilizzando i gateway VNET e le risorse di connessione. Inoltre, prevede un registrar, che contiene le informazioni necessarie su tutti i nodi di mining e di transazione distribuiti in ogni area geografica.

### <a name="standalone-and-consortium-leader-overview"></a>Panoramica leader autonomi e di consorzio

![Panoramica leader autonomi e di consorzio](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Panoramica dell'aggiunta dei membri di consorzio

![Panoramica dell'aggiunta dei membri di consorzio](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Introduzione

Questo processo richiede una sottoscrizione Azure in grado di supportare la distribuzione di più set di scalabilità di macchine virtuali e dischi gestiti. Se necessario, creare un account gratuito di Azure per iniziare.

Quando una sottoscrizione è protetta, accedere al portale di Azure. Selezionare **+ Crea una risorsa**, Marketplace (vedi tutto) e cercare **Ethereum Proof-of-Work Consortium**.

La distribuzione del modello consente di configurare in modo guidato il footprint del primo membro nella rete. Il flusso di distribuzione è suddiviso in cinque fasi: nozioni di base, gruppo di gestione delle operazioni, regioni di distribuzione, dimensioni della rete e prestazioni, impostazioni Ethereum.

### <a name="basics"></a>Nozioni di base

In **Nozioni di base**, specificare i valori dei parametri standard per qualsiasi distribuzione, ad esempio sottoscrizione, gruppo di risorse e proprietà di base della macchina virtuale.

![Nozioni di base](./media/ethereum-deployment/sample-deployment.png)

Nome parametro|Descrizione| Valori consentiti|Valori predefiniti
---|---|---|---
Creare una nuova rete o eseguire l'aggiunta a una esistente?|Creare una nuova rete o eseguire l'aggiunta a una rete di consorzio preesistente|Creazione nuova rete, aggiunta a rete esistente|Creazione di un nuovo sito
Distribuire una rete che faccia parte di un consorzio?|Una rete di consorzio permette a distribuzioni future di aggiungersi a questa rete (visibile quando *Crea nuovo* è selezionato sopra)|Consorzio autonomo|Autonoma
Prefisso della risorsa |Stringa utilizzata come base per la denominazione delle risorse (da 2 a 4 caratteri alfanumerici). Per alcune risorse viene aggiunto alla stringa un hash univoco, mentre vengono accodate informazioni specifiche sulle risorse.|Caratteri alfanumerici con lunghezza compresa tra 2 e 4|ND
Nome utente macchina virtuale| Nome utente dell'amministratore di ogni macchina virtuale distribuita (solo caratteri alfanumerici)|1-64 caratteri |gethadmin
Tipo di autenticazione|Metodo per l'autenticazione per la macchina virtuale. |Password o chiave pubblica SSH|Password
Password (tipo di autenticazione = password)|La password dell'account dell'amministratore per ognuna delle macchine virtuali distribuite. La password deve contenere 3 dei requisiti seguenti: un carattere maiuscolo, un carattere minuscolo, un numero e un carattere speciale. <br />Inizialmente tutte le macchine virtuali hanno la stessa password, ma è possibile modificarla dopo il provisioning.|12-72 caratteri|ND
Chiave SSH (tipo di autenticazione = chiave pubblica)|Chiave Secure Shell usata per l'accesso remoto.|| ND
Sottoscrizione| La sottoscrizione sul quale eseguire la distribuzione della rete di consorzio||ND
Gruppo di risorse| Gruppo di risorse nel quale eseguire la distribuzione della rete di consorzio.||ND
Località| La regione di Azure per gruppo di risorse. ||ND



### <a name="operations-management-suite"></a>Operations Management Suite

Il pannello di Operations Management Suite (OMS) consente di configurare una risorsa OMS per la rete. OMS raccoglierà e visualizzerà metriche e log utili dalla rete, fornendo la possibilità di controllare rapidamente lo stato della rete o i problemi di debug. La libera offerta di OMS non riuscirà correttamente una volta raggiunta la capacità.

![Creazione di una nuova OMS](./media/ethereum-deployment/new-oms.png)

Nome parametro|Descrizione| Valori consentiti|Valori predefiniti
---|---|---|---
Connessione a una OMS esistente|Creare una nuova istanza Log Analytics o aggiunta a un'istanza esistente|Creazione nuova istanza, aggiunta a istanza esistente|Creazione nuovo percorso Log Analytics|L'area in cui verrà distribuito il nuovo Log Analytics (visibile se è selezionato *Crea nuovo*)
Id dell'area di lavoro OMS esistente|ID dell'area di lavoro dell'istanza esistente (visibile se *Aggiungi a esistente* è selezionato) OMS Service Tier|Scegliere il piano tariffario per la nuova istanza. Ulteriori informazioni all'indirizzo https://azure.microsoft.com/pricing/details/log-analytics/ (visibile se *Aggiungi a esistente* è selezionato)|Autonomo gratuito per ogni nodo|Gratuito
Chiave primaria esistente OMS|La chiave primaria usata per connettersi all'istanza esistente di OMS (visibile se *Aggiungi a esistente* è selezionato)

### <a name="deployment-regions"></a>Regioni di distribuzione

Quindi, in **Regioni di distribuzione**, specificare gli input per **Numero di regioni** per distribuire la rete di consorzi e selezionare le regioni Azzurre in base al numero di regioni dato. La distribuzione da parte dell'utente può avvenire in massimo cinque regioni.

![Regioni di distribuzione](./media/ethereum-deployment/deployment-regions.png)

Nome parametro| Descrizione| Valori consentiti |Valori predefiniti
---|---|---|---
Numero di regioni| Numero di aree per la distribuzione della rete di consorzio|1, 2, 3, 4, 5| 2
Prima regione| Prima regione per la distribuzione della rete di consorzio|Tutte le aree di Azure consentite| Stati Uniti occidentali
Seconda regione |Seconda regione per la distribuzione della rete di consorzio (visibile solo quando è selezionato 2 come numero di regioni)|Tutte le aree di Azure consentite| Stati Uniti orientali
Terza regione| Terza regione per la distribuzione della rete di consorzio (visibile solo quando è selezionato 3 come numero di regioni)|Tutte le aree di Azure consentite| Stati Uniti centrali
Quarta regione| Quarta regione per la distribuzione della rete di consorzio (visibile solo quando è selezionato 4 come numero di regioni)|Tutte le aree di Azure consentite| Stati Uniti orientali 2
Quinta regione| Quinta regione per la distribuzione della rete di consorzio (visibile solo quando è selezionato 5 come numero di regioni)|Tutte le aree di Azure consentite| Stati Uniti occidentali 2

### <a name="network-size-and-performance"></a>Dimensioni e prestazioni della rete

In **Network size and performance** (Dimensioni e prestazioni della rete) specificare gli input per la dimensione della rete del consorzio. Ad esempio, il numero e la dimensione dei nodi di data mining e di transazione.

![Dimensioni e prestazioni della rete](./media/ethereum-deployment/network-size-performance.png)

Nome parametro |Descrizione |Valori consentiti| Valori predefiniti
---|---|---|---
Il numero di nodi di mining|Il numero di nodi di mining distribuiti per regione|2 - 15| 2
Prestazioni di archiviazione dei nodi di mining|Il tipo di disco gestito che supporta ciascuno dei nodi di mining distribuiti.|Standard o Premium|Standard
Dimensioni della macchina virtuale dei nodi di mining|Le dimensioni della macchina virtuale utilizzata per i nodi di mining.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Standard serie F, <br />Standard DS <br />e Standard FS|Standard D1v2
Numero di nodi di transazione con carico bilanciato|Il numero di nodi di transazione di cui effettuare il provisioning come parte della rete.|1-5| 2
Prestazioni di archiviazione dei nodi di transazione|Il tipo di disco gestito che supporta ciascuno dei nodi di transazione distribuiti.|Standard o Premium|Standard
Dimensioni della macchina virtuale dei nodi di transazione|Le dimensioni della macchina virtuale utilizzata per i nodi di transazione.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Standard serie F, <br />Standard DS <br />e Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Impostazioni Ethereum

Successivamente, in **Impostazioni Ethereum**, specificare le impostazioni di configurazione relative a Ethereum, come l'ID di rete e la password dell'account Ethereum o il blocco originale.

![Impostazioni Ethereum](./media/ethereum-deployment/standalone-leader-deployment.png)

Nome parametro |Descrizione |Valori consentiti|Valori predefiniti
---|---|---|---
ID ConsortiumMember|L'ID associato a ogni membro che partecipa alla rete di consorzio che consente di configurare spazi di indirizzi IP per evitare conflitti. <br /><br />L'ID membro deve essere univoco per tutte le organizzazioni della stessa rete. È necessario un ID membro univoco anche quando la stessa organizzazione esegue la distribuzione in più regioni.<br /><br />Prendere nota del valore di questo parametro in quanto è necessario condividerlo con gli altri membri aggiunti.|0-255
ID di rete Ethereum|L'ID di rete per la rete di consorzio Ethereum in fase di realizzazione. Ogni rete Ethereum ha il proprio ID di rete, di cui 1 è l'ID della rete pubblica. Sebbene l'accesso alla rete sia limitato per i nodi di mining, si consiglia comunque di utilizzarne un gran numero per evitare collisioni.|5 - 999,999,999| 10101010
Blocco originale personalizzato|Possibilità di generare automaticamente un blocco originale o di fornirne uno personalizzato.|Sì/No| No 
Password dell'account Ethereum (blocco originale personalizzato = no)|La password dell'amministratore utilizzata per proteggere l'account Ethereum importato in ogni nodo. La password deve contenere i seguenti elementi: un carattere maiuscolo, un carattere minuscolo e un numero.|12 o più caratteri|ND
Passphrase chiave privata Ethereum (blocco originale personalizzato = no)|La passphrase utilizzata per generare la chiave privata ECC associata all'account Ethereum predefinito che viene generato. Non è necessario inserire esplicitamente una chiave privata pregenerata.<br /><br />Considerare una passphrase con sufficiente casualità per garantire una valida chiave privata e nessuna sovrapposizione con altri membri del consorzio. La passphrase deve contenere minimo i seguenti elementi: un carattere maiuscolo, un carattere minuscolo e un numero.<br /><br />Nota: se due membri utilizzano la stessa passphrase, gli account generati saranno gli stessi. La stessa passphrase è utile se una singola organizzazione sta tentando la distribuzione in diverse regioni e desidera condividere un singolo account (coin base) in tutti i nodi.|12 o più caratteri|ND
Blocco originale (blocco originale personalizzato = sì)|Stringa JSON che rappresenta il blocco originale personalizzato. Maggiori dettagli sul formato del blocco originale sono disponibili qui, sotto Reti personalizzate.<br /><br />Un account Ethereum viene comunque creato quando si fornisce un blocco originale personalizzato. Considerare la possibilità di specificare un account Ethereum prefinanziato nel blocco originale per non aspettare l'attività di mining.|JSON valido |ND
Chiave condivisa per la connessione|Una chiave condivisa per la connessione tra i gateway VNET.| 12 o più caratteri|ND
URL di dati di consorzio|L'URL che indica i dati relativi alla configurazione del consorzio forniti dalla distribuzione di un altro membro. <br /><br />Queste informazioni sono fornite da un membro già connesso che ha una distribuzione client. Se è stato distribuito il resto della rete, l'URL è l'output di distribuzione del modello, denominato CONSORTIUM-DATA.||ND
Gateway VNet a cui connettersi|Il percorso della risorsa del gateway VNet a cui connettersi.<br />Queste informazioni sono fornite da un membro già connesso che ha una distribuzione client. Se è stato distribuito il resto della rete, l'URL è nell'output di distribuzione del modello, denominato CONSORTIUM_MEMBER_GATEWAY_ID. Nota: è necessario utilizzare l'URL dei dati dello stesso membro del consorzio e le risorse del gateway VNet.||ND
Registrar degli endpoint di informazioni peer|Endpoint di informazioni peer fornito dalla distribuzione di un altro membro|Endpoint valido del primo membro del consorzio|ND
Registrar della chiave di informazioni peer|Chiave primaria di informazioni peer fornito dalla distribuzione di un altro membro|Chiave primaria valida del primo membro del consorzio|ND

### <a name="summary"></a>Summary

Fare clic su tramite il pannello di riepilogo per analizzare l'input specificato e per eseguire la convalida pre-distribuzione di base.

![Summary](./media/ethereum-deployment/summary.png)

Rivedere i termini legali e sulla privacy e fare clic su **Acquista** per avviare la distribuzione. Se la distribuzione ha più di una regione o è per una rete di consorzio, questo modello predistribuisce i gateway VPN necessari per supportare la connettività di rete con gli altri membri. La distribuzione del gateway può richiedere fino a 45-50 minuti.

## <a name="post-deployment-sanity-checks"></a>Verifiche di integrità post-distribuzione

### <a name="administrator-page"></a>Pagina dell'amministratore

Una volta che la distribuzione è stata completata con successo ed è stato eseguito il provisioning di tutte le risorse, è possibile accedere alla pagina dell'amministratore per visualizzare la rete blockchain e controllare l'integrità dello stato di distribuzione. L'URL della pagina di amministrazione è il nome DNS del sistema di bilanciamento del carico; è presente nella sezione di output della distribuzione del modello denominata ADMIN_SITE.

Per trovarlo, selezionare il gruppo di risorse che è stato distribuito. Quindi, selezionare **Panoramica**e fare clic sul collegamento immediatamente sotto **Distribuzioni** che mostra il numero che ha avuto esito positivo.

![Panoramica del gruppo di risorse](./media/ethereum-deployment/resource-overview.png)

La nuova schermata mostra la cronologia di distribuzione. Selezionare la prima risorsa di distribuzione (ad esempio, microsoft-azure-blockchain.azure-blockchain-servi...) e cercare la sezione **Output** nella parte inferiore della pagina. Verrà visualizzato l'URL per la pagina di amministrazione elencato nel parametro di output di distribuzione del modello come ADMIN_SITE.

![Distribuzioni di risorsa](./media/ethereum-deployment/resource-deployments.png)

![Output di distribuzione](./media/ethereum-deployment/deployment-output.png)

Per accedere alla pagina di amministrazione, copiare l'output **ADMIN-SITE** e aprirlo in un'altra scheda.

Nella pagina di amministrazione, è possibile ottenere una panoramica ad alto livello della topologia utilizzata consultando la sezione Stato del nodo Ethereum. Include tutti i nomi host di nodo, il conteggio di peer e il blocco visualizzato più di recente. Il conteggio dei peer per ciascun nodo è compreso tra il minimo di uno inferiore al *numero totale dei nodi* e il massimo numero di peer configurato. Si noti che il conteggio dei peer non limita il numero di nodi che possono essere distribuiti all'interno della rete.
In alcuni casi, si vedrà che il numero di peer varia e può essere minore di (numero totale di nodi - 1). La differenza nel conteggio non è sempre un segno che i nodi non sono integri, poiché i fork nel libro mastro possono causare lievi variazioni nel conteggio dei peer. Infine, è possibile ispezionare l'ultimo blocco visto da ciascun nodo della rete per determinare i fork o i ritardi nel sistema.

![Stato del nodo](./media/ethereum-deployment/node-status.png)

Lo stato del nodo viene aggiornato ogni 10 secondi. Ricaricare la pagina tramite il browser o il pulsante **Ricarica** per aggiornare la visualizzazione.

### <a name="oms-portal"></a>Portale OMS

È possibile individuare il portale OMS seguendo il collegamento ipertestuale nell'output di distribuzione (OMSPORTALURL) o selezionando la risorsa OMS nel gruppo di risorse distribuito.

![URL OMS](./media/ethereum-deployment/oms-url.png)

![Risorsa OMS](./media/ethereum-deployment/oms-resource.png)

Il portale visualizza innanzitutto una panoramica delle statistiche di rete di alto livello.

![Panoramica OMS](./media/ethereum-deployment/oms-overview.png)

Facendo clic sulla panoramica, si accede a un portale per visualizzare le statistiche per ciascun nodo.

![Statistiche per ogni nodo](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Accesso ai nodi

È possibile connettersi in remoto alle macchine virtuali per i nodi di transazione tramite SSH con il nome utente e la password/chiave SSH dell'amministratore forniti. Poiché le macchine virtuali del nodo di transazione non hanno indirizzi IP pubblici propri, è necessario passare attraverso il sistema di bilanciamento del carico e specificare il numero di porta. Il comando SSH da eseguire per accedere al primo nodo di transazione è elencato nel parametro di output di distribuzione del modello come **SSH_TO_FIRST_TX_NODE** (per la distribuzione di esempio: ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Per ottenere nodi di transazione aggiuntivi, incrementare il numero di porta di uno (ad esempio, il primo nodo di transazione è sulla porta 4000).

Poiché le macchine virtuali in cui vengono eseguiti i nodi di mining non sono accessibili dall'esterno, è necessario eseguire uno dei nodi di transazione. Dopo aver creato una sessione SSH su un nodo di transazione, installare la chiave privata nel nodo delle transazioni o usare la password per iniziare una sessione SSH in uno dei nodi di mining.

**Nota**

È possibile ottenere i nomi host dal sito di amministrazione o dal portale di Azure. Nel portale di Azure, i nomi host dei nodi presenti nel set di scalabilità di macchine virtuali (VMSS) è elencato in **Istanze**, che differisce da nomi host effettivi. Ad esempio, il nome host nel portale di Azure può apparire come **mn-asdfmv-reg1_0** ma il nome host effettivo potrebbe essere **mn-asdfmv-reg**.

Ad esempio: 

Nome host del portale di Azure| Nome host effettivo
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Aggiunta di un nuovo membro di consorzio

### <a name="sharing-data"></a>Condivisione dei dati

Come primo membro (o un membro connesso) del consorzio, è necessario fornire agli altri membri alcune informazioni in modo che possano aggiungersi e stabilire la loro connessione. In particolare:

1. **Dati di configurazione del consorzio in condivisione**: un determinato insieme di dati viene utilizzato per orchestrare la connessione Ethereum tra due membri. Le informazioni necessarie, tra cui il blocco originale, l'ID di rete di consorzio e i nodi di avvio, vengono scritte in un file sui nodi di transazione del leader o di un altro membro distribuito. Il percorso di questo file è elencato nel parametro di output di distribuzione del modello denominato **CONSORTIUM-DATA**.
2. **Endpoint informazioni peer**: l'endpoint del registrar di informazioni peer per ottenere informazioni su tutti i nodi già connessi alla rete Ethereum dai leader o dalla distribuzione di un altro membro. Il database memorizza una serie di informazioni relative ad ogni nodo connesso in rete, informazioni quali il nome host del nodo, l'indirizzo IP privato, ecc. Si tratta del parametro di output di distribuzione del modello denominato **PEER_INFO_ENDPOINT**.
3. **Chiave primaria informazioni peer**: La chiave primaria del registrar di informazioni peer viene utilizzata per accedere alla chiave primaria di informazioni peer del leader o di un altro membro. Si tratta del parametro di output di distribuzione del modello denominato **PEER_INFO_PRIMARY_KEY**.


4. **Gateway VNET**: ogni membro stabilisce una connessione all'intera rete blockchain tramite un membro esistente. Per connettere VNET, è necessario il percorso della risorsa al gateway VNET del membro a cui ci si connette. Questo è il parametro dell'output di distribuzione del modello denominato **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Chiave condivisa**: un segreto prestabilito tra due membri della rete di consorzio che stanno stabilendo una connessione. Si tratta di una stringa alfanumerica (da 1 a 128 caratteri) concordata al di fuori del contesto dell'installazione remota. (Ad esempio **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Accettazione del nuovo membro

Questo passaggio deve essere eseguito dopo che il membro aggiunto ha distribuito correttamente la propria rete. Per potersi aggiungere alla rete e visualizzare il traffico delle transazioni, un membro esistente deve eseguire una configurazione finale sul proprio gateway VPN per accettare la connessione. Ciò significa che i nodi Ethereum del membro che si aggiunge non funzioneranno fino a quando non viene stabilita una connessione. Questa configurazione può essere eseguita tramite PowerShell o CLI xPlat. Un modulo PowerShell e uno script CLI xPlat sono memorizzati anche nel nodo di transazione, insieme ai dati del consorzio. Il percorso dello script si configura come i parametri di output di distribuzione denominati, rispettivamente, **PAIR-GATEWAY-PS-MODULE** e **PAIR-GATEWAY-AZURE-CLISCRIPT**.

**Configurazione di PowerShell/CLI**

Ulteriori informazioni su come iniziare a utilizzare i cmdlet di Azure PowerShell e CLI di Azure xPlat sono disponibili nella documentazione Azure.

Sarà necessaria l'ultima versione dei cmdlet di Azure installati localmente e una sessione aperta. Accedere alla sessione con le credenziali di sottoscrizione di Azure.

**PowerShell: connessione**

Scaricare il modulo PowerShell e archiviarlo localmente. Il percorso del modulo PowerShell è specificato come parametro di output per la distribuzione del modello **PAIR-GATEWAY-PS-MODULE**.

Se non è già abilitato, utilizzare il cmdlet **Set-ExecutionPolicy** per la sessione locale al fine di consentire l'esecuzione di un modulo non firmato.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Successivamente, accedere alla sottoscrizione di Azure in cui è stata installata la distribuzione del leader tramite

**Login-AzureRmAccount**

Quindi importare il modulo:

**Import-Module <filepath to downloaded file>**

Infine, eseguire la funzione con l'input appropriato:

- **MyGatewayResourceId**: percorso di risorsa del gateway. Questo è il parametro dell'output di distribuzione del modello denominato **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId**: percorso di risorsa del gateway del membro aggiunto. Questo è fornito dal membro aggiunto ed è il parametro di output di distribuzione del modello anche denominato **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : nome per identificare la specifica connessione al gateway.
- **Shared Key**: segreto prestabilito tra i due membri della rete del consorzio che stanno stabilendo una connessione.

**CreateConnection** - MyGatewayResourceId <resource path of your Gateway> -OtherGatewayResourceId <percorso di risorsa del gateway del membro aggiunto> -ConnectionName myConnection -SharedKey "MySharedKeyAbc123"

**CLI xPlat: connessione**

Scaricare lo script dell'interfaccia della riga di comando di Azure e archiviarlo localmente. Il percorso dello script dell'interfaccia della riga di comando di Azure è specificato nel parametro di distribuzione del modello denominato **PAIR-GATEWAY-AZURE-CLI-SCRIPT**.

Eseguire lo script con l'input appropriato:

- **MyGatewayResourceId**: percorso di risorsa del gateway. Questo è il parametro dell'output di distribuzione del modello denominato **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId**: percorso di risorsa del gateway del membro aggiunto. Questo è fornito dal membro associato ed è il parametro di distribuzione del modello della loro distribuzione anche chiamato **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : nome per identificare la specifica connessione al gateway.
- **Shared Key**: segreto prestabilito tra i due membri della rete del consorzio che stanno stabilendo una connessione.
- **Location**: area di Azure in cui viene distribuita la risorsa del gateway.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

L'architettura sarà la seguente dopo aver configurato correttamente la connessione tra le distribuzioni **leader** e **membro**.

![Architettura leader e membro](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Trovato nuovo account membro

### <a name="generated-genesis-block"></a>Blocco di origine generato

Come primo membro, il proprio account Ethereum è finanziato con mille miliardi di Ether se la distribuzione genera il blocco originale (Blocco originale personalizzato = no). Gli altri membri disporranno di un account che non è pre-finanziato e dovranno aspettare di accumulare Ether quando i loro nodi di mining cominceranno a eseguire il mining dei blocchi. Per evitare che i nuovi soci attendino l'Ether, sarà necessario finanziare esplicitamente i conti Ethereum dei membri aggiunti.

Per fare ciò, i membri che si aggiungono devono fornire all'utente l'account Ethereum che viene visualizzato sul loro sito web dell'amministratore. È quindi possibile utilizzare il sito Web dell'amministratore per trasferire Ether dal proprio account al loro account accedendo all'account fornito.

### <a name="custom-genesis-block"></a>Blocco originale personalizzato

Se un blocco originale personalizzato viene fornito con un account Ethereum specificato, è possibile utilizzare MetaMask o un altro strumento per trasferire Ether da tale account specificato all'account Ethereum pregenerato visibile nel sito Web dell'amministratore. Per istruzioni su come usare MetaMask, vedere [Creazione di un account Ethereum](#create-ethereum-account).

Se viene fornito un blocco originale personalizzato senza un account o non si ha accesso ad alcun account pre-allocato, sarà necessario attendere fino a quando i nodi di mining iniziano l'attività di mining per generare Ether nel proprio account (coin base). La velocità di generazione dei fondi dipende dal livello di difficoltà specificato nel blocco originale personalizzato.

## <a name="create-ethereum-account"></a>Creazione di account Ethereum

Per creare un account aggiuntivo, è possibile utilizzare una varietà di soluzioni. Una di queste soluzioni è MetaMask, un'estensione di Chrome che fornisce un insieme di credenziali di identità e una connessione a una rete Ethereum, pubblica, di test o personalizzata. MetaMask formula una transazione per registrare l'account in rete.

Questa transazione, come qualsiasi altra, è diretta a uno dei nodi di transazione ed eventualmente ne verrà eseguito il mining in un blocco come illustrato di seguito.

![Nodo di transazione](./media/ethereum-deployment/transaction-node.png)

Per installare l'estensione in Chrome, andare su Personalizza e controlla Google Chrome (pulsante di overflow) > Altri strumenti > Estensioni > Ottieni altre estensioni e cercare MetaMask.

![Estensione MetaMask](./media/ethereum-deployment/metamask-extension.png)

Una volta installata, aprire MetaMask e creare un nuovo insieme di credenziali. Per impostazione predefinita, l'insieme di credenziali sarà connesso alla rete di test di Morden. Modificare questa opzione per connettersi alla rete del consorzio privata distribuita, in particolare al sistema di bilanciamento del carico davanti ai nodi di transazione. Dall'output del modello, recuperare l'endpoint RPC Ethereum esposto alla porta 8545, denominato `ETHEREUM-RPC-ENDPOINT`, e immetterlo nell'RPC personalizzato come illustrato di seguito.

![Impostazioni MetaMask](./media/ethereum-deployment/metamask-settings.png)

Creando l'insieme di credenziali, si crea un portafoglio contenente un account. Per creare altri account, selezionare **Cambia account**, quindi il pulsante **+**.

![Creazione account MetaMask](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Avviare l'allocazione iniziale di Ether

Tramite la pagina dell'amministratore, è possibile formulare una transazione per trasferire Ether dal conto pre-assegnato a un altro conto Ethereum. Questo trasferimento di Ether è una transazione che viene inviata al nodo di transazione con mining eseguito in un blocco come illustrato di seguito.

![Nodo di transazione](./media/ethereum-deployment/transaction-node-mined.png)

Attraverso l'icona Appunti nel portafoglio MetaMask, copiare l'indirizzo dell'account Ethereum in cui si desidera trasferire Ether e tornare alla pagina dell'amministratore. Incollare l'account copiato nel campo di immissione per trasferire 1000 Ether dall'account Ethereum pre-assegnato al nuovo account creato. Fare clic su **Invia** e attendere che venga eseguito il mining della transazione in un blocco.

![Stato del nodo](./media/ethereum-deployment/node-status2.png)

Una volta che la transazione è impegnata in un blocco su cui è stato eseguito il mining, il saldo dell'account in MetaMask per il prorpio conto rifletterà il trasferimento di 1000 Ether.

![Trasferimento MetaMask](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Trasferimento di Ether tra gli account

A questo punto, è possibile eseguire transazioni all'interno della propria rete di consorzio privata. La transazione più semplice è il trasferimento di Ether da un account all'altro. Per formulare una tale transazione, è possibile utilizzare MetaMask ancora una volta, trasferendo denaro dal primo account utilizzato a un secondo account.

Dal **Portafoglio 1** in MetaMask, fare clic su Invia. Copiare l'indirizzo del secondo portafoglio creato nel campo di immissione **Indirizzo destinatario** e la quantità di Ether da trasferire nel campo di immissione **Quantità**. Fare clic su **Invia** e accettare la transazione.

![Invio transazione MetaMask](./media/ethereum-deployment/metamask-send.png)

Anche in questo caso, quando viene eseguito il mining dell'operazione che viene poi impegnata in un blocco, i saldi degli account vengono riflessi di conseguenza. Nota: dal saldo del **Portafoglio 1** vengono dedotti più di 15 Ether, poiché per elaborare la transazione è stato necessario pagare un corrispettivo di mining.

![Portafoglio 1](./media/ethereum-deployment/wallet1.png)

![Portafoglio 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Passaggi successivi

Ora si è pronti a concentrarsi sullo sviluppo di applicazioni e contratti intelligenti relativi alla propria rete blockchain di consorzio privata.
