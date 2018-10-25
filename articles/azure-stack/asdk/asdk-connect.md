---
title: Connettersi ad Azure Stack | Microsoft Docs
description: Informazioni su come connettersi al ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026931"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Connettersi a Azure Stack Development Kit

Per gestire le risorse, è innanzitutto necessario connettersi a Azure Stack Development Kit (ASDK). In questo articolo si descrivono i passaggi da eseguire per connettersi al ASDK. È possibile usare una delle opzioni di connessione seguente:

* [Connessione Desktop remoto](#connect-with-remote-desktop). Quando ci si connette tramite connessione Desktop remoto, un singolo utente può connettersi rapidamente al kit di sviluppo.
* [Rete privata virtuale (VPN)](#connect-with-vpn). Quando ci si connette tramite una VPN, più utenti possono connettersi contemporaneamente da client esterni all'infrastruttura di Azure Stack. Una connessione VPN richiede alcune configurazioni.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Connettersi ad Azure Stack tramite connessione Desktop remoto

Un singolo utente simultaneo possa gestire le risorse nel portale di operatore o il portale per gli utenti tramite connessione Desktop remoto.

1. Aprire connessione Desktop remoto (mstc.exe) e connettersi al computer host del kit di sviluppo come **AzureStack\AzureStackAdmin** usando la password specificata durante l'installazione ASDK.  

2. Nel computer host del kit di sviluppo, aprire Server Manager (ServerManager.exe). Selezionare **Server locale**, la disattivazione **configurazione sicurezza avanzata IE**, chiudere Server Manager.

3. Accedere al portale di amministrazione come **AzureStack\CloudAdmin** oppure utilizzare altre credenziali di Azure Stack operatore. È l'indirizzo del portale di amministrazione ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Accedere al portale per gli utenti come **AzureStack\CloudAdmin** oppure utilizzare altre credenziali utente in Azure Stack. È l'indirizzo del portale utente ASDK [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Per altre informazioni su quando usare l'account, vedere [nozioni fondamentali sull'amministrazione ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Connettersi ad Azure Stack con VPN

È possibile stabilire una connessione VPN a un ASDK per accedere ai portali di Azure Stack e gli strumenti installati in locale, ad esempio Visual Studio e PowerShell di split tunneling. Usa le connessioni VPN, più utenti di connettersi allo stesso tempo alle risorse di Azure Stack ospitato dal ASDK.

Connettività VPN è supportata per sia per Azure AD e Active Directory Federation Services (AD FS)-distribuzioni.

> [!NOTE]
> Una connessione VPN non fornisce la connettività all'infrastruttura di Azure Stack le macchine virtuali.

### <a name="prerequisites"></a>Prerequisiti
Prima di configurare una connessione VPN per il ASDK, verificare di che aver soddisfatto i prerequisiti seguenti.

- Installare [Azure Stack-compatibili con Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) nel computer locale.  
- Scaricare il [gli strumenti necessari per lavorare con Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Configurare la connettività VPN

Per creare una connessione VPN per il ASDK, aprire PowerShell come amministratore nel computer locale basato su Windows. Quindi, eseguire lo script seguente (i valori di indirizzo IP e la password per l'ambiente di aggiornamento):

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![Connessioni di rete](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Connettersi ad Azure Stack

Connettersi all'istanza di Azure Stack usando uno dei metodi seguenti:  

* Usare il `Connect-AzsVpn ` comando:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando richiesto, considerare attendibile l'host di Azure Stack e installare il certificato dal **AzureStackCertificateAuthority** nell'archivio certificati del computer locale. 
  
  > [!IMPORTANT]
  > Il prompt dei comandi può essere nascosto da finestra di PowerShell.

* Nel computer locale, selezionare **le impostazioni di rete** > **VPN** > **azurestack** > **connettersi**. Al prompt di accesso, immettere il nome utente (**AzureStack\AzureStackAdmin**) e la password.

### <a name="test-vpn-connectivity"></a>Testare la connettività VPN

Per testare la connessione del portale, aprire un web browser e quindi passare al portale utenti (https://portal.local.azurestack.external/) o il portale di amministrazione (https://adminportal.local.azurestack.external/). Accedere e creare le risorse.  

## <a name="next-steps"></a>Passaggi successivi

[Risoluzione dei problemi](asdk-troubleshooting.md)
