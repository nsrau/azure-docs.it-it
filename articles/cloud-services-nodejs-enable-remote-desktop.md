<properties urlDisplayName="Enable Remote Desktop" pageTitle="Abilitazione di Desktop remoto per i servizi cloud (Node.js)" metaKeywords="accesso remoto a Node.js di Azure, connessione remota a Node.js di Azure, accesso alle VM di Node.js di Azure, accesso alle macchine virtuali di Node.js di Azure" description="Informazioni su come abilitare l'accesso da Desktop remoto per le macchine virtuali che ospitano l'applicazione Node.js di Azure. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Enabling Remote Desktop in Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Abilitazione di Desktop remoto in Azure

Desktop remoto consente di accedere al desktop di un'istanza del ruolo in esecuzione in Azure. È possibile utilizzare una connessione di desktop remoto per configurare la macchina virtuale oppure per risolvere e diagnosticare i problemi dell'applicazione.

<div class="dev-callout">
	<b>Nota</b>
	<p>I passaggi illustrati in questo articolo riguardano esclusivamente le applicazioni Node ospitate come Servizio cloud di Azure.</p>
	</div>

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell]
-   [Passaggio 2: Connessione all'istanza del ruolo]
-   [Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto
    con Azure PowerShell]

## <a name="step1"> </a>Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell

Per utilizzare Desktop remoto è necessario configurare la definizione e la configurazione del servizio con un nome utente, una password e il certificato di autenticazione delle istanze del ruolo nel cloud. [Azure PowerShell] include il cmdlet **Enable-AzureServiceProjectRemoteDesktop**, che esegue questa configurazione per conto dell'utente.

Eseguire i passaggi seguenti dal computer su cui è stata creata la definizione del servizio.

1.  Dal menu **Start** scegliere **Azure PowerShell**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Passare alla directory del servizio, digitare **Enable-AzureServiceProjectRemoteDesktop**, quindi immettere un nome utente e una password da usare per l'autenticazione delle istanze del ruolo nel cloud.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di **Azure PowerShell** digitare **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Al completamento di questi passaggi, le istanze del ruolo nel cloud saranno configurate per Accesso desktop remoto.

## <a name="step2"> </a>Passaggio 2: Connessione all'istanza del ruolo

Con la distribuzione in esecuzione in Azure è possibile connettersi all'istanza del ruolo.

1.  Nel [portale di gestione di Azure] selezionare **Servizi cloud**, quindi il servizio distribuito al passaggio 1.

	![azure management portal][cloud-services]

2.  Fare clic su **Istanze** e quindi su **Produzione** o **Gestione temporanea** per visualizzare le istanze del proprio servizio. Selezionare un'istanza e quindi fare clic su **Connetti** nella parte inferiore della pagina.

    ![The instances page][3]

2.  Quando si fa clic su **Connetti**, il Web browser richiede di salvare un file con estensione rdp. Se si usa Internet Explorer fare clic su **Apri**.

    ![prompt to open or save the .rdp file][4]

3.  All'apertura del file, viene visualizzato l'avviso di sicurezza seguente:

    ![Windows security prompt][5]

4.  Fare clic su **Connetti**. Verrà visualizzato un avviso di sicurezza in cui si richiede di immettere le credenziali di accesso all'istanza. Immettere la password creata al [Passaggio 1][Step 1: Configure the service for Remote Desktop access using Azure PowerShell] e quindi fare clic su **OK**.

    ![username/password prompt][6]

Quando sarà stata stabilita la connessione, in Connessione desktop remoto è visualizzato il desktop dell'istanza in Azure. È stato correttamente ottenuto l'accesso remoto all'istanza che rende possibile eseguire qualsiasi attività necessaria alla gestione dell'applicazione.

![Remote desktop session][7]

## <a name="step3"> </a>Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto con Azure PowerShell

Quando le connessioni di desktop remoto alle istanze del ruolo nel cloud non sono più necessarie, disabilitare Accesso desktop remoto tramite [Azure PowerShell]

1.  Dal menu **Start** scegliere **Azure PowerShell**.

2.  Passare alla directory del servizio e digitare **Disable-AzureServiceProjectRemoteDesktop**:

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di **Azure PowerShell** digitare **Publish-AzureServiceProject**:

## Risorse aggiuntive

- [Accesso remoto alle istanze del ruolo in Azure] 
- [Uso di Desktop remoto con i ruoli Azure]

  [Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell]: #step1
  [Passaggio 2: Connessione all'istanza del ruolo]: #step2
  [Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto con Azure PowerShell]: #step3
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portale di gestione di Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Accesso remoto alle istanze del ruolo in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh124107.aspx
  [Uso di Desktop remoto con i ruoli Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg443832.aspx

<!--HONumber=35.2-->
