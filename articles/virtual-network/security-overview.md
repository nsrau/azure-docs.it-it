---
title: Panoramica della sicurezza di rete di Azure | Microsoft Docs
description: Informazioni sulle opzioni di sicurezza per il controllo del flusso del traffico di rete tra le risorse di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 63a313d9035422207a1ce56f0da8b388e2747685
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="network-security"></a>Sicurezza di rete

È possibile limitare il traffico di rete verso le risorse in una rete virtuale usando un gruppo di sicurezza di rete. Un gruppo di sicurezza di rete contiene un elenco di regole di sicurezza che consentono o impediscono il traffico di rete in ingresso o in uscita in base all'indirizzo IP di origine o di destinazione, alla porta e al protocollo. 

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

A ogni interfaccia di rete è associato un gruppo di sicurezza di rete oppure nessuno. Ogni interfaccia di rete si trova in una subnet di [rete virtuale](virtual-networks-overview.md). Anche a una subnet può essere associato un gruppo di sicurezza di rete oppure nessuno. 

Se applicate a una subnet, le regole di sicurezza si applicano a tutte le risorse nella subnet. In aggiunta alle interfacce di rete, nella subnet possono essere distribuite istanze di altri servizi di Azure, ad esempio HDInsight, set di scalabilità di macchine virtuali e ambienti del servizio app.

Quando un gruppo di sicurezza di rete è associato sia a un'interfaccia di rete che alla subnet in cui si trova tale interfaccia, i gruppi di sicurezza di rete vengono applicati come segue.

