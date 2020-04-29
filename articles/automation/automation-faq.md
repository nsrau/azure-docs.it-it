---
title: Domande frequenti su automazione di Azure | Microsoft Docs
description: Risposte alle domande frequenti su automazione di Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405957"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su automazione di Azure

Queste domande frequenti su Microsoft sono un elenco di domande frequenti su automazione di Azure. Per eventuali domande aggiuntive sulle funzionalità, visitare il forum di discussione e inserire le domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="update-management-solution"></a>Soluzione Gestione aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello del sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello di sistema operativo possono verificarsi tramite pacchetti. Questo potrebbe causare Gestione aggiornamenti esecuzioni in cui viene modificato il numero di versione del sistema operativo. Poiché Gestione aggiornamenti usa gli stessi metodi per aggiornare i pacchetti che un amministratore usa localmente in un computer Linux, questo comportamento è intenzionale.

Per evitare di aggiornare la versione del sistema operativo tramite Gestione aggiornamenti le distribuzioni, usare la funzionalità di **esclusione** .

In Red Hat Enterprise Linux, il nome del pacchetto da escludere è `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché non vengono applicati gli aggiornamenti critici/della sicurezza?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione consente di filtrare gli aggiornamenti che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, è possibile contrassegnare alcuni aggiornamenti in Gestione aggiornamenti con un effetto di sicurezza, anche se il computer locale non dispone di tali informazioni. Se si applicano aggiornamenti critici a un computer Linux, potrebbero esserci aggiornamenti che non sono contrassegnati come aventi un effetto di sicurezza sul computer e pertanto non sono applicati. Tuttavia, Gestione aggiornamenti possibile che il computer venga segnalato come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti tramite classificazione aggiornamenti non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, selezionando solo gli **altri aggiornamenti** come la classificazione può causare l'installazione di alcuni aggiornamenti della sicurezza aggiuntivi se gli aggiornamenti della sicurezza correlati a zypper (gestione pacchetti) o alle relative dipendenze sono necessari per primi. Si tratta di una limitazione di zypper. In alcuni casi, potrebbe essere necessario rieseguire la distribuzione degli aggiornamenti e quindi verificare la distribuzione tramite il log degli aggiornamenti.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra I tenant di Azure?

Se si dispone di computer che richiedono l'applicazione di patch in un'altra creazione di report di Azure tenant per Gestione aggiornamenti, è necessario usare una soluzione alternativa seguente per prepararli. È possibile usare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) con il `ForUpdateConfiguration` parametro specificato per creare una pianificazione. È possibile usare il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare le macchine nell'altro tenant al `NonAzureComputer` parametro. L'esempio seguente illustra come farlo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passaggi successivi

Se non è disponibile una risposta alla domanda, è possibile fare riferimento alle origini seguenti per ulteriori domande e risposte.

- [Automazione di Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [Forum di commenti e suggerimenti](https://feedback.azure.com/forums/905242-update-management)
