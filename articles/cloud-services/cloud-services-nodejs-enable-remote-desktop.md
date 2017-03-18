---
title: Abilitare Desktop remoto per i servizi cloud (Node.js)
description: Informazioni su come abilitare l&quot;accesso da Desktop remoto per le macchine virtuali che ospitano l&quot;applicazione Node.js di Azure.
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: a0141904-c9bc-478d-82af-5bceaca5cf6a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: c1551b250ace3aa6775932c441fcfe28431f8f57
ms.openlocfilehash: 09878cccc847040c59cbf285f40ac6a1a310c993
ms.lasthandoff: 12/08/2016


---
# <a name="enabling-remote-desktop-in-azure"></a>Abilitazione di Desktop remoto in Azure
Desktop remoto consente di accedere al desktop di un'istanza del ruolo in esecuzione in Azure. È possibile usare una connessione di desktop remoto per configurare la macchina virtuale oppure per risolvere e diagnosticare i problemi dell'applicazione.

> [!NOTE]
> Questo articolo si applica alle applicazioni Node.js ospitate come servizio cloud di Azure.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* Installare e configurare [Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Distribuire un’app Node.js in un servizio cloud di Azure Per altre informazioni, vedere [Creare e distribuire un'applicazione Node.js in un servizio cloud di Azure](cloud-services-nodejs-develop-deploy-app.md).

## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Passaggio 1: Usare Azure PowerShell per configurare il servizio per Accesso desktop remoto
Per usare Desktop remoto, è necessario aggiornare la definizione del servizio di Azure e la configurazione con nome utente, password e certificato. 

Eseguire i passaggi seguenti da un computer che contiene i file di origine per l'app.

1. Eseguire **Windows PowerShell** come amministratore. Nel **menu Start** o nella **schermata Start** cercare **Windows PowerShell**.
2. Passare alla directory che contiene la definizione del servizio (file con estensione csdef) e i file di configurazione del servizio (file con estensione cscfg).
3. Immettere questo cmdlet di PowerShell:
   
        Enable-AzureServiceProjectRemoteDesktop
4. Al prompt, immettere un nome utente e una password.
   
    ![enable-azureserviceprojectremotedesktop][enable-rdp]
5. Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:
   
       Publish-AzureServiceProject
   
   ![publish-azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Passaggio 2: Connettersi all'istanza del ruolo
Dopo aver pubblicato la definizione del servizio di aggiornamento, è possibile connettersi all'istanza del ruolo.

1. Nel [portale di Azure classico]selezionare **Servizi cloud** e quindi selezionare il servizio.
   
   ![portale di Azure classico][cloud-services]
2. Fare clic su **Istanze** e quindi su **Produzione** o **Staging** per visualizzare le istanze del proprio servizio. Selezionare un'istanza e quindi fare clic su **Connect** nella parte inferiore della pagina.
   
   ![Pagina delle istanze][3]
3. Quando si fa clic su **Connect**il browser Web richiede di salvare un file con estensione rdp. Aprire il file. Ad esempio, se si usa Internet Explorer fare clic su **Apri**.
   
   ![richiesta di apertura o salvataggio del file RDP][4]
4. All'apertura del file, viene visualizzato l'avviso di sicurezza seguente:
   
   ![Avviso di sicurezza di Windows][5]
5. Fare clic su **Connetti**. Verrà visualizzato un avviso di sicurezza in cui si richiede di immettere le credenziali di accesso all'istanza. Immettere la password creata in [Passaggio 1][Passaggio 1: Usare Azure PowerShell per configurare il servizio per Accesso desktop remoto] e quindi fare clic su **OK**.
   
   ![richiesta di nome utente/password][6]

Quando sarà stata stabilita la connessione, in Connessione desktop remoto è visualizzato il desktop dell'istanza in Azure. 

![Sessione desktop remoto][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Passaggio 3: Configurare il servizio per disabilitare Accesso desktop remoto
Quando le connessioni di desktop remoto alle istanze del ruolo nel cloud non sono più necessarie disabilitare l'accesso desktop remoto tramite [Azure PowerShell].

1. Immettere questo cmdlet di PowerShell:
   
       Disable-AzureServiceProjectRemoteDesktop
2. Immettere il seguente cmdlet di PowerShell per pubblicare le modifiche:
   
       Publish-AzureServiceProject

## <a name="additional-resources"></a>Risorse aggiuntive
* [Accesso remoto alle istanze del ruolo in Azure] 
* [Uso di Desktop remoto con i ruoli Azure]
* [Centro per sviluppatori di Node. js](/develop/nodejs/)

[Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[portale di Azure classico]: http://manage.windowsazure.com
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

