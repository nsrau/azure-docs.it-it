<properties 
	pageTitle="Come configurare un servizio cloud - Azure" 
	description="Informazioni su come configurare un servizio cloud in Azure. Informazioni su come aggiornare la configurazione del servizio cloud e configurare l'accesso remoto per le istanze del ruolo." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015"
	ms.author="adegeo"/>




# Come configurare i servizi cloud

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di gestione di Azure. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

È inoltre possibile abilitare la connessione Desktop remoto in uno o in tutti i ruoli in esecuzione nel servizio cloud. Desktop remoto consente di accedere al desktop dell'applicazione in esecuzione e di risolvere e diagnosticare i problemi. È possibile abilitare una connessione Desktop remoto nel ruolo anche se non è stato configurato il file di definizione del servizio (con estensione csdef) per Desktop remoto durante lo sviluppo dell'applicazione. Non è necessario ridistribuire l'applicazione per abilitare la connessione Desktop remoto.

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per altre informazioni, vedere [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).

## Aggiornare la configurazione del servizio cloud

1. Nel [portale di gestione di Azure](http://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

    ![Pagina Configurazione](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Nella pagina **Configura** è possibile configurare il monitoraggio, aggiornare le impostazioni del ruolo e scegliere il sistema operativo guest e la famiglia di istanze del ruolo.

2. In **monitoraggio** impostare il livello di monitoraggio su Dettagliato o Minimo e configurare le stringhe di connessione di diagnostica necessarie per il monitoraggio dettagliato. Per le istruzioni, vedere [Come monitorare i servizi cloud](../how-to-monitor-a-cloud-service.md).


3. Per i ruoli del servizio (raggruppati per ruolo) è possibile aggiornare le impostazioni seguenti:
    
    >**Impostazioni**<br/> Modificare i valori delle impostazioni di configurazione varie specificate negli elementi *ConfigurationSettings* del file di configurazione del servizio (con estensione cscfg).
    
    >**Certificati**<br/> Modificare l'identificazione personale del certificato usato nella crittografia SSL per un ruolo. Prima di cambiare un certificato, è necessario caricarne uno nuovo nella pagina **Certificates**. Quindi aggiornare l'identificazione personale nella stringa del certificato visualizzata nelle impostazioni del ruolo.

4. In **sistema operativo** è possibile modificare la famiglia o la versione del sistema operativo per le istanze del ruolo oppure scegliere **Automatico** per abilitare gli aggiornamenti automatici della versione corrente del sistema operativo. Le impostazioni del sistema operativo si applicano a ruoli Web e ruoli di lavoro, ma non hanno effetto sulle macchine virtuali.

    Durante la distribuzione, il sistema operativo più recente viene installato in tutte le istanze del ruolo e per impostazione predefinita i sistemi operativi vengono aggiornati automaticamente.
    
    Se è necessario che il servizio cloud venga eseguito in una versione diversa del sistema operativo a causa dei requisiti di compatibilità del codice, è possibile scegliere una famiglia e una versione del sistema operativo. Quando si sceglie una specifica versione del sistema operativo, gli aggiornamenti automatici del sistema operativo per il servizio cloud vengono sospesi. Sarà necessario assicurarsi che il sistema operativo riceva gli aggiornamenti.
    
    Se si risolvono tutti i problemi di compatibilità che possono verificarsi tra le app e la versione più recente del sistema operativo, è possibile abilitare gli aggiornamenti automatici del sistema operativo impostando la relativa versione su **Automatico**.
    
    ![Impostazioni del sistema operativo](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Per salvare le impostazioni della configurazione ed effettuarne il push nelle istanze del ruolo, fare clic su **Salva**. Per annullare le modifiche, fare clic su **Ignora**. Le opzioni **Salva** e **Ignora** vengono aggiunte alla barra dei comandi dopo la modifica di un'impostazione.

### Per aggiornare manualmente il file di configurazione di un servizio cloud

1. Scaricare un file di configurazione del servizio (.cscfg) con la configurazione corrente. Nella pagina **Configura** relativa al servizio cloud fare clic su **Download**. Fare quindi clic su**Salva** o su **Salva con nome** per salvare il file.

2. Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    a. Nella pagina **Configura** fare clic su **Carica**.
    
    ![Caricamento della configurazione](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    b. In **File di configurazione** usare **Sfoglia** per selezionare il file cscfg aggiornato.
    
    c. Se il servizio cloud contiene ruoli con un'unica istanza, selezionare la casella di controllo **Applica configurazione anche se uno o più ruoli contengono un'unica istanza** per procedere con gli aggiornamenti della configurazione per i ruoli.
    
    Se non si definiscono almeno due istanze di ogni ruolo, non è possibile garantire almeno il 99,95% di disponibilità del servizio cloud in Azure durante gli aggiornamenti della configurazione. Per altre informazioni, vedere [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).
    
    d. Fare clic su **OK** (segno di spunta).


## Configurare l'accesso remoto a istanze del ruolo

Desktop remoto consente di accedere al desktop di un ruolo in esecuzione in Azure. È possibile usare una connessione Desktop remoto per risolvere e diagnosticare i problemi dell'applicazione mentre è in esecuzione. È possibile abilitare una connessione Desktop remoto nel ruolo durante la progettazione dell'applicazione o dopo averla distribuita in Azure, mentre il ruolo è in esecuzione. Se una connessione Desktop remota viene abilitata in un ruolo in esecuzione tramite il portale di gestione, non è necessario ridistribuire l'applicazione. Per autenticare la connessione Destkop remoto è possibile usare un certificato caricato in precedenza o crearne uno nuovo.

Nella pagina **Configure** del servizio cloud è possibile abilitare Desktop remoto oppure cambiare l'account o la password di amministratore locale che si usa per connettersi alle macchine virtuali, il certificato usato nell'autenticazione o la data di scadenza.

### Per configurare l'accesso remoto nel file di definizione del servizio

Aggiungere elementi **Import** al file di definizione del servizio (.csdef) per importare i moduli RemoteAccess e RemoteForwarder nel modello del servizio. Quando questi moduli sono presenti, le impostazioni di configurazione per Desktop remoto vengono aggiunte al file di configurazione del servizio. Per completare la configurazione di Desktop remoto, è necessario importare un certificato in Azure e specificarlo nel file di configurazione del servizio. Per altre informazioni, vedere [Impostare una connessione Desktop remoto per un ruolo in Azure][].

###Per abilitare o modificare l'accesso remoto per le istanze del ruolo nel portale di gestione###

1. Fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

2. Fare clic su **Remoto**.
    
    ![Servizi cloud remoti](./media/cloud-services-how-to-configure/CloudServices_Remote.png)
    
    **Avviso:** tutte le istanze del ruolo verranno riavviate quando si attiva Desktop remoto e si fa clic su OK (segno di spunta). Per evitare un riavvio, è necessario che nel ruolo sia installato il certificato usato per crittografare la password.
    
    Per evitare un riavvio, installare un certificato e tornare in questa finestra di dialogo. Per altre informazioni, vedere [Uso di Desktop remoto con i ruoli di Azure][]. Se si sceglie un certificato esistente, verrà inviato un aggiornamento della configurazione a tutte le istanze del ruolo.

3. In **Ruoli** selezionare il ruolo da aggiornare oppure selezionare **Tutti** per tutti i ruoli.

4. Apportare una delle modifiche seguenti:
    
    - Per abilitare Desktop remoto, selezionare la casella di controllo **Enable Remote Desktop**. Per disabilitare Desktop remoto, deselezionare la casella di controllo.
    
    - Creare un account da usare nelle connessioni Desktop remoto alle istanze del ruolo.
    
    - Aggiornare la password dell'account esistente.
    
    - Selezionare un certificato caricato da usare per l'autenticazione (caricare il certificato scegliendo **Upload** nella pagina **Certificates**) o creare un nuovo certificato.
    
    - Cambiare la data di scadenza per la configurazione di Desktop remoto.

5. Dopo aver completato gli aggiornamenti della configurazione, fare clic su **OK** (segno di spunta).

6. Per connettersi a un'istanza del ruolo:
    
    a. Fare clic su **Istanze** per aprire la pagina **Istanze**.
    
    b. Selezionare un'istanza del ruolo per cui è configurato Desktop remoto.
    
    c. Fare clic su **Connetti** e seguire le istruzioni visualizzate per aprire il desktop.
    
    d. Fare clic su **Apri** e quindi su **Connetti** per avviare la connessione Desktop remoto.

### Per disabilitare l'accesso remoto per le istanze del ruolo nel portale di gestione

1. Fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

2. Fare clic su **Remoto**.

3. In **Ruoli** selezionare il ruolo da aggiornare oppure selezionare **Tutti** per tutti i ruoli.

4. Deselezionare la casella di controllo **Enable Remote Desktop**.

5. Fare clic su **OK** (segno di spunta).

[Impostare una connessione Desktop remoto per un ruolo in Azure]: https://msdn.microsoft.com/library/azure/hh124107.aspx

[Uso di Desktop remoto con i ruoli di Azure]: https://msdn.microsoft.com/library/azure/gg443832.aspx
			
 

<!---HONumber=62-->