---
title: Raccolte di runbook e moduli per l'automazione di Azure
description: I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure.  In questo articolo viene descritto come accedere a queste risorse e come contribuire alla raccolta di runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 52a0ab0a31600c1548283c7d899b17e497811b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421491"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Raccolte di runbook e moduli per l'automazione di Azure

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community.

È possibile ottenere runbook e moduli di PowerShell da PowerShell Gallery e Python runbooks dalla raccolta Script Center.You can get PowerShell runbooks and [modules](#modules-in-powershell-gallery) from the PowerShell Gallery and [Python runbooks](#python-runbooks) from the Script Center Gallery. Si può anche contribuire alla community condividendo gli scenari sviluppati personalmente, vedere Aggiunta di un runbook alla raccolta

## <a name="runbooks-in-powershell-gallery"></a>Runbook in PowerShell Gallery

PowerShell Gallery offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure.The [PowerShell Gallery](https://www.powershellgallery.com/packages) provides a variety of runbooks from Microsoft and the community that you can import into Azure Automation. Per usarne uno, scaricare un runbook dalla raccolta oppure importare direttamente i runbook dalla raccolta o dall'account di automazione nel portale di Azure.To use one, download a runbook from the gallery, or you can directly import runbooks from the gallery, or from your Automation Account in the Azure portal.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure.You can only import directly from the PowerShell Gallery using the Azure portal. Non è possibile eseguire questa funzione tramite PowerShell.You cannot perform this function using PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di tutti i runbook che si ottiene da PowerShell Gallery e prestare estrema attenzione nell'installazione e nell'esecuzione in un ambiente di produzione.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Per importare un runbook di PowerShell dalla raccolta runbook con il portale di AzureTo import a PowerShell runbook book from the Runbook Gallery with the Azure portal

1. Nel portale di Azure aprire l'account di automazione.
2. In **Automazione processi** fare clic su **Raccolta di runbook**
3. Selezionare **Origine: Raccolta PowerShell**.
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. A sinistra è possibile immettere parametri di ricerca aggiuntivi per il server di pubblicazione e il tipo.

   ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)

5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](https://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi scegliere il pulsante **Importa** .

   ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)

7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Aggiunta di un runbook di PowerShell alla raccoltaAdding a PowerShell runbook to the gallery

Microsoft incoraggia l'utente ad aggiungere runbook a PowerShell Gallery che si ritiene possa essere utile per altri clienti. PowerShell Gallery accetta moduli e script PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> I runbook grafici non sono supportati in PowerShell Gallery.Graphical runbooks are not supported in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](https://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Per importare un modulo dalla raccolta di moduli di Automazione con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
3. Fare clic su **Esplora raccolta** nella parte superiore della pagina.

   ![Raccolta di moduli](media/automation-runbook-gallery/modules-blade.png)

4. Nella pagina **Esplora raccolta** è possibile eseguire la ricerca in base ai campi seguenti:

   * Nome del modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC

5. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.

   Eseguendo il drill-down di un modulo specifico si accede a ulteriori informazioni. Queste informazioni includono un collegamento che riporta alla PowerShell Gallery, le eventuali dipendenze necessarie e tutti i cmdlet o le risorse DSC contenute nel modulo.

   ![Informazioni sul modulo di PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Per installare il modulo direttamente in Automazione di Azure, fare clic sul pulsante **Importa** .
7. Quando si fa clic sul pulsante Importa, nel riquadro **Importa ** viene visualizzato il nome del modulo che verrà importato. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
8. Nella pagina **Importa** fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa azione può richiedere alcuni minuti, in quanto occorre estrarre ogni attività.
9. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
10. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nei runbook e nella piattaforma DSC (Desired State Configuration).

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="python-runbooks"></a>Runbook di Python

I runbook di Python sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). È possibile aggiungere i runbook Python alla raccolta Script Center facendo clic su **Carica un contributo**. In tal caso, assicurarsi di aggiungere il tag **Python** quando si carica il contributo.

> [!NOTE]
> Per caricare il contenuto in [Script Center](https://gallery.technet.microsoft.com/scriptcenter) sono necessari almeno 100 punti.

## <a name="requesting-a-runbook-or-module"></a>Richiesta di un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Soluzioni comuni disponibili nella raccolta di runbookCommon solutions available in the runbook gallery

L'elenco seguente contiene alcuni runbook che forniscono soluzioni a scenari comuni. Per un elenco completo dei runbook creati dal team di Automazione di Azure, vedere [Profilo di AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion:](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) importa la versione più recente in PowerShell Gallery di tutti i moduli in un account di automazione.
* [Enable-AzureDiagnostics:](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) questo script configura Diagnostica di Azure e Log Analytics per ricevere i log di Automazione di Azure contenenti lo stato del processo e i flussi di lavoro.
* [Copy-ItemFromAzureVM:](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) questo runbook copia un file remoto da una macchina virtuale Windows Azure.Copy-ItemFromAzureVM - This runbook copies a remote file from a Windows Azure virtual machine.
* [Copy-ItemFromAzureVM:](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) questo runbook copia un file locale in una macchina virtuale di Azure.Copy-ItemFromAzureVM - This runbook copies a local file to an Azure virtual machine.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook, vedere [Manage runbook in Azure Automation](manage-runbooks.md) (Gestire i runbook in Automazione di Azure).
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell con i runbook, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
