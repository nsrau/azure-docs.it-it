<properties
	pageTitle="Configurare gli endpoint in una macchina virtuale in Azure"
	description="Informazioni su come configurare gli endpoint nel portale per consentire la comunicazione con una macchina virtuale in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

#Come configurare gli endpoint a una macchina virtuale

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

Tutte le macchine virtuali create in Azure possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale.

Quando si crea una macchina virtuale nel portale di Azure, vengono creati automaticamente gli endpoint per Desktop remoto, la comunicazione remota di Windows PowerShell e Secure Shell (SSH). È possibile configurare altri endpoint durante la creazione della macchina virtuale o successivamente all'occorrenza.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Informazioni sui gruppi di sicurezza di rete](virtual-networks-nsg.md)

Ogni endpoint dispone di una porta pubblica e di una porta privata.

- La porta pubblica viene usata dal servizio di bilanciamento del carico di Azure per restare in attesa di traffico in ingresso sulla macchina virtuale da Internet.
- La porta privata viene usata dalla macchina virtuale per restare in attesa di traffico in ingresso, in genere destinato a un'applicazione o a un servizio in esecuzione nella macchina virtuale.

I valori predefiniti per il protocollo IP e le porte TCP o UDP per i protocolli di rete noti vengono forniti quando si creano endpoint con il portale. Per gli endpoint personalizzati, è necessario specificare il protocollo IP corretto (TCP o UDP) e le porte pubbliche e private. Per distribuire il traffico in ingresso in modo casuale tra più macchine virtuali, è necessario creare un set con carico bilanciato costituito da più endpoint.

Dopo aver creato un endpoint, è possibile usare un elenco di controllo di accesso (ACL) per definire regole che autorizzano o rifiutano il traffico in ingresso alla porta pubblica dell'endpoint in base al relativo indirizzo IP di origine. Tuttavia, se la macchina virtuale è in una rete virtuale di Azure, è consigliabile usare invece i gruppi di sicurezza di rete. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](virtual-networks-nsg.md).

> [AZURE.NOTE]La configurazione del firewall per le macchine virtuali di Azure viene eseguita automaticamente per le porte associate a Desktop remoto e a SSH (Secure Shell) e nella maggior parte dei casi per la comunicazione remota di Windows PowerShell. Per le porte specificate per tutti gli altri endpoint, non viene effettuata alcuna configurazione automatica del firewall della macchina virtuale. Quando si crea un endpoint per la macchina virtuale, è necessario assicurarsi che il firewall della macchina virtuale consenta anche il traffico per il protocollo e la porta privata corrispondente alla configurazione dell'endpoint.

##Creare un endpoint

