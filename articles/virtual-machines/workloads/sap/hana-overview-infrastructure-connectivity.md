---
title: Infrastruttura e connettività a SAP HANA in Azure (istanze grandi) | Microsoft Docs
description: Configurare l'infrastruttura di connettività necessaria per l'uso di SAP HANA in Azure (istanze grandi).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d94e491d12ac43a4d85a638c79bcd3b24a4bc0ef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure 

Alcune definizioni prima di leggere questa guida. In [Panoramica e architettura di SAP HANA (istanze Large) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vengono illustrate le due classi diverse di istanze Large di Hana con:

- S72, S72m, S144, S144m, S192 e S192m, definiti SKU di "Classe di tipo I".
- S384, S384m, S384xm, S576, S768 e S960, definiti SKU di "Classe di tipo II".

In tutta la documentazione relativa alle istanze Large di HANA verranno usati identificatori di classe per fare riferimento a diverse funzionalità e requisiti in base agli SKU di istanze Large di HANA.

Altre definizioni di uso frequente includono:
- **Modulo per istanze di grandi dimensioni:** uno stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze SAP HANA in Azure.
- **SAP HANA in Azure (istanze di grandi dimensioni):** nome ufficiale dell'offerta in Azure per l'esecuzione di istanze HANA in un ambiente hardware con certificazione SAP HANA TDI e distribuito in moduli per istanze grandi dimensioni in diverse aree di Azure. Il termine **istanze Large di HANA** correlato è la forma breve di SAP HANA in Azure (istanze Large) ed è ampiamente usato in questa guida sulla distribuzione tecnica.
 

Dopo aver finalizzato l'acquisto di SAP HANA in Azure (istanze Large) con il team Microsoft dedicato agli account aziendali, è necessario specificare le informazioni seguenti per distribuire le unità di istanze Large di HANA:

- Nome del cliente
- Informazioni di contatto aziendali (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto tecniche (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto di rete tecniche (inclusi indirizzo e-mail e numero di telefono)
- Area di distribuzione di Azure: Stati Uniti occidentali, Stati Uniti orientali, Australia orientale, Australia sud-orientale, Europa occidentale e Europa settentrionale a partire da luglio 2017
- Confermare l'SKU (configurazione) di SAP HANA in Azure (istanze grandi)
- Come già illustrato in dettaglio nel documento di panoramica e architettura per le istanze Large di HANA, per ogni area di Azure in cui viene distribuito è necessario specificare:
    - Intervallo di indirizzi IP da /29 per le connessioni ER-P2P che connettono reti virtuali di Azure a istanze Large di HANA
    - Blocco CIDR da /24 usato per il pool di indirizzi IP per il server di istanze Large di HANA
- Valori dell'intervallo di indirizzi IP usato nell'attributo dello spazio di indirizzi della rete virtuale di ogni rete virtuale che si connetterà alle istanze Large di HANA
- Dati per ogni sistema di istanze HANA di grandi dimensioni:
  - Nome host da usare, preferibilmente con un nome di dominio completo
  - Indirizzo IP da usare per l'unità di istanze Large di HANA non compresa nell'intervallo del pool di indirizzi IP del server, tenendo presente che i primi 30 indirizzi IP nell'intervallo del pool di indirizzi IP del server sono riservati per uso interno nelle istanze Large di HANA
  - Nome SID di SAP HANA per l'istanza di SAP HANA (obbligatorio per creare i volumi disco richiesti legati a SAP HANA). Il nome SID di HANA è necessario per la creazione di autorizzazioni per <sidadm> nei volumi NFS, che dovranno essere associate all'unità di istanze Large di HANA. Viene usato anche come uno dei componenti del nome dei volumi disco che devono essere montati. Se si vuole eseguire più di un'istanza HANA sull'unità, è necessario elencare più nomi SID di HANA, ognuno dei quali ottiene un set separato di volumi assegnati.
  - L'ID gruppo dell'utente hana-sidadm nel sistema operativo Linux OS è necessario per creare i volumi disco correlati a SAP HANA. L'installazione di SAP HANA crea in genere il gruppo sapsys con l'ID gruppo 1001 e l'utente hana-sidadm fa parte di tale gruppo
  - L'ID utente dell'utente hana-sidadm nel sistema operativo Linux OS è necessario per creare i volumi disco correlati a SAP HANA. Se sull'unità vengono eseguite più istanze HANA, è necessario elencare tutti gli utenti <sid>adm 
- ID sottoscrizione di Azure per la sottoscrizione di Azure a cui SAP HANA in Azure (istanze Large) verrà connesso direttamente. L'ID sottoscrizione fa riferimento alla sottoscrizione di Azure che verrà addebitata con le unità di istanze Large di HANA.

Dopo aver specificato tali informazioni, Microsoft effettua il provisioning di SAP HANA in Azure (istanze Large) e restituisce le informazioni necessarie per collegare le reti virtuali di Azure a istanze Large di HANA e per accedere alle relative unità.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Connessione di macchine virtuali di Azure a istanze Large di HANA

Come già illustrato in [Panoramica e architettura di SAP HANA in Azure (istanze Large)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), la distribuzione minima di istanze Large di HANA con il livello dell'applicazione SAP in Azure ha un aspetto simile al seguente:

![Rete virtuale di Azure connessa a SAP HANA in Azure (istanze di grandi dimensioni) e in locale](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Esaminando più da vicino la rete virtuale di Azure, si rendono necessari gli elementi seguenti:

- Definizione di una rete virtuale di Azure in cui distribuire le macchine virtuali del livello di applicazione SAP.
- Viene definita una subnet predefinita nella rete virtuale di Azure, vale a dire la rete in cui vengono distribuite le macchine virtuali.
- La rete virtuale di Azure creata deve avere almeno una subnet VM e una subnet del gateway ExpressRoute. Alle subnet devono essere assegnati gli intervalli di indirizzi IP specificati e illustrati nelle sezioni seguenti.

Di seguito viene illustrata la creazione di una rete virtuale di Azure per le istanze Large di HANA.

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Creazione della rete virtuale di Azure per istanze Large di HANA

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



### <a name="different-ip-address-ranges-to-be-defined"></a>Intervalli di indirizzi IP diversi da definire 

Nelle sezioni precedenti sono già stati illustrati alcuni degli intervalli di indirizzi IP necessari per distribuire istanze Large di HANA. Esistono tuttavia altri intervalli di indirizzi IP, ancora più importanti. Più in dettaglio, prima di inviare una richiesta per la distribuzione iniziale è necessario definire gli indirizzi IP seguenti, anche se non tutti devono essere inviati a Microsoft:

- **Spazio di indirizzi della rete virtuale:** come accennato in precedenza, si tratta dell'intervallo (o degli intervalli) di indirizzi IP assegnato/i o da assegnare al parametro dello spazio di indirizzi nelle reti virtuali di Azure che si connettono all'ambiente delle istanze Large di SAP HANA. Per questo parametro dello spazio di indirizzi è consigliabile usare un valore multiriga costituito dagli intervalli di indirizzi IP della subnet della macchina virtuale di Azure e dall'intervallo di indirizzi IP della subnet del gateway di rete virtuale, come illustrato nelle figure precedenti. Questo intervallo NON deve sovrapporsi con gli intervalli di indirizzi ER-P2P o del pool di indirizzi IP del server. Per ottenere questo intervallo di indirizzi IP, il team o il provider del servizio di rete aziendale deve fornire uno o più intervalli di indirizzi IP che non siano già usati all'interno della rete. Esempio: se la subnet della rete virtuale di Azure (vedere sopra) è 10.0.1.0/24 e la subnet del gateway di Azure (vedere sotto) è 10.0.2.0/28, lo spazio di indirizzi della rete virtuale di Azure consigliato sarà di due righe, 10.0.1.0/24 e 10.0.2.0/28. Anche se è possibile aggregare i valori dello spazio di indirizzi, è consigliabile fare in modo che corrispondano agli intervalli di indirizzi della subnet, per evitare che gli intervalli di indirizzi IP vengano inavvertitamente riusati in futuro in spazi di indirizzi più grandi in altri punti della rete. **Lo spazio di indirizzi della rete virtuale è un intervallo di indirizzi IP che deve essere inviato a Microsoft quando si richiede una distribuzione iniziale.**

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

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Passaggi successivi dopo la definizione degli intervalli di indirizzi
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


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA

Dopo aver definito tutti gli intervalli di indirizzi IP e aver ottenuto i dati da Microsoft, è possibile avviare la connessione della rete virtuale creata in precedenza alle istanze Large di HANA. Una volta creata la rete virtuale di Azure, è necessario creare un gateway ExpressRoute sulla rete per collegarla al circuito ExpressRoute che collega il tenant del cliente allo stamp di istanze di grandi dimensioni.

> [!NOTE] 
> Questo passaggio può richiedere fino a 30 minuti; il nuovo gateway viene creato nella sottoscrizione Azure specificata e viene quindi connesso alla rete virtuale di Azure indicata.

Se esiste già un gateway, controllare se sia o meno un gateway ExpressRoute. Se non si tratta di un gateway ExpressRoute, deve essere eliminato e ricreato come tale. Se è già presente un gateway ExpressRoute, poiché la rete virtuale di Azure è già connessa al circuito ExpressRoute per la connettività locale, passare alla sezione Collegamento di reti virtuali di seguito.

- Usare il (nuovo) [portale di Azure](https://portal.azure.com/) o PowerShell per creare un gateway VPN ExpressRoute connesso alla rete virtuale.
  - Se si usa il portale di Azure, aggiungere un nuovo **Gateway di rete virtuale** e selezionare **ExpressRoute** come tipo di gateway.
  - Se, invece, si usa PowerShell, scaricare e usare innanzitutto la versione più recente di [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) per garantire un'esperienza ottimale. I seguenti comandi creano un gateway ExpressRoute. Il testo preceduto dal simbolo _$_ è costituito da variabili definite dall'utente, da aggiornare in base alle informazioni personali specifiche.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

In questo esempio è stato usato lo SKU del gateway HighPerformance. Le opzioni disponibili sono HighPerformance o UltraPerformance e sono gli unici SKU del gateway supportati per SAP HANA in Azure (istanze Large).

> [!IMPORTANT]
> Per le istanze Large di HANA con i tipi di SKU S384, S384m, S384xm, S576, S768 e S960 (SKU di Classe di tipo II), è obbligatorio usare lo SKU del gateway UltraPerformance.

### <a name="linking-vnets"></a>Collegamento delle reti virtuali

Ora che la rete virtuale di Azure dispone di un gateway ExpressRoute, usare le informazioni di autorizzazione ottenute da Microsoft per connettere il gateway ExpressRoute al circuito ExpressRoute di SAP HANA in Azure (istanze grandi) creato per questa connettività. Questa operazione può essere eseguita usando il portale di Azure o PowerShell. Sebbene il portale sia la scelta consigliata, di seguito sono riportate le istruzioni per usare PowerShell. 

- Eseguire questi comandi per ogni gateway di rete virtuale usando un AuthGUID diverso per ogni connessione. Le prime due voci illustrate nello script seguente provengono dalle informazioni ottenute da Microsoft. Inoltre, AuthGUID è specifico per ogni rete virtuale e il relativo gateway. Per aggiungere un'altra rete virtuale di Azure, quindi, è necessario ottenere un altro AuthID per il circuito ExpressRoute che connette istanze Large di HANA ad Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Se si vuole collegare il gateway a più circuiti ExpressRoute associati alla sottoscrizione, è possibile che sia necessario eseguire più volte questo passaggio. Ad esempio, sarà probabilmente necessario connettere lo stesso gateway di rete virtuale al circuito ExpressRoute che si connette alla rete virtuale locale.

## <a name="adding-more-ip-addresses-or-subnets"></a>Aggiunta di più indirizzi IP o subnet

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando per aggiungere più indirizzi IP o subnet.

In questo caso, è consigliabile aggiungere il nuovo intervallo di indirizzi IP allo spazio di indirizzi della rete virtuale come nuovo intervallo anziché generare un nuovo intervallo aggregato. In entrambi i casi è necessario inviare la modifica a Microsoft per consentire la connettività in uscita dal nuovo intervallo di indirizzi IP alle unità di istanze Large di HANA nel client. È possibile aprire una richiesta di supporto tecnico di Azure per ottenere il nuovo spazio di indirizzi della rete virtuale aggiunto. Dopo aver ricevuto la conferma, eseguire i passaggi successivi.

Per creare una subnet aggiuntiva dal portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network); per creare da PowerShell, vedere [Creare una rete virtuale usando PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Aggiunta di reti virtuali

Dopo aver collegato inizialmente una o più reti virtuali Azure, si potrebbe voler aggiungerne di ulteriori in grado di accedere a SAP HANA in Azure (istanze grandi). In primo luogo, inviare una richiesta di supporto di Azure includendo le informazioni specifiche sulla distribuzione di Azure in questione e gli intervalli di spazi di indirizzi IP per lo spazio di indirizzi della rete virtuale di Azure. SAP HANA nella gestione del servizio Azure indica a quel punto le informazioni necessarie per connettere le reti virtuali aggiuntive ed ExpressRoute. Per ogni rete virtuale è necessaria una chiave di autorizzazione univoca per connettersi al circuito ExpressRoute e alle istanze Large di HANA.

Procedura per aggiungere una nuova rete virtuale di Azure:

1. Completare il primo passaggio del processo introduttivo: vedere la sezione _Creazione di una rete virtuale di Azure_.
2. Completare il secondo passaggio del processo introduttivo: vedere la sezione _Creazione di una subnet del gateway_.
3. Per connettere le reti virtuali aggiuntive al circuito ExpressRoute per istanze Large di HANA, aprire una richiesta di supporto di Azure con informazioni sulla nuova rete virtuale e richiedere una nuova chiave di autorizzazione.
4. Al termine del processo di autorizzazione, usare le informazioni di autorizzazioni ottenute da Microsoft per completare il terzo passaggio del processo introduttivo: vedere la sezione _Collegamento delle reti virtuali_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento della larghezza di banda del circuito ExpressRoute

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene consigliato di aumentare la larghezza di banda del circuito ExpressRoute di SAP HANA in Azure (istanze grandi), creare una richiesta di supporto di Azure. (è possibile richiedere un aumento per una larghezza di banda a circuito singolo fino a un massimo di 10 Gbps). Si riceverà la notifica al termine dell'operazione; non sono necessarie azioni aggiuntive per abilitare la velocità più elevata in Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Aggiunta di un circuito ExpressRoute aggiuntivo

Consultarsi con il team di gestione dei servizi SAP HANA in Azure. Se viene comunicata la necessità di un circuito ExpressRoute aggiuntivo, inviare una richiesta di supporto di Azure per creare un nuovo circuito ExpressRoute (e per ottenere le informazioni di autorizzazione necessarie per la connessione). Lo spazio degli indirizzi che verrà usato nelle reti virtuali deve essere definito prima di inviare la richiesta, in modo da poter ricevere l'autorizzazione dal team di gestione dei servizi di SAP HANA in Azure.

Una volta creato il nuovo circuito e completata la configurazione da parte del team di gestione dei servizi di SAP HANA in Azure, si riceverà una notifica contenente le informazioni necessarie per continuare. Seguire i passaggi descritti in precedenza per creare e collegare la nuova rete virtuale a questo circuito aggiuntivo. Non è possibile connettere reti virtuali di Azure a questo circuito aggiuntivo se sono già connesse a un altro circuito ExpressRoute per SAP HANA (istanze Large) nella stessa area di Azure.

## <a name="deleting-a-subnet"></a>Eliminazione di una subnet

Per rimuovere una subnet di rete virtuale, è possibile usare il portale di Azure, PowerShell o l'interfaccia della riga di comando. Se l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure è un intervallo aggregato, non è necessario contattare Microsoft. È tuttavia necessario tenere presente che la rete virtuale sta ancora propagando lo spazio di indirizzi della route BGP che include la subnet eliminata. Se l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure è stato definito come più intervalli di indirizzi IP, uno dei quali era assegnato alla subnet eliminata, è necessario eliminare quest'ultimo dallo spazio di indirizzi della rete virtuale e quindi indicare a SAP HANA in gestione dei servizi di Azure che va rimosso dagli intervalli con cui SAP HANA in Azure (istanze Large) può comunicare.

Per eliminare una subnet, vedere [Eliminare una subnet](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) per altre informazioni.

## <a name="deleting-a-vnet"></a>Eliminazione di una rete virtuale

Per eliminare una rete virtuale, vedere [Delete a virtual network](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network) (Eliminare una rete virtuale). SAP HANA in gestione dei servizi di Azure consente di rimuovere le autorizzazioni esistenti nel circuito ExpressRoute per SAP HANA in Azure (istanze Large) e di rimuovere l'intervallo di indirizzi IP della rete virtuale di Azure o lo spazio di indirizzi della rete virtuale di Azure per la comunicazione con istanze Large di HANA.

Dopo aver rimosso la rete virtuale, aprire una richiesta di supporto di Azure per specificare gli intervalli di spazi di indirizzi IP da rimuovere.

Per garantire una rimozione completa, rimuovere gli elementi seguenti:

- Connessione ExpressRoute, gateway di rete virtuale, indirizzo IP pubblico del gateway di rete virtuale e rete virtuale

## <a name="deleting-an-expressroute-circuit"></a>Eliminazione di un circuito ExpressRoute

Per rimuovere un circuito ExpressRoute aggiuntivo per SAP HANA in Azure (istanze Large), aprire una richiesta di supporto di Azure con SAP HANA in Azure Service Management e richiedere l'eliminazione del circuito. È possibile eliminare o mantenere la rete virtuale nella sottoscrizione di Azure in base alle necessità. Tuttavia, è necessario eliminare la connessione tra il circuito ExpressRoute per istanze Large di HANA e il gateway di rete virtuale collegato.

Se si desidera rimuovere una rete virtuale, seguire le linee guida indicate in Eliminazione di una rete virtuale nella sezione precedente.


