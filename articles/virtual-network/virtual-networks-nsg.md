<properties 
   pageTitle="Che cos'è un gruppo di sicurezza di rete"
   description="Informazioni sui gruppi di sicurezza di rete"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Che cos'è un gruppo di sicurezza di rete

È possibile usare un gruppo di sicurezza di rete per controllare il traffico verso una o più istanze di macchina virtuale (VM) in una rete virtuale. Un gruppo di sicurezza di rete è un oggetto di primo livello associato alla propria sottoscrizione. Contiene regole di controllo di accesso che consentono o negano il traffico verso le istanze di macchina virtuale. Le regole di un gruppo di sicurezza di rete possono essere modificate in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate. Per usare un gruppo di sicurezza di rete, è necessario disporre di una rete virtuale (VNet) associata a un'area (località).

>[AZURE.WARNING]I gruppi di sicurezza di rete non sono compatibili con reti virtuali associate a un gruppo di affinità. Se non si dispone di una rete virtuale di area e si vuole controllare il traffico verso gli endpoint, vedere [Che cos'è un elenco di controllo di accesso di rete (ACL)](../virtual-networks-acl).

È possibile associare un gruppo di sicurezza di rete a una macchina virtuale o a una subnet in una rete virtuale. Se è associato a una macchina virtuale, il gruppo di sicurezza di rete si applica a tutto il traffico inviato e ricevuto dall'istanza della macchina virtuale. Se è associato a una subnet della rete virtuale, si applica a tutto il traffico inviato e ricevuto da TUTTE le istanze di macchina virtuale della subnet. Una macchina virtuale o una subnet può essere associata a un solo gruppo di sicurezza di rete, ognuno dei quali può contenere un massimo di 200 regole. È possibile disporre di 100 gruppi di sicurezza di rete per sottoscrizione.

>[AZURE.NOTE]Gli elenchi di controllo di accesso basati su endpoint e i gruppi di sicurezza di rete non sono supportati nella stessa istanza di macchina virtuale. Se si vuole usare un gruppo di sicurezza di rete ed è già presente un elenco di controllo di accesso basato su endpoint, rimuovere prima l'elenco di controllo di accesso. Per informazioni su come procedere, vedere [Gestione degli elenchi di controllo di accesso (ACL) per gli endpoint tramite PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

## Funzionamento di un gruppo di sicurezza di rete

I gruppi di sicurezza di rete sono diversi dagli elenchi di controllo di accesso basati su endpoint. Gli elenchi di controllo di accesso basati su endpoint funzionano solo sulla porta pubblica esposta tramite l'endpoint di input. Un gruppo di sicurezza di rete funziona su una o più istanze di macchina virtuale e controlla tutto il traffico in ingresso e in uscita nella macchina virtuale.

Un gruppo di sicurezza di rete ha un valore *Nome* impostato, è associato a un valore *Area* e include un'etichetta descrittiva. Contiene due tipi di regole, **In ingresso** e **In uscita**. Le regole In ingresso vengono applicate ai pacchetti in ingresso in una macchina virtuale, mentre le regole In uscita vengono applicate ai pacchetti in uscita dalla macchina virtuale. Le regole vengono applicate nell'host in cui si trova la macchina virtuale. Un pacchetto in ingresso o in uscita deve soddisfare una regola **Consenti** per essere autorizzato, in caso contrario verrà eliminato.

Le regole vengono elaborate nell'ordine di priorità. Ad esempio, una regola con un numero di priorità più basso (come 100) viene elaborata prima delle regole con numeri di priorità più alti (come 200). Dopo che viene trovata una corrispondenza, non vengono elaborate altre regole.

Una regola specifica quanto segue:

- **Nome:** identificatore univoco per la regola

- **Tipo:** In ingresso/In uscita

- **Priorità:** <You can specify an integer between 100 and 4096>

- **Indirizzo IP di origine:** CIDR dell'intervallo di indirizzi IP di origine

- **Intervallo porte di origine:** <integer or range between 0 and 65536>

- **Intervallo IP di destinazione:** CIDR dell'intervallo di indirizzi IP di destinazione

- **Intervallo porte di destinazione:** <integer or range between 0 and 65536>

- **Protocollo:** <è consentito TCP, UDP o "*">

- **Accesso:** Consenti/Nega

### Regole predefinite

Un gruppo di sicurezza di rete contiene regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente. Le regole predefinite descrivono le impostazioni consigliate dalla piattaforma. Come illustrato dalle regole predefinite seguenti, il traffico che origina e termina in una rete virtuale è consentito sia in ingresso che in uscita.

Mentre la connettività a Internet è consentita per la direzione in uscita, per impostazione predefinita è bloccata per la direzione in ingresso. È disponibile una regola predefinita per consentire al servizio di bilanciamento del carico di Azure di eseguire il probe dell'integrità della macchina virtuale. È possibile eseguire l'override di questa regola se la macchina virtuale o il set di macchine virtuali del gruppo di sicurezza di rete non fa parte del set con carico bilanciato.

Le regole predefinite sono:

**In ingresso**

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| CONSENTI RETE VIRTUALE IN INGRESSO | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | CONSENTI |
| CONSENTI BILANCIAMENTO DEL CARICO DI AZURE IN INGRESSO | 65001 | AZURE_LOADBALANCER | * | * | * | * | CONSENTI |
| NEGA TUTTO IN INGRESSO | 65500 | * | * | * | * | * | NEGA |

**In uscita**

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| CONSENTI RETE VIRTUALE IN USCITA | 65000 | VIRTUAL_NETWORK | * | VIRTUAL_NETWORK | * | * | CONSENTI |
| CONSENTI INTERNET IN USCITA | 65001 | * | * | INTERNET | * | * | CONSENTI |
| NEGA TUTTO IN USCITA | 65500 | * | * | * | * | * | NEGA |

### Regole speciali per l'infrastruttura

Le regole per il gruppo di sicurezza di rete sono esplicite. Non viene consentito né negato traffico oltre a quanto specificato nelle regole del gruppo di sicurezza di rete. Esistono tuttavia due tipi di traffico che vengono sempre consentiti, indipendentemente dalla specifica del gruppo di sicurezza di rete. Si tratta di regole previste a supporto dell'infrastruttura.

- **IP virtuale del nodo host:** i servizi di infrastruttura di base come DHCP, DNS e il monitoraggio dello stato vengono forniti mediante l'indirizzo IP dell'host virtualizzato 168.63.129.16. Questo indirizzo IP pubblico appartiene a Microsoft e sarà l'unico indirizzo IP virtualizzato usato in tutte le aree a questo scopo. Questo indirizzo IP è mappato all'indirizzo IP fisico del computer server (nodo host) che ospita la macchina virtuale. Il nodo host svolge la funzione di inoltro DHCP, di resolver ricorsivo DNS e di origine probe per il probe di integrità del bilanciamento del carico e per il probe di integrità del computer. La comunicazione con questo indirizzo IP non deve essere considerata come un attacco.

- **Licenze (servizio di gestione delle chiavi):** le immagini Windows in esecuzione sulle macchine virtuali devono essere concesse in licenza. A tale scopo viene inviata una richiesta di licenza ai server host del servizio di gestione delle chiavi che gestiscono le query di questo tipo. Questo avverrà sempre sulla porta in uscita 1688.

### Tag predefiniti

I tag predefiniti sono identificatori forniti dal sistema per risolvere una categoria di indirizzi IP. I tag predefiniti possono essere specificati in regole definite dal cliente. Di seguito sono illustrati i tag predefiniti:

- **VIRTUAL_NETWORK:** questo tag predefinito identifica tutto lo spazio di indirizzi della rete. Include lo spazio di indirizzi della rete virtuale (CIDR di IP in Azure), nonché tutto lo spazio di indirizzi locale connesso (reti locali). Include anche gli spazi di indirizzi tra reti virtuali.

- **AZURE_LOADBALANCER:** questo tag predefinito identifica il bilanciamento del carico dell'infrastruttura di Azure. Viene convertito in un IP del data center di Azure da cui avranno origine i probe di integrità di Azure. È necessario solo se la macchina virtuale o il set di macchine virtuali associato al gruppo di sicurezza di rete fa parte di un set con carico bilanciato.

- **INTERNET:** questo tag predefinito identifica lo spazio di indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica. Questo intervallo include anche lo spazio di IP pubblici appartenenti ad Azure.

### Porte e intervalli di porte

Le regole del gruppo di sicurezza di rete possono essere specificate su un'unica porta di origine/destinazione o su un intervallo di porte. Questo è particolarmente utile se si vuole aprire un ampio intervallo di porte per un'applicazione, ad esempio FTP. L'intervallo può essere solo sequenziale e non può essere combinato con una specifica di porta singola.

Per specificare un intervallo di porte, usare il segno "-", come mostrato di seguito nel parametro *DestinationPortRange*:

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### Traffico ICMP

Le regole del gruppo di sicurezza di rete correnti consentono solo i protocolli "TCP" o "UDP". Non esiste un tag specifico per "ICMP". Il traffico ICMP è tuttavia consentito in una rete virtuale per impostazione predefinita tramite le regole della rete virtuale in ingresso che consentono il traffico da/verso qualsiasi porta e protocollo ("*") all'interno della rete virtuale.

## Associazione di gruppi di sicurezza di rete

Associazione di un gruppo di sicurezza di rete a una macchina virtuale: quando un gruppo di sicurezza di rete viene associato direttamente a una macchina virtuale, le regole di accesso alla rete nel gruppo di sicurezza di rete vengono applicate direttamente a tutto il traffico destinato alla macchina virtuale. Quando il gruppo di sicurezza di rete viene aggiornato a seguito di modifiche delle regole, tali modifiche si riflettono nella gestione del traffico entro pochi minuti. Se l'associazione del gruppo di sicurezza di rete alla macchina virtuale viene annullata, viene ripristinato lo stato precedente al gruppo di sicurezza di rete, ovvero vengono usati i valori predefiniti di sistema precedenti all'introduzione del gruppo di sicurezza di rete.

Associazione di un gruppo di sicurezza di rete a una subnet: se un gruppo di sicurezza di rete viene associato direttamente a una subnet, le regole di accesso alla rete nel gruppo di sicurezza di rete vengono applicate a tutte le macchine virtuali nella subnet. Se le regole di accesso nel gruppo di sicurezza di rete vengono aggiornate, le modifiche vengono applicate a tutte le macchine virtuali nella subnet entro pochi minuti.

Associazione di un gruppo di sicurezza di rete a una subnet e a una macchina virtuale: è possibile associare un gruppo di sicurezza di rete a una macchina virtuale e un gruppo di sicurezza di rete diverso alla subnet in cui si trova la macchina virtuale. Questo approccio è supportato e, in questo caso, la macchina virtuale ottiene due livelli di protezione. Per il traffico in ingresso, al pacchetto vengono applicate le regole di accesso specificate nella subnet, seguite dalle regole specificate nella macchina virtuale. Nel caso di traffico in uscita, al pacchetto vengono applicate prima le regole specificate nella macchina virtuale e quindi le regole specificate nella subnet, come illustrato nel diagramma seguente

![Elenchi di controllo di accesso e gruppi di sicurezza di rete](./media/virtual-networks-nsg/figure1.png)

Quando un gruppo di sicurezza di rete viene associato a una macchina virtuale o una subnet, le regole di controllo di accesso alla rete diventano molto esplicite. La piattaforma non inserisce alcuna regola implicita per consentire il traffico su una porta specifica. In questo caso, se si crea un endpoint nella macchina virtuale, è necessario creare anche una regola per consentire il traffico da Internet. In caso contrario, l'indirizzo VIP:<Port> non sarà accessibile dall'esterno.

Si supponga ad esempio di creare una nuova macchina virtuale e un nuovo gruppo di sicurezza di rete. Si associa il gruppo di sicurezza di rete alla macchina virtuale. La macchina virtuale può comunicare con altre macchine virtuali nella rete virtuale tramite la regola CONSENTI RETE VIRTUALE IN INGRESSO. La macchina virtuale può anche effettuare connessioni in uscita a Internet usando la regola CONSENTI INTERNET IN USCITA. In seguito si crea un endpoint sulla porta 80 per ricevere il traffico destinato al sito Web in esecuzione nella macchina virtuale. I pacchetti destinati alla porta 80 all'indirizzo VIP (indirizzo IP virtuale pubblico) da Internet non raggiungono la macchina virtuale finché non si aggiunge al gruppo di sicurezza di rete una regola simile alla seguente.

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Accesso |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | CONSENTI |

## Considerazioni sulla progettazione

È necessario comprendere come le macchine virtuali comunicano con i servizi di infrastruttura e i servizi PaaS ospitati da Azure quando si progetta il NSGs. La maggior parte dei servizi PaaS di Azure, ad esempio database SQL e archiviazione, sono accessibili solo tramite un indirizzo Internet pubblico. Lo stesso vale per le probe di bilanciamento del carico.

Uno scenario comune in Azure è la separazione dei ruoli di macchine virtuali e PaaS nelle subnet in base al fatto che questi oggetti richiedano l'accesso a internet o meno. In questo scenario, potrebbe esserci una subnet con macchine virtuali o istanze del ruolo che richiedono l'accesso ai servizi Paas di Azure, ad esempio database SQL e archiviazione, ma che non richiedono le comunicazioni in ingresso o in uscita all’Internet pubblico.

Immaginiamo la seguente regola NSG per tale scenario:

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|INTERNET NON DISPONIBILE|100| VIRTUAL_NETWORK|& #42;|INTERNET|& #42;|TCP|NEGA| 

Poiché la regola consiste nel negare gli accessi ad Internet dalla rete virtuale, le macchine virtuali non saranno in grado di accedere a qualsiasi servizio PaaS di Azure che richieda un endpoint Internet pubblico, ad esempio database SQL.

Invece di utilizzare una regola di negazione, bisognerebbe prendere in considerazione di utilizzare una regola per consentire l'accesso dalla rete virtuale a Internet, ma che neghi l'accesso da Internet alla rete virtuale, come illustrato di seguito:

| Nome | Priorità | IP di origine | Porta di origine | IP di destinazione | Porta di destinazione | Protocollo | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|A INTERNET|100| VIRTUAL_NETWORK|& #42;|INTERNET|& #42;|TCP|CONSENTI|
|DA INTERNET|110| INTERNET|& #42;|VIRTUAL_NETWORK|& #42;|TCP|NEGA| 


## Pianificazione: flusso di lavoro dei gruppi di sicurezza di rete

Di seguito sono illustrati i passaggi del flusso di lavoro di base quando si usano gruppi di sicurezza di rete.

### Flusso di lavoro: creare e associare un gruppo di sicurezza di rete

1. Creare un gruppo di sicurezza di rete.

1. Aggiungere regole di sicurezza di rete, a meno che non siano sufficienti le regole predefinite.

1. Associare il gruppo di sicurezza di rete a una macchina virtuale.

1. Aggiornare la macchina virtuale.

1. Dopo l'aggiornamento, le regole del gruppo di sicurezza di rete avranno effetto immediato.

### Flusso di lavoro: aggiornare un gruppo di sicurezza di rete esistente

1. Aggiungere, eliminare o aggiornare una regola in un gruppo di sicurezza di rete esistente.

1. Tutte le macchine virtuali associate al gruppo di sicurezza di rete riceveranno gli aggiornamenti entro pochi minuti. Non è necessario aggiornare una macchina virtuale se la regola del gruppo di sicurezza di rete è già associata alla macchina virtuale.

### Flusso di lavoro: modifica dell'associazione di un gruppo di sicurezza di rete

1. Associare un nuovo gruppo di sicurezza di rete a una macchina virtuale già associata a un altro gruppo di sicurezza di rete.

1. Aggiornare la macchina virtuale.

1. Le regole del nuovo gruppo di sicurezza di rete saranno effettive entro pochi minuti.

## Come creare, configurare e gestire i gruppi di sicurezza di rete

Al momento i gruppi di sicurezza di rete possono essere configurati e modificati solo usando cmdlet di PowerShell e API REST. Non è possibile configurare gruppi di sicurezza di rete tramite il portale di gestione. I cmdlet di PowerShell seguenti consentono di creare, configurare e gestire i gruppi di sicurezza di rete.

**Creare un gruppo di sicurezza di rete**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**Aggiungere o aggiornare regole**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**Eliminare una regola da un gruppo di sicurezza di rete**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**Associare un gruppo di sicurezza di rete a una macchina virtuale**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Visualizzare le NSG associate a una macchina virtuale**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**Rimuovere un gruppo di sicurezza di rete da una macchina virtuale**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Associare un gruppo di sicurezza di rete a una subnet**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Visualizzare le NSG associate a una subnet**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**Rimuovere un gruppo di sicurezza di rete da una subnet**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Eliminare un gruppo di sicurezza di rete**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**Ottenere i dettagli e le regole di un gruppo di sicurezza di rete**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**Visualizzare tutti gli Azure PowerShell cmdlet correlati alle NSG**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=July15_HO4-->