<properties 
	pageTitle="Come configurare un servizio cloud (portale) | Microsoft Azure" 
	description="Informazioni su come configurare un servizio cloud in Azure. Informazioni su come aggiornare la configurazione del servizio cloud e configurare l'accesso remoto per le istanze del ruolo. Questi esempi utilizzano il portale di Azure." 
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
	ms.date="04/22/2016"
	ms.author="adegeo"/>




# Come configurare i servizi cloud

> [AZURE.SELECTOR]
- [Portale di Azure](cloud-services-how-to-configure-portal.md)
- [Portale di Azure classico](cloud-services-how-to-configure.md)

È possibile configurare le impostazioni più comuni di un servizio cloud nel portale di Azure. In alternativa, se si preferisce aggiornare direttamente i file di configurazione, scaricare un file di configurazione del servizio da aggiornare, quindi caricare il file aggiornato e aggiornare il servizio cloud con le modifiche apportate alla configurazione. In ogni caso, per gli aggiornamenti della configurazione viene effettuato il push in tutte le istanze del ruolo.

È anche possibile gestire le istanze dei ruoli del servizio cloud o creare una connessione Desktop remoto per tali servizi.

Azure può garantire il 99,95 di disponibilità del servizio durante gli aggiornamenti della configurazione solo se si dispone di almeno due istanze del ruolo per ogni ruolo. In questo modo, una macchina virtuale può elaborare le richieste dei client mentre l'altra viene aggiornata. Per altre informazioni, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Modificare un servizio cloud

Dopo aver aperto il [portale di Azure](https://portal.azure.com/), passare al servizio cloud. Da qui è possibile gestire molti aspetti.

![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)

I collegamenti **Impostazioni** o **Tutte le impostazioni** consentiranno di accedere al pannello **Impostazioni** nel quale è possibile modificare le **proprietà** e la **configurazione**, gestire i **certificati**, configurare le **regole di avviso** e gestire gli **utenti** che hanno accesso a questo servizio cloud.

![Pannello delle impostazioni del servizio cloud di Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
Il sistema operativo utilizzato per il servizio cloud non può essere modificato tramite il **portale di Azure**, ma è possibile modificare questa impostazione solo tramite il [portale di Azure classico](http://manage.windowsazure.com/). Questa operazione è illustrata in dettaglio [qui](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## Monitoraggio

È possibile aggiungere avvisi al servizio cloud. Fare clic su **Impostazioni** > **Regole di avviso** > **Aggiungi avviso**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Da qui è possibile configurare un avviso. La casella di riepilogo a discesa **Metrica** consente di configurare un avviso per i tipi di dati seguenti.

- Lettura disco
- Scrittura disco
- Rete in ingresso
- Rete in uscita
- Percentuale CPU 

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### Configurazione del monitoraggio da un riquadro della metrica

Anziché usare **Impostazioni** > **Regole di avviso** è possibile fare clic su uno dei riquadri metrici nella sezione **Monitoraggio** del pannello del **servizio cloud**.

![Monitoraggio del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Da qui è possibile personalizzare il grafico usato con il riquadro oppure aggiungere una regola di avviso.


## Riavviare il computer, ricreare l'immagine o creare una connessione Desktop remoto

In questo momento non è possibile configurare Desktop remoto con il **portale di Azure**. È tuttavia possibile configurarlo con il [portale di Azure classico](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) o [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Per iniziare, fare clic sull'istanza del servizio cloud.

![Istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Dal pannello visualizzato è possibile avviare una connessione Desktop remoto, riavviare l'istanza o ricrearne l'immagine in remoto (iniziare con un'immagine aggiornata).

![Pulsanti di istanza del servizio cloud](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## Riconfigurare il file con estensione cscfg

Potrebbe essere necessario riconfigurare il servizio cloud con il file di [configurazione del servizio (cscfg)](cloud-services-model-and-package.md#cscfg). È prima necessario scaricare il file con estensione cscfg, modificarlo, quindi caricarlo.

1. Fare clic sull'icona **Impostazioni** o sul collegamento **Tutte le impostazioni** per aprire il pannello **Impostazioni**.

    ![Pagina Impostazioni](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. Fare clic sull’elemento **Configurazione**.

    ![Blade Configurazione](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. Fare clic sul pulsante **Download**.

    ![Download](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. Dopo aver aggiornato il file di configurazione del servizio, caricare e applicare gli aggiornamenti della configurazione:

    ![Caricamento](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
5. Selezionare il file con estensione cscfg e fare clic su **OK**.

			
## Passaggi successivi

* Procedura [distribuire un servizio cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0511_2016-->