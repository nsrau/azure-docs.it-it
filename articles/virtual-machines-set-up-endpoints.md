<properties urlDisplayName="Set up endpoints" pageTitle="Configurare gli endpoint in una macchina virtuale in Azure" metaKeywords="Azure config setup, configuring vm connection" description="Learn how to setup communication with a virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/29/2014" ms.author="kathydav" />

#Come configurare gli endpoint a una macchina virtuale

**Nota**: per altre informazioni su come connettersi direttamente alle macchine virtuali mediante nome host o su come configurare connessioni cross-premise, vedere [Panoramica di Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

Tutte le macchine virtuali create in Azure possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, altre risorse in Internet o altre reti virtuali richiedono gli endpoint per gestire il traffico di rete in ingresso alla macchina virtuale. 

Durante la creazione di una macchina virtuale nel portale di gestione è possibile creare gli endpoint, come ad esempio per Desktop remoto, la comunicazione remota di Windows PowerShell o Secure Shell (SSH). Dopo avere creato la macchina virtuale, è possibile aggiungere altri endpoint in base alle esigenze. È inoltre possibile gestire il traffico in ingresso nella porta pubblica mediante la configurazione di regole per l'elenco di controllo di accesso di rete dell'endpoint. In questo articolo viene illustrato come eseguire entrambe queste attività.

Ogni endpoint dispone di una porta pubblica e di una porta privata.

- La porta privata viene usata internamente dalla macchina virtuale per l'ascolto del traffico sull'endpoint.

- La porta pubblica viene usata dal servizio di bilanciamento del carico di Azure per comunicare con la macchina virtuale da risorse esterne. Dopo avere creato un endpoint, è possibile usare l'elenco di controllo di accesso di rete per definire regole che consentano di isolare e controllare il traffico in ingresso sulla porta pubblica. Per altre informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](http://go.microsoft.com/fwlink/p/?LinkId=303816).

I valori predefiniti per le porte e il protocollo per gli endpoint vengono forniti al momento della creazione degli endpoint tramite il portale di gestione. Per tutti gli altri endpoint, è necessario specificare le porte e il protocollo durante la creazione. Le risorse possono connettersi a un endpoint tramite il protocollo TCP o UDP. Il protocollo TCP include la comunicazione HTTP e HTTPS.  

**Importante**: la configurazione del firewall viene eseguita automaticamente per le porte associate a Desktop remoto e a SSH (Secure Shell) e nella maggior parte dei casi per la comunicazione remota di Windows PowerShell. Per le porte specificate per tutti gli altri endpoint, non viene effettuata alcuna configurazione automatica del firewall nel sistema operativo guest. Quando si crea un endpoint, è necessario configurare le porte appropriate nel firewall per consentire il traffico che si intende indirizzare tramite l'endpoint.

###Creare un endpoint###

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com) se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale da configurare.

3. Fare clic su **Endpoint**. Nella pagina Endpoint sono elencati tutti gli endpoint disponibili per la macchina virtuale.

	![Endpoints](./media/virtual-machines-set-up-endpoints/endpointswindows.png)

4.	Fare clic su **Aggiungi**.

	Verrà visualizzata la finestra di dialogo **Aggiungi endpoint**. Scegliere il tipo di endpoint. Se si sta creando un nuovo set con carico bilanciato, scegliere autonomo per creare il primo endpoint del set.
	
5. In **Nome** digitare un nome per l'endpoint.

6. In Protocollo specificare **TCP** o **UDP**.

7. In **Porta pubblica** e **Porta privata** digitare i numeri di porta che si desidera usare. Questi numeri di porta possono essere diversi. La porta pubblica è il punto di ingresso per la comunicazione dall'esterno di Azure e viene usata dal servizio di bilanciamento del carico di Azure. È possibile usare le regole relative a porte private e firewall sulla macchina virtuale per reindirizzare il traffico in modo appropriato per l'applicazione.

8. Fare clic su **Crea un set con carico bilanciato** se questo endpoint sarà il primo in un set con carico bilanciato. Quindi, nella pagina **Configura set con carico bilanciato** specificare un nome, il protocollo e i dettagli del probe. I set con carico bilanciato richiedono un probe in modo da monitorare l'integrità del set. Per altre informazioni, vedere [Bilanciamento del carico delle macchine virtuali](http://www.windowsazure.com/it-it/manage/windows/common-tasks/how-to-load-balance-virtual-machines/).  

9.	Fare clic sul segno di spunta per creare l'endpoint.

	L'endpoint verrà quindi visualizzato nella pagina **Endpoint**.

	![Endpoint creation successful](./media/virtual-machines-set-up-endpoints/endpointwindowsnew.png)

###Gestire l'elenco di controllo di accesso su un endpoint###

Per aggiungere, modificare o rimuovere un elenco di controllo di accesso su un endpoint, attenersi alla procedura seguente.

**Nota**: se l'endpoint fa parte di un set con carico bilanciato, qualsiasi modifica apportata all'elenco di controllo di accesso su un endpoint verrà applicata a tutti gli endpoint del set.

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com) se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale da configurare.

3. Fare clic su **Endpoint**. Nella pagina Endpoint sono elencati tutti gli endpoint disponibili per la macchina virtuale.

    ![ACL list](./media/virtual-machines-set-up-endpoints/EndpointsShowsDefaultEndpointsForVM.png)

4. Selezionare l'endpoint appropriato nell'elenco. 

5. Fare clic su **Gestisci ACL**.

    Verrà visualizzata la finestra di dialogo **Specificare i dettagli ACL per l'endpoint**.

    ![Specify ACL details](./media/virtual-machines-set-up-endpoints/EndpointACLdetails.png)

6. Usare le righe nell'elenco per aggiungere, eliminare o modificare le regole per un elenco di controllo di accesso. Il valore di Remote Subnet corrisponde all'intervallo di indirizzi IP che è possibile consentire o negare come regola. Le regole sono valutate nell'ordine, dalla prima fino all'ultima. Questo significa che le regole devono essere elencate dalla meno restrittiva alla più restrittiva. Per alcuni esempi e ulteriori informazioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](http://go.microsoft.com/fwlink/p/?LinkId=303816).

<!--HONumber=35_1-->
