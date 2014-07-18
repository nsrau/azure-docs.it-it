<properties  linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="starra" solutions="" manager="cynthn" editor="tysonn" />

<h1><a  id="configure-a-backup-vault-tutorial" ></a>Configurazione del servizio Backup di Microsoft Azure per il backup semplice e rapido di Windows Server</h1>


 
<div  class="dev-callout"> 
<strong>Nota</strong>
 
<p>Per completare questa esercitazione è necessario disporre di un account Azure in cui sia abilitata la funzionalità Backup di Azure.</p>
<ul> 
<li>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a  href="/en-us/pricing/free-trial/">versione di valutazione gratuita di Azure</a>.</li> 
 
<li>Se si dispone di un account, ma è necessario abilitare l'anteprima del servizio Backup di Azure, vedere <a  href="/en-us/develop/net/tutorials/create-a-windows-azure-account/#enable" target="_blank">Abilitare l'anteprima delle funzionalità di Azure</a>.</li>
</ul>
 
<p>Dopo aver inviato la richiesta di iscrizione al programma di anteprima del servizio Backup, attendere che lo stato diventi attivo. Tutti i clienti vengono approvati automaticamente, pertanto l'attesa dovrebbe essere breve.</p> 
</div>

 Per eseguire il backup di file e dati da Windows Server ad Azure, è
necessario creare un insieme di credenziali per il backup nell'area geografica in cui archiviare i dati. In questa esercitazione verrà illustrata la procedura dettagliata per la creazione dell'insieme di credenziali da utilizzare per l'archiviazione dei backup, il caricamento di un certificato nell'insieme di credenziali e l'installazione di un agente di backup. Verrà inoltre fornita una panoramica delle attività di gestione del backup disponibili tramite il portale di gestione.

 
<div  class="dev-callout"> 
<strong>Prima di iniziare</strong> 
<p>Per completare questa esercitazione è necessario disporre di un certificato X.509 v3 per registrare i server con gli insiemi di credenziali di backup.  Il certificato deve disporre di una chiave lunga almeno 2048 bit e deve trovarsi nell'archivio certificati personale del computer locale. Nel certificato che viene installato nel server deve essere presente la relativa chiave privata. Per caricare il certificato nel portale di gestione di Azure è necessario esportare la chiave pubblica come file in formato CER.</p> 

<p>È possibile utilizzare:</p> 
<ul>
<li>Il proprio certificato autofirmato, creato utilizzando lo strumento di creazione certificati, OPPURE</li> 

<li>Qualsiasi certificato SSL valido rilasciato da un'Autorità di certificazione (CA, Certificate Authority) considerata attendibile da Microsoft e i cui certificati radice vengono distribuiti attraverso il programma Microsoft Root Certificate. Per ulteriori informazioni su questo programma, vedere <a  href="http://go.microsoft.com/fwlink/p/?LinkId=294666">Membri del programma Windows Root Certificate</a>.</li>
</ul> 

<p>Di seguito sono elencati altri attributi del certificato da verificare:</p> 

<ul>
<li>Deve contenere la proprietà per l'utilizzo chiavi avanzato Autenticazione server</li>

<li>Deve essere in corso di validità e il periodo di validità non deve essere superiore a 3 anni</li>  
</ul>

<p>Per utilizzare un certificato autofirmato, attenersi alla procedura seguente: </p>
<ol>
<li>Scaricare lo <a  href="http://go.microsoft.com/fwlink/p/?LinkID=294662">strumento di creazione certificati (MakeCert.exe)</a>.</li>  


<li>Aprire il prompt dei comandi con privilegi di amministratore ed eseguire il comando seguente, sostituendo <i>NomeCertificato</i> con il nome del certificato e specificando l'effettiva data di scadenza del certificato dopo -e: <code>makecert.exe -r -pe -n CN=NomeCertificato -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 NomeCertificato.cer</code></li>
</ol>
<p>
Se si registrerà un server diverso rispetto a quello utilizzato per creare il certificato, sarà necessario esportare il file con estensione pfx, che contiene la chiave privata, copiarlo nell'altro server e importarlo nell'archivio certificati personale di tale server. 
</p>
<p>
Per istruzioni dettagliate sul processo di caricamento dei certificati dell'insieme di credenziali e per ulteriori informazioni sull'esportazione e importazione di file pfx, vedere <a  href="http://go.microsoft.com/fwlink/p/?LinkID=294662">Gestire i certificati di insiemi di credenziali</a>.</p> 
</div>

 <h2><a  id="create" ></a>Creazione di un insieme di credenziali per il backup</h2>


