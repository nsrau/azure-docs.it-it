<properties urlDisplayName="Enable Remote Desktop" pageTitle="Abilitare Desktop remoto per i servizi cloud (Node.js)" metaKeywords="Azure Node.js remote access, Azure Node.js remote connection, Azure Node.js VM access, Azure Node.js virtual machine access" description="Informazioni su come abilitare l'accesso da Desktop remoto per le macchine virtuali che ospitano l'applicazione Node.js di Azure. " metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Abilitazione di Desktop remoto in Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Abilitazione di Desktop remoto in Azure

Desktop remoto permette di accedere al desktop di un'istanza
del ruolo in esecuzione in Azure. È possibile usare una connessione di desktop remoto per
configurare la macchina virtuale oppure per risolvere e diagnosticare i problemi
dell'applicazione.

<div class="dev-callout">
    <b>Nota</b>
    <p>I passaggi illustrati in questo articolo riguardano esclusivamente le applicazioni Node ospitate come Servizio cloud di Azure.</p>
    </div>

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell][Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell]
-   [Passaggio 2: Connessione all'istanza del ruolo][Passaggio 2: Connessione all'istanza del ruolo]
-   [Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto
    con Azure PowerShell][Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto
    con Azure PowerShell]

## <a name="step1"> </a>Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell

Per usare Desktop remoto è necessario configurare la definizione e
la configurazione del servizio con un nome utente, una password e il certificato di
autenticazione delle istanze del ruolo nel cloud. [Azure PowerShell][Azure PowerShell] include il cmdlet **Enable-AzureServiceProjectRemoteDesktop** che esegue
questa configurazione per conto dell'utente.

Eseguire i passaggi seguenti dal computer su cui è stata creata la
definizione del servizio.

1.  Nel menu **Start** selezionare **Azure PowerShell**.

    ![Voce del menu Start di Azure PowerShell][Voce del menu Start di Azure PowerShell]

2.  Passare alla directory del servizio, digitare
    **Enable-AzureServiceProjectRemoteDesktop** e quindi immettere un nome utente e
    una password da usare per l'autenticazione delle istanze del ruolo nel
    cloud.

    ![enable-azureserviceprojectremotedesktop][enable-azureserviceprojectremotedesktop]

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di
    **Azure PowerShell** digitare
    **Publish-AzureServiceProject**.

    ![publish-azureserviceproject][publish-azureserviceproject]

Al completamento di questi passaggi, le istanze del ruolo
nel cloud saranno configurate per Accesso desktop remoto.

## <a name="step2"> </a>Passaggio 2: Connessione all'istanza del ruolo

Con la distribuzione in esecuzione in Azure è possibile connettersi
all'istanza del ruolo.

1.  Nel [portale di gestione di Azure][portale di gestione di Azure] selezionare **Servizi cloud** e quindi il servizio distribuito al passaggio 1.

    ![portale di gestione di Azure][1]

2.  Fare clic su **Instances** e quindi fare clic su **Production** o su **Staging** per visualizzare le istanze del proprio servizio. Selezionare un'istanza e quindi fare clic su **Connect** nella parte inferiore della pagina.

    ![Pagina delle istanze][Pagina delle istanze]

3.  Quando si fa clic su **Connetti** il browser Web richiede di salvare un
    file con estensione rdp. Se si usa Internet Explorer fare clic su **Apri**.

    ![richiesta di apertura o salvataggio del file RDP][richiesta di apertura o salvataggio del file RDP]

4.  All'apertura del file, viene visualizzato l'avviso di sicurezza seguente:

    ![Avviso di sicurezza di Windows][Avviso di sicurezza di Windows]

5.  Fare clic su **Connetti**. Verrà visualizzato un avviso di sicurezza in cui si richiede di immettere
    le credenziali di accesso all'istanza. Immettere la password creata
    al [passaggio 1][Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell] e quindi fare clic su **OK**.

    ![richiesta di nome utente/password][richiesta di nome utente/password]

Quando sarà stata stabilita la connessione, in Connessione desktop remoto è visualizzato
il desktop dell'istanza in Azure. È stato correttamente ottenuto
l'accesso remoto all'istanza che rende possibile eseguire qualsiasi attività necessaria alla
gestione dell'applicazione.

![Sessione desktop remoto][Sessione desktop remoto]

## <a name="step3"> </a>Passaggio 3: Configurazione del servizio per disabilitare Accesso desktop remoto con Azure PowerShell

Quando le connessioni di desktop remoto alle istanze del ruolo
nel cloud non sono più necessarie disabilitare l'accesso desktop remoto tramite [Azure PowerShell][Azure PowerShell].

1.  Nel menu **Start** selezionare **Azure PowerShell**.

2.  Passare alla directory del servizio e digitare
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Pubblicare le modifiche della configurazione del servizio nel cloud. Al prompt di
    **Azure PowerShell** digitare
    **Publish-AzureServiceProject**:

## Risorse aggiuntive

-   [Accesso remoto alle istanze del ruolo in Azure][Accesso remoto alle istanze del ruolo in Azure]
-   [Uso di Desktop remoto con i ruoli Azure][Uso di Desktop remoto con i ruoli Azure]

  [Passaggio 1: Configurazione del servizio per Accesso desktop remoto con Azure PowerShell]: #step1
  [Passaggio 2: Connessione all'istanza del ruolo]: #step2
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  [Voce del menu Start di Azure PowerShell]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
  [enable-azureserviceprojectremotedesktop]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
  [publish-azureserviceproject]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [Pagina delle istanze]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [richiesta di apertura o salvataggio del file RDP]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [Avviso di sicurezza di Windows]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [richiesta di nome utente/password]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [Sessione desktop remoto]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  [Accesso remoto alle istanze del ruolo in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh124107.aspx
  [Uso di Desktop remoto con i ruoli Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg443832.aspx
