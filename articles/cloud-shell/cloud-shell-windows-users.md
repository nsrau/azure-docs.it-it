---
title: Azure Cloud Shell per gli utenti Windows | Microsoft Docs
description: Guida per gli utenti che non hanno familiarità con i sistemi Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 109067899884c04cbd283b5eef1d7e0089556b07
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301727"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell in Azure Cloud Shell per utenti Windows

Nel mese di maggio 2018, le modifiche hanno [annunciato](https://azure.microsoft.com/blog/pscloudshellrefresh/) PowerShell in Azure Cloud Shell.
L'esperienza di PowerShell in Azure Cloud Shell ora esegue [PowerShell Core 6](https://github.com/powershell/powershell) in un ambiente Linux.
Con questa modifica, potrebbero esserci alcune differenze nell'esperienza di PowerShell in Cloud Shell rispetto a un'esperienza Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Distinzione tra maiuscole e minuscole nel file system

In Windows il file system non fa distinzione tra maiuscole e minuscole mentre in Linux sì.
In precedenza `file.txt` e `FILE.txt` erano considerati lo stesso file, mentre adesso sono considerati file diversi.
Le maiuscole/minuscole devono essere usate in modo appropriato durante le operazioni di `tab-completing` nel file system.
Alcune esperienze specifiche di PowerShell, ad esempio le operazioni di `tab-completing` di nomi cmdlet, parametri e valori non fanno distinzione tra maiuscole e minuscole.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Alias di Windows PowerShell a confronto con le utility di Linux

Alcuni alias di PowerShell esistenti hanno gli stessi nomi dei comandi Linux predefiniti, ad esempio,, `cat` , `ls` `sort` `sleep` e così via. In PowerShell Core 6 sono stati rimossi gli alias che entrano in conflitto con i comandi predefiniti di Linux.
Di seguito sono riportati gli alias comuni che sono stati rimossi, assieme ai comandi equivalenti:  

|Alias rimossi   |Comando equivalente   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Persistenza $HOME

In precedenza gli utenti potevano rendere persistenti solo gli script e altri file nell'unità cloud.
Ora la directory $HOME dell'utente è persistente anche tra le sessioni.

## <a name="powershell-profile"></a>Profilo di PowerShell

Per impostazione predefinita, non viene creato alcun profilo di PowerShell dell'utente.
Per creare il profilo, creare una directory `PowerShell` sotto `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

In `$HOME/.config/PowerShell` è possibile creare i file di profilo `profile.ps1` e/o `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Novità in PowerShell Core 6

Per altre informazioni sulle nuove funzionalità in PowerShell Core 6, fare riferimento alla [documentazione PowerShell](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1) e al post del blog [Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) (Introduzione a PowerShell Core).