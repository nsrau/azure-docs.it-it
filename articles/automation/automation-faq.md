---
title: Domande frequenti su automazione di Azure | Microsoft Docs
description: Risposte alle domande frequenti su automazione di Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925813"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su automazione di Azure

Queste domande frequenti su Microsoft sono un elenco di domande frequenti su automazione di Azure. Per eventuali domande aggiuntive sulle funzionalità, visitare il forum di discussione e inserire le domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="update-management-solution"></a>Soluzione Gestione aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello del sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello di sistema operativo possono verificarsi tramite pacchetti. Questo può causare esecuzioni di Gestione aggiornamenti in cui viene modificato il numero di versione del sistema operativo. Poiché Gestione aggiornamenti usa gli stessi metodi per aggiornare i pacchetti che un amministratore utilizzerebbe localmente nel computer Linux, questo comportamento è intenzionale.

Per evitare di aggiornare la versione del sistema operativo tramite Gestione aggiornamenti le distribuzioni, usare la funzionalità di **esclusione** .

In Red Hat Enterprise Linux il nome del pacchetto da escludere è redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché non vengono applicati gli aggiornamenti critici/della sicurezza?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione consente di filtrare gli aggiornamenti applicati al computer che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, alcuni aggiornamenti possono essere contrassegnati Gestione aggiornamenti con un effetto di sicurezza, anche se il computer locale non dispone di tali informazioni. Di conseguenza, se si applicano aggiornamenti critici a un computer Linux, potrebbero essere presenti aggiornamenti che non sono contrassegnati come aventi un effetto di sicurezza sul computer e pertanto gli aggiornamenti non vengono applicati. Tuttavia, Gestione aggiornamenti possibile che il computer venga segnalato come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti tramite classificazione aggiornamenti non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, la selezione di *solo* **altri aggiornamenti** come la classificazione può causare l'installazione di alcuni aggiornamenti della sicurezza anche se sono necessari prima gli aggiornamenti della sicurezza relativi a zypper (gestione pacchetti) o alle relative dipendenze. Si tratta di una limitazione di zypper. In alcuni casi, potrebbe essere necessario eseguire di nuovo la distribuzione degli aggiornamenti. Per verificare, controllare il log di aggiornamento.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra I tenant di Azure?

Se si dispone di computer in un altro tenant di Azure per Gestione aggiornamenti che è necessario applicare patch, è necessario usare la soluzione alternativa seguente per prepararli. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con l'opzione `-ForUpdate` per creare una pianificazione e usare il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare le macchine nell'altro tenant al parametro `-NonAzureComputer`. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passaggi successivi

Se la domanda non è disponibile, è possibile fare riferimento al forum seguente per ulteriori domande e risposte.

- [Automazione di Azure](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Per commenti e suggerimenti generali sulla soluzione Gestione aggiornamenti, visitare il [Forum dei commenti](https://feedback.azure.com/forums/905242-update-management).