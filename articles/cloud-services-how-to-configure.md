<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="davidmu"></tags>

# <span id="configurecloudservice"></span></a>Come configurare i servizi cloud

[WACOM.INCLUDE [disclaimer][]]

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di gestione di Azure. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

È inoltre possibile abilitare la connessione Desktop remoto in uno o in tutti i ruoli in esecuzione nel servizio cloud. Desktop remoto consente di accedere al desktop dell'applicazione in esecuzione e di risolvere e diagnosticare i problemi. È possibile abilitare una connessione Desktop remoto nel ruolo anche se non è stato configurato il file di definizione del servizio (con estensione csdef) per Desktop remoto durante lo sviluppo dell'applicazione. Non è necessario ridistribuire l'applicazione per abilitare la connessione Desktop remoto.

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo (macchine virtuali) per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per ulteriori informazioni, vedere [Contratti di servizio][].

## Sommario

-   [Procedura: Aggiornare la configurazione del servizio cloud][]
-   [Procedura: Configurare l'accesso remoto a istanze del ruolo][]

## <span id="update"></span></a>Procedura: Aggiornare la configurazione del servizio cloud

1.  Nel [portale di gestione di Azure][] fare clic su **Cloud Services**. Quindi fare clic sul nome del servizio cloud per aprire il dashboard.

2.  Fare clic su **Configure**.

    Nella pagina **Configure** è possibile configurare il monitoraggio, aggiornare le impostazioni del ruolo e scegliere il sistema operativo guest e la famiglia di istanze del ruolo (macchine virtuali).

    ![Pagina Configurazione][]

3.  Nelle impostazioni di monitoraggio impostare il livello di monitoraggio su Verbose o Minimal e configurare le stringhe di connessione di diagnostica necessarie per il monitoraggio dettagliato. Per le istruzioni, vedere [Come monitorare i servizi cloud][].

4.  Per i ruoli del servizio (raggruppati per ruolo) è possibile aggiornare le impostazioni seguenti:

    > -   **Settings**:modificare i valori delle impostazioni di configurazione varie specificate negli elementi *ConfigurationSettings* del file di configurazione del servizio (.cscfg).

    > -   **Certificates**: modificare l'identificazione personale del certificato utilizzato nella crittografia SSL per un ruolo. Prima di cambiare un certificato, è necessario caricarne uno nuovo nella pagina **Certificates**. Quindi aggiornare l'identificazione personale nella stringa del certificato visualizzata nelle impostazioni del ruolo.

5.  Nelle **impostazioni del sistema operativo** è possibile modificare la famiglia o la versione del sistema operativo per le istanze del ruolo (macchine virtuali) oppure scegliere **Automatic** per riprendere gli aggiornamenti automatici della versione corrente del sistema operativo. Le impostazioni del sistema operativo si applicano a ruoli Web e ruoli di lavoro, ma non hanno effetto sui ruoli VM aggiunti ai servizi ospitati nel portale di gestione di Azure precedente.

    Quando si distribuisce un nuovo servizio cloud, è possibile scegliere il sistema operativo Windows Server 2008 R2, Windows Server 2008 con Service Pack 2 (SP2) o Windows Server 2012. Durante la distribuzione, il sistema operativo più recente viene installato in tutte le istanze del ruolo e per impostazione predefinita i sistemi operativi vengono aggiornati automaticamente.

    Se è necessario che il servizio cloud venga eseguito in una versione diversa del sistema operativo a causa dei requisiti di compatibilità del codice, è possibile scegliere una famiglia e una versione del sistema operativo. Quando si sceglie una specifica versione del sistema operativo, gli aggiornamenti automatici del sistema operativo per il servizio cloud vengono sospesi. Sarà necessario assicurarsi che il sistema operativo riceva gli aggiornamenti.

    Se si risolvono tutti i problemi di compatibilità che possono verificarsi tra le app e la versione più recente del sistema operativo, è possibile riprendere gli aggiornamenti automatici del sistema operativo impostando la relativa versione su **Automatic**.

    ![Impostazioni del sistema operativo][]

6.  Per salvare le impostazioni della configurazione ed effettuarne il push nelle istanze del ruolo, fare clic su **Save**. Per annullare le modifiche, fare clic su **Discard**. Le opzioni **Save** e **Discard** vengono aggiunte alla barra dei comandi dopo la modifica di un'impostazione.

### Per aggiornare manualmente il file di configurazione di un servizio cloud

1.  Scaricare un file di configurazione del servizio (.cscfg) con la configurazione corrente. Nella pagina **Configure** relativa al servizio cloud fare clic su **Download**. Fare quindi clic su**Save** o su **Save As** per salvare il file.

2.  Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    a. Nella pagina **Configure** fare clic su **Upload**.

    Verrà visualizzata la finestra di dialogo **Upload a New Configuration File**.

    ![Caricamento della configurazione][]

    b. In **Configuration file** premere **Browse** per selezionare il file .cscfg aggiornato.

    c. Se il servizio cloud contiene ruoli con un'unica istanza, selezionare la casella di controllo **Apply configuration even if one or more roles contain a single instance** per procedere con gli aggiornamenti della configurazione per i ruoli.

    Se non si definiscono almeno due istanze di ogni ruolo, non è possibile garantire almeno il 99,95% di disponibilità del servizio cloud in Azure durante gli aggiornamenti della configurazione. Per ulteriori informazioni, vedere [Contratti di servizio][1].

    d. Fare clic su OK (segno di spunta).

## <span id="remoteaccess"></span></a>Procedura: Configurare l'accesso remoto a istanze del ruolo

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile utilizzare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione. È possibile abilitare una connessione Desktop remoto nel ruolo durante la progettazione dell'applicazione o dopo averla distribuita in Azure, mentre il ruolo è in esecuzione. Se una connessione Desktop remota viene abilitata in un ruolo in esecuzione tramite il portale di gestione, non è necessario ridistribuire l'applicazione. Per autenticare la connessione Destkop remoto è possibile utilizzare un certificato caricato in precedenza o crearne uno nuovo.

Nella pagina **Configure** del servizio cloud è possibile abilitare Desktop remoto oppure cambiare l'account o la password di amministratore locale che si utilizza per connettersi alle macchine virtuali, il certificato utilizzato nell'autenticazione o la data di scadenza.

<div class="dev-callout"> 
<b>Nota</b> 
<p>Se il servizio cloud &egrave; composto da due o pi&ugrave; macchine virtuali basate su Windows Server connesse, non &egrave; necessario configurare l'accesso remoto, in quanto queste macchine virtuali sono configurate automaticamente per Desktop remoto.</p> 
</div>

### Per configurare l'accesso remoto nel file di definizione del servizio

Aggiungere elementi **Import** al file di definizione del servizio (.csdef) per importare i moduli RemoteAccess e RemoteForwarder nel modello del servizio. Quando questi moduli sono presenti, le impostazioni di configurazione per Desktop remoto vengono aggiunte al file di configurazione del servizio. Per completare la configurazione di Desktop remoto, è necessario importare un certificato in Azure e specificarlo nel file di configurazione del servizio. Per ulteriori informazioni, vedere [Impostare una connessione Desktop remoto per un ruolo in Azure][].

### Per abilitare o modificare l'accesso remoto per le istanze del ruolo nel portale di gestione

1.  Accedere al [portale di gestione][portale di gestione di Azure] e fare clic su **Cloud Services**. Quindi fare clic sul nome del servizio cloud per aprire il dashboard.

2.  Aprire la pagina **Configure** relativa al servizio cloud e fare clic su **Remote**.

    Nella pagina **Configure Remote Desktop** verranno visualizzate le eventuali impostazioni aggiunte al file di configurazione del servizio quando è stato distribuito il servizio cloud, come illustrato di seguito.

    ![Servizi cloud remoti][]

> [WACOM.NOTE]
> **Avviso:** Tutte le istanze del ruolo verranno riavviate la prima volta che si abilita Desktop remoto e si fa clic su OK (segno di spunta). Per evitare un riavvio, è necessario che nel ruolo sia installato il certificato utilizzato per crittografare la password. Se non è installato alcun certificato, verrà visualizzata questa opzione: ![CloudServices\_CreateNewCertDropDown][]

    To prevent a restart, install a certificate and then return to this dialog (see [Using Remote Desktop with Azure Roles][] for more information). If you choose an existing certificate, then a configuration update will be sent to all the instances in the role.

1.  In **Roles** selezionare il ruolo del servizio da aggiornare oppure selezionare **All** per tutti i ruoli.

2.  Apportare una delle modifiche seguenti:

-   Per abilitare Desktop remoto, selezionare la casella di controllo **Enable Remote Desktop**. Per disabilitare Desktop remoto, deselezionare la casella di controllo.

-   Creare un account da utilizzare nelle connessioni Desktop remoto alle istanze del ruolo.

-   Aggiornare la password dell'account esistente.

-   Selezionare un certificato caricato da utilizzare per l'autenticazione (caricare il certificato scegliendo **Upload** nella pagina **Certificates**) o creare un nuovo certificato.

-   Cambiare la data di scadenza per la configurazione di Desktop remoto.

1.  Dopo aver completato gli aggiornamenti della configurazione, fare clic su OK (segno di spunta).

2.  Per connettersi a un'istanza del ruolo:

    a. Fare clic su **Instances** per aprire la pagina **Instances**.

    b. Selezionare un'istanza del ruolo per cui è configurato Desktop remoto.

    c. Fare clic su **Connect** e seguire le istruzioni visualizzate per aprire il desktop della macchina virtuale.

    d. Fare clic su **Open** e quindi su **Connect** per avviare la connessione Desktop remoto.

### Per disabilitare l'accesso remoto per le istanze del ruolo nel portale di gestione

1.  Accedere al [portale di gestione][portale di gestione di Azure] e fare clic su **Cloud Services**. Quindi fare clic sul nome del servizio cloud per aprire il dashboard.

2.  Aprire la pagina **Configure** relativa al servizio cloud e fare clic su **Remote**.

3.  In **Roles** selezionare il ruolo del servizio da aggiornare oppure selezionare **All** per tutti i ruoli.

4.  Deselezionare la casella di controllo **Enable Remote Desktop**.

5.  Fare clic su OK (segno di spunta).

  [disclaimer]: ../includes/disclaimer.md
  [Contratti di servizio]: https://www.windowsazure.com/it-it/support/legal/sla/
  [Procedura: Aggiornare la configurazione del servizio cloud]: #update
  [Procedura: Configurare l'accesso remoto a istanze del ruolo]: #remoteaccess
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  [Pagina Configurazione]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png
  [Come monitorare i servizi cloud]: ../how-to-monitor-a-cloud-service/
  [Impostazioni del sistema operativo]: ./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png
  [Caricamento della configurazione]: ./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png
  [1]: http://www.windowsazure.com/it-it/support/legal/sla/
  [Impostare una connessione Desktop remoto per un ruolo in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh124107.aspx
  [Servizi cloud remoti]: ./media/cloud-services-how-to-configure/CloudServices_Remote.png
  [CloudServices\_CreateNewCertDropDown]: ./media/cloud-services-how-to-configure/CloudServices_CreateNewCertDropDown.png
