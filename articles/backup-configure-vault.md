<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# <span id="configure-a-backup-vault-tutorial"></span></a>Configurazione del servizio Backup di Microsoft Azure per il backup semplice e rapido di Windows Server

<div class="dev-callout"> 
<strong>Nota</strong>
 
<p>Per completare l'esercitazione, &egrave; necessario un account Azure. Questa esercitazione illustra la procedura per abilitare la funzionalit&agrave; Backup di Azure. In precedenza era necessario creare o acquisire un certificato X.509 v3 per registrare il server di backup. I certificati sono ancora supportati, ma per facilitare la registrazione dell'insieme di credenziali di Azure con un server &egrave; possibile generare una credenziale di insieme direttamente dalla pagina Avvio rapido. </p>
<ul> 
<li>Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="/it-it/pricing/free-trial/">versione di valutazione gratuita di Azure</a>.</li> 
 

</ul>
 

</div>

Per eseguire il backup di file e dati da Windows Server ad Azure, è necessario creare un insieme di credenziali per il backup nell'area geografica in cui archiviare i dati. Questa esercitazione illustra la procedura dettagliata per la creazione dell'insieme di credenziali da utilizzare per l'archiviazione dei backup, il download di una credenziale di insieme e l'installazione di un agente di backup. Viene inoltre fornita una panoramica delle attività di gestione del backup disponibili tramite il portale di gestione.

## <span id="create"></span></a>Creazione di un insieme di credenziali per il backup

1.  Accedere al [portale di gestione][portale di gestione].

2.  Fare clic su **Nuovo**, scegliere **Data Services**, quindi **Servizi di ripristino**, fare clic su **Insieme di credenziali per il backup** e infine su **Creazione rapida**.

3.  In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali di backup.

4.  In **Area** selezionare l'area geografica per l'insieme di credenziali per il backup.
     ![Nuovo insieme di credenziali di backup][Nuovo insieme di credenziali di backup]

5.  Fare clic su **Crea insieme di credenziali**.

    La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Dopo la creazione dell'insieme di credenziali per il backup verrà visualizzato un messaggio di conferma. L'insieme di credenziali verrà elencato nelle risorse di Servizi di ripristino come **Attivo**.
    ![Creazione dell'insieme di credenziali per il backup][Creazione dell'insieme di credenziali per il backup]

6.  Se all'account aziendale sono associate più sottoscrizioni, scegliere l'account corretto da associare all'insieme di credenziali per il backup.

## <span id="upload"></span></a>Download di una credenziale di insieme

Le credenziali di insieme sostituiscono i certificati come metodo per registrare il servizio di Azure con il server. È ancora possibile usare i certificati, ma le credenziali di insieme sono più facili da usare perché vengono generate e scaricate dal portale di Azure.

1.  Accedere al [portale di gestione][portale di gestione].

2.  Fare clic su **Servizi di ripristino**, quindi selezionare l'insieme di credenziali per il backup che si desidera registrare con un server. Viene visualizzata la pagina Avvio rapido relativa a tale insieme di credenziali.

3.  Nella pagina Avvio rapido fare clic su **Download vault credentials** per chiedere al portale di generare e scaricare le credenziali di insieme da usare per la registrazione del server con l'insieme di credenziali per il backup.

4.  Il portale genera una credenziale di insieme usando una combinazione costituita dal nome dell'insieme e dalla data corrente. Fare clic su **Salva** per scaricare le credenziali di insieme nella cartella Download dell'account locale. In alternativa, scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per le credenziali di insieme. Poiché non è possibile modificare le credenziali di insieme, non vi sono motivi per fare clic su Apri. Una volta scaricate le credenziali, verrà richiesto di aprire la cartella. Fare clic su **x** per chiudere il menu.

## <span id="download"></span></a>Download e installazione di un agente di backup

1.  Accedere al [portale di gestione][portale di gestione].

2.  Fare clic su **Servizi di ripristino**, quindi selezionare un insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.

