---
title: Connettersi a Azure Stack | Documenti Microsoft
description: Informazioni su come connettersi a Azure Stack
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: sngun
ms.openlocfilehash: 914f2e5d10aa341cea5eba8c24c7c37610e6b626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Per gestire le risorse, è necessario connettersi al Kit di sviluppo dello Stack di Azure. I dettagli di questo argomento i passaggi necessari per connettersi al kit di sviluppo. È possibile utilizzare una delle opzioni di connessione seguente:

* [Desktop remoto](#connect-with-remote-desktop): consente di connettersi rapidamente nel kit di sviluppo di un singolo utente simultaneo.
* [Rete privata virtuale (VPN, Virtual Private Network)](#connect-with-vpn): consente di connettersi più utenti simultanei dai client di fuori dell'infrastruttura di Azure Stack (richiede la configurazione).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Connettersi a Azure Stack con Desktop remoto
Con una connessione Desktop remoto, è possibile utilizzare un singolo utente simultaneo con il portale per gestire le risorse.

1. Aprire una connessione Desktop remoto e connettersi al kit di sviluppo. Immettere **AzureStack\AzureStackAdmin** come il nome utente e la password amministrativa fornito durante l'impostazione dello Stack di Azure.  

2. Dal computer kit di sviluppo, aprire Server Manager, fare clic su **Server locale**, disattivare sicurezza avanzata di Internet Explorer e quindi chiudere Server Manager.

3. Per aprire il portale, passare a (https://portal.local.azurestack.external/) e accedere utilizzando le credenziali utente.


## <a name="connect-to-azure-stack-with-vpn"></a>Connettersi a Azure Stack con VPN

È possibile stabilire un tunnel split connessione di rete privata virtuale (VPN, Virtual Private Network) a un Kit di sviluppo dello Stack di Azure. Tramite la connessione VPN, è possibile accedere al portale di amministrazione, portale per gli utenti e installato localmente strumenti quali Visual Studio e PowerShell per gestire le risorse di Azure Stack. La connettività VPN è supportata in entrambi Directory(AAD) attiva di Azure e distribuzioni basate su Active Directory Federation Services(AD FS). Le connessioni VPN consentono più client di connettersi allo Stack di Azure nello stesso momento. 

> [!NOTE] 
> Questa connessione VPN non fornisce la connettività all'infrastruttura di Azure Stack macchine virtuali. 

### <a name="prerequisites"></a>Prerequisiti

* Installare [Stack Azure compatibile Azure PowerShell](azure-stack-powershell-install.md) nel computer locale.  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurare la connettività VPN

Per creare una connessione VPN al kit di sviluppo, aprire una sessione di PowerShell con privilegi elevata da un computer basato su Windows locale ed eseguire lo script seguente (assicurarsi di aggiornare il i valori di password e l'indirizzo IP per l'ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se il set di backup ha esito positivo, dovrebbe essere **azurestack** nell'elenco delle connessioni VPN.

![Connessioni di rete](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Connettersi all'istanza dello Stack di Azure utilizzando uno dei due metodi seguenti:  

* Tramite il `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando richiesto, considerare attendibile l'host dello Stack di Azure e installare il certificato da **AzureStackCertificateAuthority** nell'archivio certificati del computer locale. (il prompt dei comandi potrebbe essere visualizzato dietro la finestra della sessione PowerShell). 

* Aprire il computer locale **le impostazioni di rete** > **VPN** > fare clic su **azurestack** > **connettersi**. Al prompt di accesso, immettere il nome utente (AzureStack\AzureStackAdmin) e la password.

### <a name="test-the-vpn-connectivity"></a>Testare la connettività VPN

Per testare la connessione del portale, aprire un browser Internet e passare al portale per gli utenti (https://portal.local.azurestack.external/), accedere e creare risorse.  

## <a name="next-steps"></a>Passaggi successivi



