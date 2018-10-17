---
title: Configurazione della connettività da una macchina virtuale a un SAP HANA di Azure (istanze Large) | Microsoft Docs
description: Configurazione della connettività dalla macchina virtuale per usare SAP HANA di Azure (Istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167676"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Connessione di macchine virtuali di Azure a istanze Large di HANA

Come già illustrato in [Panoramica e architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), la distribuzione minima di istanze Large di HANA con il livello dell'applicazione SAP in Azure ha un aspetto simile al seguente:

![Rete virtuale di Azure connessa a SAP HANA in Azure (istanze di grandi dimensioni) e in locale](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Esaminando più da vicino la rete virtuale di Azure, si rendono necessari gli elementi seguenti:

- Definizione di una rete virtuale di Azure in cui distribuire le macchine virtuali del livello di applicazione SAP.
- Viene definita una subnet predefinita nella rete virtuale di Azure, vale a dire la rete in cui vengono distribuite le macchine virtuali.
- La rete virtuale di Azure creata deve avere almeno una subnet VM e una subnet del gateway ExpressRoute. Alle subnet devono essere assegnati gli intervalli di indirizzi IP specificati e illustrati nelle sezioni seguenti.

Di seguito viene illustrata la creazione di una rete virtuale di Azure per le istanze Large di HANA.

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Creazione della rete virtuale di Azure per istanze Large di HANA

>[!Note]
>La rete virtuale di Azure per istanze Large di HANA deve essere creata usando il modello di distribuzione Azure Resource Manager. Il vecchio modello di distribuzione di Azure, comunemente noto come modello di distribuzione classico, non è supportato per le istanze Large di HANA.

La rete virtuale può essere creata mediante il portale di Azure, PowerShell, il modello di Azure o l'interfaccia della riga di comando di Azure (vedere [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Nell'esempio seguente viene illustrata una rete virtuale creata tramite il portale di Azure.

Vengono ora esaminate alcune delle definizioni di una rete virtuale di Azure tramite il portale di Azure e il modo in cui sono correlare agli intervalli di indirizzi IP elencati di seguito. Per **spazio indirizzi** si intende lo spazio di indirizzi che la rete virtuale di Azure può usare, nonché l'intervallo di indirizzi che verrà usato dalla rete virtuale per la propagazione della route BGP. Lo **Spazio indirizzi** viene visualizzato qui:

![Spazio indirizzi della rete virtuale di Azure visualizzato nel portale di Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

In questo caso, con 10.16.0.0/16 la rete virtuale di Azure ha a disposizione un intervallo di indirizzi IP piuttosto ampio. Ciò significa che tutti gli intervalli di indirizzi IP delle subnet successive all'interno di questa rete virtuale possono avere i relativi intervalli all'interno di tale spazio di indirizzi. In genere non è consigliabile specificare un intervallo di indirizzi così ampio per una singola rete virtuale in Azure. Si passi ora alle subnet definite nella rete virtuale di Azure:

![Subnet di rete virtuale di Azure e relativi intervalli di indirizzi IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Viene esaminata una rete virtuale con una prima subnet della macchina virtuale denominata "default" e una subnet denominata "GatewaySubnet".
Nella sezione seguente, l'intervallo di indirizzi IP della subnet denominata "default" nella figura è indicato come **intervallo di indirizzi IP della subnet della macchina virtuale di Azure**. Nelle sezioni seguenti l'intervallo di indirizzi IP della subnet del gateway è indicato come **intervallo di indirizzi IP della subnet del gateway di rete virtuale**. 

Nel caso illustrato dalle due figure precedenti lo **spazio di indirizzi della rete virtuale** comprende sia l'**intervallo di indirizzi IP della subnet VM di Azure** che l'**intervallo di indirizzi IP della subnet del gateway di rete virtuale**. 

In altri casi in cui è necessario conservare o indicare in modo specifico gli intervalli di indirizzi IP, è possibile limitare lo **spazio di indirizzi della rete virtuale** a intervalli specifici usati da ogni subnet. In tal caso è possibile definire lo **spazio di indirizzi della rete virtuale** come più intervalli specifici, come illustrato di seguito:

![Spazio di indirizzi della rete virtuale di Azure con due spazi](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

In questo caso per lo **spazio di indirizzi della rete virtuale** sono stati definiti due spazi, identici agli intervalli di indirizzi IP definiti per l'**intervallo di indirizzi IP della subnet della macchina virtuale di Azure** e per l'**intervallo di indirizzi IP della subnet del gateway di rete virtuale**.

È possibile usare qualsiasi standard di denominazione per le subnet del tenant, vale a dire le subnet VM. È consentita, tuttavia, **una sola subnet del gateway per ogni rete virtuale** che si connette al circuito ExpressRoute di SAP HANA in Azure (istanze Large). La **subnet del gateway deve sempre chiamarsi "GatewaySubnet"** per assicurare il corretto posizionamento del gateway ExpressRoute.

> [!WARNING] 
> È fondamentale che la subnet del gateway sia sempre denominata "GatewaySubnet".

È possibile usare più subnet VM, anche con intervalli di indirizzi non contigui. Come indicato in precedenza, tuttavia, gli intervalli di indirizzi devono essere inclusi nello **spazio di indirizzi della rete virtuale**, in forma aggregata o come elenco degli intervalli di indirizzi esatti delle subnet VM e della subnet del gateway.

Per riepilogare gli aspetti più importanti relativi a una rete virtuale di Azure che si connette a istanze Large di HANA:

- È necessario inviare a Microsoft lo **spazio di indirizzi della rete virtuale** quando si esegue una distribuzione iniziale di istanze Large di HANA. 
- Lo **spazio di indirizzi della rete virtuale** può essere un intervallo più ampio che comprende sia gli intervalli di indirizzi IP della subnet VM di Azure che l'intervallo di indirizzi IP della subnet del gateway di rete virtuale.
- In alternativa è possibile inviare come **spazio di indirizzi della rete virtuale** più intervalli di indirizzi IP che comprendono sia gli intervalli di indirizzi IP della subnet VM che l'intervallo di indirizzi IP della subnet del gateway di rete virtuale.
- Lo **spazio di indirizzi della rete virtuale** definito viene usato per la propagazione di route BGP.
- La subnet del gateway deve essere denominata "**GatewaySubnet**".
- Lo **spazio di indirizzi della rete virtuale** viene usato come filtro sul lato dell'istanza Large di HANA per consentire o negare il traffico da Azure alle unità di istanze Large di HANA. Se le informazioni sul routing BGP della rete virtuale di Azure e gli intervalli di indirizzi IP configurati per il filtro sul lato dell'istanza Large di HANA non corrispondono, si verificano problemi di connettività.
- Alcuni dettagli sulla subnet del gateway vengono illustrati più avanti, nella sezione "Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA".



## <a name="different-ip-address-ranges-to-be-defined"></a>Intervalli di indirizzi IP diversi da definire 

Nelle sezioni precedenti sono già stati illustrati alcuni degli intervalli di indirizzi IP necessari per distribuire istanze Large di HANA. Esistono tuttavia altri intervalli di indirizzi IP, ancora più importanti. Più in dettaglio, prima di inviare una richiesta per la distribuzione iniziale è necessario definire gli indirizzi IP seguenti, anche se non tutti devono essere inviati a Microsoft:

- **Spazio di indirizzi della rete virtuale:** come accennato in precedenza, si tratta dell'intervallo (o degli intervalli) di indirizzi IP assegnato/i o da assegnare al parametro dello spazio di indirizzi nelle reti virtuali di Azure che si connettono all'ambiente delle istanze Large di SAP HANA. Per questo parametro dello spazio di indirizzi è consigliabile usare un valore multiriga costituito dagli intervalli di indirizzi IP della subnet della macchina virtuale di Azure e dall'intervallo di indirizzi IP della subnet del gateway di rete virtuale, come illustrato nelle figure precedenti. Questo intervallo NON deve sovrapporsi con l'intervallo di indirizzi locale, gli intervalli di indirizzi ER-P2P e gli intervalli del pool di indirizzi IP del server. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire uno o più intervalli di indirizzi IP che non siano già usati all'interno della rete. Esempio: se la subnet della rete virtuale di Azure (vedere sopra) è 10.0.1.0/24 e la subnet del gateway di Azure (vedere sotto) è 10.0.2.0/28, lo spazio di indirizzi della rete virtuale di Azure consigliato sarà di due righe, 10.0.1.0/24 e 10.0.2.0/28. Anche se è possibile aggregare i valori dello spazio di indirizzi, è consigliabile fare in modo che corrispondano agli intervalli di indirizzi della subnet, per evitare che gli intervalli di indirizzi IP vengano inavvertitamente riusati in futuro in spazi di indirizzi più grandi in altri punti della rete. **Lo spazio di indirizzi della rete virtuale è un intervallo di indirizzi IP che deve essere inviato a Microsoft quando si richiede una distribuzione iniziale.**

- **Intervallo di indirizzi IP della subnet della macchina virtuale di Azure:** come accennato in precedenza, questo è l'intervallo di indirizzi IP assegnato o da assegnare al parametro della subnet di rete virtuale di Azure nella rete virtuale di Azure che si connette all'ambiente delle istanze Large di SAP HANA. Questo intervallo di indirizzi IP viene usato per assegnare indirizzi IP alle macchine virtuali di Azure. Gli indirizzi IP esterni a questo intervallo potranno connettersi ai server delle istanze Large di SAP HANA. Se necessario, è possibile usare più subnet VM di Azure. È consigliabile usare un blocco CIDR da /24 di Microsoft per ogni subnet VM di Azure. Questo intervallo di indirizzi deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale di Azure. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire un intervallo di indirizzi IP che non sia già in uso all'interno della rete.

- **Intervallo di indirizzi IP della subnet del gateway di rete virtuale:** di seguito sono riportate le dimensioni consigliate a seconda delle funzionalità che si intende usare.
   - Gateway ExpressRoute con prestazioni extra: blocco indirizzi da /26, necessari per gli SKU di "Classe di tipo II"
   - Coesistenza con VPN ed ExpressRoute tramite un gateway ExpressRoute ad alte prestazioni o più piccolo: blocco indirizzi da /27.
   - Tutti gli altri casi: blocco indirizzi da /28. Questo intervallo di indirizzi deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale e deve essere incluso nei valori usati nello spazio di indirizzi della rete virtuale di Azure da inviare a Microsoft. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire un intervallo di indirizzi IP che non sia già in uso all'interno della rete. 

- **Intervallo di indirizzi per la connettività ER-P2P:** si tratta dell'intervallo di indirizzi IP per la connessione ExpressRoute (ER) peer-to-peer (P2P) all'istanza Large di SAP HANA. Deve essere un intervallo di indirizzi IP CIDR da /29. Questo intervallo NON deve sovrapporsi con l'intervallo di indirizzi IP locale o altri intervalli di indirizzi IP di Azure. Viene usato per configurare la connettività ER dal gateway ExpressRoute della rete virtuale di Azure ai server di istanze Large di SAP HANA. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire un intervallo di indirizzi IP che non sia già in uso all'interno della rete. **Questo intervallo di indirizzi IP deve essere inviato a Microsoft quando si richiede una distribuzione iniziale**
  
- **Intervallo di indirizzi del pool di indirizzi IP del server:** questo intervallo di indirizzi IP viene usato per assegnare singoli indirizzi IP ai server delle istanze Large di HANA. Le dimensioni della subnet consigliate sono un blocco CIDR da /24, ma se necessario posso essere inferiori, fino a un minimo di 64 indirizzi IP. In questo intervallo i primi 30 indirizzi IP sono riservati per l'uso da parte di Microsoft. Non dimenticare di tenerne conto nella scelta delle dimensioni dell'intervallo. Questo intervallo NON deve sovrapporsi con l'indirizzo IP locale o altri indirizzi IP di Azure. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire un intervallo di indirizzi IP che non siano attualmente usati all'interno della rete. Un blocco CIDR univoco /24 (scelta consigliata) da usare per l'assegnazione di indirizzi IP specifici necessari per SAP HANA in Azure (istanze di grandi dimensioni). **Questo intervallo di indirizzi IP deve essere inviato a Microsoft quando si richiede una distribuzione iniziale**
 
Anche se è necessario definire e pianificare gli intervalli di indirizzi IP appena visti, non tutti devono essere trasmessi a Microsoft. Per riepilogare, di seguito sono riportati gli intervalli di indirizzi IP da trasmettere a Microsoft:

- Spazi di indirizzi della rete virtuale di Azure
- Intervallo di indirizzi per la connettività ER-P2P
- Intervallo di indirizzi del pool di indirizzi IP del server

Per aggiungere altre reti virtuali aggiuntive che devono connettersi a istanze Large di HANA, è necessario inviare a Microsoft il nuovo spazio di indirizzi della rete virtuale di Azure da aggiungere. 

Di seguito è riportato un esempio dei vari intervalli, con alcuni intervalli di esempio che è necessario configurare e inviare a Microsoft. Come si può notare, il valore relativo allo spazio di indirizzi della rete virtuale di Azure non è aggregato nel primo esempio, ma è definito dagli intervalli del primo intervallo di indirizzi IP della subnet della rete virtuale di Azure e dall'intervallo di indirizzi IP della subnet del gateway di rete virtuale. Per usare più subnet VM all'interno della rete virtuale di Azure, è necessario configurare e inviare gli intervalli di indirizzi IP aggiuntivi delle altre subnet VM come parte dello spazio di indirizzi della rete virtuale di Azure.

![Intervalli di indirizzi IP necessari nella distribuzione minima di SAP HANA in Azure (istanze Large)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

È anche possibile aggregare i dati inviati a Microsoft. In questo caso, solo lo spazio di indirizzi della rete virtuale di Azure include uno spazio. Usando gli intervalli di indirizzi IP dell'esempio precedente, lo spazio di indirizzi IP aggregato della rete virtuale risulterebbe come illustrato di seguito:

![Seconda possibilità di intervalli di indirizzi IP necessari nella distribuzione minima di SAP HANA in Azure (istanze Large)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Come si può notare, invece di due intervalli più piccoli che definiscono lo spazio di indirizzi della rete virtuale di Azure si ottiene un intervallo più ampio che include 4096 indirizzi IP. Una definizione così ampia dello spazio di indirizzi lascia inutilizzati alcuni intervalli piuttosto ampi. Dato che i valori dello spazio di indirizzi della rete virtuale vengono usati per la propagazione di route BGP, l'uso degli intervalli inutilizzati in locale o in un altro punto della rete può causare problemi di routing. È quindi consigliabile mantenere lo spazio di indirizzi strettamente allineato con l'effettivo spazio di indirizzi della subnet usato. Se necessario, è sempre possibile aggiungere nuovi valori dello spazio di indirizzi in un secondo momento, senza incorrere in tempi di inattività della rete virtuale.
 
> [!IMPORTANT] 
> L'intervallo di indirizzi IP ER-P2P, il pool di indirizzi IP del server e lo spazio di indirizzi di rete virtuale di Azure **NON** si devono sovrapporre tra loro o con qualsiasi altro intervallo usato in altri punti della rete. Ognuno di essi deve essere discreto e, come mostrano le figure sopra riportate, non deve essere una subnet di qualsiasi altro intervallo. In caso di sovrapposizioni tra gli intervalli, la rete virtuale di Azure potrebbe non riuscire a connettersi al circuito ExpressRoute.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Passaggi successivi dopo la definizione degli intervalli di indirizzi
Dopo aver definito gli intervalli di indirizzi IP, devono succedersi le attività seguenti:

1. Inviare gli intervalli di indirizzi IP per lo spazio di indirizzi della rete virtuale di Azure, la connettività ER-P2P e l'intervallo di indirizzi del pool di indirizzi IP del server, insieme agli altri dati elencati all'inizio del documento. A questo punto è anche possibile iniziare a creare la rete virtuale e le subnet delle macchine virtuali. 
2. Microsoft crea un circuito ExpressRoute tra la sottoscrizione di Azure e il timbro dell'istanza Large di HANA.
3. Microsoft crea una rete tenant sul timbro dell'istanza Large.
4. Microsoft configura la rete nell'infrastruttura di SAP HANA in Azure (istanze Large) per accettare gli indirizzi IP dallo spazio di indirizzi della rete virtuale di Azure che comunica con le istanze Large di HANA.
5. In base allo SKU di SAP HANA in Azure (istanze Large) acquistato, Microsoft assegna un'unità di calcolo in una rete tenant, alloca e monta la risorsa di archiviazione e installa il sistema operativo (SUSE o RedHat Linux). Gli indirizzi IP per tali unità vengono esclusi dall'intervallo di indirizzi del pool di indirizzi del server inviato a Microsoft.

Al termine del processo di distribuzione, Microsoft fornisce i dati seguenti:
- Informazioni necessarie per connettere le reti virtuali di Azure al circuito ExpressRoute che connette le reti virtuali di Azure alle istanze Large di HANA:
     - Chiave/i di autorizzazione
     - PeerID ExpressRoute
- Dati per accedere a istanze Large di HANA dopo aver configurato il circuito ExpressRoute e la rete virtuale di Azure.

La sequenza di connessione di istanze Large di HANA è disponibile anche nel documento [End to End Setup for SAP HANA Large Instances](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) (Configurazione end-to-end di istanze Large di SAP HANA). Molti dei passaggi seguenti sono illustrati nella distribuzione di esempio di questo documento. 

**Passaggi successivi**

- Consultare [Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA](hana-connect-vnet-express-route.md).