---
title: Creare un account di Automazione di Azure autonomo
description: Questo articolo illustra la procedura per creare, testare e usare l'autenticazione di un'entità di sicurezza di esempio in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32fafaeb6332ca0e76dbc8d72f11872a82ca1cbe
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779148"
---
# <a name="create-a-standalone-azure-automation-account"></a>Creare un account di Automazione di Azure autonomo

Questo articolo mostra come creare un account di Automazione di Azure nel portale di Azure. È possibile usare l'account di automazione del portale per valutare e ottenere informazioni sull'automazione senza usare soluzioni di gestione aggiuntive o l'integrazione con i log di monitoraggio di Azure. È possibile aggiungere tali soluzioni di gestione oppure integrarsi con i log di monitoraggio di Azure per il monitoraggio avanzato dei processi di Runbook in qualsiasi momento in futuro.

Con un account di Automazione è possibile autenticare i runbook gestendo le risorse in Azure Resource Manager o nel modello di distribuzione classico. Un account di Automazione può gestire le risorse in tutte le aree e le sottoscrizioni di uno specifico tenant.

Quando si crea un account di Automazione nel portale di Azure, vengono creati automaticamente questi account:

* **Account RunAs**. Questo account esegue le attività seguenti:
  * Crea un'entità servizio in Azure Active Directory (Azure AD).
  * Crea un certificato.
  * Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Azure Resource Manager usando i runbook.
* **Account RunAs classico**. Questo account carica un certificato di gestione. Il certificato gestisce le risorse classiche tramite runbook.

Con questi account già creati è possibile iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.

## <a name="permissions-required-to-create-an-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione

Per creare o aggiornare un account di Automazione e completare le attività descritte in questo articolo, è necessario disporre dei privilegi e delle autorizzazioni seguenti:

* Per creare un account di automazione, l'account utente di Azure AD deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Proprietario per le risorse**Microsoft. Automation**. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Nel portale di Azure, in **Azure Active Directory** > **gestire** > **le impostazioni utente**, se **registrazioni app** è impostata su **Sì**, gli utenti non amministratori nel tenant di Azure ad possono [registrare Active Applicazioni di directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se l'opzione **Registrazioni per l'app** è impostata su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questo scenario, viene visualizzato questo messaggio nella pagina **Aggiungi account di Automazione**: "Non si hanno le autorizzazioni per aprire il file."

Gli utenti inizialmente aggiunti al ruolo di amministratore globale o coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e quindi aggiunti nuovamente al ruolo utente completo in Active Directory.

Per verificare i ruoli utente:

1. Nel portale di Azure passare al riquadro **Azure Active Directory**.
1. Selezionare **Utenti e gruppi**.
1. Selezionare **Tutti gli utenti**.
1. Dopo aver selezionato un utente specifico selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Creare un nuovo account di Automazione nel portale di Azure

Per creare un account di Automazione di Azure nel portale di Azure, seguire questa procedura:

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
1. Selezionare **+ Crea una risorsa**.
1. Cercare **Automazione**. Nei risultati della ricerca selezionare **Automazione**.

   ![Cercare e selezionare Automazione e controllo in Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Nella schermata successiva selezionare **Crea**.

   ![Aggiungi account di Automazione](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se viene visualizzato il messaggio seguente nel riquadro **Aggiungi account di Automazione** l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
   >
   > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-without-perms.png)

1. Nella casella **Nome** del riquadro **Aggiungi account di Automazione** immettere un nome per il nuovo account di Automazione. Dopo che è stato scelto, questo nome non può essere modificato. *I nomi degli account di Automazione devono essere univoci in ogni area e gruppo di risorse. I nomi per gli account di Automazione che sono stati eliminati potrebbero non essere subito disponibili.*
1. Se sono disponibili più sottoscrizioni, nella casella **Sottoscrizione** specificare la sottoscrizione che si vuole usare per il nuovo account.
1. In **Gruppo di risorse** immettere o selezionare un gruppo di risorse nuovo o esistente.
1. In **Località** selezionare una località per il data center di Azure.
1. Per l'opzione **Crea un account RunAs di Azure**, assicurarsi che sia selezionato **Sì** e quindi selezionare **Crea**.

   > [!NOTE]
   > Se si sceglie di non creare l'account RunAs selezionando **No** per **Crea un account RunAs di Azure**, viene visualizzato un messaggio nel riquadro **Aggiungi account di Automazione**. Anche se l'account viene creato nel portale di Azure, l'account non ha un'identità di autenticazione corrispondente nella sottoscrizione del modello di distribuzione classico o nel servizio directory della sottoscrizione di Azure Resource Manager. Pertanto, l'account di Automazione non ha accesso alle risorse nella sottoscrizione. Questo impedirà ai runbook che fanno riferimento a questo account di completare l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
   >
   > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
   >

1. Per tenere traccia dello stato della creazione dell'account di Automazione, scegliere **Notifiche** dal menu.

### <a name="resources-included"></a>Risorse incluse

Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. Dopo la creazione, è possibile eliminare questi runbook in tutta sicurezza, se non li si vuole mantenere. Gli account RunAs possono essere usati per l'autenticazione dell'account in un runbook e devono essere lasciati, a meno che non se ne crei un altro o non ce ne sia bisogno. La tabella seguente offre un riepilogo delle risorse per l'account RunAs.

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial |Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs. Il runbook ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript |Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs. Il runbook ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Runbook di Python di esempio che illustra come eseguire l'autenticazione con l'account RunAs. I runbook elencato tutti i gruppi di risorse presenti nella sottoscrizione. |
| AzureRunAsCertificate |Asset di certificato creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. Il certificato esegue l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager dai runbook. La durata di questo certificato è di un anno. |
| AzureRunAsConnection |Asset di connessione creato automaticamente quando si crea un account di Automazione o si usa lo script di PowerShell per un account esistente. |

La tabella seguente offre un riepilogo delle risorse per l'account RunAs classico.

| Risorsa | DESCRIZIONE |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Runbook grafico di esempio. Il runbook ottiene tutte le macchine virtuali classiche in una sottoscrizione tramite l'account RunAs classico (certificato). Visualizza quindi i nomi e lo stato delle macchine virtuali. |
| Runbook di script AzureClassicAutomationTutorial |Runbook di PowerShell di esempio. Il runbook ottiene tutte le macchine virtuali classiche in una sottoscrizione tramite l'account RunAs classico (certificato). Visualizza quindi i nomi e lo stato delle macchine virtuali. |
| AzureClassicRunAsCertificate |Asset del certificato creato automaticamente. Il certificato esegue l'autenticazione con Azure per poter gestire le risorse classiche di Azure dai runbook. La durata di questo certificato è di un anno. |
| AzureClassicRunAsConnection |Asset di connessione creato automaticamente. L'asset esegue l'autenticazione con Azure per poter gestire le risorse classiche di Azure dai runbook. |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a usare i runbook di Python2, vedere [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Primo runbook di Python2).

