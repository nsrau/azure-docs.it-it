---
title: Configurazione della connettività da una macchina virtuale a un SAP HANA di Azure (istanze Large) | Microsoft Docs
description: Configurazione della connettività da una macchina virtuale per usare SAP HANA di Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 919e253b6d9ddf8d65f86897a299416e93f3e660
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099925"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Connessione di macchine virtuali di Azure a istanze Large di HANA

L'articolo [Informazioni su SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) indica che la distribuzione minima di istanze Large di HANA con il livello dell'applicazione SAP in Azure ha un aspetto simile al seguente:

![Rete virtuale di Azure connessa a SAP HANA in Azure (istanze di grandi dimensioni) e in locale](./media/hana-overview-architecture/image1-architecture.png)

Guardando più vicino al lato rete virtuale di Azure, è necessario:

- Definizione di una rete virtuale di Azure in cui distribuire le macchine virtuali del livello di applicazione SAP.
- Definizione di una subnet predefinita nella rete virtuale di Azure, ovvero la rete in cui vengono distribuite le macchine virtuali.
- La rete virtuale di Azure creata deve avere almeno una subnet di macchine virtuali e una subnet del gateway di macchine virtuali ExpressRoute. Alle subnet devono essere assegnati gli intervalli di indirizzi IP specificati e illustrati nelle sezioni seguenti.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Creare la rete virtuale di Azure per istanze Large di HANA.

>[!Note]
>La rete virtuale di Azure per istanze Large di HANA deve essere creata usando il modello di distribuzione Azure Resource Manager. Il vecchio modello di distribuzione di Azure, comunemente noto come modello di distribuzione classico, non è supportato dalle istanze Large di HANA.

La rete virtuale può essere creata mediante il portale di Azure, PowerShell, un modello di Azure o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Creare una rete virtuale tramite il portale di Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). L'esempio seguente illustra una rete virtuale creata tramite il portale di Azure.

Quando si fa riferimento allo **spazio degli indirizzi** in questa documentazione, allo spazio degli indirizzi che la rete virtuale di Azure è autorizzato a usare. nonché l'intervallo di indirizzi che verrà usato dalla rete virtuale per la propagazione della route BGP. Lo **spazio di indirizzi** può essere visualizzato qui:

