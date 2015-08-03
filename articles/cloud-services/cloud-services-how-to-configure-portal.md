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
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# Come configurare i servizi cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di gestione di Azure. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

È inoltre possibile abilitare la connessione Desktop remoto in uno o in tutti i ruoli in esecuzione nel servizio cloud. Desktop remoto consente di accedere al desktop dell'applicazione in esecuzione e di risolvere e diagnosticare i problemi. È possibile abilitare una connessione Desktop remoto nel ruolo anche se non è stato configurato il file di definizione del servizio (con estensione csdef) per Desktop remoto durante lo sviluppo dell'applicazione. Non è necessario ridistribuire l'applicazione per abilitare la connessione Desktop remoto.

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per altre informazioni, vedere [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).

## Modificare un servizio cloud

1. Nel [portale di anteprima di Azure](http://portal.azure.com/) passare al servizio cloud.

2. Fare clic sull’icona **Impostazioni** o sul collegamento **Essentials/Tutte le impostazioni** per aprire il blade **Impostazioni**.

    ![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    Da qui è possibile visualizzare le **proprietà**, modificare la **configurazione**, gestire i **certificati** e gestire gli **utenti** che hanno accesso al servizio cloud.

2. Nella sezione **Monitoraggio** è possibile fare clic su qualsiasi riquadro per configurare gli avvisi.

    ![Monitoraggio del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. Nella sezione **Ruoli e istanze** è possibile fare clic su qualsiasi ruolo del servizio cloud per gestire l'istanza.

    ![Istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    In modalità remota è possibile connettersi, riavviare o ricreare l'immagine del servizio cloud.
    
    ![Pulsanti di istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]Il sistema operativo utilizzato per il servizio cloud non può essere modificato tramite il **portale di anteprima di Azure**, ma è possibile modificare questa impostazione solo tramite il [portale di non anteprima](http://manage.windowsazure.com/). Questa operazione è illustrata in dettaglio [qui](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Aggiornare il file di configurazione di un servizio cloud

1. Innanzitutto, scaricare il file di configurazione del servizio cloud esistente (.cscfg).

    1. Nel [portale di anteprima di Azure](http://portal.azure.com/) passare al servizio cloud.

    2. Fare clic sull’icona **Impostazioni** o sul collegamento **Essentials/Tutte le impostazioni** per aprire il blade **Impostazioni**.

        ![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. Fare clic sull’elemento **Configurazione**.

        ![Blade Configurazione](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. Fare clic sul pulsante **Download**.

        ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    1. Seguire i primi 3 passaggi precedenti per aprire il blade **Configurazione** per il servizio cloud.
    
    2. Fare clic sul pulsante **Carica**.

        ![Caricamento](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. Selezionare il file con estensione cscfg e fare clic su **OK**.

## Configurare l'accesso remoto a istanze del ruolo

L’accesso remoto non può essere configurato tramite il **portale di anteprima di Azure**, ma è possibile modificare questa impostazione solo tramite il [portale di non anteprima](http://manage.windowsazure.com/). Questa operazione è illustrata in dettaglio [qui](cloud-services-how-to-configure.md#configure-remote-access-to-role-instances).
			
 

<!---HONumber=July15_HO4-->