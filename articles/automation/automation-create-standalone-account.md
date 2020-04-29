---
title: Creare un account di Automazione di Azure autonomo
description: Questo articolo illustra la procedura per creare, testare e usare l'autenticazione di un'entità di sicurezza di esempio in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: 72a40363edf0e83eea26ee697ce992226da0db4f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392281"
---
# <a name="create-a-standalone-azure-automation-account"></a>Creare un account di Automazione di Azure autonomo

Questo articolo mostra come creare un account di Automazione di Azure nel portale di Azure. È possibile usare l'account di automazione del portale per valutare e ottenere informazioni sull'automazione senza usare soluzioni di gestione aggiuntive o l'integrazione con i log di monitoraggio di Azure. È possibile aggiungere tali soluzioni di gestione oppure integrarsi con i log di monitoraggio di Azure per il monitoraggio avanzato dei processi di Runbook in qualsiasi momento in futuro.

Con un account di Automazione è possibile autenticare i runbook gestendo le risorse in Azure Resource Manager o nel modello di distribuzione classico. Un account di Automazione può gestire le risorse in tutte le aree e le sottoscrizioni di uno specifico tenant.

Quando si crea un account di automazione nella portale di Azure, l'account **RunAs** viene creato automaticamente. Questo account esegue le attività seguenti:

* Crea un'entità servizio in Azure Active Directory (Azure AD).
* Crea un certificato.
* Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Azure Resource Manager usando i runbook.

Con questo account creato automaticamente, è possibile iniziare rapidamente a compilare e distribuire manuali operativi per supportare le esigenze di automazione.

## <a name="permissions-required-to-create-an-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione

Per creare o aggiornare un account di Automazione e completare le attività descritte in questo articolo, è necessario disporre dei privilegi e delle autorizzazioni seguenti:

* Per creare un account di automazione, è necessario aggiungere l'account utente Azure AD a un ruolo con autorizzazioni equivalenti al ruolo proprietario `Microsoft.Automation` per le risorse. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Nel portale di Azure, in **Azure Active Directory** > **gestire** > **le impostazioni utente**, se **registrazioni app** è impostata su **Sì**, gli utenti non amministratori nel tenant di Azure ad possono [registrare Active Directory applicazioni](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se l'opzione **Registrazioni per l'app** è impostata su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD.

Se non si è membri dell'istanza di Active Directory della sottoscrizione prima di essere aggiunti al ruolo amministratore globale/coamministratore della sottoscrizione, si viene aggiunti a Active Directory come Guest. In questo scenario viene visualizzato questo messaggio nel riquadro Aggiungi account di automazione:`You do not have permissions to create.`

Se un utente viene aggiunto per primo al ruolo amministratore globale/coamministratore, è possibile rimuovere l'utente dall'istanza Active Directory della sottoscrizione. È possibile leggere l'utente per il ruolo utente in Active Directory.

Per verificare i ruoli utente:

1. Nel portale di Azure passare al riquadro Azure Active Directory.
1. Selezionare **utenti e gruppi**.
1. Selezionare **tutti gli utenti**.
1. Dopo aver selezionato un utente specifico selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Creare un nuovo account di Automazione nel portale di Azure

Per creare un account di Automazione di Azure nel portale di Azure, seguire questa procedura:

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
1. Selezionare **+ Crea una risorsa**.
1. Cercare **Automazione**. Nei risultati della ricerca selezionare **Automazione**.

   ![Cercare e selezionare Automazione e controllo in Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Nella schermata successiva selezionare **Crea nuovo**.

   ![Aggiungi account di Automazione](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se viene visualizzato il messaggio seguente nel riquadro Aggiungi account di Automazione l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
   >
   > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-without-perms.png)

1. Nel riquadro Aggiungi account di automazione immettere un nome per il nuovo account di automazione nel campo **nome** . Non è possibile modificare questo nome dopo che è stato scelto. 

    > [!NOTE]
    > I nomi degli account di Automazione devono essere univoci in ogni area e gruppo di risorse. I nomi degli account di automazione eliminati potrebbero non essere immediatamente disponibili.

1. Se si dispone di più di una sottoscrizione, utilizzare il campo **sottoscrizione** per specificare la sottoscrizione da utilizzare per il nuovo account.
1. In **Gruppo di risorse** immettere o selezionare un gruppo di risorse nuovo o esistente.
1. In **Località** selezionare una località per il data center di Azure.
1. Per l'opzione **Crea account RunAs di Azure** , verificare che sia selezionata l'opzione **Sì** , quindi fare clic su **Crea**.

   > [!NOTE]
   > Se si sceglie di non creare l'account RunAs selezionando **No** per **Crea un account RunAs di Azure**, viene visualizzato un messaggio nel riquadro Aggiungi account di Automazione. Anche se l'account viene creato nel portale di Azure, l'account non ha un'identità di autenticazione corrispondente nella sottoscrizione del modello di distribuzione classico o nel servizio directory della sottoscrizione di Azure Resource Manager. Pertanto, l'account di Automazione non ha accesso alle risorse nella sottoscrizione. Questo impedirà ai runbook che fanno riferimento a questo account di completare l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
   >
   > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
   >

1. Per tenere traccia dello stato di avanzamento della creazione dell'account di automazione, selezionare **notifiche** dal menu.

### <a name="resources-included"></a>Risorse incluse

Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. Dopo la creazione, è possibile eliminare questi runbook in tutta sicurezza, se non li si vuole mantenere. Gli account RunAs possono essere usati per l'autenticazione dell'account in un runbook e devono essere lasciati, a meno che non se ne crei un altro o non ce ne sia bisogno. La tabella seguente offre un riepilogo delle risorse per l'account RunAs.

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial |Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs. Il runbook ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript |Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs. Il runbook ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Runbook di Python di esempio che illustra come eseguire l'autenticazione con l'account RunAs. I runbook elencato tutti i gruppi di risorse presenti nella sottoscrizione. |
| AzureRunAsCertificate |Asset di certificato creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. Il certificato esegue l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager dai runbook. La durata di questo certificato è di un anno. |
| AzureRunAsConnection |Asset di connessione creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. |

## <a name="create-a-classic-run-as-account"></a>Creare un account RunAs classico

Gli account RunAs classici non vengono più creati per impostazione predefinita quando si crea un account di automazione di Azure. Se è ancora necessario un account RunAs classico:

1. Dall'account di automazione selezionare **account RunAs** in **Impostazioni account**.
2. Selezionare l' **account RunAs classico di Azure**.
3. Fare clic su **Crea** per procedere con la creazione dell'account RunAs classico.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* Per iniziare a usare PowerShell manuali operativi, vedere [il primo Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per iniziare a usare manuali operativi del flusso di lavoro PowerShell, vedere [il primo Runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a usare Python 2 manuali operativi, vedere [il primo python2 Runbook](automation-first-runbook-textual-python2.md).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