![Spazio di indirizzi di una rete virtuale di Azure visualizzato nel portale di Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Nell'esempio precedente, con 10.16.0.0/16, alla rete virtuale di Azure è stato assegnato un intervallo di indirizzi IP piuttosto grande e ampio da usare. Ciò significa che tutti gli intervalli di indirizzi IP delle subnet successive in questa rete virtuale possono avere i relativi intervalli all'interno di tale spazio. In genere non è consigliabile specificare un intervallo di indirizzi così ampio per una singola rete virtuale in Azure. Esaminiamo ora le subnet definite nella rete virtuale di Azure.

![Subnet di rete virtuale di Azure e intervalli di indirizzi IP relativi](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Viene esaminata una rete virtuale con una prima subnet di macchine virtuali denominata "default" e un'altra subnet denominata "GatewaySubnet".

Nei due elementi grafici precedenti, lo **spazio degli indirizzi della rete virtuale** copre sia **l'intervallo di indirizzi IP della subnet della VM di Azure** che quello del gateway di rete virtuale.

È possibile limitare lo **spazio indirizzi della rete virtuale** agli intervalli specifici usati da ogni subnet. È anche possibile definire lo **spazio di indirizzi** di una rete virtuale come più intervalli specifici, come illustrato di seguito:

![Spazio di indirizzi della rete virtuale di Azure con due spazi](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

In questo caso per lo **spazio di indirizzi della rete virtuale** sono definiti due spazi, uguali agli intervalli di indirizzi IP definiti per l'intervallo di indirizzi IP della subnet di Macchine virtuali di Azure e del gateway di rete virtuale. 

È possibile usare qualsiasi standard di denominazione per le subnet del tenant, vale a dire le subnet VM. È consentita, tuttavia, **una sola subnet del gateway per ogni rete virtuale** che si connette al circuito ExpressRoute di SAP HANA in Azure (istanze Large). **Questa subnet del gateway deve essere denominata "GatewaySubnet"** per assicurarsi che il gateway ExpressRoute sia correttamente inserito.

> [!WARNING] 
> È fondamentale che la subnet del gateway sia sempre denominata "GatewaySubnet".

È possibile usare più subnet di macchine virtuali, anche con intervalli di indirizzi non contigui. Tali intervalli di indirizzi devono essere inclusi nello **spazio di indirizzi** della rete virtuale, in forma aggregata o come elenco degli intervalli di indirizzi esatti delle subnet di macchine virtuali e della subnet del gateway.

Di seguito vengono riepilogati gli aspetti importanti relativi a una rete virtuale di Azure che si connette a istanze Large di HANA.

- Quando si esegue una distribuzione iniziale di istanze large di HANA, è necessario inviare lo **spazio di indirizzi della rete virtuale** a Microsoft. 
- Lo **spazio di indirizzi della rete virtuale** può essere un intervallo più ampio che include sia gli intervalli di indirizzi IP della subnet di Macchine virtuali di Azure che quelli del gateway di rete virtuale.
- In alternativa, è possibile inviare più intervalli che includono sia gli intervalli di indirizzi IP diversi della subnet di macchine virtuali che l'intervallo di indirizzi IP della subnet del gateway di rete virtuale.
- Lo **spazio di indirizzi di rete virtuale** viene usato per la propagazione del routing BGP.
- Il nome della subnet del gateway deve essere: **"GatewaySubnet"** .
- Lo spazio di indirizzi viene usato come filtro sul lato dell'istanza Large di HANA per consentire o negare il traffico da Azure alle unità di istanze Large di HANA. Verificare che le informazioni sul routing BGP della rete virtuale di Azure e gli intervalli di indirizzi IP configurati per il filtro sul lato dell'istanza Large di HANA corrispondano per evitare problemi di connettività.
- Alcuni dettagli sulla subnet del gateway vengono illustrati più avanti, nella sezione **Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA**.



## <a name="different-ip-address-ranges-to-be-defined"></a>Intervalli di indirizzi IP diversi da definire 

Sono già stati introdotti alcuni degli intervalli di indirizzi IP necessari per la distribuzione di istanze large di HANA. Esistono tuttavia altri intervalli di indirizzi IP importanti Non tutti gli intervalli di indirizzi IP seguenti devono essere inviati a Microsoft. ma è necessario definirli prima di inviare una richiesta per la distribuzione iniziale.

- **Spazio indirizzo rete virtuale**: Lo **spazio degli indirizzi della rete virtuale** è costituito dagli intervalli di indirizzi IP assegnati al parametro dello spazio degli indirizzi nelle reti virtuali di Azure. Queste reti si connettono all'ambiente dell'istanza Large di SAP HANA. Per questo parametro dello spazio degli indirizzi è consigliabile usare un valore multiriga. Deve essere costituito dall'intervallo di subnet della macchina virtuale di Azure e da uno o più intervalli di subnet del gateway di Azure. Questo intervallo di subnet è stato illustrato nelle figure precedenti. NON deve sovrapporsi all'intervallo di indirizzi locale, di indirizzi ER-P2P e del pool di indirizzi IP del server. Come si ottengono questi intervalli di indirizzi IP? Il team o il provider di servizi della rete aziendale deve fornire uno o più intervalli di indirizzi IP che non vengono usati nella rete. Ad esempio, la subnet della macchina virtuale di Azure è 10.0.1.0/24 e la subnet della subnet del gateway di Azure è 10.0.2.0/28.  È consigliabile che lo spazio di indirizzi della rete virtuale di Azure sia definito come segue: 10.0.1.0/24 e 10.0.2.0/28. Sebbene i valori dello spazio degli indirizzi possano essere aggregati, è consigliabile fare in modo che corrispondano agli intervalli della subnet, per evitare che gli intervalli di indirizzi IP non usati vengano inavvertitamente usati nuovamente in spazi di indirizzi più grandi in altri punti della rete. **Lo spazio degli indirizzi della rete virtuale è un intervallo di indirizzi IP. Deve essere inviato a Microsoft quando si richiede una distribuzione iniziale**.
- **Intervallo di indirizzi IP della subnet della macchina virtuale di Azure:** questo intervallo di indirizzi IP è quello assegnato al parametro della subnet della rete virtuale di Azure. Questo parametro è nella rete virtuale di Azure e si connette all'ambiente dell'istanza Large di SAP HANA. Questo intervallo di indirizzi IP viene usato per assegnare indirizzi IP alle macchine virtuali di Azure. Gli indirizzi IP esterni a questo intervallo potranno connettersi ai server delle istanze Large di SAP HANA. Se necessario, è possibile usare più subnet di macchine virtuali di Azure. È consigliabile usare un blocco CIDR da /24 di Microsoft per ogni subnet di macchina virtuale di Azure. Questo intervallo di indirizzi deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale di Azure. Come si ottiene questo intervallo di indirizzi IP? Il team o il provider del servizio di rete aziendale deve indicare uno o più intervalli di indirizzi IP che non siano già usati nella rete.
- **Intervallo di indirizzi IP della subnet del gateway di rete virtuale:** a seconda delle funzionalità che si prevede di usare, la dimensione consigliata è:
   - Gateway ExpressRoute con prestazioni extra: blocco indirizzi da /26, necessari per SKU di classe di tipo II.
   - Coesistenza con VPN ed ExpressRoute tramite un gateway di rete virtuale ExpressRoute ad alte prestazioni (o più piccolo): blocco indirizzi da /27.
   - Tutti gli altri casi: blocco indirizzi da /28. Questo intervallo di indirizzi deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale e deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale di Azure da inviare a Microsoft. Come si ottiene questo intervallo di indirizzi IP? Il team o il provider del servizio di rete aziendale deve indicare un intervallo di indirizzi IP che non sia già in uso nella rete. 
- **Intervallo di indirizzi per la connettività ER-P2P:** si tratta dell'intervallo di indirizzi IP per la connessione ExpressRoute (ER) peer-to-peer (P2P) all'istanza Large di SAP HANA. Deve essere un intervallo di indirizzi IP CIDR da /29. Questo intervallo NON deve sovrapporsi con l'intervallo di indirizzi IP locale o altri intervalli di indirizzi IP di Azure. Viene usato per configurare la connettività ER dal gateway virtuale ExpressRoute ai server di istanze Large di SAP HANA. Come si ottiene questo intervallo di indirizzi IP? Il team o il provider del servizio di rete aziendale deve indicare un intervallo di indirizzi IP che non sia già in uso nella rete. **Questo intervallo è un intervallo di indirizzi IP. Deve essere inviato a Microsoft quando si richiede una distribuzione iniziale**.  
- **Intervallo di indirizzi del pool di indirizzi IP del server:** questo intervallo di indirizzi IP viene usato per assegnare singoli indirizzi IP ai server delle istanze Large di HANA. Le dimensioni della subnet consigliate sono un blocco CIDR da /24, ma se necessario posso essere inferiori, fino a un minimo di 64 indirizzi IP. In questo intervallo i primi 30 indirizzi IP sono riservati per l'uso da parte di Microsoft. Assicurarsi di tenerne conto nella scelta delle dimensioni dell'intervallo. Questo intervallo NON deve sovrapporsi con l'indirizzo IP locale o altri indirizzi IP di Azure. Come si ottiene questo intervallo di indirizzi IP? Il team o il provider del servizio di rete aziendale deve indicare un intervallo di indirizzi IP che non sia già in uso nella rete.  **Questo intervallo di indirizzi IP deve essere inviato a Microsoft quando si richiede una distribuzione iniziale**.

Intervalli di indirizzi IP facoltativi che devono essere inviati a Microsoft:

- Se si sceglie di usare [ExpressRoute copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) per abilitare il routing diretto da unità locali a istanze large di Hana, è necessario riservare un altro intervallo di indirizzi IP. Questo intervallo potrebbe non sovrapporsi ad altri intervalli di indirizzi IP definiti in precedenza.
- Se si sceglie di usare [ExpressRoute copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) per abilitare il routing diretto da un tenant di istanze large di Hana in un'area di Azure a un altro tenant di istanze large di Hana in un'altra area di Azure, è necessario riservare un altro intervallo di indirizzi IP. Questo intervallo potrebbe non sovrapporsi ad altri intervalli di indirizzi IP definiti in precedenza.

Per ulteriori informazioni su ExpressRoute Copertura globale e sull'utilizzo in istanze large di HANA, controllare i documenti:

- [Architettura di rete SAP HANA (istanze large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Connettere una rete virtuale alle istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
È necessario definire e pianificare gli intervalli di indirizzi IP descritti in precedenza. Tuttavia, non occorre trasmetterli tutti a Microsoft. Gli intervalli di indirizzi IP per cui è necessario assegnare un nome da trasmettere a Microsoft sono i seguenti:

- Spazi di indirizzi di rete virtuale di Azure
- Intervallo di indirizzi per la connettività ER-P2P
- Intervallo di indirizzi del pool di indirizzi IP del server

Se si aggiungono altre reti virtuali che devono connettersi a istanze Large di HANA, è necessario inviare a Microsoft il nuovo spazio di indirizzi della rete virtuale di Azure da aggiungere. 

Di seguito è riportato un esempio dei vari intervalli, con alcuni intervalli di esempio che è necessario configurare e inviare a Microsoft. Il valore per lo spazio di indirizzi della rete virtuale di Azure non viene aggregato nel primo esempio. Tuttavia, è definito dal primo intervallo di indirizzi IP della subnet di macchine virtuali di Azure e dall'intervallo di indirizzi IP della subnet del gateway di rete virtuale. 

Per usare più subnet di macchine virtuali nella rete virtuale di Azure, è necessario configurare e inviare gli intervalli di indirizzi IP aggiuntivi delle altre subnet di macchine virtuali come parte dello spazio di indirizzi della rete virtuale di Azure.

![Intervalli di indirizzi IP necessari nella distribuzione minima di SAP HANA in Azure (istanze Large)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Il grafico non Mostra gli intervalli di indirizzi IP aggiuntivi necessari per l'uso facoltativo di ExpressRoute Copertura globale.

È anche possibile aggregare i dati inviati a Microsoft. In questo caso, solo lo spazio di indirizzi della rete virtuale di Azure include uno spazio. Se si usano gli intervalli di indirizzi IP dell'esempio precedente, lo spazio di indirizzi IP aggregato della rete virtuale risulterebbe quello illustrato di seguito:

![Seconda possibilità di intervalli di indirizzi IP necessari nella distribuzione minima di SAP HANA in Azure (istanze Large)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Nell'esempio, anziché due intervalli minori che definiscono lo spazio di indirizzi della rete virtuale di Azure, si ottiene un intervallo più ampio che include 4096 indirizzi IP. Una definizione così ampia dello spazio di indirizzi lascia inutilizzati alcuni intervalli piuttosto ampi. Dato che i valori dello spazio di indirizzi della rete virtuale vengono usati per la propagazione di route BGP, l'uso degli intervalli inutilizzati in locale o in un altro punto della rete può causare problemi di routing. Il grafico non Mostra gli intervalli di indirizzi IP aggiuntivi necessari per l'uso facoltativo di ExpressRoute Copertura globale.

Si consiglia di lasciare lo spazio degli indirizzi strettamente allineato con lo spazio di indirizzi della subnet effettivo usato. Se necessario, è sempre possibile aggiungere nuovi valori dello spazio di indirizzi in un secondo momento, senza incorrere in tempi di inattività della rete virtuale.
 
> [!IMPORTANT] 
> L'intervallo di indirizzi IP ER-P2P, il pool di indirizzi IP del server e lo spazio di indirizzi di rete virtuale di Azure **NON** devono sovrapporsi reciprocamente né devono sovrapporsi ad alcun altro intervallo usato nella rete. Ogni intervallo deve essere discreto e non può essere una subnet di qualsiasi altro intervallo, come illustrato nelle figure precedenti. In caso di sovrapposizioni tra gli intervalli, la rete virtuale di Azure potrebbe non riuscire a connettersi al circuito ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Passaggi successivi dopo la definizione degli intervalli di indirizzi
Dopo aver definito gli intervalli di indirizzi IP, devono succedersi gli eventi seguenti:

1. Invio degli intervalli di indirizzi IP per lo spazio di indirizzi della rete virtuale di Azure, la connettività ER-P2P e l'intervallo di indirizzi del pool di indirizzi IP del server, insieme agli altri dati elencati all'inizio del documento. A questo punto è anche possibile iniziare a creare la rete virtuale e le subnet delle macchine virtuali. 
2. Microsoft crea un circuito ExpressRoute tra la sottoscrizione di Azure e il timbro dell'istanza Large di HANA.
3. Microsoft crea una rete tenant sul timbro dell'istanza Large.
4. Microsoft configura la rete nell'infrastruttura di SAP HANA in Azure (istanze Large) per accettare gli indirizzi IP dallo spazio di indirizzi della rete virtuale di Azure che comunica con le istanze Large di HANA.
5. In base allo SKU di SAP HANA in Azure (istanze Large) acquistato, Microsoft assegna un'unità di calcolo in una rete tenant, alloca e monta la risorsa di archiviazione e installa il sistema operativo (SUSE o Red Hat Linux). Gli indirizzi IP per tali unità vengono esclusi dall'intervallo di indirizzi del pool di indirizzi del server inviato a Microsoft.

Al termine del processo di distribuzione, Microsoft fornisce i dati seguenti:
- Informazioni necessarie per connettere le reti virtuali di Azure al circuito ExpressRoute che connette le reti virtuali di Azure alle istanze Large di HANA:
     - Chiave/i di autorizzazione
     - PeerID ExpressRoute
- Dati per accedere a istanze Large di HANA dopo aver configurato il circuito ExpressRoute e la rete virtuale di Azure.

È anche possibile trovare la sequenza di connessione di istanze Large di HANA nel documento [SAP HANA on Azure (Large Instances) Setup](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) (Configurazione di istanze Large di SAP HANA in Azure). Molti dei passaggi seguenti sono illustrati nella distribuzione di esempio di questo documento. 

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Collegare una rete virtuale alle istanze Large di HANA](hana-connect-vnet-express-route.md).
