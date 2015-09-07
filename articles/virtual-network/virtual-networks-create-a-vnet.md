<properties
   pageTitle="Creare una rete virtuale"
	description="Seguire i passaggi per creare con facilità una rete virtuale di base."
	services="virtual-network"
	documentationCenter=""
	authors="telmos"
	manager="carolz"
	editor="tysonn"/>

<tags
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="07/06/2015"
	ms.author="telmosampaio"/>

# Creare una rete virtuale

Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare tra loro in modo sicuro senza dover passare attraverso Internet. La creazione di una rete virtuale solo cloud dedicata è un processo relativamente semplice e rapido. Poiché una rete virtuale solo cloud non è destinata alla connettività cross-premise, non è necessario acquisire e configurare un dispositivo VPN o i certificati di autenticazione.

Dopo aver creato la rete virtuale, è possibile aggiungere nuove macchine virtuali e istanze PaaS. Si noti che se si utilizza il portale di gestione per creare le macchine virtuali, è opportuno selezionare **Da raccolta** in modo da poter specificare la rete virtuale. Questo passaggio è importante perché non è possibile tornare indietro e inserire una macchina virtuale in una rete virtuale dopo aver creato la macchina virtuale.

[Nota di Azure] **Utilizzare questa procedura per creare una rete virtuale solo cloud dedicata.** A causa della maggiore complessità coinvolta nella creazione di una configurazione cross-premise, non utilizzare questa procedura per creare una rete virtuale che in un secondo momento verrà connessa alla rete locale. Se si vuole creare una connessione cross-premise protetta tra Azure e la rete locale, vedere la pagina relativa alle [informazioni sulla connettività cross-premise protetta](vpn-gateway-cross-premises-options.md).

## Creazione della rete virtuale

1. Accedere al **portale di gestione**.
2. Nell'angolo inferiore sinistro della schermata fare clic su **New**. Nel riquadro di spostamento, fare clic su **Servizi di rete**, quindi fare clic su **Rete virtuale**. Fare clic su **Custom Create** per avviare la configurazione guidata.
3. Nella pagina **Virtual Network Details** immettere le informazioni seguenti e quindi fare clic sulla freccia Next in basso a destra. Per ulteriori informazioni sulle impostazioni nella pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Come gestire le proprietà della rete virtuale](../virtual-networks-settings).
	-  **Name:** assegnare un nome alla rete virtuale. Questo nome verrà usato quando si distribuiscono le macchine virtuali e i servizi, pertanto è consigliabile non specificare un nome troppo complicato.

	-  **Location:** selezionare l'area desiderata nell'elenco a discesa. La rete virtuale verrà creata in un data center di Azure situato nell'area specificata.



4. Nella pagina **Server DNS e connettività VPN** non apportare modifiche. Passare semplicemente alla pagina successiva facendo clic sulla freccia. Per impostazione predefinita, Azure fornisce la risoluzione dei nomi di base per la rete virtuale. È possibile che i requisiti della risoluzione dei nomi in uso siano più complessi di quelli che possono essere gestiti dalla risoluzione dei nomi di Azure. In tal caso, è possibile aggiungere in un secondo momento una macchina virtuale che esegue DNS per la rete virtuale. Per ulteriori informazioni sulla risoluzione dei nomi di Azure e DNS, vedere [Risoluzione dei nomi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
5. Nella pagina **Spazi di indirizzi della rete virtuale** è possibile immettere lo spazio degli indirizzi che si vuole usare per la rete virtuale. A meno che non si richieda un determinato intervallo di indirizzi IP interni per le macchine virtuali o si desideri creare una subnet specifica per le macchine virtuali che riceveranno un DIP statico, non è necessario apportare modifiche in questa pagina. Se si intende creare più subnet, è possibile eseguire l'operazione in questa pagina facendo clic su **Aggiungi subnet**. Per ulteriori informazioni sulle impostazioni nella pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Come gestire le proprietà della rete virtuale](../virtual-networks-settings).

	-  Per ulteriori informazioni sulle impostazioni nella pagina dei dettagli, vedere la sezione **Dettagli della rete virtuale** in [Come gestire le proprietà della rete virtuale](../virtual-networks-settings).
	-  Poiché non è necessario connettere la rete privata virtuale alla rete locale tramite una configurazione VPN cross-premise, non è necessario coordinare le impostazioni con gli intervalli di indirizzi IP di rete esistenti in locale. Se si intende creare una configurazione cross-premise in un secondo momento, è necessario coordinare gli spazi di indirizzi ora con gli intervalli già presenti nel sito locale per evitare problemi di routing. La modifica degli intervalli in un secondo momento può essere complicata e spesso comporta la ripetizione della distribuzione.


6. Fare clic sul segno di spunta in basso a destra nella pagina Spazi di indirizzi della rete virtuale per iniziare a creare la rete virtuale. Al termine della creazione della rete virtuale, verrà visualizzato Creata nell'elenco Stato nella pagina relativa alle reti del portale di gestione
7. Dopo averla creata, è possibile distribuire nella rete virtuale. Ad esempio, se si vuole distribuire una macchina virtuale in una rete virtuale, vedere [Come creare una macchina virtuale personalizzata](../virtual-machines/virtual-machines-create-custom.md). Selezionare **Da raccolta** quando si crea una nuova macchina virtuale per avere la possibilità di selezionare la rete virtuale. Si noti che se sono già presenti macchine virtuali e istanze PaaS distribuite, non possono semplicemente essere spostate nella nuova rete virtuale. Ciò accade perché le impostazioni di configurazione della rete vengono stabilite durante la distribuzione. È necessario ridistribuirle nuovamente nella nuova rete virtuale.



## Passaggi successivi
-  Leggere altre informazioni sulle [reti virtuali](../virtual-network/virtual-networks-overview.md) in Azure. 

-  [Aggiungere una macchina virtuale](../virtual-machines/virtual-machines-create-custom.md) a una rete virtuale.

<!---HONumber=August15_HO9-->