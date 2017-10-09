---
title: Risoluzione dei problemi di Azure Cloud Shell (anteprima) | Documentazione Microsoft
description: Risoluzione dei problemi di Azure Cloud Shell
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 32d4d4d10e5d8986e2dfe94430f52db8f038e245
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshooting-azure-cloud-shell"></a>Risoluzione dei problemi di Azure Cloud Shell
Le risoluzioni più conosciute per i problemi in Azure Cloud Shell includono:

## <a name="error-400-disallowedoperation"></a>Error: 400 DisallowedOperation
 - **Dettagli**: quando si usa una sottoscrizione di Azure Active Directory, non è possibile creare la risorsa di archiviazione.
 - **Risoluzione**: usare una sottoscrizione di Azure in grado di creare le risorse di archiviazione. Le sottoscrizioni di Azure Active Directory non sono in grado di creare le risorse di Azure.

## <a name="powershell-resolutions"></a>Risoluzioni di PowerShell

### <a name="no-home-directory-persistence"></a>Nessuna persistenza directory $Home
  - **Dettagli**: nessuna applicazione (ad esempio: git, vim e così via) che scrive dati in $Home verrà resa persistente tra le sessioni di PowerShell.
  - **Risoluzione**: nel proprio profilo PowerShell, creare un collegamento simbolico alla cartella specifica dell’applicazione in `clouddrive` per $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C non consente di uscire da un prompt Cmdlet
 - **Dettagli**: quando si prova a uscire dal prompt Cmdlet, `Ctrl+C` non consente di uscire dal prompt.
 - **Risoluzione**: per uscire dal prompt, premere `Ctrl+C` e quindi `Enter`.

### <a name="gui-applications-are-not-supported"></a>Le applicazioni GUI non sono supportate
  - **Dettagli**: se un utente avvia un'applicazione GUI, il prompt non ricompare.  Ad esempio, quando un utente utilizza `git` per chiudere un repository privato abilitato a due fattori di autenticazione, viene visualizzata una finestra di dialogo per il codice di autenticazione a due fattori.
  - **Risoluzione**: `Ctrl+C` per uscire dal comando.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help online non apre la pagina della Guida
 - **Dettagli**: se un utente digita `Get-Help Find-Module -online`, visualizza un messaggio di errore, ad esempio:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
 - **Risoluzione**: copiare l'url e aprirlo manualmente nella finestra del browser.
 
### <a name="troubleshooting-remote-management-of-azure-vms"></a>Risoluzione dei problemi di gestione remota delle macchine virtuali di Azure
 - **Dettagli**: a causa delle impostazioni predefinite di Windows Firewall per la gestione remota Windows, all'utente può comparire l'errore seguente: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
 - **Risoluzione**: assicurarsi che la macchina virtuale sia in esecuzione. È possibile eseguire `Get-AzureRmVM -Status` per conoscere lo stato della macchina virtuale.  Aggiungere quindi una nuova regola firewall nella macchina virtuale remota per consentire le connessioni di gestione remota Windows da una qualsiasi subnet, ad esempio,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 È possibile utilizzare [Azure custom script extension] [customex] per evitare l'accesso alla macchina virtuale remota per l'aggiunta della nuova regola firewall.
 È possibile salvare lo script precedente in un file, ad esempio `addfirerule.ps1`, e caricarlo nel proprio contenitore di archiviazione di Azure.
 Provare quindi il comando seguente:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```
