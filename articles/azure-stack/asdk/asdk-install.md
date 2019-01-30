---
title: Installare Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Viene descritto come installare Azure Stack Development Kit (ASDK).
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
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 09/10/2018
ms.openlocfilehash: 363e0868542f56df8c37639b2af7ac295be97da2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249906"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installare Azure Stack Development Kit (ASDK)
Dopo aver [Prepara il computer host ASDK](asdk-prepare-host.md), può essere distribuito il ASDK nell'immagine CloudBuilder.vhdx usando la procedura seguente in questo articolo.

## <a name="install-the-asdk"></a>Installare il ASDK
I passaggi descritti in questo articolo mostrano come distribuire il ASDK usando un'interfaccia utente grafica (GUI) fornita scaricando ed eseguendo il **asdk installer.ps1** script di PowerShell.

> [!NOTE]
> L'interfaccia utente di programma di installazione per Azure Stack Development Kit è un script open source basato su WCF e PowerShell.


1. Dopo che il computer host viene avviato correttamente nell'immagine CloudBuilder.vhdx, l'accesso utilizzando le credenziali di amministratore specificato quando si [preparato il computer host kit di sviluppo](asdk-prepare-host.md) per l'installazione ASDK. Deve trattarsi di quello utilizzato per le credenziali di amministratore locale host kit di sviluppo.
2. Aprire una console di PowerShell con privilegi elevata ed eseguire la  **&lt;lettera unità > \AzureStack_Installer\asdk-installer.ps1** script (che potrebbero essere in un'unità diversa da quella C:\ nell'immagine CloudBuilder.vhdx). Fare clic su **Installa**.

    ![](media/asdk-install/1.PNG) 

3. Nel Provider di identità **tipo** casella di riepilogo a discesa, selezionare **Cloud di Azure** oppure **ADFS**. Sotto **Password amministratore locale** digitare la password dell'amministratore locale (che deve corrispondere alla password di amministratore locale configurato corrente) le **Password** casella e quindi fare clic su  **Avanti**.
    - **Cloud di Azure**: Configura Azure Active Directory (Azure AD) come provider di identità. Per usare questa opzione, è necessaria una connessione internet, il nome completo di Azure AD tenant di directory nel formato *NomeDominio*. onmicrosoft.com oppure ad Azure AD verifica nome di dominio personalizzato e le credenziali di amministratore globale per l'oggetto specificato Directory. Dopo la distribuzione, l'autorizzazione di amministratore globale di Azure Active Directory non è necessaria. Tuttavia, alcune operazioni potrebbero richiedere le credenziali di amministratore globale. Ad esempio, uno script di programma di installazione di provider di risorse o una nuova funzionalità che richiedono un'autorizzazione da concedere. È possibile temporaneamente riattivare le autorizzazioni di amministratore globale dell'account o utilizzare un account di amministratore globale separata che è un proprietario del *predefinita sottoscrizione provider*.
    - **AD FS**: Il servizio di directory stamp predefinito viene usato come provider di identità. L'account predefinito per l'accesso è azurestackadmin@azurestack.local, e la password da utilizzare è specificata come parte del programma di installazione.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Per ottenere risultati ottimali, anche se si vuole usare un ambiente disconnesso Azure Stack tramite AD FS come provider di identità, è consigliabile installare ASDK mentre si è connessi a internet. In questo modo, è possibile attivare la versione di valutazione di Windows Server 2016 disponibili con l'installazione di kit di sviluppo in fase di distribuzione.
4. Selezionare una scheda di rete da utilizzare per il kit di sviluppo e quindi fare clic su **successivo**.

    ![](media/asdk-install/3.PNG)

5. Selezionare DHCP o la configurazione di rete statica per la macchina virtuale BGPNAT01.
    > [!TIP]
    > La macchina virtuale BGPNAT01 è il router perimetrale che offre funzionalità NAT e VPN per Azure Stack.

    - **DHCP** (default): La macchina virtuale Ottiene la configurazione di rete IP dal server DHCP.
    - **Statico**: Usare questa opzione solo se DHCP non è possibile assegnare un indirizzo IP valido per Azure Stack accedere a Internet. **Un indirizzo IP statico deve essere specificato con la lunghezza di subnet mask in formato CIDR (ad esempio, 10.0.0.5/24)**.
    - Tipo in un valore valido **indirizzo IP del server di tempo** indirizzo. Questa operazione necessaria campo imposta l'ora del server da utilizzare con il kit di sviluppo. Questo parametro deve essere fornito come un indirizzo IP del server ora valido. I nomi dei server non sono supportati.

      > [!TIP]
      > Per trovare un server come indirizzo IP, visitare [pool.ntp.org](http://pool.ntp.org) o effettuare il ping time.windows.com. 

    - **Facoltativamente**, si impostano i valori seguenti:
        - **VLAN ID**: Imposta l'ID VLAN. Usare questa opzione solo se l'host e AzS-BGPNAT01 deve configurare l'ID VLAN per l'accesso di rete fisica (e internet). 
        - **Server d'inoltro DNS**: Un server DNS viene creato come parte della distribuzione di Azure Stack. Per consentire ai computer all'interno della soluzione per risolvere i nomi all'esterno di timbro, forniscono server di infrastruttura DNS esistente. Il server DNS nel timbro inoltra le richieste di risoluzione nome sconosciuto a questo server.

    ![](media/asdk-install/4.PNG)

6. Nel **verifica della proprietà delle schede di interfaccia di rete** pagina, si noterà un indicatore di stato. Una volta completata la verifica, fare clic su **successivo**.

    ![](media/asdk-install/5.PNG)

9. Sul **Summary** pagina, fare clic su **Distribuisci** per avviare Installazione ASDK nel computer host kit di sviluppo.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > È inoltre possibile copiare i comandi di installazione di PowerShell che verranno usati per installare il kit di sviluppo. Ciò è utile se avrai bisogno [ridistribuire il ASDK nel computer host tramite PowerShell](asdk-deploy-powershell.md).

10. Se stai eseguendo una distribuzione di Azure AD, verrà chiesto di immettere le credenziali di account di amministratore globale di Azure AD alcuni minuti dopo l'avvio dell'installazione.

    ![](media/asdk-install/7.PNG)

11. Il processo di distribuzione richiederà alcune ore, durante i quali il computer host verrà riavviato automaticamente in una sola volta. Se si desidera monitorare l'avanzamento della distribuzione, accedere come azurestack\AzureStackAdmin dopo il riavvio dell'host di kit di sviluppo. Quando la distribuzione ha esito positivo, verrà visualizza la console di PowerShell: **COMPLETAMENTO: Azione 'Deployment'**. 
    > [!IMPORTANT]
    > Se si accede come amministratore locale dopo che il computer viene aggiunto al dominio, non verrà visualizzata l'avanzamento della distribuzione. Non rieseguire la distribuzione, invece accedere come azurestack\AzureStackAdmin per convalidare che venga eseguito.

    ![](media/asdk-install/8.PNG)

Complimenti, è stato installato correttamente il ASDK.

Se la distribuzione non riesce per qualche motivo, è possibile [ridistribuire](asdk-redeploy.md) da zero o utilizzare i seguenti comandi PowerShell, dalla stessa finestra di PowerShell con privilegi elevata, riavviare la distribuzione dall'ultimo passaggio ha esito positivo:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Passaggi successivi
[Configurazione post-distribuzione](asdk-post-deploy.md)
