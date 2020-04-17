---
title: Raccolte di runbook e moduli per l'automazione di Azure
description: I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure.  In questo articolo viene descritto come accedere a queste risorse e aggiungere i runbook alla raccolta.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535554"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Raccolte di runbook e moduli per l'automazione di Azure

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community. È possibile ottenere runbook e moduli di PowerShell da PowerShell Gallery e Python runbooks dalla raccolta Script Center.You can get PowerShell runbooks and [modules](#modules-in-powershell-gallery) from the PowerShell Gallery and [Python runbooks](#use-python-runbooks) from the Script Center Gallery. È inoltre possibile contribuire alla community condividendo [gli scenari sviluppati.](#add-a-powershell-runbook-to-the-gallery) 

## <a name="runbooks-in-powershell-gallery"></a>Runbook in PowerShell Gallery

PowerShell Gallery offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure.The [PowerShell Gallery](https://www.powershellgallery.com/packages) provides a variety of runbooks from Microsoft and the community that you can import into Azure Automation. Per usarne uno, scaricare un runbook dalla raccolta oppure importare direttamente i runbook dalla raccolta o dall'account di automazione nel portale di Azure.To use one, download a runbook from the gallery, or you can directly import runbooks from the gallery, or from your Automation account in the Azure portal.

> [!NOTE]
> I runbook grafici non sono supportati in PowerShell Gallery.Graphical runbooks are not supported in PowerShell Gallery.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure.You can only import directly from the PowerShell Gallery using the Azure portal. Non è possibile eseguire questa funzione tramite PowerShell.You cannot perform this function using PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di tutti i runbook che si ottiene da PowerShell Gallery e prestare estrema attenzione nell'installazione e nell'esecuzione in un ambiente di produzione.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](https://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.

## <a name="common-solutions-available-in-powershell-gallery"></a>Soluzioni comuni disponibili in PowerShell GalleryCommon solutions available in PowerShell Gallery

L'elenco seguente contiene alcuni runbook che forniscono soluzioni a scenari comuni. Per un elenco completo dei runbook creati dal team di Automazione di Azure, vedere [Profilo di AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion:](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) importa la versione più recente di tutti i moduli in un account di automazione da PowerShell Gallery.
   * [Enable-AzureDiagnostics:](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) configura Diagnostica di Azure e Log Analytics per ricevere i log di Automazione di Azure contenenti lo stato del processo e i flussi di lavoro.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Copia un file remoto da una macchina virtuale di Windows Azure.Copy-ItemFromAzureVM - Copies a remote file from a Windows Azure virtual machine.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Copia un file locale in una macchina virtuale di Azure.Copy-ItemFromAzureVM - Copies a local file to an Azure virtual machine.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importare un runbook di PowerShell dalla raccolta di runbook con il portale di AzureImport a PowerShell runbook from the runbook gallery with the Azure portal

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Raccolta Runbooks** in **Automazione processo**.
3. Selezionare **Origine: Raccolta PowerShell**.
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. A sinistra è possibile immettere parametri di ricerca aggiuntivi per il server di pubblicazione e il tipo.

   ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)

5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](https://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi fare clic su **Importa**.

   ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)

7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Il runbook viene visualizzato nella scheda **Runbooks** per l'account di automazione.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Aggiungere un runbook di PowerShell alla raccoltaAdd a PowerShell runbook to the gallery

Microsoft incoraggia l'utente ad aggiungere runbook a PowerShell Gallery che si ritiene possa essere utile per altri clienti. PowerShell Gallery accetta moduli e script PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importare un modulo dalla raccolta moduli con il portale di AzureImport a module from the module gallery with the Azure portal

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
3. Fare clic su **Esplora raccolta** nella parte superiore della pagina.

   ![Raccolta di moduli](media/automation-runbook-gallery/modules-blade.png)

4. Nella pagina Esplora raccolta è possibile eseguire la ricerca in base ai campi seguenti:

   * Nome del modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC

5. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.

   Eseguendo il drill-down di un modulo specifico si accede a ulteriori informazioni. Queste informazioni includono un collegamento che riporta alla PowerShell Gallery, le eventuali dipendenze necessarie e tutti i cmdlet o le risorse DSC contenute nel modulo.

   ![Informazioni sul modulo di PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Per installare il modulo direttamente in Automazione di Azure, fare clic su **Importa**.
7. Nel riquadro Importa è possibile visualizzare il nome del modulo da importare. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
8. Nel riquadro Importa fare clic **su OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa azione potrebbe richiedere un paio di minuti poiché ogni attività deve essere estratta.
9. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
10. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nei runbook e nelle risorse DSC.

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="use-python-runbooks"></a>Utilizzare i runbook Python

I runbook di Python sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). È possibile aggiungere i runbook Python alla raccolta Script Center facendo clic su **Carica un contributo**. Quando lo fai, assicurati di `Python` aggiungere il tag quando carichi il tuo contributo.

> [!NOTE]
> Per caricare il contenuto [in Script Center,](https://gallery.technet.microsoft.com/scriptcenter)sono necessari almeno 100 punti.

## <a name="request-a-runbook-or-module"></a>Richiedere un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook, vedere [Gestire il runbook in Automazione di Azure.To](manage-runbooks.md)get started with runbooks, see Manage runbook in Azure Automation.
* Per comprendere le differenze tra PowerShell e PowerShell Workflow con i runbook, vedere [Learning PowerShell workflow](automation-powershell-workflow.md).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
