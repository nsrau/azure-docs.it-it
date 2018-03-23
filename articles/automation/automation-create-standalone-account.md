---
title: Creare un account di Automazione di Azure autonomo
description: Questo articolo illustra la procedura per creare, testare e usare l'autenticazione di un'entità di sicurezza di esempio in Automazione di Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 5ea3d1af6f8bb4a6c0ef45560d8707afc58f61b1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Creare un account di Automazione di Azure autonomo
Questo articolo mostra come creare un account di Automazione di Azure nel portale di Azure. È possibile usare l'account di Automazione del portale per valutare il servizio Automazione e scoprire di più su questo servizio, senza dover usare soluzioni di gestione aggiuntive o l'integrazione con Azure Log Analytics in Operations Management Suite (OMS). È possibile aggiungere tali soluzioni di gestione o attivare l'integrazione con Log Analytics per il monitoraggio avanzato di processi del runbook in qualsiasi momento in futuro. 

Con un account di Automazione è possibile autenticare i runbook gestendo le risorse in Azure Resource Manager o nel modello di distribuzione classico.

Quando si crea un account di Automazione nel portale di Azure, vengono creati automaticamente questi account:

* **Account RunAs**. Questo account esegue le attività seguenti:
  - Crea un'entità servizio in Azure Active Directory (Azure AD).
  - Crea un certificato.
  - Assegna il controllo degli accessi in base al ruolo Collaboratore, che permette di gestire le risorse di Azure Resource Manager usando i runbook.
* **Account RunAs classico**. Questo account carica un certificato di gestione. Il certificato gestisce le risorse classiche tramite runbook.

Con questi account già creati è possibile iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.  

## <a name="permissions-required-to-create-an-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione
Per creare o aggiornare un account di Automazione e completare le attività descritte in questo articolo, è necessario disporre dei privilegi e delle autorizzazioni seguenti: 

* Per creare un account di automazione, l'account utente di Azure AD deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Proprietario per le risorse**Microsoft. Automation**. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  
* Nel portale di Azure, in **Azure Active Directory** > **Gestisci** > **Registrazioni per l'app**, se l'opzione **Registrazioni per l'app** è impostata su **Sì**, gli utenti senza privilegi di amministratore nel tenant di Azure AD possono [registrare applicazioni Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Se l'opzione **Registrazioni per l'app** è impostata su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD. 

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questo scenario, viene visualizzato questo messaggio nella pagina **Aggiungi account di Automazione**: "Le autorizzazioni non sono sufficienti per creare." 

Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente al ruolo Utente completo in Active Directory.

Per verificare i ruoli utente:
1. Nel portale di Azure passare al riquadro **Azure Active Directory**.
2. Selezionare **Utenti e gruppi**.
3. Selezionare **Tutti gli utenti**. 
4. Dopo aver selezionato un utente specifico selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Creare un nuovo account di Automazione nel portale di Azure
Per creare un account di Automazione di Azure nel portale di Azure, seguire questa procedura:    

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Selezionare **+ Crea una risorsa**.
3. Cercare **Automazione**. Nei risultati della ricerca selezionare **Automazione**.<br><br> ![Cercare e selezionare Automazione e controllo in Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
4. Nella schermata successiva selezionare **Crea**.
  ![Aggiungi account di Automazione](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
  
  > [!NOTE]
  > Se viene visualizzato il messaggio seguente nel riquadro **Aggiungi account di Automazione** l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
  >
  > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-without-perms.png)
  >
5. Nella casella **Nome** del riquadro **Aggiungi account di Automazione** immettere un nome per il nuovo account di Automazione.
6. Se sono disponibili più sottoscrizioni, nella casella **Sottoscrizione** specificare la sottoscrizione che si vuole usare per il nuovo account. 
7. In **Gruppo di risorse** immettere o selezionare un gruppo di risorse nuovo o esistente. 
8. In **Località** selezionare una località per il data center di Azure.
9. Per l'opzione **Crea un account RunAs di Azure**, assicurarsi che sia selezionato **Sì** e quindi selezionare **Crea**.
    
  > [!NOTE]
  > Se si sceglie di non creare l'account RunAs selezionando **No** per **Crea un account RunAs di Azure**, viene visualizzato un messaggio nel riquadro **Aggiungi account di Automazione**. Anche se l'account viene creato nel portale di Azure, l'account non ha un'identità di autenticazione corrispondente nella sottoscrizione del modello di distribuzione classico o nel servizio directory della sottoscrizione di Azure Resource Manager. Pertanto, l'account di Automazione non ha accesso alle risorse nella sottoscrizione. Questo impedirà ai runbook che fanno riferimento a questo account di completare l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
  >
  > ![Avviso per Aggiungi account di Automazione](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
  >
10. Per tenere traccia dello stato della creazione dell'account di Automazione, scegliere **Notifiche** dal menu.

### <a name="resources-included"></a>Risorse incluse
Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse. La tabella seguente offre un riepilogo delle risorse per l'account RunAs.

| Risorsa | DESCRIZIONE |
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
