---
title: Reimpostare la password o la configurazione di Desktop remoto in una VM Windows in Azure | Microsoft Docs
description: Informazioni su come reimpostare la password di un account o i servizi Desktop remoto in una VM Windows creata usando il modello di distribuzione classico tramite il portale di Azure o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 2a38c792decdc571421c6bed7d2edc5f442591ca
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una VM Windows creata usando il modello di distribuzione classico
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. È anche possibile [eseguire questa procedura per le VM create con il modello di distribuzione di Resource Manager](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Se non è possibile connettersi a una macchina virtuale Windows, è possibile reimpostare la password di amministratore locale o la configurazione del servizio Desktop remoto. È possibile usare il portale di Azure o l'estensione di accesso alla VM in Azure PowerShell per reimpostare la password.

## <a name="ways-to-reset-configuration-or-credentials"></a>Modalità per ripristinare la configurazione o le credenziali
È possibile reimpostare i Servizi Desktop remoto e le credenziali in modi diversi, in base alle esigenze specifiche:

- [Ripristinare con il Portale di Azure](#azure-portal)
- [Ripristinare con Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portale di Azure
È possibile reimpostare il servizio Desktop remoto dal [portale di Azure](https://portal.azure.com). Per espandere il menu del portale, fare clic sulle tre barre nell'angolo superiore sinistro e quindi fare clic su **Macchine virtuali (versione classica)**:

![Cercare la macchina virtuale di Azure](./media/reset-rdp/Portal-Select-Classic-VM.png)

Selezionare la macchina virtuale Windows e quindi fare clic su **Reimposta accesso remoto**. Per reimpostare la configurazione di Desktop remoto viene visualizzata la finestra di dialogo seguente:

![Ripristinare la pagina di configurazione RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

È anche possibile reimpostare il nome utente e la password dell'account amministratore locale. Dalla macchina virtuale fare clic su **Supporto e risoluzione dei problemi** > **Reimposta password**. Viene visualizzato il pannello per la reimpostazione della password:

![Pagina di reimpostazione della password](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Dopo aver immesso il nuovo nome utente e la nuova password, fare clic su **Salva**.

## <a name="vmaccess-extension-and-powershell"></a>Estensione VMAccess e PowerShell
Assicurarsi che l'agente di macchine virtuali sia installato nella macchina virtuale. Per usare l'agente di macchine virtuali è sufficiente che quest'ultimo sia disponibile. Non è necessario che sia installata l'estensione VMAccess. Verificare che l'agente di macchine virtuali sia già installato usando il comando seguente. Sostituire "myCloudService" e "myVM" con i nomi rispettivamente del servizio cloud e della VM. Per individuare questi nomi eseguire `Get-AzureVM` senza parametri.

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Se il comando **write-host** restituisce **True**, l'agente di macchine virtuali è installato. Se restituisce **False**, nel post di blog di Azure relativo all' [agente di macchine virtuali ed estensioni, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) sono disponibili istruzioni e un collegamento per il download.

Se la macchina virtuale è stata creata mediante il portale, controllare se `$vm.GetInstance().ProvisionGuestAgent` restituisce **True**. In caso contrario, è possibile impostarla usando questo comando:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Questo comando, durante l'esecuzione del comando **Set-AzureVMExtension** nei passaggi successivi, evita l'errore relativo alla necessità di abilitare l'agente guest di provisioning sull'oggetto VM prima della configurazione dell'estensione di accesso alle VM IaaS.

### <a name="reset-the-local-administrator-account-password"></a>**Reimpostare una password dell'account amministratore locale**
Creare credenziali di accesso con il nome dell'account amministratore locale attuale e una nuova password, quindi eseguire `Set-AzureVMAccessExtension` come segue.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Se si digita un nome diverso rispetto all'account attuale, l'estensione VMAccess assegnerà un nuovo nome all'account amministratore locale, assegnerà la password a tale account ed effettuerà la disconnessione da Desktop remoto. Se l'account amministratore locale è disabilitato, l'estensione VMAccess lo abiliterà.

Questi comandi reimpostano anche la configurazione del servizio Desktop remoto.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Reimpostare la configurazione del servizio Desktop remoto**
Per reimpostare la configurazione del servizio Desktop remoto, eseguire il comando seguente.

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

L'estensione VMAccess esegue due comandi sulla macchina virtuale:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Questo comando abilita il gruppo predefinito Windows Firewall, che consente il traffico in ingresso di Desktop remoto, che usa la porta TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Questo comando imposta il valore fDenyTSConnections del Registro di sistema su 0, abilitando le connessioni a Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi
Se l'estensione di accesso alla VM di Azure non risponde ed è impossibile reimpostare la password, [reimpostare la password di Windows locale offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Questo metodo è un processo più avanzato e richiede di connettere il disco rigido virtuale della VM problematica a un'altra VM. Seguire prima i passaggi illustrati in questo articolo e provare a reimpostare la password offline solo come ultima risorsa.

[Estensioni VM e funzionalità di Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

