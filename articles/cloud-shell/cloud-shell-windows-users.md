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
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861222"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell in Azure Cloud Shell per utenti Windows

Nel mese di maggio 2018, le modifiche hanno [annunciato](https://azure.microsoft.com/blog/pscloudshellrefresh/) PowerShell in Azure Cloud Shell.  L'esperienza di PowerShell in Azure Cloud Shell è ora PowerShell Core 6 in Linux.
Con questa modifica, alcuni aspetti di PowerShell in Cloud Shell possono essere diversi da quelli previsti in Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Maiuscole/minuscole

In Windows, il file system non fa distinzione tra maiuscole e minuscole.  In Windows, il file system fa distinzione tra maiuscole e minuscole.
Ciò significa che in precedenza `file.txt` e `FILE.txt` erano considerati lo stesso file, mentre adesso sono considerati file diversi.
Le maiuscole/minuscole devono essere usate in modo appropriato durante il completamento di `tab` nel file system.  Alcune esperienze specifiche di PowerShell, ad esempio i cmdlet `tab`, non fanno distinzione tra maiuscole e minuscole. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Alias di Windows PowerShell a confronto con le utility di Linux

I comandi esistente in Linux, ad esempio `ls`, `sort` e `sleep`, hanno la precedenza sui relativi alias di PowerShell.  Di seguito sono riportati alias comunemente rimossi, assieme ai comandi equivalenti:  

|Alias rimossi   |Comando equivalente   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Persistenza $home vs $home\clouddrive

Per gli utenti con script e altri file persistenti nell'unità cloud, la directory $HOME ora è persistente tra le sessioni.

## <a name="powershell-profile"></a>Profilo di PowerShell

Per impostazione predefinita, non viene creato alcun profilo di PowerShell.  Per creare il profilo, creare una directory `PowerShell` sotto `$HOME/.config`.  In `$HOME/.config/PowerShell` è possibile creare il profilo con il nome `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Novità in PowerShell Core 6

Per altre informazioni sulle nuova funzionalità in PowerShell Core 6, fare riferimento alla [documentazione PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) e al post del blog [Introduzione a PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