1.	Accedere al [portale](http://manage.windowsazure.com/), se questa operazione non è già stata eseguita.
2.	Fare clic su **Macchine virtuali** e quindi scegliere il nome della macchina virtuale da configurare.
3.	Fare clic su **Endpoint**. Nella pagina **Endpoint** sono elencati tutti gli endpoint correnti per la macchina virtuale.

	![Endpoint](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	Nella barra delle applicazioni, fare clic su **Aggiungi**.
5.	Nel **Aggiungi un endpoint a una macchina virtuale**, selezionare il tipo di endpoint.

	- Se si crea un nuovo endpoint che non fa parte di un set con carico bilanciato o corrisponde al primo endpoint in un nuovo set con carico bilanciato, scegliere **Aggiungi un endpoint autonomo** e quindi fare clic sulla freccia sinistra.
	- In alternativa, scegliere **Aggiungi un endpoint a un set con carico bilanciato esistente**, selezionare il nome del set con carico bilanciato e quindi fare clic sulla freccia sinistra. Nella pagina **Specificare i dettagli dell'endpoint** digitare un nome per l'endpoint e quindi fare clic sul segno di spunta per creare l'endpoint.

6.	Nella pagina **Specificare i dettagli dell'endpoint** digitare un nome per l'endpoint in **Nome**. È anche possibile scegliere un nome di protocollo di rete nell'elenco, che compilerà i valori iniziali per il **Protocollo**, la **Porta pubblica** e la **Porta privata**.
7.	Per un endpoint personalizzato in **Protocollo**, scegliere **TCP** o **UDP**.
8.	Per le porte personalizzate, in **Porta pubblica** digitare il numero di porta per il traffico in ingresso da Internet. In **Porta privata**, digitare il numero di porta su cui la macchina virtuale è in attesa. Questi numeri di porta possono essere diversi. Assicurarsi che il firewall nella macchina virtuale sia stato configurato per consentire il traffico corrispondente al protocollo (nel passaggio 7) e la porta privata.
9.	Se questo endpoint sarà il primo in un set con carico bilanciato, fare clic su **Crea un set con carico bilanciato**, quindi fare clic sulla freccia a destra. Nella pagina **Configura il set con carico bilanciato** specificare un nome di set con carico bilanciato, un protocollo e una porta probe, l'intervallo probe e il numero di probe inviati. Il bilanciamento del carico di Azure invia probe alle macchine virtuali in un set con carico bilanciato per monitorarne la disponibilità. Il bilanciamento del carico di Azure non inoltra il traffico alle macchine virtuali che non rispondono al probe. Fare clic sulla freccia destra.
10.	Fare clic sul segno di spunta per creare l'endpoint.

Il nuovo endpoint verrà elencato nella pagina **endpoint**.

![Creazione dell'endpoint completata](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

Per utilizzare un cmdlet di Azure PowerShell per impostare questa funzionalità, vedere [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

##Gestire l'elenco di controllo di accesso su un endpoint

Per definire il set di computer che può inviare il traffico, l'elenco di controllo di accesso in un endpoint può limitare il traffico in base all'indirizzo IP di origine. Per aggiungere, modificare o rimuovere un elenco di controllo di accesso su un endpoint, attenersi alla procedura seguente.

> [AZURE.NOTE]se l'endpoint fa parte di un set con carico bilanciato, qualsiasi modifica apportata all'elenco di controllo di accesso su un endpoint verrà applicata a tutti gli endpoint del set.

Se la macchina virtuale si trova in una rete virtuale di Azure, è consigliabile usare i gruppi di sicurezza di rete anziché gli elenchi di controllo di accesso. Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete](virtual-networks-nsg.md).

1.	Accedere al [portale](http://manage.windowsazure.com/), se questa operazione non è già stata eseguita.
2.	Fare clic su **Macchine virtuali** e quindi scegliere il nome della macchina virtuale da configurare.
3.	Fare clic su **Endpoint**. Selezionare l'endpoint appropriato nell'elenco.

    ![Elenco di controllo di accesso](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

5.	Nella barra delle applicazioni fare clic su **Gestisci ACL** per aprire la finestra di dialogo che consente di **specificare i dettagli di ACL**.

    ![Immissione dei dettagli sull'elenco di controllo di accesso](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6.	Usare le righe nell'elenco per aggiungere, eliminare o modificare le regole per un elenco di controllo di accesso e modificarne l'ordine. Il valore **Subnet remota** è un intervallo di indirizzi IP per il traffico in ingresso da Internet usato dal servizio di bilanciamento del carico di Azure per autorizzare o rifiutare il traffico in base all'indirizzo IP di origine. Assicurarsi di specificare l'intervallo di indirizzi IP nel formato CIDR, noto anche come formato di prefisso di indirizzo. Un esempio è 131.107.0.0/16.

È possibile usare regole per consentire solo il traffico da computer specifici corrispondenti ai computer su Internet oppure rifiutare il traffico da intervalli di indirizzi specifici e noti.

Le regole sono valutate nell'ordine, dalla prima fino all'ultima. Questo significa che le regole devono essere ordinate dalla meno restrittiva alla più restrittiva. Per alcuni esempi e altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](../virtual-network/virtual-networks-acl/).

Per usare un cmdlet di Azure PowerShell per impostare questa funzionalità, vedere [Gestione degli elenchi di controllo di accesso (ACL) per gli endpoint tramite PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

## Risorse aggiuntive

[Bilanciamento del carico per i servizi di infrastruttura di Azure](virtual-machines-load-balance.md)

<!---HONumber=Oct15_HO4-->