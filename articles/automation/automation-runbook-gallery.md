---
title: Raccolte di runbook e moduli per Automazione di Azure | Documentazione Microsoft
description: I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure.  In questo articolo viene descritto come accedere a queste risorse e come contribuire alla raccolta di runbook.
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 70bbc131f153efd88816450c239920c79665fdff
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Raccolte di runbook e moduli per l'automazione di Azure
Anziché creare runbook e moduli personalizzati in Automazione di Azure, si può ricorrere a una serie di scenari già creati da Microsoft e dalla community.  Questi scenari possono essere usati senza alcuna modifica oppure possono essere utilizzati come punto di partenza apportando tutte le modifiche necessarie in base alle specifiche esigenze.

È possibile trovare runbook nella [raccolta di runbook](#runbooks-in-runbook-gallery) e moduli in [PowerShell Gallery](#modules-in-powerShell-gallery).  Si può anche contribuire alla community condividendo gli scenari sviluppati personalmente.

## <a name="runbooks-in-runbook-gallery"></a>Runbook nella raccolta di runbook
La [raccolta di runbook](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure. È possibile scaricare un runbook dalla raccolta disponibile nello [Script Center di TechNet](https://gallery.technet.microsoft.com/scriptcenter/site/upload) oppure importare runbook direttamente dalla raccolta dal portale di Azure classico o dal portale di Azure.

È possibile importare solo direttamente dalla raccolta di runbook usando il portale di Azure classico o il portale di Azure. Non è possibile eseguire questa funzione tramite Windows PowerShell.

> [!NOTE]
> È necessario convalidare il contenuto di qualsiasi runbook ottenuto dalla raccolta di runbook e prestare particolare attenzione durante la loro installazione ed esecuzione in un ambiente di produzione. |
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-classic-portal"></a>Per importare un runbook dalla raccolta di runbook con il portale di Azure classico
1. Nel portale di Azure fare clic su **Nuovo**, **Servizi app**, **Automazione**, **Runbook**, **Da raccolta**.
2. Selezionare una categoria per visualizzare i runbook correlati e quindi selezionare un runbook per visualizzarne i dettagli. Dopo aver selezionato il runbook desiderato, fare clic sul pulsante con la freccia destra.
   
    ![raccolta di runbook](media/automation-runbook-gallery/runbook-gallery.png)
3. Esaminare il contenuto del runbook e prendere nota di eventuali requisiti nella descrizione. Al termine, fare clic sul pulsante con la freccia destra.
4. Immettere i dettagli del runbook e quindi fare clic sul pulsante con il segno di spunta. Il nome di runbook è già specificato.
5. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Per importare un runbook dalla raccolta di runbook con il portale di Azure
1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Sfoglia raccolta** .
   
    ![Pulsante Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery-button.png)
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli.
   
    ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)
5. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](http://gallery.technet.microsoft.com/).
6. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi scegliere il pulsante **Importa** .
   
    ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)
7. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
8. Il runbook viene visualizzato nella scheda **Runbook** per l'account di automazione.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Aggiunta di un runbook alla raccolta di runbook
Microsoft consiglia di aggiungere alla raccolta dei runbook i runbook ritenuti più utili per gli altri clienti.  È possibile aggiungere un runbook [caricandolo nello Script Center](http://gallery.technet.microsoft.com/site/upload) , a condizione che si considerino i seguenti dettagli.

* Per il runbook da visualizzare nella procedura guidata, è necessario specificare *Windows Azure* nel campo **Categoria** e *Automazione* nel campo **Sottocategoria**.  
* Il caricamento deve interessare un singolo file con estensione PS1 o GRAPHRUNBOOK.  Se il runbook richiede eventuali moduli, runbook figlio o asset, è consigliabile elencare questi elementi nella descrizione dell'invio e nella sezione dei commenti del runbook.  Se si dispone di uno scenario che richiede più runbook, caricare ciascuna soluzione separatamente ed elencare i nomi dei runbook correlati in ognuna delle relative descrizioni. Assicurarsi di usare gli stessi tag in modo che vengano visualizzati nella stessa categoria. L'utente dovrà fare riferimento alla descrizione per sapere se sono necessari altri runbook per il corretto funzionamento dello scenario.
* Se si pubblica un **runbook grafico** (non un flusso di lavoro grafico), aggiungere il tag "GraficoPS". 
* Inserire un frammento di codice di PowerShell o di un flusso di lavoro PowerShell nella descrizione mediante l’icona **Inserisci sezione di codice** .
* Nei risultati della raccolta di runbook viene visualizzato il riepilogo del caricamento. Sarà pertanto necessario fornire informazioni dettagliate che consentano a un utente di identificare le funzionalità del runbook.
* È consigliabile assegnare da uno a tre dei seguenti tag al caricamento.  Il runbook viene elencato nella procedura guidata nelle categorie corrispondenti ai relativi tag.  Qualsiasi tag non incluso nell'elenco viene ignorato dalla procedura guidata. Se non si specifica alcun tag corrispondente, il runbook viene elencato nella categoria Altro.
  
  * Backup
  * Capacity Management
  * Controllo modifiche
  * Conformità
  * Ambiente di testing/sviluppo
  * Ripristino di emergenza
  * Monitoraggio
  * Applicazione di patch
  * Provisioning
  * Correzione
  * Gestione del ciclo di vita VM
* L'automazione aggiorna la raccolta una volta all'ora. Pertanto i contributi non verranno visualizzati immediatamente.

## <a name="modules-in-powershell-gallery"></a>Moduli in PowerShell Gallery
I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](http://www.powershellgallery.com)sono disponibili moduli esistenti che è possibile installare in Automazione di Azure.  È possibile avviare questa raccolta dal portale di Azure e installarli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente.  Non è possibile installare i moduli direttamente dal portale di Azure classico, ma è possibile scaricarli e installarli come qualsiasi altro modulo.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Per importare un modulo dalla raccolta di moduli di Automazione con il portale di Azure
1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Moduli** in **Risorse condivise** per aprire l'elenco di moduli.
4. Fare clic su **Esplora raccolta** nella parte superiore della pagina.
   
    ![Raccolta di moduli](media/automation-runbook-gallery/modules-blade.png) <br>
5. Nella pagina **Esplora raccolta** è possibile eseguire la ricerca in base ai campi seguenti:
   
   * Nome modulo
   * Tag
   * Autore
   * Nome della risorsa cmdlet/DSC
6. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.  
   Quando si analizza un modulo specifico, è possibile visualizzare altre informazioni sul modulo, inclusi un collegamento a PowerShell Gallery, eventuali dipendenze necessarie e tutte le risorse cmdlet/DSC contenute nel modulo.
   
    ![Informazioni sul modulo di PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. Per installare il modulo direttamente in Automazione di Azure, fare clic sul pulsante **Importa** .
   
    ![Pulsante Importa modulo](media/automation-runbook-gallery/module-import-button.png)
8. Quando si fa clic sul pulsante Importa, nel riquadro **Importa**  viene visualizzato il nome del modulo che verrà importato. Se tutte le dipendenze sono installate, il pulsante **OK** è attivo. Se non sono presenti tutte le dipendenze richieste, è necessario importarle prima di importare il modulo.
9. Fare clic su **OK** per importare il modulo. Durante l'importazione del modulo nell'account, Automazione di Azure estrae i metadati sul modulo e i cmdlet.
   
    ![Pagina di importazione del modulo](media/automation-runbook-gallery/module-import-blade.png)
   
    Poiché ogni attività deve essere estratta potrebbero volerci alcuni minuti.
10. Si riceverà una notifica iniziale per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.
11. Dopo aver importato il modulo, è possibile visualizzare le attività disponibili e usare le risorse nei runbook e in Desired State Configuration.

## <a name="requesting-a-runbook-or-module"></a>Richiesta di un runbook o un modulo
È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Fasi successive
* Per iniziare a usare i runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
* Per comprendere le differenze tra PowerShell e i flussi di lavoro di PowerShell con i runbook, vedere [Informazioni sul flusso di lavoro di Windows PowerShell](automation-powershell-workflow.md)

