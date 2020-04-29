---
title: Raccolte di runbook e moduli per l'automazione di Azure
description: I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure.  Questo articolo descrive come è possibile accedere a queste risorse e contribuire alla raccolta di manuali operativi.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535554"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Raccolte di runbook e moduli per l'automazione di Azure

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community. È possibile ottenere manuali operativi e [moduli](#modules-in-powershell-gallery) di PowerShell dal PowerShell Gallery e [manuali operativi Python](#use-python-runbooks) dalla raccolta di script Center. È anche possibile contribuire alla community condividendo gli [scenari che si sviluppano](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Manuali operativi in PowerShell Gallery

Il [PowerShell Gallery](https://www.powershellgallery.com/packages) offre un'ampia gamma di manuali operativi di Microsoft e della community che è possibile importare in automazione di Azure. Per usarlo, scaricare un Runbook dalla raccolta oppure è possibile importare direttamente manuali operativi dalla raccolta oppure dall'account di automazione nel portale di Azure.

> [!NOTE]
> Manuali operativi grafici non supportati in PowerShell Gallery.

È possibile importare solo direttamente dalla PowerShell Gallery usando il portale di Azure. Non è possibile eseguire questa funzione usando PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di qualsiasi manuali operativi ottenuto dalla PowerShell Gallery e prestare particolare attenzione durante l'installazione e l'esecuzione in un ambiente di produzione.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](https://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.

## <a name="common-solutions-available-in-powershell-gallery"></a>Soluzioni comuni disponibili in PowerShell Gallery

L'elenco seguente contiene alcuni manuali operativi che forniscono soluzioni agli scenari comuni. Per un elenco completo dei manuali operativi creati dal team di automazione di Azure, vedere [profilo AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) : importa la versione più recente di tutti i moduli in un account di automazione da PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) : configura Diagnostica di Azure e log Analytics per ricevere i log di automazione di Azure contenenti lo stato del processo e i flussi del processo.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) : copia un file remoto da una macchina virtuale di Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) : copia un file locale in una macchina virtuale di Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importare un Runbook di PowerShell dalla raccolta runbook con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **raccolta manuali operativi** in **automazione processi**.
3. Selezionare l' **origine: PowerShell Gallery**.
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. A sinistra è possibile immettere parametri di ricerca aggiuntivi per il server di pubblicazione e il tipo.

   ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)

5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](https://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi fare clic su **Importa**.

   ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)

7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Runbook viene visualizzato nella scheda **manuali operativi** per l'account di automazione.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Aggiungere un Runbook di PowerShell alla raccolta

Microsoft consiglia di aggiungere manuali operativi al PowerShell Gallery che si ritiene utile ad altri clienti. PowerShell Gallery accetta moduli e script PowerShell. Per aggiungere un Runbook, è possibile [caricarlo nell'PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importare un modulo dalla raccolta di moduli con il portale di Azure

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

6. Per installare il modulo direttamente in automazione di Azure, fare clic su **Importa**.
7. Nel riquadro importa è possibile visualizzare il nome del modulo da importare. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
8. Nel riquadro Importa fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa operazione potrebbe richiedere alcuni minuti perché ogni attività deve essere estratta.
9. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
10. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nelle risorse manuali operativi e DSC.

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="use-python-runbooks"></a>Usare Python manuali operativi

I runbook di Python sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). È possibile aggiungere Python manuali operativi alla raccolta di script Center facendo clic su **carica un contributo**. Quando si esegue questa operazione, assicurarsi di aggiungere il `Python` tag quando si carica il contributo.

> [!NOTE]
> Per caricare contenuto in [Script Center](https://gallery.technet.microsoft.com/scriptcenter), è necessario un minimo di 100 punti.

## <a name="request-a-runbook-or-module"></a>Richiedere un Runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare manuali operativi, vedere [gestire Runbook in automazione di Azure](manage-runbooks.md).
* Per comprendere le differenze tra PowerShell e il flusso di lavoro PowerShell con manuali operativi, vedere [formazione del flusso di lavoro PowerShell](automation-powershell-workflow.md).
* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
