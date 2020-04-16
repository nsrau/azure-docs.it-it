---
title: Domande frequenti sull'automazione di Azure Documenti Microsoft
description: Risposte alle domande frequenti sull'automazione di Azure.Answers to frequently asked questions about Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405957"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su Automazione di AzureAzure Automation frequently asked questions

Queste domande frequenti su Microsoft sono un elenco di domande frequenti sull'automazione di Azure.This Microsoft FAQ is a list of commonly asked questions about Azure Automation. Se hai altre domande sulle sue capacità, vai al forum di discussione e pubblica le tue domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="update-management-solution"></a>Soluzione Gestione aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello di sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello di sistema operativo potrebbero verificarsi tramite pacchetti. Ciò potrebbe portare a corse di gestione degli aggiornamenti in cui cambia il numero di versione del sistema operativo. Poiché Gestione aggiornamenti utilizza gli stessi metodi per aggiornare i pacchetti che un amministratore utilizza localmente su un computer Linux, questo comportamento è intenzionale.

Per evitare di aggiornare la versione del sistema operativo tramite le distribuzioni di Gestione aggiornamenti, utilizzare la funzionalità **di esclusione.**

In Red Hat Enterprise Linux, il `redhat-release-server.x86_64`nome del pacchetto da escludere è .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché gli aggiornamenti critici/di sicurezza non vengono applicati?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione consente di filtrare gli aggiornamenti che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, è possibile contrassegnare alcuni aggiornamenti in Gestione aggiornamenti con un impatto sulla sicurezza, anche se il computer locale non dispone di tali informazioni. Se si applicano aggiornamenti critici a un computer Linux, potrebbero essere presenti aggiornamenti che non sono contrassegnati come aventi un impatto sulla sicurezza su tale computer e pertanto non vengono applicati. Tuttavia, gestione aggiornamenti potrebbe ancora segnalare il computer come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti tramite la classificazione degli aggiornamenti non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, selezionando SOLO **Altri aggiornamenti** come classificazione può causare l'installazione di alcuni aggiornamenti della sicurezza aggiuntivi se gli aggiornamenti della sicurezza relativi a zypper (gestione pacchetti) o le relative dipendenze sono necessari per primi. Si tratta di una limitazione di zypper. In alcuni casi, potrebbe essere necessario eseguire nuovamente la distribuzione dell'aggiornamento e quindi verificare la distribuzione tramite il log di aggiornamento.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra tenant di Azure?

Se si dispone di computer che necessitano di applicazione di patch in un altro tenant di Azure che segnala alla gestione degli aggiornamenti, è necessario usare una soluzione alternativa seguente per pianificarli. È possibile utilizzare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) con il `ForUpdateConfiguration` parametro specificato per creare una pianificazione. È possibile utilizzare il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare `NonAzureComputer` i computer nell'altro tenant al parametro. L'esempio seguente illustra come farlo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passaggi successivi

Se la tua domanda non trova risposta qui, puoi fare riferimento alle seguenti fonti per ulteriori domande e risposte.

- [Automazione di Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Forum di commenti e suggerimenti](https://feedback.azure.com/forums/905242-update-management)
