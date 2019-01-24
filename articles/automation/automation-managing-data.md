---
title: Gestione dei dati di Automazione di Azure
description: Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.  Include attualmente conservazione dei dati e backup del ripristino di emergenza di Automazione di Azure in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b133947a7daf1977f084ee4008599882b51d9e8b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428478"
---
# <a name="managing-azure-automation-data"></a>Gestione dei dati di Automazione di Azure
Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.

## <a name="data-retention"></a>Conservazione dei dati
Quando si elimina una risorsa in Automazione di Azure, i dati corrispondenti vengono conservati per 90 giorni per finalità di controllo, prima della rimozione permanente.  In tale periodo non sarà possibile visualizzare o usare la risorsa.  Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato.

Automazione di Azure elimina automaticamente e rimuove definitivamente i processi dopo 90 giorni.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Dati | Criterio |
|:--- |:--- |
| Account |Rimosso definitivamente 90 giorni dopo l'eliminazione dell'account da parte di un utente. |
| Asset |Rimosso definitivamente 90 giorni dopo l'eliminazione dell'asset da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include l'asset da parte di un utente. |
| Moduli |Rimossi definitivamente 90 giorni dopo l'eliminazione del modulo da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include il modulo da parte di un utente. |
| Runbook |Rimossi definitivamente 90 giorni dopo l'eliminazione della risorsa da parte di un utente o 90 giorni dopo l'eliminazione dell'account che include la risorsa da parte di un utente. |
| Processi |Eliminati e rimossi definitivamente 90 giorni dopo l'ultima modifica, ad esempio dopo il completamento, l'arresto o la sospensione del processo. |
| Configurazioni di nodo/File MOF |La configurazione di nodo precedente verrà rimossa definitivamente 90 giorni dopo che viene generata una nuova configurazione di nodo. |
| Nodi DSC |Vengono rimossi in modo permanente 90 giorni dopo l’annullamento della registrazione del nodo dall’Account di automazione tramite il portale di Azure o il cmdlet [Unregister AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) in Windows PowerShell. I nodi vengono rimossi in modo permanente anche 90 giorni dopo che l'account che possiede il nodo viene eliminato da un utente. |
| Report sul nodo |Vengono rimossi in modo permanente 90 giorni dopo la generazione di un nuovo report per quel nodo |

I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli.

Tuttavia, se si desidera conservare i dati per un periodo di tempo più lungo, è possibile trasferire i log del processo runbook a Log Analytics.  Per maggiori informazioni, consultare [Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Backup di Automazione di Azure
Quando si elimina un account di automazione in Microsoft Azure, vengono eliminati tutti gli oggetti presenti nell'account, ad esempio Runbook, moduli, configurazioni, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account.  È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo. 

### <a name="runbooks"></a>Runbook
È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell.  Questi file di script possono essere importati in un account di Automazione, come illustrato in [Creazione o importazione di un Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Moduli di integrazione
Non è possibile esportare i moduli di integrazione da Automazione di Azure.  È necessario assicurare che siano disponibili all'esterno dell'account di Automazione.

### <a name="assets"></a>Asset
Non è possibile esportare [asset](https://msdn.microsoft.com/library/dn939988.aspx) da Automazione di Azure.  Usando il portale di Azure, è necessario annotare i dettagli di variabili, credenziali, certificati, connessioni e pianificazioni.  È quindi necessario creare manualmente eventuali asset usati dai Runbook importati in un altro account di Automazione.

È possibile usare i [cmdlet di Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) per recuperare i dettagli di asset non crittografati e salvarli come riferimento futuro o creare asset equivalenti in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o il campo password delle credenziali mediante i cmdlet.  Se non si conoscono questi valori, sarà possibile recuperarli da un Runbook attraverso le attività [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Non è possibile esportare certificati da Automazione di Azure.  È necessario assicurarsi che eventuali certificati siano disponibili all'esterno di Azure.

### <a name="dsc-configurations"></a>Configurazioni DSC
È possibile esportare le configurazioni in file di script tramite il portale di Azure o il cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) in Windows PowerShell. Queste configurazioni possono essere importate e usate in un altro account di automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure
Replica geografica, standard negli account di automazione di Azure, backup dei dati di account in un'area geografica diversa per la ridondanza. È possibile scegliere un'area primaria quando si configura l’account, poi un'area secondaria viene assegnata automaticamente. I dati secondari, copiati dall'area primaria, vengono continuamente aggiornati in caso di perdita di dati.  

Nella tabella seguente vengono illustrate le associazioni di aree primarie e secondarie disponibili:

| Primaria | Secondario |
| --- | --- |
| Stati Uniti centro-meridionali |Stati Uniti centro-settentrionali |
| Stati Uniti orientali 2 |Stati Uniti centrali |
| Europa occidentale |Europa settentrionale |
| Asia sudorientale |Asia orientale |
| Giappone orientale |Giappone occidentale |

Nell'eventualità improbabile che vengano persi dei dati di un’area primaria, Microsoft tenta di ripristinarli. Qualora non fosse possibile ripristinare i dati primari, viene eseguito il failover geografico e i clienti interessati saranno informati attraverso la propria sottoscrizione.


