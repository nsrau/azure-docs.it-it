<properties urlDisplayName="Configure a Backup Vault" pageTitle="Configurare Servizi di ripristino di Azure per eseguire il backup di Windows Server in modo semplice e rapido" metaKeywords="ripristino di emergenza" description="Esercitazione che illustra come usare il servizio di backup nell'offerta cloud di Microsoft Azure per eseguire il backup di Windows Server nel cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="markgal" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="markgal" />



<h1><a id="configure-a-backup-vault-tutorial"></a>Configurare il servizio Backup di Azure per il backup semplice e rapido di Windows Server</h1>
<div class="dev-callout"> 
<strong>Nota</strong>
 
<p>Per completare l'esercitazione, è necessario un account Azure. Questa esercitazione accompagnerà l'utente nel corso dell'abilitazione della funzionalità di backup di Azure. In precedenza era necessario creare o acquisire un certificato X.509 v3 per poter registrare il proprio server di backup. I certificati sono ancora supportati, ma per semplificare la registrazione degli insiemi di credenziali di Azure con un server, è possibile generare un insieme di credenziali direttamente dalla pagina di avvio rapido. </p>
<ul> 
<li>Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="/it-it/pricing/free-trial/">versione di valutazione gratuita di Azure</a>.</li> 
 

</ul>
 

</div>
  

<p>Per eseguire il backup di file e dati da Windows Server ad Azure, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui archiviare i dati. In questa esercitazione verrà illustrata la procedura dettagliata per la creazione dell'insieme di credenziali da usare per l'archiviazione dei backup, il download di un insieme di credenziali e l'installazione di un agente di backup. Verrà inoltre fornita una panoramica delle attività di gestione del backup disponibili tramite il portale di gestione.</p>



<h2><a id="create"></a>Creare un insieme di credenziali per il backup</h2>

1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Nuovo**, quindi su **Servizi dati**, su **Servizi di ripristino**, su **Insieme di credenziali per il backup** e infine su **Creazione rapida**.

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali di backup.

4. In **Area** selezionare l'area geografica per l'insieme di credenziali per il backup.  
![New backup vault](http://i.imgur.com/8ptgjuo.png)

5. Fare clic su **Crea insieme di credenziali**.

	La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Dopo la creazione dell'insieme di credenziali per il backup verrà visualizzato un messaggio di conferma. L'insieme di credenziali risulterà elencato nelle risorse di Servizi di ripristino come **Attivo**. 
![backup vault creation](http://i.imgur.com/grtLcKM.png)

3. Se sono disponibili più sottoscrizioni associate al proprio account aziendale, scegliere l'account corretto per associare l'insieme di credenziali per il backup.

<h2><a id="upload"></a>Scaricare una credenziale di insieme</h2>

Le credenziali di insieme sostituiscono i certificati come metodo di registrazione del servizio Azure con il proprio server. È ancora possibile usare i certificati, ma le credenziali di insieme sono più facili da usare, poiché è possibile generarle e usarle tramite il portale di Azure.  

1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup da registrare con un server.  Viene visualizzata la pagina Avvio rapido per quell'insieme di credenziali per il backup.
	

3. Nella pagina Avvio rapido fare clic su **Scarica credenziali di insieme** per richiedere al portale di generare e scaricare le credenziali che si useranno per registrare il server con l'insieme di credenziali per il backup.

4. Il portale genererà una credenziale di insieme usando una combinazione del nome dell'insieme di credenziali e della data attuale. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella di download dell'account locale o scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per le credenziali. Non è possibile modificare le credenziali di insieme, pertanto non è necessario fare clic su Apri. Una volta scaricate le credenziali, verrà richiesto se si vuole aprire la cartella. Fare clic su **x** per chiudere questo menu.

<h2><a id="download"></a>Scaricare e installare un agente di backup</h2>
1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Servizi di ripristino**, quindi selezionare un insieme di credenziali per visualizzarne la pagina Avvio rapido.

3. Nella pagina Avvio rapido selezionare il tipo di agente da scaricare. È possibile scegliere tra **Scarica Azure Backup Agent**, **Windows Server e System Center Data Protection Manager** o **Windows Server Essentials**.  Per altre informazioni, vedere:

	* [Installazione di Azure Backup Agent per Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager](http://technet.microsoft.com/it-it/library/hh831761.aspx#BKMK_installagent)
	* [Installazione di Azure Backup Agent per Windows Server 2012 Essentials](http://technet.microsoft.com/it-it/library/jj884318.aspx)

Dopo l'installazione dell'agente sarà possibile usare l'interfaccia di gestione locale appropriata, ad esempio lo snap-in di Microsoft Management Console, la console System Center Data Protection Manager o il dashboard di Windows Server Essentials, per configurare i criteri di backup per il server.
	
  

<h2><a id="manage"></a>Gestire server e insiemi di credenziali per il backup</h2>
1. Accedere al [portale di gestione](https://manage.windowsazure.com).

2. Fare clic su **Servizi di ripristino**, quindi fare clic sul nome dell'insieme di credenziali per il backup per visualizzare la pagina Avvio rapido. 

3. Fare clic su **Dashboard** per visualizzare la panoramica di utilizzo per il server. Nella parte inferiore del dashboard è possibile eseguire le azioni seguenti:
	* **Gestisci certificato**. Se per registrare un server è stato usato un certificato, eseguire questa azione per aggiornare il certificato corrente. Se si usano credenziali di insieme, non eseguire **Gestisci certificato**.
	* **Elimina**. Consente di eliminare l'insieme di credenziali per il backup corrente. Se un insieme di credenziali per il backup non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. **Elimina** viene abilitata solo dopo l'eliminazione di tutti i server registrati dall'insieme di credenziali.
	* **Credenziali di insieme**. Usare questa voce di menu di riepilogo rapido per configurare le credenziali di insieme. 

3. Fare clic su **Elementi protetti** per visualizzare gli elementi di cui è stato eseguito il backup dai server. Questo elenco è solo a scopo informativo.  
![Protected Items][protected-itmes]

4. Fare clic su **Server** per visualizzare i nomi dei server registrati nell'insieme di credenziali. Da questo punto è possibile eseguire le attività seguenti:
	* **Consenti ripetizione della registrazione**. Quando questa opzione è selezionata per un server, è possibile usare la procedura di registrazione guidata nell'agente per registrare nuovamente il server con l'insieme di credenziali per il backup. La ripetizione della registrazione di un server può essere necessaria per un errore nel certificato, oppure se è stato necessario ricreare il server. La ripetizione della registrazione è consentita una sola volta per nome server.
	* **Elimina**. Consente di eliminare un server dall'insieme di credenziali per il backup. Tutti i dati archiviati associati al server verranno eliminati immediatamente.

		![Deleted Server][deleted-server]

<h2><a id="next"></a>Passaggi successivi</h2>

- Per altre informazioni sul servizio Backup di Azure, vedere [Panoramica di Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425). 

- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

[new-backup-vault]: ./media/backup-configure-vault/RS_howtobackup1.png
[backup-vault-create]: ./media/backup-configure-vault/RS_howtobackup2.png
[manage-cert]: ./media/backup-configure-vault/RS_howtoupload1.png
[install-agent]: ./media/backup-configure-vault/RS_howtodownload1.png
[deleted-server]: ./media/backup-configure-vault/RS_deletedserver.png
[protected-itmes]: ./media/backup-configure-vault/RS_protecteditems.png

<!--HONumber=35.2-->
