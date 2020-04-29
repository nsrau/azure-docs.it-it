---
title: Gestione dei dati di Automazione di Azure
description: Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.  Include attualmente conservazione dei dati e backup del ripristino di emergenza di Automazione di Azure in Automazione di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984658"
---
# <a name="managing-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene diversi argomenti per la gestione dei dati in un ambiente di automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in automazione di Azure, questa viene mantenuta per un numero di giorni a scopo di controllo prima della rimozione permanente. Non è possibile visualizzare o usare la risorsa durante questo periodo di tempo. Questo criterio si applica anche alle risorse che appartengono a un account di automazione eliminato.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Data | Policy |
|:--- |:--- |
| Account |Un account viene rimosso definitivamente 30 giorni dopo la sua eliminazione da un utente. |
| Asset |Un asset viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede l'asset. |
| Nodi DSC |Un nodo DSC viene rimosso definitivamente 30 giorni dopo l'annullamento della registrazione da un account di automazione usando portale di Azure o il cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows PowerShell. Un nodo viene inoltre rimosso definitivamente 30 giorni dopo che un utente ha eliminato l'account che possiede il nodo. |
| Processi |Un processo viene eliminato e rimosso definitivamente 30 giorni dopo la modifica, ad esempio dopo il completamento del processo, viene arrestato o sospeso. |
| Moduli |Un modulo viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione dell'account che include il modulo. |
| Configurazioni di nodo/File MOF |Una configurazione del nodo precedente viene rimossa definitivamente 30 giorni dopo la generazione di una nuova configurazione del nodo. |
| Report sul nodo |Un rapporto del nodo viene rimosso definitivamente 90 giorni dopo la generazione di un nuovo report per tale nodo. |
| Runbook |Una Runbook viene rimossa definitivamente 30 giorni dopo l'eliminazione della risorsa da parte di un utente o 30 giorni dopo l'eliminazione dell'account che possiede la risorsa. |

I criteri di conservazione vengono applicati a tutti gli utenti e attualmente non possono essere personalizzati. Tuttavia, se è necessario tenere i dati per un periodo di tempo più lungo, è possibile [inviare i dati del processo di automazione di Azure ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup dei dati

Quando si elimina un account di automazione in Azure, vengono eliminati tutti gli oggetti presenti nell'account. Gli oggetti includono manuali operativi, moduli, configurazioni, impostazioni, processi e asset. Non possono essere recuperati dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup del contenuto dell'account di automazione prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. È possibile importare questi file di script in un altro account di automazione, come descritto in [gestire manuali operativi in automazione di Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione da automazione di Azure. È necessario renderli disponibili al di fuori dell'account di automazione.

### <a name="assets"></a>Asset

Non è possibile esportare gli asset di automazione di Azure: certificati, connessioni, credenziali, pianificazioni e variabili. È invece possibile usare il portale di Azure e i cmdlet di Azure per prendere nota dei dettagli di questi asset. Usare quindi questi dettagli per creare gli asset usati da manuali operativi importati in un altro account di automazione.

Non è possibile recuperare i valori per le variabili crittografate o i campi password delle credenziali usando i cmdlet. Se non si conoscono questi valori, è possibile recuperarli in un Runbook. Per recuperare i valori delle variabili, vedere [Asset variabili in automazione di Azure](shared-resources/variables.md). Per altre informazioni sul recupero dei valori delle credenziali, vedere [asset delle credenziali in automazione di Azure](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni DSC in file di script usando il portale di Azure o il cmdlet [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) in Windows PowerShell. È possibile importare e usare queste configurazioni in un altro account di automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

La replica geografica è standard negli account di automazione di Azure. Quando si configura l'account, è possibile scegliere un'area primaria. Il servizio di replica geografica di automazione interna assegna automaticamente un'area secondaria all'account. Il servizio esegue quindi il backup continuo dei dati dell'account dall'area primaria all'area secondaria. L'elenco completo delle aree primarie e secondarie si trova in [continuità aziendale e ripristino di emergenza (BCdR): aree abbinate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Il backup creato dal servizio di replica geografica di automazione è una copia completa degli asset di automazione, delle configurazioni e del tipo. Questo backup può essere usato se l'area primaria diventa inattiva e perde i dati. Nel caso improbabile in cui i dati di un'area primaria vadano perduti, Microsoft tenta di recuperarla. Se la società non riesce a ripristinare i dati primari, viene usato il failover automatico e viene segnalata la situazione tramite la sottoscrizione di Azure. 

Il servizio di replica geografica di automazione non è accessibile direttamente ai clienti esterni se si verifica un errore a livello di area. Se si desidera mantenere la configurazione di automazione e manuali operativi durante gli errori a livello di area:

1. Selezionare un'area secondaria da associare all'area geografica dell'account di automazione primario.

2. Creare un account di automazione nell'area secondaria.

3. Nell'account primario esportare i manuali operativi come file di script.

4. Importare il manuali operativi nell'account di automazione nell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle risorse protette in automazione di Azure, vedere [crittografare asset protetti in automazione di Azure](automation-secure-asset-encryption.md).

* Per altre informazioni sulla replica geografica, vedere [creazione e uso della replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).