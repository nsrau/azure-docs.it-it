<properties 
	pageTitle="Come configurare un servizio cloud | Microsoft Azure" 
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
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Come configurare i servizi cloud

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-configure-portal.md)
- [Azure classic portal](cloud-services-how-to-configure.md)

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di Azure classico. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

Il portale di Azure classico consente inoltre di [abilitare la connessione Desktop remoto per un ruolo nei Servizi Cloud di Azure](cloud-services-role-enable-remote-desktop.md)

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Modificare un servizio cloud

1. Nel [portale di Azure classico](http://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

    ![Pagina Configurazione](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Nella pagina **Configura** è possibile configurare il monitoraggio, aggiornare le impostazioni del ruolo e scegliere il sistema operativo guest e la famiglia di istanze del ruolo.

2. In **monitoraggio** impostare il livello di monitoraggio su Dettagliato o Minimo e configurare le stringhe di connessione di diagnostica necessarie per il monitoraggio dettagliato.

3. Per i ruoli del servizio (raggruppati per ruolo) è possibile aggiornare le impostazioni seguenti:
    
    >**Settings**:modificare i valori delle impostazioni di configurazione varie specificate negli elementi *ConfigurationSettings* del file di configurazione del servizio (.cscfg).
    >
    >**Certificates**: modificare l'identificazione personale del certificato utilizzato nella crittografia SSL per un ruolo. Prima di cambiare un certificato, è necessario caricarne uno nuovo nella pagina **Certificates**. Quindi aggiornare l'identificazione personale nella stringa del certificato visualizzata nelle impostazioni del ruolo.

4. In **sistema operativo** è possibile modificare la famiglia o la versione del sistema operativo per le istanze del ruolo oppure scegliere **Automatico** per abilitare gli aggiornamenti automatici della versione corrente del sistema operativo. Le impostazioni del sistema operativo si applicano a ruoli Web e ruoli di lavoro, ma non hanno effetto sulle macchine virtuali.

    Durante la distribuzione, il sistema operativo più recente viene installato in tutte le istanze del ruolo e per impostazione predefinita i sistemi operativi vengono aggiornati automaticamente.
    
    Se è necessario che il servizio cloud venga eseguito in una versione diversa del sistema operativo a causa dei requisiti di compatibilità del codice, è possibile scegliere una famiglia e una versione del sistema operativo. Quando si sceglie una specifica versione del sistema operativo, gli aggiornamenti automatici del sistema operativo per il servizio cloud vengono sospesi. Sarà necessario assicurarsi che il sistema operativo riceva gli aggiornamenti.
    
    Se si risolvono tutti i problemi di compatibilità che possono verificarsi tra le app e la versione più recente del sistema operativo, è possibile abilitare gli aggiornamenti automatici del sistema operativo impostando la relativa versione su **Automatico**.
    
    ![Impostazioni del sistema operativo](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Per salvare le impostazioni della configurazione ed effettuarne il push nelle istanze del ruolo, fare clic su **Salva**. Per annullare le modifiche, fare clic su **Ignora**. Le opzioni **Salva** e **Ignora** vengono aggiunte alla barra dei comandi dopo la modifica di un'impostazione.

## Aggiornare il file di configurazione di un servizio cloud

1. Scaricare un file di configurazione del servizio (.cscfg) con la configurazione corrente. Nella pagina **Configura** relativa al servizio cloud fare clic su **Download**. Fare quindi clic su**Salva** o su **Salva con nome** per salvare il file.

2. Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    1. Nella pagina **Configura** fare clic su **Carica**.
    
        ![Caricamento della configurazione](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. In **File di configurazione** usare **Sfoglia** per selezionare il file cscfg aggiornato.
    
    3. Se il servizio cloud contiene ruoli con un'unica istanza, selezionare la casella di controllo **Applica configurazione anche se uno o più ruoli contengono un'unica istanza** per procedere con gli aggiornamenti della configurazione per i ruoli.
    
        Se non si definiscono almeno due istanze di ogni ruolo, non è possibile garantire almeno il 99,95% di disponibilità del servizio cloud in Azure durante gli aggiornamenti della configurazione. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).
    
    4. Fare clic su **OK** (segno di spunta).


## Passaggi successivi

* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage.md).
* [Impostare una connessione Desktop remoto per un ruolo nei servizi cloud di Azure](cloud-services-role-enable-remote-desktop.md)
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate.md).

<!---HONumber=AcomDC_0128_2016-->