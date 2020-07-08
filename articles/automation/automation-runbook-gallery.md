---
title: Usare runbook e moduli di Automazione di Azure in PowerShell Gallery
description: Questo articolo descrive come usare runbook e moduli di Microsoft e della community in PowerShell Gallery.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf2a60e1387ccdfb24ff5c5e166780c00506bac8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832300"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Usare runbook e moduli in PowerShell Gallery

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community. È possibile ottenere i runbook e i [moduli](#modules-in-powershell-gallery) di PowerShell da PowerShell Gallery e i [runbook Python](#use-python-runbooks) dalla raccolta di Script Center. È anche possibile contribuire alla community condividendo gli [scenari sviluppati personalmente](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Runbook in PowerShell Gallery

[PowerShell Gallery](https://www.powershellgallery.com/packages) offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure. Per usare un runbook, scaricarne uno dalla raccolta disponibile oppure importare runbook direttamente dalla raccolta o dall'account di automazione nel portale di Azure.

> [!NOTE]
> PowerShell Gallery non supporta i runbook grafici.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure. Non è possibile eseguire questa funzione tramite PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di qualsiasi runbook ottenuto da PowerShell Gallery e prestare particolare attenzione in caso di installazione ed esecuzione in un ambiente di produzione.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](https://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Scenari comuni disponibili in PowerShell Gallery

L'elenco seguente contiene alcuni runbook che supportano scenari comuni. Per un elenco completo dei runbook creati dal team di Automazione di Azure, vedere il [profilo AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/): importa la versione più recente di tutti i moduli di un account di automazione da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/): configura Diagnostica di Azure e Log Analytics per ricevere i log di Automazione di Azure contenenti lo stato e i flussi del processo.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/): copia un file remoto da una macchina virtuale di Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/): copia un file locale in una macchina virtuale di Windows Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importare un runbook PowerShell dalla raccolta di runbook con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. In **Automazione processi** selezionare **Raccolta di runbook**.
3. Selezionare **Origine: PowerShell Gallery**.
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. A sinistra è possibile immettere parametri di ricerca aggiuntivi per il server di pubblicazione e il tipo.

   ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)

5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](https://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi fare clic su **Importa**.

   ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)

7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Aggiungere un runbook di PowerShell alla raccolta

Microsoft consiglia di aggiungere a PowerShell Gallery i runbook ritenuti più utili per gli altri clienti. PowerShell Gallery accetta moduli e script PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importare un modulo dalla raccolta di moduli con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
3. Fare clic su **Esplora raccolta** nella parte superiore della pagina.

   ![Raccolta di moduli](media/automation-runbook-gallery/modules-blade.png)

4. Nella pagina "Esplora raccolta" è possibile eseguire la ricerca in base ai campi seguenti:

   * Nome del modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC

5. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.

   Eseguendo il drill-down di un modulo specifico si accede a ulteriori informazioni. Queste informazioni includono un collegamento che riporta alla PowerShell Gallery, le eventuali dipendenze necessarie e tutti i cmdlet o le risorse DSC contenute nel modulo.

   ![Informazioni sul modulo di PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Per installare il modulo direttamente in Automazione di Azure, fare clic su **Importa**.
7. Nel riquadro Importa è possibile visualizzare il nome del modulo da importare. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
8. Nel riquadro Importa, fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa azione può richiedere alcuni minuti, in quanto occorre estrarre ogni attività.
9. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
10. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nelle risorse DSC e dei runbook.

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="use-python-runbooks"></a>Usare runbook di Python

I runbook di Python sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). È possibile apportare contribuiti ai runbook di Python nella raccolta di Script Center facendo clic su **Upload a contribution** (Carica contributo). In tal caso, assicurarsi di aggiungere il tag `Python` quando si carica il contributo.

> [!NOTE]
> Per caricare il contenuto in [Script Center](https://gallery.technet.microsoft.com/scriptcenter), è necessario un minimo di 100 punti.

## <a name="request-a-runbook-or-module"></a>Richiedere un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda sulla [pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare un runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* * Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
