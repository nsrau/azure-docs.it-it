<properties 
   pageTitle="Come creare una rete virtuale (VNet)"
	description="Informazioni su come creare una rete virtuale (VNet)"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/08/2015"
	ms.author="telmos"/>

# Come creare una rete virtuale (VNet)

Quando si crea una rete virtuale (o VNet), i servizi e le macchine virtuali al suo interno possono comunicare tra loro in modo sicuro senza dover passare attraverso Internet. La creazione di una rete virtuale di Azure è un processo relativamente semplice e veloce se tale rete non deve essere connessa ad altre VNet o alla rete locale, in quanto non sarà necessario acquisire e configurare un dispositivo VPN o coordinare gli indirizzi IP scelti con altre VNet o con la rete locale.

>[AZURE.WARNING]Non usare questa procedura per creare una rete virtuale da connettere successivamente ad altre VNet o alla rete locale. Se si vuole creare una connessione ibrida o cross-premise sicura, vedere [Informazioni sulla connettività cross-premise protetta della rete virtuale](vpn-gateway-cross-premises-options.md). Se si vuole creare una rete virtuale che si connette a un'altra VNet, vedere [Configurare una connessione tra reti virtuali](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

## Configurare la rete virtuale

1. Accedere al **portale di gestione di Azure**.

1. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.

1. Nella pagina **Dettagli della rete virtuale** immettere le informazioni seguenti:

	![Dettagli della rete virtuale](./media/virtual-networks-create-vnet/IC736054.png)

	- **Nome:** assegnare un nome alla rete virtuale. Ad esempio, qui è stato creato il nome *EastUSVNet*. È tuttavia possibile creare qualunque nome si desideri. Questo nome verrà usato quando si distribuiscono le macchine virtuali e i servizi, pertanto è consigliabile non specificare un nome troppo complicato.

	- **Località:** selezionare la località (regione) dall'elenco a discesa. La località è direttamente correlata alla posizione fisica in cui si intende far risiedere le risorse (macchine virtuali) quando vengono distribuite nella rete virtuale. Ad esempio, se si vuole che le macchine virtuali vengano posizionate fisicamente negli *Stati Uniti Orientali*, selezionare tale località (regione). Dopo aver creato la rete virtuale, non è possibile modificare la regione associata.

1. Nella pagina **Server DNS e connettività VPN** non apportare modifiche. Passare semplicemente alla pagina successiva facendo clic sulla freccia. Per impostazione predefinita, Azure fornisce la risoluzione dei nomi di base per la rete virtuale. È possibile che i requisiti della risoluzione dei nomi in uso siano più complessi di quelli che possono essere gestiti dalla risoluzione dei nomi di Azure. In tal caso, è possibile aggiungere in un secondo momento una macchina virtuale che esegue DNS alla rete virtuale. Per altre informazioni sulla risoluzione dei nomi di Azure e DNS, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio degli indirizzi che si vuole usare per la rete virtuale. A meno che non si richieda un determinato intervallo di indirizzi IP interni per le macchine virtuali o si desideri creare una subnet specifica per le macchine virtuali che riceveranno un DIP statico, non è necessario apportare modifiche in questa pagina. Se si intende creare più subnet, è possibile eseguire l'operazione in questa pagina facendo clic su **Aggiungi subnet**.

	Informazioni aggiuntive:

	- Gli intervalli in questa pagina includono gli indirizzi IP interni dinamici (DIP) che le macchine virtuali riceveranno quando vengono distribuite nella rete virtuale. Questi indirizzi IP vengono usati solo per la comunicazione all'interno della rete virtuale. Non sono indirizzi IP per endpoint Internet.

	- Poiché la rete virtuale privata non verrà connessa alla rete locale tramite una configurazione VPN cross-premise, non è necessario coordinare queste impostazioni con gli intervalli di indirizzi IP di rete locali esistenti. Se si intende creare una configurazione cross-premise in un secondo momento, è necessario coordinare gli spazi di indirizzi ora con gli intervalli già presenti nel sito locale per evitare problemi di routing. La modifica successiva degli intervalli può essere un'operazione complicata, soprattutto se sono presenti intervalli di indirizzi che si sovrappongono.

	![Spazio di indirizzi](./media/virtual-networks-create-vnet/IC716778.png)

1. Fare clic sul segno di spunta in basso a destra nella pagina **Spazi di indirizzi della rete virtuale** per avviare la creazione della rete virtuale. Al termine della creazione della rete virtuale, verrà visualizzata la dicitura **Creato** in **Stato** nella pagina **Reti** del portale di gestione.

1. Dopo aver creato la rete virtuale, è possibile creare le macchine virtuali e le istanze PaaS al suo interno. Assicurarsi di selezionare **Da raccolta** quando si crea una nuova macchina virtuale per avere la possibilità di selezionare la rete virtuale. Si noti che se sono già presenti macchine virtuali e istanze PaaS distribuite, non possono semplicemente essere spostate nella nuova rete virtuale. Ciò accade perché le impostazioni di configurazione della rete necessarie vengono aggiunte durante la distribuzione.

## Aggiunta di macchine virtuali alla rete virtuale

Dopo aver creato la rete virtuale, è possibile aggiungervi nuove macchine virtuali. È importante prima creare la rete virtuale e quindi distribuire la macchina virtuale. Dopo aver distribuito una macchina virtuale, non è infatti possibile spostarla in una rete virtuale senza doverla ridistribuire. Se si usa il portale di gestione per creare le macchine virtuali, l'interfaccia per distribuirle in una rete virtuale è disponibile solo selezionando **Nuovo/ Calcolo/ Macchina virtuale/ Da raccolta**. Quando si esegue la procedura guidata per creare la macchina virtuale, nella pagina **Configurazione macchina virtuale** verrà visualizzata la voce **Regione/Gruppo di affinità/Rete virtuale**. Dall'elenco a discesa selezionare la rete virtuale già creata. Per altre informazioni sulla creazione di macchine virtuali, vedere l'articolo relativo alle [macchine virtuali di Azure](../virtual-machines).

## Passaggi successivi

[Come gestire le proprietà della rete virtuale (VNet)](../virtual-networks-settings)

[Come gestire i server DNS usati da una rete virtuale (VNet)](../virtual-networks-manage-dns-in-vnet)

[Come usare gli indirizzi IP pubblici in una rete virtuale](../virtual-networks-public-ip-within-vnet)

[Come eliminare una rete virtuale (VNet)](../virtual-networks-delete-vnet)
 

<!---HONumber=August15_HO9-->