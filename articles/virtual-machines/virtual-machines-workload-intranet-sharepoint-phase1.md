<properties
	pageTitle="Farm di SharePoint Server 2013 fase 1 | Microsoft Azure"
	description="Creare la rete virtuale e altri elementi dell'infrastruttura di Azure nella fase 1 della farm di SharePoint Server 2013 in Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Carico di lavoro di farm intranet di SharePoint Fase 1: Configurare l'infrastruttura di SQL Server

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modello di distribuzione di Gestione risorse

In questa fase della distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn in servizi di infrastruttura di Azure, viene realizzata l'infrastruttura di rete e di archiviazione di Azure in Gestione dei servizi di Azure. È necessario completare questa fase prima di passare alla [Fase 2](virtual-machines-workload-intranet-sharepoint-phase2.md). Per conoscere tutte le fasi, vedere [Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

È necessario eseguire il provisioning di Azure con questi componenti di rete di base:

- Una rete virtuale cross-premise con una subnet.
- Tre servizi cloud di Azure.
- Un account di archiviazione di Azure per archiviare le immagini di dischi rigidi virtuali e dischi dati aggiuntivi.

## Prima di iniziare

Prima di iniziare la configurazione dei componenti di Azure, è necessario compilare le tabelle seguenti. Per agevolare le procedure per la configurazione di Azure, stampare questa sezione e prendere nota delle informazioni necessarie o copiare questa sezione in un documento e procedere con la compilazione.

Per le impostazioni della rete virtuale (VNet), compilare la Tabella V.

