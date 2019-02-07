---
title: Connettersi ad Azure Stack | Microsoft Docs
description: Informazioni su come connettere Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 4bc4387dd56de96edd17fcd8e5672cfda96cf67b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816408"
---
# <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Per gestire le risorse, è necessario connettersi a Azure Stack Development Kit. Questo articolo descrive in dettaglio i passaggi necessari per connettersi al kit di sviluppo. È possibile usare una delle opzioni di connessione seguente:

* Desktop remoto: consente a un singolo utente simultaneo di connettersi rapidamente nel Kit di sviluppo.
* Rete privata virtuale (VPN): consente a più utenti simultanei di connettersi da client esterni all'infrastruttura di Azure Stack (richiede la configurazione).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Connettersi ad Azure Stack con Desktop remoto
Con una connessione Desktop remoto, un singolo utente simultaneo possibile collaborare con il portale per gestire le risorse.

1. Aprire una connessione Desktop remoto e connettersi al kit di sviluppo. Immettere **AzureStack\AzureStackAdmin** come il nome utente e la password amministrativa fornito durante l'installazione di Azure Stack.  

2. Dal computer di sviluppo kit, aprire Server Manager, fare clic su **Server locale**, disattivare sicurezza avanzata di Internet Explorer e quindi chiudere Server Manager.

3. Per aprire il portale, passare a (https://portal.local.azurestack.external/) e accedere usando le credenziali dell'utente.


## <a name="connect-to-azure-stack-with-vpn"></a>Connettersi ad Azure Stack con VPN

È possibile stabilire una connessione di rete privata virtuale (VPN) per un database di Azure Stack Development Kit di split tunneling. Tramite la connessione VPN, è possibile accedere al portale di amministratore, portale per gli utenti e installato localmente strumenti quali Visual Studio e PowerShell per gestire le risorse di Azure Stack. Connettività VPN è supportata in entrambe le directory (Azure Active aAd) e distribuzioni basate su Active Directory Federation Services(AD FS). Le connessioni VPN consentono più client di connettersi ad Azure Stack nello stesso momento. 

> [!NOTE] 
> Questa connessione VPN non fornisce la connettività all'infrastruttura di Azure Stack le macchine virtuali. 

### <a name="prerequisites"></a>Prerequisiti

* Installare [compatibile con Azure PowerShell di Azure Stack](azure-stack-powershell-install.md) nel computer locale.  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configurare la connettività VPN

Per creare una connessione VPN al kit di sviluppo, aprire una sessione di PowerShell con privilegi elevata nel computer locale basato su Windows ed eseguire lo script seguente (assicurarsi di aggiornare i valori IP indirizzo e la password per l'ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

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

Se la configurazione ha esito positivo, si noterà **azurestack** nell'elenco delle connessioni VPN.

![Connessioni di rete](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Connettersi all'istanza di Azure Stack usando uno dei due metodi seguenti:  

* Tramite il `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando richiesto, considerare attendibile l'host di Azure Stack e installare il certificato dal **AzureStackCertificateAuthority** nell'archivio certificati del computer locale. (la richiesta potrebbe essere visualizzato dietro la finestra della sessione PowerShell). 

* Aprire nel computer locale **le impostazioni di rete** > **VPN** > fare clic su **azurestack** > **connettersi**. Al prompt di accesso, immettere il nome utente (AzureStack\AzureStackAdmin) e la password.

### <a name="test-the-vpn-connectivity"></a>Testare la connettività VPN

Per testare la connessione del portale, aprire un browser Internet e passare al portale utenti (https://portal.local.azurestack.external/), accedere e creare le risorse.  

## <a name="next-steps"></a>Passaggi successivi



