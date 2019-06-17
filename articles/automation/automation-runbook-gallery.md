---
title: Raccolte di runbook e moduli per l'automazione di Azure
description: I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure.  In questo articolo viene descritto come accedere a queste risorse e come contribuire alla raccolta di runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20aafc117ad8b6bd625894180fdfe79bd86192bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737405"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Raccolte di runbook e moduli per l'automazione di Azure

Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere ad alcuni scenari già creati da Microsoft e dalla community.

È possibile ottenere i runbook di PowerShell e [moduli](#modules-in-powershell-gallery) da PowerShell Gallery e [i runbook di Python](#python-runbooks) dalla raccolta Script Center. È anche possibile contribuire alla community condividendo gli scenari che si sviluppano, vedere Aggiunta di un runbook alla raccolta

## <a name="runbooks-in-powershell-gallery"></a>Runbook in PowerShell Gallery

Il [PowerShell Gallery](https://www.powershellgallery.com/packages) offre un'ampia gamma di runbook da Microsoft e dalla community che è possibile importare in automazione di Azure. Per usare uno, scaricare un runbook dalla raccolta, o è possibile importare runbook direttamente dalla raccolta o dall'Account di automazione nel portale di Azure.

È possibile importare solo direttamente da PowerShell Gallery usando il portale di Azure. È possibile eseguire questa funzione tramite PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di qualsiasi runbook ottenuto da PowerShell Gallery e prestare particolare attenzione durante l'installazione ed esecuzione in un ambiente di produzione.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Per importare un runbook di PowerShell dalla raccolta di Runbook con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. In **Automazione processi** fare clic su **Raccolta di runbook**
3. Selezionare **origine: PowerShell Gallery**.
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. A sinistra è possibile immettere parametri di ricerca aggiuntivi per il server di pubblicazione e il tipo.

   ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)

5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](https://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi scegliere il pulsante **Importa** .

   ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)

7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Aggiunta di un runbook di PowerShell per la raccolta

Microsoft consiglia di aggiungere i runbook in PowerShell Gallery che si ritiene potrebbero essere utili ad altri clienti. PowerShell Gallery accetta moduli e script di PowerShell. È possibile aggiungere un runbook [caricandolo in PowerShell Gallery](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> I runbook grafici non sono supportati in PowerShell Gallery.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](https://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di Azure e installare i moduli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Per importare un modulo dalla raccolta di moduli di Automazione con il portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
3. Fare clic su **Esplora raccolta** nella parte superiore della pagina.

   ![Raccolta di moduli](media/automation-runbook-gallery/modules-blade.png)

4. Nella pagina **Esplora raccolta** è possibile eseguire la ricerca in base ai campi seguenti:

   * Nome modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC

5. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.  

   Eseguendo il drill-down di un modulo specifico si accede a ulteriori informazioni. Queste informazioni includono un collegamento che riporta alla PowerShell Gallery, le eventuali dipendenze necessarie e tutti i cmdlet o le risorse DSC contenute nel modulo.

   ![Informazioni sul modulo di PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Per installare il modulo direttamente in Automazione di Azure, fare clic sul pulsante **Importa** .
7. Quando si fa clic sul pulsante Importa, nel riquadro **Importa**  viene visualizzato il nome del modulo che verrà importato. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
8. Nella pagina **Importa** fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet. Questa azione può richiedere alcuni minuti, in quanto occorre estrarre ogni attività.
9. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
10. Dopo l'importazione del modulo si vedranno le attività disponibili. È possibile usare le risorse del modulo nei runbook e nella piattaforma DSC (Desired State Configuration).

> [!NOTE]
> I moduli che supportano solo i componenti di base di PowerShell non sono supportati in automazione di Azure e non sono in grado di essere importati nel portale di Azure o distribuiti direttamente da PowerShell Gallery.

## <a name="python-runbooks"></a>Runbook di Python

I runbook di Python sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). È possibile contribuire i runbook di Python nella raccolta di Script Center facendo **carica un contributo**. In tal caso, assicurarsi di aggiungere il tag **Python** quando si carica il contributo.

> [!NOTE]
> Per caricare contenuti da [nello Script Center](https://gallery.technet.microsoft.com/scriptcenter) è necessario un minimo di 100 punti. 

## <a name="requesting-a-runbook-or-module"></a>Richiesta di un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare i runbook, vedere [Manage runbook in Azure Automation](manage-runbooks.md) (Gestire i runbook in Automazione di Azure).
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell con i runbook, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)