- **Traffico in ingresso**: il gruppo di sicurezza di rete associato alla subnet in cui si trova l'interfaccia di rete viene valutato per primo. Tutto il traffico consentito tramite il gruppo di sicurezza di rete associato alla subnet viene quindi valutato in base al gruppo di sicurezza di rete associato all'interfaccia di rete. Può ad esempio essere necessario l'accesso in ingresso da Internet a una macchina virtuale sulla porta 80. Se si associa un gruppo di sicurezza di rete sia all'interfaccia di rete che alla subnet in cui si trova tale interfaccia, il gruppo di sicurezza di rete associato alla subnet e all'interfaccia di rete deve consentire la porta 80. Se la porta 80 è stata consentita solo tramite il gruppo di sicurezza di rete associato alla subnet o all'interfaccia di rete in cui si trova la subnet, la comunicazione non riesce a causa delle regole di sicurezza predefinite. Per informazioni dettagliate, vedere [Regole di sicurezza predefinite](#default-security-rules). Se è stato applicato un gruppo di sicurezza di rete solo alla subnet o all'interfaccia di rete e il gruppo contiene una regola che consente il traffico in ingresso sulla porta 80, ad esempio, la comunicazione ha esito positivo. 
- **Traffico in uscita**: il gruppo di sicurezza di rete associato all'interfaccia di rete viene valutato per primo. Tutto il traffico consentito tramite il gruppo di sicurezza di rete associato all'interfaccia di rete viene quindi valutato in base al gruppo di sicurezza di rete associato alla subnet.

È possibile che non sempre si conosca quando i gruppi di sicurezza di rete sono applicati sia a un'interfaccia di rete che a una subnet. Le regole di aggregazione applicate a un'interfaccia di rete possono essere verificate facilmente visualizzando le [regole di sicurezza effettive](virtual-network-manage-nsg-arm-portal.md) per un'interfaccia di rete. È anche possibile usare la funzionalità di [verifica del flusso IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher per determinare se è consentita la comunicazione da o verso un'interfaccia di rete. Lo strumento indica se la comunicazione è consentita e quale regola di sicurezza di rete consente o impedisce il traffico.
 
> [!NOTE]
> I gruppi di sicurezza di rete sono associati a subnet oppure a macchine virtuali e servizi cloud distribuiti con il modello di distribuzione classica, anziché a interfacce di rete nel modello di distribuzione Resource Manager. Per altre informazioni in proposito, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Lo stesso gruppo di sicurezza di rete può essere applicato a qualsiasi numero desiderato di singole interfacce di rete e subnet.

## <a name="security-rules"></a>Regole di sicurezza

Un gruppo di sicurezza di rete può contenere zero regole o il numero di regole desiderato, entro i [limiti](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) della sottoscrizione di Azure. Ogni regola specifica le proprietà seguenti:

|Proprietà  |Spiegazione  |
|---------|---------|
|Nome|Nome univoco all'interno del gruppo di sicurezza di rete.|
|Priorità | Numero compreso tra 100 e 4096. Le regole vengono elaborate in ordine di priorità. I numeri più bassi vengono elaborati prima di quelli più elevati perché hanno priorità più alta. Quando il traffico corrisponde a una regola, l'elaborazione viene interrotta. Di conseguenza, le regole con priorità più bassa (numeri più elevati) che hanno gli stessi attributi di regole con priorità più elevata non vengono elaborate.|
|Origine o destinazione| Singolo indirizzo IP, blocco CIDR (ad esempio 10.0.0.0/24), tag di servizio o gruppo di sicurezza delle applicazioni oppure uno qualsiasi. Per altre informazioni, vedere [Tag di servizio](#service-tags) e [Gruppi di sicurezza delle applicazioni](#application-security-groups). Specificando un intervallo, un tag di servizio o un gruppo di sicurezza delle applicazioni è possibile creare un minor numero di regole di sicurezza. La possibilità di specificare più intervalli e indirizzi IP singoli in una regola (detta regola di sicurezza ottimizzata) è disponibile in versione di anteprima. Non si possono specificare più tag di servizio o gruppi di applicazioni. È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più indirizzi IP e intervalli di indirizzi IP nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica.|
|Protocol     | TCP, UDP o Qualsiasi, che include TCP, UDP e ICMP. Non è possibile specificare solo ICMP. Se è necessario ICMP, si deve usare Qualsiasi. |
|Direzione| Definisce se la regola si applica al traffico in ingresso o in uscita.|
|Intervallo di porte     |È possibile specificare una singola porta o un intervallo di porte. Ad esempio, è possibile specificare 80 oppure 10000-10005. Specificando intervalli è possibile creare un minor numero di regole di sicurezza. La possibilità di specificare più intervalli di porte e porte singole in una regola (detta regola di sicurezza ottimizzata) è disponibile in versione di anteprima. Prima di usare le regole di sicurezza ottimizzate, vedere le informazioni importanti relative alle [funzionalità in anteprima](#preview-features). È possibile creare regole di sicurezza ottimizzate solo in gruppi di sicurezza di rete creati tramite il modello di distribuzione Resource Manager. Non si possono specificare più porte o intervalli di porte nella stessa regola di sicurezza nei gruppi di sicurezza di rete creati tramite il modello di distribuzione classica.   |
|Azione     | Consentire o impedire.        |

**Considerazioni**

- **IP virtuale del nodo host**: servizi di infrastruttura di base come DHCP, DNS e il monitoraggio dell'integrità vengono forniti tramite gli indirizzi IP host virtualizzati 168.63.129.16 e 169.254.169.254. Questi indirizzi IP pubblici appartengono a Microsoft e sono gli unici indirizzi IP virtualizzati usati in tutte le aree a questo scopo. Questi indirizzi IP vengono mappati all'indirizzo IP fisico del computer server (nodo host) che ospita la VM. Il nodo host svolge la funzione di inoltro DHCP, di resolver ricorsivo DNS e di origine probe per il probe di integrità del bilanciamento del carico e per il probe di integrità del computer. La comunicazione con questi indirizzi IP non è un attacco. Se si blocca il traffico da o verso questi indirizzi IP, una macchina virtuale potrebbe non funzionare correttamente.
- **Licenze (Servizio di gestione delle chiavi)**: le immagini Windows in esecuzione nelle VM devono essere concesse in licenza. Per verificare la concessione della licenza, viene inviata una richiesta ai server host del Servizio di gestione delle chiavi che gestiscono le query di questo tipo. La richiesta viene inviata in uscita tramite la porta 1688.
- **Macchine virtuali in pool con carico bilanciato**: l'intervallo di porte e indirizzi di origine applicato è quello del computer di origine e non quello del servizio di bilanciamento del carico. L'intervallo di porte e indirizzi di destinazione riguarda il computer di destinazione e non il servizio di bilanciamento del carico.
- **Istanze di servizi di Azure**: nelle subnet delle reti virtuali vengono distribuite istanze di diversi servizi di Azure, ad esempio HDInsight, ambienti del servizio app e set di scalabilità di macchine virtuali. Assicurarsi di acquisire familiarità con i requisiti relativi alle porte per ogni servizio prima di applicare un gruppo di sicurezza di rete alla subnet in cui è distribuita la risorsa. Se si bloccano le porte richieste dal servizio, il servizio non funzionerà correttamente. 

Le regole di sicurezza sono regole con stato. Se si specifica una regola di sicurezza in uscita per qualsiasi indirizzo sulla porta 80, ad esempio, non è necessario specificare una regola di sicurezza in ingresso per la risposta al traffico in uscita. È necessario specificare una regola di sicurezza in ingresso solo se la comunicazione viene avviata all'esterno. Questa considerazione si applica anche al contrario. Se il traffico in ingresso è consentito su una porta, non è necessario specificare una regola di sicurezza in uscita per rispondere al traffico sulla porta. Per informazioni sui limiti associati alla creazione di regole di sicurezza, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Regole di sicurezza ottimizzate

Le regole ottimizzate semplificano la definizione della sicurezza per le reti virtuali, perché consentono di definire criteri di sicurezza di rete più estesi e complessi con un minor numero di regole. È possibile combinare più porte, più indirizzi IP espliciti, tag di servizio e gruppi di sicurezza delle applicazioni in un'unica regola di sicurezza facilmente comprensibile. Usare regole ottimizzate nei campi relativi a origine, destinazione e porte di una regola. Quando si crea una regola, è possibile specificare più porte, intervalli CIDR e indirizzi IP specifici. Per semplificare la gestione della definizione delle regole di sicurezza, combinare le regole di sicurezza ottimizzate con tag di servizio o gruppi di sicurezza delle applicazioni. 

Le regole di sicurezza ottimizzate sono disponibili in versione di anteprima. Per informazioni sui limiti associati alla creazione di regole di sicurezza ottimizzate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Le funzionalità in anteprima non offrono lo stesso livello di disponibilità e affidabilità delle funzionalità in versione di disponibilità generale. Le funzionalità sono disponibili solo nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Australia orientale, Australia sud-orientale e Regno Unito meridionale.

## <a name="default-security-rules"></a>Regole di sicurezza predefinite

Se a una subnet o un'interfaccia di rete non è associato un gruppo di sicurezza di rete, tutto il traffico in uscita o in ingresso da o verso la subnet o l'interfaccia di rete è consentito. Non appena viene creato un gruppo di sicurezza di rete, Azure crea nel gruppo le regole predefinite seguenti.

### <a name="inbound"></a>In ingresso

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorità|Sorgente|Porte di origine|Destination|Porte di destinazione|Protocol|Accesso|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Tutti|CONSENTI|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorità|Sorgente|Porte di origine|Destination|Porte di destinazione|Protocol|Accesso|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Tutti|CONSENTI|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorità|Sorgente|Porte di origine|Destination|Porte di destinazione|Protocol|Accesso|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Tutti|NEGA|

### <a name="outbound"></a>In uscita

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorità|Sorgente|Porte di origine| Destination | Porte di destinazione | Protocol | Accesso |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Tutti | CONSENTI |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorità|Sorgente|Porte di origine| Destination | Porte di destinazione | Protocol | Accesso |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Tutti | CONSENTI |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorità|Sorgente|Porte di origine| Destination | Porte di destinazione | Protocol | Accesso |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Tutti | NEGA |

Nelle colonne **Origine** e **Destinazione**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* sono [tag di servizio](#tags), anziché indirizzi IP. Nella colonna del protocollo, **Tutti** include TCP, UDP e ICMP. Quando si crea una regola, è possibile specificare TCP, UDP o tutti i protocolli, ma non ICMP soltanto. Se la regola richiede ICMP, come protocollo è quindi necessario selezionare *Tutti*. Nelle colonne **Origine** e **Destinazione**, *0.0.0.0/0* rappresenta tutti gli indirizzi.
 
Non è possibile rimuovere le regole predefinite, ma è possibile eseguirne l'override creando regole con priorità più alta.

## <a name="service-tags"></a>Tag di servizio

 Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Nella definizione delle regole di sicurezza possono essere usati i tag di servizio seguenti. I nomi variano leggermente a seconda del [modello di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (*Resource Manager*) o *VIRTUAL_NETWORK** (distribuzione classica): questo tag include lo spazio indirizzi della rete virtuale (tutti gli intervalli CIDR definiti per la rete virtuale), tutti gli spazi indirizzi locali connessi e le reti virtuali [con peering](virtual-network-peering-overview.md) o una rete virtuale connessa a un [gateway di rete virtuale](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) o **AZURE_LOADBALANCER** (distribuzione classica): questo tag identifica il servizio di bilanciamento del carico dell'infrastruttura di Azure. Viene convertito in un [indirizzo IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) da cui hanno origine i probe di integrità di Azure. Se non si usa Azure Load Balancer, è possibile eseguire l'override di questa regola.
* **Internet** (Resource Manager) o **INTERNET** (distribuzione classica): questo tag identifica lo spazio indirizzi IP pubblici di Azure. Gli indirizzi inclusi nel tag sono elencati nel documento relativo allo [spazio degli IP pubblici appartenenti ad Azure](https://www.microsoft.com/download/details.aspx?id=41653), che viene aggiornato regolarmente.
* **AzureTrafficManager** (solo Resource Manager): questo tag identifica lo spazio indirizzi IP per il servizio Gestione traffico di Azure.
* **Storage** (solo Resource Manager): questo tag identifica lo spazio indirizzi IP per il servizio Archiviazione di Azure. Se si specifica *Storage* come valore, verrà consentito o impedito il traffico verso il servizio di archiviazione. Se si vuole consentire l'accesso al servizio di archiviazione solo in una determinata [area](https://azure.microsoft.com/regions), è possibile specificare tale area. Se ad esempio si vuole consentire l'accesso ad Archiviazione di Azure solo nell'area Stati Uniti orientali, è possibile specificare *Storage.EastUS* come tag di servizio. Gli altri tag di servizio di area disponibili sono i seguenti: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS e Storage.WestUS2. Il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Archiviazione di Azure, ma non uno specifico account di archiviazione di Azure.
* **Sql** (solo Resource Manager): questo tag identifica i prefissi di indirizzo dei servizi Database SQL di Azure e Azure SQL Data Warehouse. Per questo tag di servizio è possibile specificare solo aree specifiche. Se ad esempio si vuole consentire l'accesso a Database SQL di Azure solo nell'area Stati Uniti orientali, è possibile specificare *Sql.EastUS* come tag di servizio. Non è possibile specificare solo Sql per tutte le aree di Azure. Si devono specificare singolarmente le aree. Gli altri tag di servizio di area disponibili sono Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS e Sql.WestUS2. Il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non uno specifico database SQL di Azure.

> [!WARNING]
> I tag di servizio AzureTrafficManager, Storage e Sql sono disponibili in versione di anteprima. Le funzionalità in anteprima non offrono lo stesso livello di disponibilità e affidabilità delle funzionalità in versione di disponibilità generale. I tag di servizio sono disponibili solo nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Australia orientale, Australia sud-orientale e Regno Unito meridionale.

> [!NOTE]
> Se si implementa un endpoint servizio di rete virtuale per un servizio come Archiviazione di Azure o Database SQL di Azure, Azure aggiunge una route a una subnet di rete virtuale per il servizio. I prefissi di indirizzo per la route sono gli stessi prefissi di indirizzo o intervalli CIDR del tag di servizio corrispondente.

## <a name="application-security-groups"></a>Gruppi di sicurezza delle applicazioni

I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi. Questa funzionalità consente di riusare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti. La piattaforma gestisce la complessità degli indirizzi IP espliciti e di più set di regole, consentendo all'utente di concentrarsi sulla logica di business.

Si può specificare un gruppo di sicurezza delle applicazioni come origine e destinazione in una regola di sicurezza. Dopo aver definito i criteri di sicurezza, è possibile creare le macchine virtuali e assegnare le interfacce di rete nella macchina virtuale a un gruppo di sicurezza delle applicazioni. I criteri vengono applicati in base all'appartenenza a gruppi di sicurezza delle applicazioni di ogni interfaccia di rete in una macchina virtuale. L'esempio seguente illustra come è possibile usare un gruppo di sicurezza delle applicazioni per tutti i server Web nella sottoscrizione:

1. Creare un gruppo di sicurezza delle applicazioni denominato *WebServers*.
2. Creare un gruppo di sicurezza di rete denominato *MyNSG*.
3. Creare una regola di sicurezza in ingresso nel gruppo di sicurezza di rete, specificando il tag di servizio *Internet* come indirizzo di origine e il gruppo di sicurezza delle applicazioni *WebServers* come indirizzo di destinazione, e consentire le porte 80 e 443.
4. Distribuire una macchina virtuale che esegue un'applicazione server Web. Specificare che l'interfaccia di rete nella macchina virtuale è membro del gruppo di sicurezza delle applicazioni *WebServers*. Le porte 80 e 443 saranno quindi consentite per la macchina virtuale, nonché per tutti i successivi server Web creati e definiti come membri del gruppo di sicurezza delle applicazioni *WebServers*. 

Se si creano altre regole, specificando altri gruppi di sicurezza delle applicazioni come destinazione, tali regole non vengono applicate ai server Web nell'esempio precedente. Le regole che specificano un gruppo di sicurezza delle applicazioni vengono applicate solo alle interfacce di rete che sono membri di tale gruppo. I gruppi di sicurezza delle applicazioni, insieme alle regole di sicurezza ottimizzate e ai tag di servizio, consentono di gestire la sicurezza di rete nella sottoscrizione creando un numero minimo di gruppi di sicurezza di rete.
 
Per informazioni sui limiti associati alla creazione di gruppi di sicurezza delle applicazioni e all'inclusione di tali gruppi nelle regole di sicurezza, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

I gruppi di sicurezza delle applicazioni sono disponibili in versione di anteprima. Prima di usare gruppi di sicurezza di rete, è necessario eseguire l'apposita registrazione completando i passaggi da 1 a 5 per [creare un gruppo di sicurezza di rete con gruppi di sicurezza delle applicazioni](create-network-security-group-preview.md#powershell) e vedere le informazioni importanti relative alle [funzionalità in anteprima](#preview-features). Durante l'anteprima, i gruppi di sicurezza delle applicazioni sono limitati all'ambito della rete virtuale. Le reti virtuali di cui è stato eseguito il peering con riferimenti incrociati a gruppi di sicurezza delle applicazioni in un gruppo di sicurezza di rete non sono incluse. 

Le funzionalità in anteprima non offrono lo stesso livello di disponibilità e affidabilità delle funzionalità in versione di disponibilità generale. Prima di usare gruppi di sicurezza delle applicazioni, è necessario eseguire l'apposita registrazione. Le funzionalità sono disponibili solo nell'area Stati Uniti centro-occidentali.

## <a name="next-steps"></a>Passaggi successivi

* Completare l'esercitazione relativa alla [creazione di un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md)
* Completare l'esercitazione relativa alla [creazione di un gruppo di sicurezza di rete con gruppi di sicurezza delle applicazioni](create-network-security-group-preview.md)