1.  Accedere al [portale di gestione][1].
    
    [WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

2.  Fare clic su **Recovery Services**, quindi su **Create New**,
    scegliere **Backup Vault** e infine fare clic su **Quick Create**.
    
    ![Nuovo insieme di credenziali di
    backup](./media/backup-configure-vault/RS_howtobackup1.png)

3.  In **Name** immettere un nome descrittivo per identificare
    l'insieme di credenziali di backup.

4.  In **Region** selezionare l'area geografica per l'insieme di
    credenziali per il backup.

5.  In **Subscription** immettere la sottoscrizione di Azure da
    utilizzare con l'insieme di credenziali per il backup.

6.  Fare clic su **Create Backup vault**.
    
    La creazione dell'insieme di credenziali per il backup può
    richiedere alcuni minuti. Per verificare lo stato, è possibile
    monitorare le notifiche nella parte inferiore del portale. Dopo la
    creazione dell'insieme di credenziali per il backup verrà
    visualizzato un messaggio di conferma. L'insieme di credenziali
    verrà elencato nelle risorse di Servizi di ripristino come
    **Online**.
    
    ![Creazione dell'insieme di credenziali per il
    backup](./media/backup-configure-vault/RS_howtobackup2.png)

<h2><a  id="upload" ></a>Caricamento di un certificato</h2>


1.  Accedere al [portale di gestione][1].

2.  Fare clic su **Recovery Services**, fare clic sul nome dell'insieme
    di credenziali per il backup che sarà identificato dal certificato,
    quindi fare clic su **Manage certificate**.
    
    ![Manage
    certificate](./media/backup-configure-vault/RS_howtoupload1.png)

3.  Nella finestra di dialogo **Manage Certificate** fare clic su Browse
    Your Computer per individuare il file con estensione cer da
    utilizzare con l'insieme di credenziali per il backup.
    <h2><a  id="download" ></a>Download e installazione di un agente di backup</h2>
    

4.  Accedere al [portale di gestione][1].

5.  Fare clic su **Recovery Services**, quindi fare clic sul nome
    dell'insieme di credenziali per il backup per visualizzare il
    relativo dashboard.

6.  Fare clic su **Install Agent**
    
    ![Installazione
    agente](./media/backup-configure-vault/RS_howtodownload1.png)

7.  Verrà visualizzata una finestra di dialogo in cui è possibile
    selezionare l'agente da scaricare:
    * Agente per Windows Server 2012 e System Center 2012 SP1 - Data
      Protection Manager
    * Agente per Windows Server 2012 Essentials

8.  Selezionare l'agente appropriato. Si verrà reindirizzati all'Area
    download Microsoft, in cui è possibile scaricare il software agente.
    Per ulteriori informazioni, vedere:
    
    * [Installazione dell'agente di backup di Azure per Windows Server
      2012 e System Center 2012 SP1 - Data Protection Manager][2]
    * [Installazione dell'agente di backup di Azure per Windows Server
      2012 Essentials][3]

Dopo l'installazione dell'agente sarà possibile utilizzare l'interfaccia di gestione locale appropriata, ad esempio lo snap-in di Microsoft Management Console, la console System Center Data Protection Manager o il dashboard di Windows Server Essentials, per configurare i criteri di backup per il server.

<h2><a  id="manage" ></a>Gestione di server e insiemi di credenziali per il backup</h2>


1.  Accedere al [portale di gestione][1].

2.  Fare clic su **Recovery Services**, quindi fare clic sul nome
    dell'insieme di credenziali per il backup per visualizzare il
    relativo dashboard. Da questo punto è possibile eseguire le attività
    seguenti:
    * **Manage certificate**. Consente di aggiornare il certificato
      caricato in precedenza.
    * **Delete**. Consente di eliminare l'insieme di credenziali per il
      backup corrente. Se un insieme di credenziali per il backup non è
      più in uso, è possibile eliminarlo per liberare spazio di
      archiviazione. L'opzione di<strong> </strong>eliminazione viene
      abilitata solo dopo l'eliminazione di tutti i server registrati
      dall'insieme di credenziali.

3.  Fare clic su **Protected Items** per visualizzare gli elementi di
    cui è stato eseguito il backup dai server. Questo elenco è solo a
    scopo informativo.  
     ![Elementi
    protetti](./media/backup-configure-vault/RS_protecteditems.png)

4.  Fare clic su **Servers** per visualizzare i nomi dei server
    registrati nell'insieme di credenziali. Da questo punto è possibile
    eseguire le attività seguenti:
    * **Allow Re-register**. Quando questa opzione è selezionata per un
      server, è possibile utilizzare la procedura di registrazione
      guidata nell'agente per registrare nuovamente il server con
      l'insieme di credenziali per il backup. La ri-registrazione di un
      server può essere necessaria per un errore nel certificato, oppure
      se è stato necessario ricreare il server. La ri-registrazione è
      consentita una sola volta per nome server.
    * **Delete**. Consente di eliminare un server dall'insieme di
      credenziali per il backup. Tutti i dati archiviati associati al
      server verranno eliminati immediatamente.
      
      ![Server
      eliminato](./media/backup-configure-vault/RS_deletedserver.png)

<h2><a  id="next" ></a>Passaggi successivi</h2>


* Per ulteriori informazioni sul servizio Backup di Azure, vedere
  [Panoramica di Azure Backup][4].

* Visitare il [Forum su Backup di Azure][5].



[1]: https://manage.windowsazure.com
[2]: http://technet.microsoft.com/en-us/library/hh831761.aspx#BKMK_installagent
[3]: http://technet.microsoft.com/en-us/library/jj884318.aspx
[4]: http://go.microsoft.com/fwlink/p/?LinkId=222425
[5]: http://go.microsoft.com/fwlink/p/?LinkId=290933