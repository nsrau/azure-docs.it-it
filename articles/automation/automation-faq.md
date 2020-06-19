---
title: Domande frequenti su Automazione di Azure | Microsoft Docs
description: Questo articolo contiene le risposte alle domande frequenti su Automazione di Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 1cd20e28d1b36167154059adf728a9cfdf8102bc
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836618"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su Automazione di Azure

Questo articolo contiene un elenco di domande frequenti Microsoft su Automazione di Azure. Per altre domande sulle funzionalità di questo servizio, visitare il forum di discussione. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="update-management"></a>Gestione degli aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello del sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, è possibile che gli aggiornamenti a livello di sistema operativo vengano eseguiti tramite pacchetti. Di conseguenza potrebbero verificarsi esecuzioni di Gestione aggiornamenti in cui il numero di versione del sistema operativo cambia. Poiché Gestione aggiornamenti usa gli stessi metodi per l'aggiornamento dei pacchetti usati localmente da un amministratore in un computer Linux, questo comportamento è intenzionale.

Per evitare l'aggiornamento della versione del sistema operativo tramite distribuzioni di Gestione aggiornamenti, usare la funzionalità **Esclusione**.

In Red Hat Enterprise Linux il nome del pacchetto da escludere è `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché non vengono applicati aggiornamenti critici e della sicurezza?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione filtra gli aggiornamenti che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, è possibile contrassegnare alcuni aggiornamenti in Gestione aggiornamenti come elementi che influiscono sulla sicurezza, anche se questa informazione non è indicata nel computer locale. Se si applicano aggiornamenti critici a un computer Linux, è possibile che alcuni non vengano contrassegnati come elementi che influiscono sulla sicurezza del computer e che quindi non vengano applicati. Tuttavia, Gestione aggiornamenti potrebbe comunque segnalare tale computer come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti in base alla relativa classificazione non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, se si seleziona SOLO **Altri aggiornamenti** come classificazione, è possibile che vengano installati anche alcuni aggiornamenti della sicurezza aggiuntivi se per prima cosa sono richiesti quelli correlati a zypper (gestione pacchetti) o alle relative dipendenze. Si tratta di una limitazione di zypper. In alcuni casi può essere necessario eseguire di nuovo la distribuzione degli aggiornamenti e quindi verificarla tramite l'apposito log.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra tenant di Azure?

Se si hanno computer in un altro tenant di Azure che segnalano a Gestione aggiornamenti che necessitano di una patch, occorrerà usare la seguente soluzione alternativa per pianificarli. È possibile usare il cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) con il parametro `ForUpdateConfiguration` per creare una pianificazione. Usare quindi il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) e passare i computer dell'altro tenant al parametro `NonAzureComputer`. L'esempio seguente illustra come farlo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>Passaggi successivi

Se la risposta a una domanda non è disponibile in questo articolo, è possibile fare riferimento alle fonti seguenti per ulteriori domande e risposte.

- [Automazione di Azure](https://docs.microsoft.com/answers/topics/azure-automation.html)
- [Forum di commenti e suggerimenti](https://feedback.azure.com/forums/905242-update-management)
