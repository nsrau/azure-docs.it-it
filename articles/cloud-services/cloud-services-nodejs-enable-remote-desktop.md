<properties 
	pageTitle="Abilitare Desktop remoto per i servizi cloud (Node.js)" 
	description="Informazioni su come abilitare l'accesso da Desktop remoto per le macchine virtuali che ospitano l'applicazione Node.js di Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="robmcm"/>


# Abilitazione di Desktop remoto in Azure

Desktop remoto consente di accedere al desktop di un'istanza del ruolo in esecuzione in Azure. È possibile usare una connessione di desktop remoto per configurare la macchina virtuale oppure per risolvere e diagnosticare i problemi dell'applicazione.

> [AZURE.NOTE]Questo articolo si applica alle applicazioni Node.js ospitate come servizio cloud di Azure.


## Prerequisiti

- Installare e configurare [Azure PowerShell](../install-configure-powershell.md).
- Distribuire un’app Node.js in un servizio cloud di Azure Per altre informazioni, vedere [Creazione e distribuzione di un'applicazione Node.js in un servizio cloud di Azure](cloud-services-nodejs-develop-deploy-app.md).


## Passaggio 1: Usare Azure PowerShell per configurare il servizio per Accesso desktop remoto

Per usare Desktop remoto, è necessario aggiornare la definizione del servizio di Azure e la configurazione con nome utente, password e certificato.

Eseguire i passaggi seguenti da un computer che contiene i file di origine per l'app.

1. Eseguire **Azure PowerShell** come amministratore. Dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**.

2.  Passare alla directory che contiene la definizione del servizio (file con estensione csdef) e i file di configurazione del servizio (file con estensione cscfg).

3. Immettere questo cmdlet di PowerShell:

		Enable-AzureServiceProjectRemoteDesktop

4. Al prompt, immettere un nome utente e una password.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:

    	Publish-AzureServiceProject

	![publish-azureserviceproject][publish-project]

## Passaggio 2: Connettersi all'istanza del ruolo

Dopo aver pubblicato la definizione del servizio di aggiornamento, è possibile connettersi all'istanza del ruolo.

1.  Nel [portale di gestione di Azure] selezionare **Servizi cloud** e quindi selezionare il servizio.

	![portale di gestione di Azure][cloud-services]

2.  Fare clic su **Instances** e quindi fare clic su **Production** o su **Staging** per visualizzare le istanze del proprio servizio. Selezionare un'istanza e quindi fare clic su **Connect** nella parte inferiore della pagina.

    ![Pagina delle istanze][3]

2.  Quando si fa clic su **Connect** il browser Web richiede di salvare un file con estensione rdp. Aprire il file. Ad esempio, se si usa Internet Explorer fare clic su **Apri**.

    ![richiesta di apertura o salvataggio del file RDP][4]

3.  All'apertura del file, viene visualizzato l'avviso di sicurezza seguente:

    ![Avviso di sicurezza di Windows][5]

4.  Fare clic su **Connect**. Verrà visualizzato un avviso di sicurezza in cui si richiede di immettere le credenziali di accesso all'istanza. Immettere la password creata in [Passaggio 1](Passaggio 1: Usare Azure PowerShell per configurare il servizio per Accesso desktop remoto) e quindi fare clic su **OK**.

    ![richiesta di nome utente/password][6]

Quando sarà stata stabilita la connessione, in Connessione desktop remoto è visualizzato il desktop dell'istanza in Azure.

![Sessione desktop remoto][7]

## Passaggio 3: Configurare il servizio per disabilitare Accesso desktop remoto 

Quando le connessioni di desktop remoto alle istanze del ruolo nel cloud non sono più necessarie disabilitare l'accesso desktop remoto tramite [Azure PowerShell].

1.  Immettere questo cmdlet di PowerShell:

    	Disable-AzureServiceProjectRemoteDesktop

2.  Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:

    	Publish-AzureServiceProject

## Risorse aggiuntive

- [Accesso remoto alle istanze del ruolo in Azure] 
- [Uso di Desktop remoto con i ruoli Azure]
- [Centro per sviluppatori di Node. js](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[portale di gestione di Azure]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Accesso remoto alle istanze del ruolo in Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Uso di Desktop remoto con i ruoli Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

<!----HONumber=AcomDC_1125_2015-->