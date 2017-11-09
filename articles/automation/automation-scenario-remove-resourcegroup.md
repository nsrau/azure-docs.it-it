---
title: Automatizzare la rimozione di gruppi di risorse | Documentazione Microsoft
description: Versione del flusso di lavoro PowerShell di uno scenario di Automazione di Azure che include runbook per rimuovere tutti i gruppi di risorse nella sottoscrizione.
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: 8b23e55a597f293b17183e80eea6c2763aabe9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Scenario di Automazione di Azure: automatizzare la rimozione di gruppi di risorse
Molti clienti creano più gruppi di risorse. Alcuni potrebbero essere usati per gestire applicazioni di produzione e altri come ambienti di sviluppo, test e staging. La rimozione di un gruppo di risorse con un clic è più complessa rispetto all'automazione della distribuzione di queste risorse. È possibile semplificare questa comune attività di gestione con Automazione di Azure. Ciò è utile se si usa una sottoscrizione di Azure con un limite di spesa tramite un'offerta riservata ai membri, ad esempio di MSDN o del programma Microsoft Partner Network Cloud Essentials.

Questo scenario si basa su un runbook di PowerShell ed è progettato per rimuovere uno o più gruppi di risorse specificati dalla sottoscrizione. L'impostazione predefinita del runbook prevede l'esecuzione preliminare di un test, per evitare di eliminare per errore il gruppo di risorse prima di essere pronti a completare la procedura.   

## <a name="getting-the-scenario"></a>Come ottenere lo scenario
Questo scenario è costituito da un runbook di PowerShell scaricabile da [PowerShell Gallery](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Può anche essere importato direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure.<br><br>

| Runbook | Descrizione |
| --- | --- |
| Remove-ResourceGroup |Rimuove uno o più gruppi di risorse di Azure e le risorse associate dalla sottoscrizione. |

<br>
Per questo runbook vengono definiti i parametri di input seguenti:

| Parametro | Descrizione |
| --- | --- |
| NameFilter (obbligatorio) |Specifica un filtro per i nomi per limitare i gruppi di risorse che si intende eliminare. È possibile passare più valori usando un elenco delimitato da virgole.<br>Il filtro non rileva la distinzione tra maiuscole e minuscole e troverà la corrispondenza con tutti i gruppi di risorse contenenti la stringa. |
| PreviewMode (facoltativo) |Esegue il runbook per verificare quali gruppi di risorse verrebbero eliminati, ma non esegue alcuna azione.<br>Il valore predefinito è **true** che consente di evitare l'eliminazione accidentale di uno o più gruppi di risorse passati al runbook. |

## <a name="install-and-configure-this-scenario"></a>Installare e configurare lo scenario
### <a name="prerequisites"></a>Prerequisiti
Questo runbook esegue l'autenticazione con l' [account RunAs di Azure](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Installare e pubblicare i runbook
Dopo aver scaricato il runbook, è possibile importarlo usando la procedura descritta tra quelle di [importazione di un runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Pubblicare il runbook dopo averlo importato correttamente nell'account di automazione.

## <a name="using-the-runbook"></a>Uso del runbook
I passaggi seguenti illustrano l'esecuzione di questo runbook e consentono di acquisire familiarità con il suo funzionamento. In questo esempio verrà semplicemente testato il runbook. Il gruppo di risorse non verrà effettivamente eliminato.  

1. Dal portale di Azure aprire l'account di automazione e fare clic su **Runbook**.
2. Selezionare il runbook **Remove-ResourceGroup** e fare clic su **Avvia**.
3. Quando si avvia il runbook, viene aperto il pannello **Avvia runbook** ed è possibile configurare i parametri. Immettere i nomi dei gruppi di risorse della sottoscrizione che è possibile usare per il test e la cui eventuale eliminazione accidentale non causerebbe problemi.<br> ![Parametri di Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > Verificare che il parametro **Previewmode** sia impostato su **true** per evitare di eliminare i gruppi di risorse selezionati.  **Si noti** che questo runbook non rimuoverà il gruppo di risorse contenente l'account di automazione che esegue il runbook stesso.  
   >
   >
4. Dopo avere configurato i valori di tutti i parametri, fare clic su **OK**. Il runbook verrà inserito in coda per l'esecuzione.  

Per visualizzare i dettagli del processo del runbook **Remove-ResourceGroup** nel portale di Azure, selezionare **Processi** nel runbook. Nel riepilogo del processo saranno visualizzati i parametri di input e il flusso di output, oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate.<br> ![Stato del processo del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png)

Il **Riepilogo processi** include i messaggi dai flussi di output, di avviso e di errore. Selezionare **Output** per visualizzare i risultati dettagliati dell'esecuzione del runbook.<br> ![Risultati di output del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a creare il runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md).
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
