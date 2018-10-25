---
title: Connettersi ad Azure Stack | Microsoft Docs
description: Informazioni su come connettersi ad Azure Stack.
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
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49993170"
---
# <a name="connect-to-azure-stack-development-kit"></a>Connettersi a Azure Stack Development Kit

*Si applica a: Azure Stack Development Kit*

Per gestire le risorse, è innanzitutto necessario connettersi a Azure Stack Development Kit. In questo articolo si descrivono i passaggi da eseguire per connettersi al kit di sviluppo. È possibile usare una delle opzioni di connessione seguente:

* [Connessione Desktop remoto](#connect-with-remote-desktop). Quando ci si connette tramite connessione Desktop remoto, un singolo utente può connettersi rapidamente al kit di sviluppo.
* [Rete privata virtuale (VPN)](#connect-with-vpn). Quando ci si connette tramite una VPN, più utenti possono connettersi contemporaneamente da client esterni all'infrastruttura di Azure Stack. Una connessione VPN richiede alcune configurazioni.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Connettersi ad Azure Stack tramite connessione Desktop remoto

Un singolo utente simultaneo possa gestire le risorse nel portale di operatore o il portale per gli utenti tramite connessione Desktop remoto.

1. Aprire connessione Desktop remoto e connettersi al kit di sviluppo. Per il nome utente, immettere **AzureStack\AzureStackAdmin**. Usare la password di operatore specificato durante la configurazione di Azure Stack.  

2. Nel computer di kit di sviluppo, aprire Server Manager. Selezionare **Server locale**deselezionare la **Internet Explorer Enhanced Security** casella di controllo e quindi chiudere Server Manager.

3. Per aprire la [portale per gli utenti](azure-stack-key-features.md#portal), passare a https://portal.local.azurestack.external/. Accedere usando le credenziali dell'utente. Per aprire Azure Stack [portale operator](azure-stack-key-features.md#portal), passare a https://adminportal.local.azurestack.external/. Accedere usando le credenziali di Azure Active Directory (Azure AD) specificata durante l'installazione.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Connettersi ad Azure Stack con VPN

È possibile stabilire una connessione VPN a un database di Azure Stack Development Kit di split tunneling. È possibile utilizzare una connessione VPN per accedere al portale di operatore di Azure Stack, il portale per gli utenti e gli strumenti installati in locale, ad esempio Visual Studio e PowerShell per gestire le risorse di Azure Stack. La connettività VPN è supportata in Azure AD e le distribuzioni di Active Directory Federation Services (ADFS). Le connessioni VPN rendono possibili per più client per connettersi ad Azure Stack nello stesso momento.

> [!NOTE]
> Una connessione VPN non fornisce la connettività all'infrastruttura di Azure Stack le macchine virtuali.

### <a name="prerequisites"></a>Prerequisiti

1. Installare [Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md) nel computer locale.  
2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Configurare la connettività VPN

Per creare una connessione VPN al kit di sviluppo, aprire Windows PowerShell come amministratore nel computer locale basato su Windows. Quindi, eseguire lo script seguente (i valori di indirizzo IP e la password per l'ambiente di aggiornamento):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se il programma di installazione ha esito positivo, **azurestack** viene visualizzato nell'elenco delle connessioni VPN.

![Connessioni di rete](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Connettersi all'istanza di Azure Stack usando uno dei metodi seguenti:  

* Usare il `Connect-AzsVpn ` comando:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando richiesto, considerare attendibile l'host di Azure Stack e installare il certificato dal **AzureStackCertificateAuthority** nell'archivio certificati del computer locale. (La richiesta potrebbe essere nascosta dalla finestra di PowerShell).

* Nel computer locale, selezionare **le impostazioni di rete** > **VPN** > **azurestack** > **connettersi**. Al prompt di accesso, immettere il nome utente (**AzureStack\AzureStackAdmin**) e la password.

### <a name="test-vpn-connectivity"></a>Testare la connettività VPN

Per testare la connessione del portale, aprire un web browser e quindi passare al portale utenti (https://portal.local.azurestack.external/) o il portale di operatore (https://adminportal.local.azurestack.external/). Accedere e creare le risorse.  

## <a name="next-steps"></a>Passaggi successivi

[Rendere disponibili agli utenti di Azure Stack le macchine virtuali](azure-stack-tutorial-tenant-vm.md)