Elemento | Elemento di configurazione | Descrizione | Valore
--- | --- | --- | ---
1\. | Nome della rete virtuale | Nome da assegnare alla rete virtuale di Azure (ad esempio SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Posizione della rete virtuale | Il centro dati di Azure che conterrà la rete virtuale. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nome della rete locale | Nome da assegnare alla rete dell'organizzazione. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Indirizzo IP del dispositivo VPN | Indirizzo IPv4 pubblico dell'interfaccia del dispositivo VPN su Internet. Collaborare con il reparto IT per determinare questo indirizzo. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Spazio degli indirizzi di rete virtuale | Lo spazio degli indirizzi (definito in un singolo prefisso di indirizzo privato) per la rete virtuale. Collaborare con il reparto IT per determinare questo spazio degli indirizzi. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primo server DNS finale | Il quarto indirizzo IP possibile per lo spazio degli indirizzi della subnet della rete virtuale (vedere la tabella S). Collaborare con il reparto IT per stabilire questi indirizzi. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Secondo server DNS finale | Il quinto indirizzo IP possibile per lo spazio degli indirizzi della subnet della rete virtuale (vedere la tabella S). Collaborare con il reparto IT per stabilire questi indirizzi. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella V: Configurazione della rete virtuale cross-premise**

Compilare la tabella S per la subnet di questa soluzione. Assegnare alla subnet un nome descrittivo, un spazio degli indirizzi IP basato sullo spazio degli indirizzi di rete virtuale e uno scopo descrittivo. Lo spazio degli indirizzi deve essere nel formato Classless Interdomain Routing (CIDR), noto anche come formato di prefisso di rete. Un esempio è 10.24.64.0/20. Collaborare con il reparto IT per determinare questo spazio degli indirizzi dallo spazio degli indirizzi di rete virtuale.

Item | Nome della subnet | Spazio degli indirizzi della subnet | Scopo
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella S: Subnet nella rete virtuale**

> [AZURE.NOTE]Questa architettura predefinita usa una singola subnet per motivi di semplicità. Se si desidera sovrapporre un set di filtri di traffico per emulare l'isolamento di subnet, è possibile utilizzare i [Gruppi di sicurezza di rete](virtual-networks-nsg.md) di Azure.

Per i due server DNS locali da usare quando si impostano inizialmente i controller di dominio nella rete virtuale, compilare la Tabella D. Assegnare a ciascun server DNS un nome descrittivo e un singolo indirizzo IP. Non è necessario che il nome descrittivo corrisponda al nome host o al nome del computer del server DNS. Si noti che sono elencate due voci vuote, ma è possibile aggiungerne altre. Collaborare con il reparto IT per stabilire questo elenco.

Elemento | Nome descrittivo per il server DNS | Indirizzo IP del server DNS
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella D: Server DNS locali**

Per distribuire pacchetti dalla rete locale alla rete dell'organizzazione attraverso la connessione VPN da sito a sito, è necessario configurare la rete virtuale con una rete locale che contiene un elenco di spazi degli indirizzi (in notazione CIDR) per tutte le posizioni raggiungibili nella rete locale dell'organizzazione. L'elenco di spazi degli indirizzi che definiscono la rete locale non deve includere o sovrapporsi allo spazio degli indirizzi utilizzato per altre reti virtuali o altre reti locali. In altre parole, gli spazi degli indirizzi per reti locali e reti virtuali configurate devono essere univoci.

Per il set di spazi degli indirizzi della rete locale, compilare la tabella L. Si noti che sono elencate tre voci vuote, ma in genere ne occorrono di più. Collaborare con il reparto IT per determinare questo elenco di spazi degli indirizzi.

Elemento | Spazio degli indirizzi della rete virtuale
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella L: Prefissi degli indirizzi per la rete locale**

Per creare la rete virtuale con le impostazioni dalle tabelle V, S, D e G, utilizzare le istruzioni in [Creare una rete virtuale cross-premise utilizzando le tabelle di configurazione](virtual-machines-workload-deploy-vnet-config-tables.md).

> [AZURE.NOTE]Questa procedura descrive la creazione di una rete virtuale che utilizza una connessione VPN da sito a sito. Per informazioni sull'utilizzo di ExpressRoute per la connessione da sito a sito, vedere [Panoramica tecnica di ExpressRoute](../expressroute/expressroute-introduction.md).

Dopo aver creato la rete virtuale di Azure, il portale di gestione di Azure determinerà le operazioni seguenti:

- L'indirizzo IPv4 pubblico del gateway VPN di Azure per la rete virtuale.
- La chiave precondivisa IPsec (Internet Protocol security) per la connessione VPN da sito a sito.

Per visualizzare questi elementi nel portale di gestione di Azure dopo aver creato la rete virtuale, fare clic su **Reti**, selezionare il nome della rete virtuale e scegliere l’opzione di menu **Dashboard**.

Quindi, configurare il gateway di rete virtuale per creare una connessione VPN da sito a sito sicura. Per istruzioni, vedere [Configurare un gateway di rete virtuale nel portale di gestione](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

Creare quindi la connessione VPN da sito a sito tra la nuova rete virtuale e un dispositivo VPN locale. Per informazioni dettagliate ed istruzioni, vedere [Configurare un gateway di rete virtuale nel portale di gestione](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

Successivamente, verificare che lo spazio degli indirizzi della rete virtuale sia raggiungibile da una rete locale. Ciò avviene di solito aggiungendo una route corrispondente allo spazio di indirizzi di rete virtuale al dispositivo VPN e annunciando quindi tale route al resto dell'infrastruttura di della rete dell'organizzazione. Collaborare con il reparto IT per stabilire come fare.

Usare quindi le istruzioni disponibili in [Come installare e configurare Azure PowerShell](../install-configure-powershell.md) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.

Selezionare innanzitutto la sottoscrizione di Azure corretta con questi comandi. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con i nomi corretti.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

È possibile ottenere il nome della sottoscrizione dalla proprietà **SubscriptionName** dell’output del comando **Get-AzureSubscription**.

Successivamente, creare i tre servizi cloud necessari per la farm di SharePoint. Compilare la tabella C.

Item | Scopo | Nome del servizio cloud
--- | --- | ---
1\. | Controller di dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Server di SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | SharePoint Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella c: Nomi dei servizi cloud**

È necessario selezionare un nome univoco per ogni servizio cloud. *Il nome del servizio cloud può contenere solo lettere, numeri e trattini. Il primo e ultimo carattere nel campo deve essere una lettera o un numero.*

Ad esempio, è possibile assegnare al primo servizio cloud il nome DCs-*UniqueSequence*, in cui *UniqueSequence* è un’abbreviazione dell’organizzazione. Se, ad esempio, il nome dell'organizzazione è Tailspin Toys, è possibile assegnare al servizio cloud il nome CS-Tailspin.

Per verificare l'univocità del nome, è possibile usare il seguente comando di Azure PowerShell nel computer locale.

	Test-AzureName -Service <Proposed cloud service name>

Se questo comando restituisce "False", il nome proposto è univoco. Quindi, creare il servizio cloud nel modo seguente.

	New-AzureService -Service <Unique cloud service name> -Location "<Table V – Item 2 – Value column>"

Registrare il nome effettivo di ogni servizio cloud appena creato nella tabella C.

Successivamente, creare un account di archiviazione per la farm di SharePoint. *È necessario selezionare un nome univoco che contenga solo lettere minuscole e numeri.* È possibile verificare l'univocità del nome dell'account di archiviazione con il comando seguente di Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Se questo comando restituisce "False", il nome proposto è univoco. Quindi, creare l'account di archiviazione e impostare l’abbonamento da utilizzare con questi comandi.

	$staccount="<Unique storage account name>"
	New-AzureStorageAccount -StorageAccountName $staccount -Location "<Table V – Item 2 – Value column>"
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Successivamente, definire i nomi di quattro set di disponibilità. Compilare la tabella A.

Elemento | Scopo | Nome set di disponibilità
--- | --- | ---
1\. | Controller di dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Server di SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Server applicazioni SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Server Web front-end di SharePoint | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabella A: Nome set di disponibilità**

Questi nomi saranno necessari quando si creano le macchine virtuali nelle fasi 2, 3 e 4.

Questa è la configurazione risultante dal corretto completamento di questa fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## Passaggio successivo

Per avviare la configurazione di questo carico di lavoro, andare alla [Fase 2: Configurare di controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md).

## Risorse aggiuntive

[Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Linee guida sull'implementazione dei servizi di infrastruttura di Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carico di lavoro dei servizi di infrastruttura di Azure: applicazione line-of-business a disponibilità elevata](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO4-->