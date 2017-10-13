---
title: Reimpostare la password o la configurazione di Desktop remoto in una VM Windows | Microsoft Docs
description: Informazioni su come reimpostare la password di un account o i servizi Desktop remoto in una VM Windows tramite il portale di Azure o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: 19228fc57f09dee00eb7f1c72c560c7b27e825a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una VM Windows
Se non è possibile connettersi a una macchina virtuale Windows, è possibile reimpostare la password di amministratore locale o la configurazione del servizio Desktop remoto. È possibile usare il portale di Azure o l'estensione di accesso alla VM in Azure PowerShell per reimpostare la password. Se si usa PowerShell, verificare che il [modulo di PowerShell più recente sia installato e configurato](/powershell/azure/overview) e di avere eseguito l'accesso alla sottoscrizione di Azure. È anche possibile [eseguire questi passaggi per le macchine virtuali create con il modello di distribuzione classica](reset-rdp.md).

## <a name="ways-to-reset-configuration-or-credentials"></a>Modalità per ripristinare la configurazione o le credenziali
È possibile reimpostare i Servizi Desktop remoto e le credenziali in modi diversi, in base alle esigenze specifiche:

- [Ripristinare con il Portale di Azure](#azure-portal)
- [Ripristinare con Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portale di Azure
Per espandere il menu del portale, fare clic sulle tre barre nell'angolo superiore sinistro e quindi fare clic su **Macchine virtuali**:

![Cercare la macchina virtuale di Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare una password dell'account amministratore locale**

Selezionare la macchina virtuale Windows, quindi fare clic su **Supporto e risoluzione dei problemi** > **Reimposta password**. Viene visualizzato il pannello per la reimpostazione della password:

![Pagina di reimpostazione della password](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Immettere il nome utente e una nuova password, quindi fare clic su **Aggiorna**. Provare a connettersi di nuovo alla macchina virtuale.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Reimpostare la configurazione del servizio Desktop remoto**

Selezionare la macchina virtuale Windows, quindi fare clic su **Supporto e risoluzione dei problemi** > **Reimposta password**. Viene visualizzato il pannello per la reimpostazione della password. 

![Ripristinare la configurazione RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Selezionare **Reset configuration only** (Ripristina solo la configurazione) dal menu a discesa, quindi fare clic su **Aggiorna**. Provare a connettersi di nuovo alla macchina virtuale.


## <a name="vmaccess-extension-and-powershell"></a>Estensione VMAccess e PowerShell
Verificare che il [modulo di PowerShell più recente sia installato e configurato](/powershell/azure/overview) e di avere eseguito l'accesso alla sottoscrizione di Azure con il cmdlet `Login-AzureRmAccount`.

### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare una password dell'account amministratore locale**
Ripristinare la password o il nome utente di amministratore usando il cmdlet di PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). Creare le credenziali dell'account come segue:

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> Se si digita un nome diverso rispetto all'account di amministratore locale attuale sulla macchina virtuale, l'estensione VMAccess assegnerà un nuovo nome all'account amministratore locale, assegnerà la password specificata a tale account ed effettuerà la disconnessione da Desktop remoto. Se l'account amministratore locale sulla macchina virtuale è disabilitato, l'estensione VMAccess lo abilita.

Nell'esempio seguente le credenziali della macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup` vengono aggiornate alle credenziali specificate.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**Reimpostare la configurazione del servizio Desktop remoto**
Reimpostare l'accesso remoto alla macchina virtuale con il cmdlet di PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). Nell'esempio seguente viene ripristinata l'estensione di accesso denominata `myVMAccess` nella macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> In qualsiasi momento una VM può avere un solo agente di accesso. Per impostare le proprietà dell'agente di accesso alla macchina virtuale, è possibile usare l'opzione `-ForceRerun`. Quando si usa `-ForceRerun`, assicurarsi di usare lo stesso nome per l'agente di accesso alla macchina virtuale impostato nei comandi precedenti.

Se non è ancora possibile connettersi in remoto alla macchina virtuale, vedere altre procedure da provare in [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="next-steps"></a>Passaggi successivi
Se l'estensione di accesso alla VM di Azure non risponde ed è impossibile reimpostare la password, [reimpostare la password di Windows locale offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questo metodo è un processo più avanzato e richiede di connettere il disco rigido virtuale della VM problematica a un'altra VM. Seguire prima i passaggi illustrati in questo articolo e provare a reimpostare la password offline solo come ultima risorsa.

[Estensioni VM e funzionalità di Azure](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

