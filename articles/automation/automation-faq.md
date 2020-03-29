---
title: Domande frequenti sull'automazione di Azure Documenti Microsoft
description: Risposte alle domande frequenti sull'automazione di Azure.Answers to frequently asked questions about Azure Automation.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925813"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su Automazione di AzureAzure Automation frequently asked questions

Queste domande frequenti su Microsoft sono un elenco di domande frequenti sull'automazione di Azure.This Microsoft FAQ is a list of commonly asked questions about Azure Automation. Se hai altre domande sulle sue capacità, vai al forum di discussione e pubblica le tue domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="update-management-solution"></a>Soluzione Gestione aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello di sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, gli aggiornamenti a livello di sistema operativo potrebbero verificarsi tramite pacchetti. Questo può causare esecuzioni di Gestione aggiornamenti in cui viene modificato il numero di versione del sistema operativo. Poiché Gestione aggiornamenti utilizza gli stessi metodi per aggiornare i pacchetti che un amministratore utilizzerebbe localmente nel computer Linux, questo comportamento è intenzionale.

Per evitare di aggiornare la versione del sistema operativo tramite le distribuzioni di Gestione aggiornamenti, utilizzare la funzionalità **di esclusione.**

In Red Hat Enterprise Linux il nome del pacchetto da escludere è redhat-release-server.x86_64.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché gli aggiornamenti critici/di sicurezza non vengono applicati?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione consente di filtrare gli aggiornamenti applicati al computer che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, alcuni aggiornamenti possono essere contrassegnati in Gestione aggiornamenti come aventi un impatto sulla sicurezza, anche se il computer locale non dispone di tali informazioni. Di conseguenza, se si applicano aggiornamenti critici a un computer Linux, potrebbero essere presenti aggiornamenti che non sono contrassegnati come con un impatto sulla sicurezza su tale computer e pertanto gli aggiornamenti non vengono applicati. Tuttavia, gestione aggiornamenti potrebbe ancora segnalare tale computer come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti tramite la classificazione degli aggiornamenti non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, la selezione *solo* di **altri aggiornamenti** come classificazione può causare l'installazione anche di alcuni aggiornamenti della sicurezza se gli aggiornamenti della sicurezza relativi a zypper (gestione pacchetti) o le relative dipendenze sono necessari per primi. Si tratta di una limitazione di zypper. In alcuni casi, potrebbe essere necessario eseguire nuovamente la distribuzione degli aggiornamenti. Per verificare, controllare il log di aggiornamento.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra tenant di Azure?

Se si dispone di computer in un altro tenant di Azure che segnalano a Gestione aggiornamenti che è necessario applicare la patch, è necessario usare la soluzione seguente per pianificarli. È possibile usare il cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) con l'opzione `-ForUpdate` per creare una pianificazione e usare il cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passare i computer nell'altro tenant al `-NonAzureComputer` parametro. L'esempio seguente illustra come farlo:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passaggi successivi

Se la tua domanda non trova risposta qui, puoi fare riferimento al seguente forum per ulteriori domande e risposte.

- [Automazione di AzureAzure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

Per un feedback generale sulla soluzione di gestione degli aggiornamenti, visitare il forum di [commenti e suggerimenti](https://feedback.azure.com/forums/905242-update-management).