3.  Nella pagina Avvio rapido selezionare il tipo di agente che si desidera scaricare. È possibile scegliere **Download Azure Backup Agent**, **Windows Server and System Center Data Protection Manager** o **Windows Server Essentials**. Per ulteriori informazioni, vedere:

    -   [Installazione dell'agente di backup di Azure per Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager][Installazione dell'agente di backup di Azure per Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager]
    -   [Installazione dell'agente di backup di Azure per Windows Server 2012 Essentials][Installazione dell'agente di backup di Azure per Windows Server 2012 Essentials]

Dopo l'installazione dell'agente sarà possibile utilizzare l'interfaccia di gestione locale appropriata, ad esempio lo snap-in di Microsoft Management Console, la console System Center Data Protection Manager o il dashboard di Windows Server Essentials, per configurare i criteri di backup per il server.

## <span id="manage"></span></a>Gestione di server e insiemi di credenziali per il backup

1.  Accedere al [portale di gestione][portale di gestione].

2.  Fare clic su **Servizi di ripristino**, quindi fare clic sul nome dell'insieme di credenziali per il backup per visualizzare la relativa pagina Avvio rapido.

3.  Fare clic su **Dashboard** per visualizzare la panoramica sull'utilizzo del server. Nella parte inferiore del dashboard è possibile eseguire le operazioni seguenti:

    -   **Manage certificate**. Se per la registrazione del server è stato usato un certificato, per aggiornare quest'ultimo usare lo stesso server. Se si usano le credenziali di insieme, non usare la funzionalità **Gestisci certificato**.
    -   **Delete**. Consente di eliminare l'insieme di credenziali per il backup corrente. Se un insieme di credenziali per il backup non è più in uso, è possibile eliminarlo per liberare spazio di archiviazione. L'opzione di **** eliminazione viene abilitata solo dopo l'eliminazione di tutti i server registrati dall'insieme di credenziali.
    -   **Vault credentials**. Usare questa voce del menu di riepilogo rapido per configurare le credenziali di insieme.

4.  Fare clic su **Protected Items** per visualizzare gli elementi di cui è stato eseguito il backup dai server. Questo elenco viene visualizzato solo a scopo informativo.
    ![Elementi protetti][Elementi protetti]

5.  Fare clic su **Servers** per visualizzare i nomi dei server registrati nell'insieme di credenziali. Da questo punto è possibile eseguire le attività seguenti:

    -   **Consenti ripetizione della registrazione**. Quando questa opzione è selezionata per un server, è possibile utilizzare la procedura di registrazione guidata nell'agente per registrare nuovamente il server con l'insieme di credenziali per il backup. La ri-registrazione di un server può essere necessaria per un errore nel certificato, oppure se è stato necessario ricreare il server. La ri-registrazione è consentita una sola volta per nome server.
    -   **Delete**. Consente di eliminare un server dall'insieme di credenziali per il backup. Tutti i dati archiviati associati al server verranno eliminati immediatamente.

        ![Server eliminato][Server eliminato]

## <span id="next"></span></a>Passaggi successivi

-   Per ulteriori informazioni sul servizio Backup di Azure, vedere [Panoramica di Azure Backup][Panoramica di Azure Backup].

-   Visitare il [Forum su Backup di Azure][Forum su Backup di Azure].

  [portale di gestione]: https://manage.windowsazure.com
  [Nuovo insieme di credenziali di backup]: http://i.imgur.com/506c7ch.png
  [Creazione dell'insieme di credenziali per il backup]: http://i.imgur.com/grtLcKM.png
  [Installazione dell'agente di backup di Azure per Windows Server 2012 e System Center 2012 SP1 - Data Protection Manager]: http://technet.microsoft.com/it-it/library/hh831761.aspx#BKMK_installagent
  [Installazione dell'agente di backup di Azure per Windows Server 2012 Essentials]: http://technet.microsoft.com/it-it/library/jj884318.aspx
  [Elementi protetti]: ./media/backup-configure-vault/RS_protecteditems.png
  [Server eliminato]: ./media/backup-configure-vault/RS_deletedserver.png
  [Panoramica di Azure Backup]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [Forum su Backup di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=290933
