---
title: "Funzionalità di PowerShell in Azure Cloud Shell (anteprima) | Documentazione Microsoft"
description: "Panoramica delle funzionalità di PowerShell in Azure Cloud Shell"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: 03706914db2b6b09ff31d5ed4b25871e7a53298a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Funzionalità e strumenti per PowerShell in Azure Cloud Shell (anteprima)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Strumenti e funzionalità è disponibile anche per [Bash](features.md).

PowerShell in Cloud Shell (anteprima) viene eseguito in `Windows Server 2016`.

## <a name="features"></a>Funzionalità

### <a name="secure-automatic-authentication"></a>Autenticazione automatica sicura

PowerShell in Cloud Shell (anteprima) autentica in modo sicuro e automatico l'accesso agli account per Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>File di persistenza tra le sessioni

Per rendere persistenti i file fra le sessioni, Cloud Shell illustra come associare una condivisione di File di Microsoft Azure al primo avvio.
Al termine, Cloud Shell assocerà automaticamente la risorsa di archiviazione (montata come `$home\clouddrive`) per tutte le sessioni future.
Poiché ogni richiesta per Cloud Shell viene allocata su un computer temporaneo, i file esterni a `$home\clouddrive` e lo stato del computer non sono mantenuti tra le sessioni.

[Altre informazioni sul collegamento delle condivisioni di file di Azure a Cloud Shell](persisting-shell-storage-powershell.md).

### <a name="azure-drive-azure"></a>Unità Azure (Azure:)

PowerShell in Cloud Shell (anteprima) permette di avviare l'unità Azure (`Azure:`).
L’unità Azure consente una facile individuazione e la navigazione tra le risorse di Azure, ad es. calcolo, rete, archiviazione ecc. analogamente alla navigazione di file system.
È possibile continuare a utilizzare i consueti [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure) per gestire queste risorse.
Tutte le modifiche apportate alle risorse di Azure, sia se eseguite direttamente nel portale di Azure che tramite i cmdlet di Azure PowerShell, vengono applicate immediatamente anche all'unità Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Consapevolezza contestuale
- **Ambito del gruppo di risorse**: quando all'interno del contesto di un percorso del gruppo di risorse nell’unità Azure (`Azure:`), il nome del gruppo di risorse viene passato automaticamente al cmdlet di Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: questo cmdlet permette di visualizzare l'elenco dei comandi applicabili nel contesto della posizione nell’unità Azure (`Azure:`). Ad esempio, mostra solo i comandi correlati all’archiviazione quando l'utente è in `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Modifica dello script esteso PowerShell

Quando si utilizza VIM per modificare i file PowerShell (`.ps1,.psm1,.psd1`), si ottengono automaticamente l'evidenziazione della sintassi e il supporto IntelliSense.
Il supporto IntelliSense è implementato tramite un plugin vim che interagisce con un'istanza locale di [servizi editor PowerShell](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Utilizzare `TAB` per ottenere il completamento (IntelliSense) nei nomi di comando, i nomi dei parametri e i valori di parametro (se applicabile).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Modello estendibile

Utilizzando [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), è possibile facilmente installare (e aggiornare) moduli e script personalizzati dalla [PowerShell Gallery](https://www.powershellgallery.com).
Dopo l'installazione, i moduli vengono resi automaticamente persistenti tra le sessioni di Cloud Shell.

> [!TIP]
> I moduli installati dagli utenti vengono salvati nella cartella `$Home\CloudDrive\.pscloudshell\WindowsPowerShell`. Viene creato un collegamento simbolico a questa cartella nella cartella dei documenti dell'utente (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Gestione delle macchine virtuali Guest

Utilizzando i due comandi incorporati (`Enter-AzureRmVM` e `Invoke-AzureRmVMCommand`) è possibile gestire in remoto le macchine virtuali di Azure.
Questi comandi si basano sulla comunicazione remota di PowerShell e richiedono la connettività PowerShell per le macchine virtuali di Azure.

## <a name="tools"></a>Strumenti

|**Categoria**    |**Nome**                                 |
|----------------|-----------------------------------------|
|Strumenti di Azure     |[Azure PowerShell (5.2.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Interfaccia della riga di comando di Azure (2.0.26)](https://docs.microsoft.com/cli/azure)|
|Editor di testo    |vim<br> nano                             |
|Gestione pacchetti |PowerShellGet<br> Modulo PackageManagement<br> npm<br> pip |
|Controllo del codice sorgente  |git                                      |
|Database       |[Modulo SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [Utilità sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Strumenti di test      |Pester                                   |

## <a name="language-support"></a>Supporto per le lingue

|**Lingua**|**Versione**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 e [6.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido con PowerShell in Cloud Shell (anteprima)](quickstart-powershell.md)

[Informazioni su Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
