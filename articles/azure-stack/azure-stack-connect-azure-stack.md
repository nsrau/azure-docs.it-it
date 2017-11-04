---
title: Connettersi a Azure Stack | Documenti Microsoft
description: Informazioni su come connettersi allo Stack di Azure.
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
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: ba2359739b1d9cd265879227a499c94f93d8e4c6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

*Si applica a: Azure Stack Development Kit*

Per gestire le risorse, è necessario innanzitutto connettersi al Kit di sviluppo dello Stack di Azure. In questo articolo si descrivono i passaggi da eseguire per connettersi al kit di sviluppo. È possibile utilizzare una delle opzioni di connessione seguente:

* [Connessione Desktop remoto](#connect-with-remote-desktop). Quando ci si connette tramite connessione Desktop remoto, un singolo utente può connettersi rapidamente del kit di sviluppo.
* [Rete privata virtuale (VPN)](#connect-with-vpn). Quando ci si connette tramite una VPN, più utenti possono connettersi simultaneamente da client esterni all'infrastruttura di Azure Stack (richiede l'installazione).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Connettersi a Azure Stack utilizzando connessione Desktop remoto
Un singolo utente simultaneo può gestire le risorse nel portale di operatore o il portale per gli utenti tramite connessione Desktop remoto.

1. Aprire connessione Desktop remoto e connettersi al kit di sviluppo. Per il nome utente, immettere **AzureStack\AzureStackAdmin**. Utilizzare la password di operatore specificato durante l'impostazione dello Stack di Azure.  

2. Nel computer di kit di sviluppo, aprire Server Manager. Selezionare **Server locale**, deselezionare il **protezione avanzata di Internet Explorer** casella di controllo e quindi chiudere Server Manager.

3. Per aprire la [portale per gli utenti](azure-stack-key-features.md#portal), passare a https://portal.local.azurestack.external/. Accedere utilizzando le credenziali utente. Per aprire lo Stack di Azure [portale operatore](azure-stack-key-features.md#portal), passare a https://adminportal.local.azurestack.external/. Accedere utilizzando le credenziali di Azure Active Directory (Azure AD) specificato durante l'installazione.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Connettersi a Azure Stack utilizzando VPN

È possibile stabilire un tunnel split connessione VPN a un Kit di sviluppo dello Stack di Azure. È possibile utilizzare una connessione VPN per accedere al portale di operatore di Stack di Azure, il portale per gli utenti e istallati strumenti come Visual Studio e PowerShell per gestire le risorse di Azure Stack. La connettività VPN è supportata in Azure AD e nelle distribuzioni di Active Directory Federation Services (ADFS). Le connessioni VPN consentono più client di connettersi allo Stack di Azure nello stesso momento. 

> [!NOTE] 
> Una connessione VPN non fornisce la connettività all'infrastruttura di Azure Stack macchine virtuali. 

### <a name="prerequisites"></a>Prerequisiti

1. Installare [Azure Stack compatibili con Azure PowerShell](azure-stack-powershell-install.md) nel computer locale.  
2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Configurare la connettività VPN

Per creare una connessione VPN al kit di sviluppo, aprire Windows PowerShell come amministratore nel computer locale basati su Windows. Quindi, eseguire lo script seguente (aggiornare l'indirizzo IP e una password, i valori per l'ambiente):

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

Connettersi all'istanza dello Stack di Azure utilizzando uno dei metodi seguenti:  

* Utilizzare il `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando richiesto, considerare attendibile l'host dello Stack di Azure e installare il certificato da **AzureStackCertificateAuthority** nell'archivio certificati del computer locale. (La richiesta potrebbe essere nascosta dalla finestra di PowerShell). 

* Nel computer locale, selezionare **le impostazioni di rete** > **VPN** > **azurestack** > **connettersi**. Al prompt di accesso, immettere il nome utente (**AzureStack\AzureStackAdmin**) e la password.

### <a name="test-vpn-connectivity"></a>Testare la connettività VPN

Per testare la connessione del portale, aprire un web browser e andare al portale per gli utenti (https://portal.local.azurestack.external/) o il portale di operatore (https://adminportal.local.azurestack.external/). Eseguire l'accesso e creazione di risorse.  

## <a name="next-steps"></a>Passaggi successivi

[Rendere disponibili agli utenti di Azure Stack macchine virtuali](azure-stack-tutorial-tenant-vm.md)

