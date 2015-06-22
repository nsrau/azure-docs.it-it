<properties 
	pageTitle="Abilitare Desktop remoto per i servizi cloud (Node.js)" 
	description="Informazioni su come abilitare l'accesso da Desktop remoto per le macchine virtuali che ospitano l'applicazione Node.js di Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="mwasson"/>






# Abilitazione di Desktop remoto in Azure

Desktop remoto consente di accedere al desktop di un'istanza del ruolo in esecuzione in Azure. È possibile usare una connessione di desktop remoto per configurare la macchina virtuale oppure per risolvere e diagnosticare i problemi dell'applicazione.

> [AZURE.NOTE]Questo articolo si applica alle applicazioni Node ospitate come servizio cloud di Azure.


## Passaggio 1: Usare Azure PowerShell per configurare il servizio per Accesso desktop remoto

Per usare Desktop remoto è necessario configurare la definizione e la configurazione del servizio con un nome utente, una password e il certificato di autenticazione delle istanze del ruolo nel cloud. [Azure PowerShell] include il cmdlet **Enable-AzureServiceProjectRemoteDesktop** che esegue questa configurazione per conto dell'utente.

Eseguire i passaggi seguenti dal computer su cui è stata creata la definizione del servizio.

1.  Nel menu **Start** selezionare **Azure PowerShell**.

	![Voce del menu Start di Azure PowerShell][powershell-menu]

2.  Passare alla directory del servizio, digitare **Enable-AzureServiceProjectRemoteDesktop** e quindi immettere un nome utente e una password da usare per l'autenticazione delle istanze del ruolo nel cloud.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di **Azure PowerShell** digitare **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Al completamento di questi passaggi, le istanze del ruolo nel cloud saranno configurate per Accesso desktop remoto.

## Passaggio 2: Connettersi all'istanza del ruolo

Con la distribuzione in esecuzione in Azure è possibile connettersi all'istanza del ruolo.

1.  Nel [portale di gestione di Azure] selezionare **Cloud Services** e quindi il servizio distribuito al passaggio 1

	![portale di gestione di Azure][cloud-services]

2.  Fare clic su **Instances** e quindi fare clic su **Production** o su **Staging** per visualizzare le istanze del proprio servizio. Selezionare un'istanza e quindi fare clic su **Connect** nella parte inferiore della pagina.

    ![Pagina delle istanze][3]

2.  Quando si fa clic su **Connect** il browser Web richiede di salvare un file con estensione rdp. Se si usa Internet Explorer fare clic su **Apri**.

    ![richiesta di apertura o salvataggio del file RDP][4]

3.  All'apertura del file, viene visualizzato l'avviso di sicurezza seguente:

    ![Avviso di sicurezza di Windows][5]

4.  Fare clic su **Connect**. Verrà visualizzato un avviso di sicurezza in cui si richiede di immettere le credenziali di accesso all'istanza. Immettere la password creata al [passaggio 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell] e quindi fare clic su **OK**.

    ![richiesta di nome utente/password][6]

Quando sarà stata stabilita la connessione, in Connessione desktop remoto è visualizzato il desktop dell'istanza in Azure. È stato correttamente ottenuto l'accesso remoto all'istanza che rende possibile eseguire qualsiasi attività necessaria alla gestione dell'applicazione.

![Sessione desktop remoto][7]

## Passaggio 3: Configurare il servizio per disabilitare Accesso desktop remoto 

Quando le connessioni di desktop remoto alle istanze del ruolo nel cloud non sono più necessarie disabilitare l'accesso desktop remoto tramite [Azure PowerShell]

1.  Nel menu **Start** selezionare **Azure PowerShell**.

2.  Passare alla directory del servizio e digitare **Disable-AzureServiceProjectRemoteDesktop**:

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di **Azure PowerShell** digitare **Publish-AzureServiceProject**:

## Risorse aggiuntive

- [Accesso remoto alle istanze del ruolo in Azure] 
- [Uso di Desktop remoto con i ruoli Azure]


[Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[portale di gestione di Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
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

<!--HONumber=54--> 