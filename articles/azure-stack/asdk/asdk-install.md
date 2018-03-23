---
title: Installare il Kit di sviluppo di Azure Stack (ASDK) | Documenti Microsoft
description: Viene descritto come installare il Kit di sviluppo dello Stack di Azure (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 7b8fe61731a9412c61152bc58e55deebb611d011
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installare il Kit di sviluppo di Azure Stack (ASDK)
Dopo aver [la preparazione del computer host ASDK](asdk-prepare-host.md), il ASDK può essere distribuito nell'immagine CloudBuilder.vhdx usando la procedura seguente in questo articolo.

## <a name="install-the-asdk"></a>Installare il ASDK
I passaggi descritti in questo articolo mostrano come distribuire ASDK utilizzando un'interfaccia utente grafica (GUI) fornita, scaricare ed eseguire il **asdk installer.ps1** script di PowerShell.

> [!NOTE]
> L'interfaccia utente di programma di installazione per il Kit di sviluppo dello Stack di Azure è uno script open source basato su WCF e PowerShell.


1. Dopo che il computer host viene avviato correttamente nell'immagine CloudBuilder.vhdx, l'accesso utilizzando le credenziali di amministratore specificato quando si [preparato il computer host kit sviluppo](asdk-prepare-host.md) per l'installazione ASDK. Questo sarà lo stesso come credenziali di amministratore locale host kit di sviluppo.
2. Aprire una console di PowerShell con privilegi elevata ed eseguire il  **&lt;lettera di unità > \AzureStack_Installer\asdk-installer.ps1** script (che potrebbero essere in un'unità diversa da quella dell'immagine CloudBuilder.vhdx C:\). Fare clic su **Installa**.

    ![](media/asdk-install/1.PNG) 

3. Nel Provider di identità **tipo** casella di riepilogo a discesa, selezionare **Cloud di Azure** oppure **ADFS**. Sotto **Password di amministratore locale** digitare la password di amministratore locale (che deve corrispondere alla password di amministratore locale configurato corrente) nei **Password** casella e quindi fare clic su  **Avanti**.
    - **Cloud di Azure**: consente di configurare Azure Active Directory (Azure AD) come provider di identità. Per usare questa opzione, necessaria una connessione internet, il nome completo di Azure Active Directory tenant di directory nel formato *domainname*. onmicrosoft.com o Azure Active Directory verifica nome di dominio personalizzato e le credenziali di amministratore globale per l'oggetto specificato Directory. 
    - **ADFS**: il servizio di directory timbro predefinito viene utilizzato come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password da usare è quello fornito come parte del programma di installazione.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Per ottenere risultati ottimali, anche se si desidera utilizzare un ambiente disconnesso Azure Stack mediante ADFS come provider di identità, è consigliabile installare ASDK durante la connessione a internet. In questo modo, è possibile attivare la versione di valutazione di Windows Server 2016 inclusa con l'installazione del kit di sviluppo in fase di distribuzione.
4. Selezionare una scheda di rete da utilizzare per il kit di sviluppo e quindi fare clic su **Avanti**.

    ![](media/asdk-install/3.PNG)

5. Selezionare una configurazione di rete statica per la macchina virtuale BGPNAT01 o DHCP.
    > [!TIP]
    > La macchina virtuale BGPNAT01 è il router perimetrale che offre funzionalità NAT e VPN per lo Stack di Azure.

    - **DHCP** (impostazione predefinita): la macchina virtuale Ottiene la configurazione della rete IP dal server DHCP.
    - **Statico**: usare questa opzione solo se DHCP non è possibile assegnare un indirizzo IP valido per lo Stack di Azure accedere a Internet. **Un indirizzo IP statico deve essere specificato con la lunghezza subnetmask nel formato CIDR (ad esempio, 10.0.0.5/24)**.
    - Tipo in un valore valido **ora IP del server** indirizzo. Questa operazione necessaria campo imposta il tempo server da utilizzare con il kit di sviluppo. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.

      > [!TIP]
      > Per trovare l'indirizzo IP di un server, visitare [pool.ntp.org](http:\\pool.ntp.org) o time.windows.com ping. 

    - **Facoltativamente**, impostare i valori seguenti:
        - **ID VLAN**: imposta l'ID VLAN. Utilizzare questa opzione solo se l'host e AzS BGPNAT01 necessario configurare l'ID VLAN per accedere alla rete fisica (e internet). 
        - **Server d'inoltro DNS**: un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per la risoluzione dei nomi di fuori l'indicatore, fornire i server di infrastruttura DNS esistente. Il server DNS in timbro inoltra le richieste di risoluzione nome sconosciuto al server.

    ![](media/asdk-install/4.PNG)

6. Nel **verifica proprietà scheda di interfaccia di rete** pagina, verrà visualizzato un indicatore di stato. Quando si verifica è completa, fare clic su **successivo**.

    ![](media/asdk-install/5.PNG)

9. Nel **riepilogo** fare clic su **Distribuisci** per avviare Installazione ASDK nel computer host kit di sviluppo.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > È inoltre possibile copiare i comandi di installazione di PowerShell che verranno utilizzati per installare il kit di sviluppo. Questo è utile se avrai bisogno [ridistribuire ASDK nel computer host tramite PowerShell](asdk-deploy-powershell.md).

10. Se stai eseguendo una distribuzione di Azure AD, verrà chiesto di immettere le credenziali dell'account di amministratore globale di Azure AD alcuni minuti dopo l'avvio dell'installazione.

    ![](media/asdk-install/7.PNG)

11. Il processo di distribuzione richiede alcune ore, durante il quale il computer host verrà automaticamente riavviato una sola volta. Se si desidera monitorare lo stato di distribuzione, accedere come azurestack\AzureStackAdmin dopo il riavvio dell'host di kit di sviluppo. Quando la distribuzione ha esito positivo, consente di visualizzare la console di PowerShell: **completa: azione "Distribuzione"**. 
    > [!IMPORTANT]
    > Se si accede come amministratore locale dopo che è connesso il computer al dominio, non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece Accedi come azurestack\AzureStackAdmin verificare che sia in esecuzione.

    ![](media/asdk-install/8.PNG)

Complimenti, è stato installato correttamente il ASDK.

Se la distribuzione non riesce per qualche motivo, è possibile [ridistribuire](asdk-redeploy.md) da zero o utilizzare i seguenti comandi di PowerShell, dalla stessa finestra di PowerShell con privilegi elevata, riavviare la distribuzione dall'ultimo passaggio ha esito positivo:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Passaggi successivi
[Configurazione della distribuzione post](asdk-post-deploy.md)