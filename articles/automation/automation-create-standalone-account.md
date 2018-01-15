---
title: Creare un account di Automazione di Azure autonomo | Microsoft Docs
description: "Esercitazione che illustra come creare, testare e usare l'autenticazione dell'entità di sicurezza in Automazione di Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: 0397b45753ea64d1a33916d5e0dff12d6e1d80aa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Creare un account di Automazione di Azure autonomo
Questo argomento illustra come creare un account di Automazione dal portale di Azure se si vuole valutare e imparare a usare Automazione di Azure senza includere soluzioni di gestione aggiuntive o l'integrazione con OMS Log Analytics per offrire il monitoraggio avanzato dei processi dei runbook.  È possibile aggiungere le soluzioni di gestione o eseguire l'integrazione con Log Analytics in qualsiasi momento in futuro.  Con l'account di Automazione è possibile autenticare i runbook che gestiscono risorse in Azure Resource Manager o nella distribuzione classica di Azure.

Quando si crea un account di Automazione nel portale di Azure, viene creato automaticamente quanto segue:

* Account RunAs che crea una nuova entità servizio in Azure Active Directory e un certificato e assegna il ruolo Collaboratore per il controllo degli accessi in base al ruolo, che viene usato per gestire le risorse di Resource Manager con i runbook.   
* Account RunAs classico, caricando un certificato di gestione che viene usato per gestire le risorse classiche con i runbook.  

Questo semplifica il processo e consente di iniziare rapidamente la compilazione e distribuzione di runbook per supportare le esigenze di automazione.  

## <a name="permissions-required-to-create-automation-account"></a>Autorizzazioni necessarie per creare un account di Automazione
Per creare o aggiornare un account di Automazione, è necessario avere le autorizzazioni e i privilegi specifici seguenti, necessari per il completamento di questo argomento.   
 
* Per creare un account di Automazione, l'account utente di AD deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Proprietario per le risorse di Microsoft.Automation, come indicato nell'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  
* Se le impostazioni delle registrazioni dell'app sono impostate su **Sì**, gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Se le impostazioni delle registrazioni dell'app sono impostate su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD. 

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questa situazione si riceve un avviso di tipo "L'utente non è autorizzato a creare..." nel pannello **Aggiungi account di Automazione**. Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure selezionare **Utenti e gruppi**, **Tutti gli utenti** e, dopo avere selezionato l'utente specifico, selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Creare un account di Automazione dal Portale di Azure
La procedura descritta in questa sezione consente di creare un account di Automazione di Azure nel portale di Azure.    

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Fare clic su **Nuovo**.<br><br> ![Selezionare l'opzione Nuovo nel portale di Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Cercare **Automazione** e quindi nei risultati della ricerca selezionare **Automation & Control***.<br><br> ![Cercare e selezionare Automazione nel Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br><br>![Aggiungi account di Automazione](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > Se nel pannello **Aggiungi account di Automazione** viene visualizzato l'avviso seguente, l'account non è membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.<br><br>![Aggiungi account di Automazione, avviso](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.
5. Se si hanno più sottoscrizioni, specificarne una per il nuovo account, un **gruppo di risorse** nuovo o esistente e la **località** di un data center di Azure.
6. Verificare che sia selezionato il valore **Sì** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.  
   
   > [!NOTE]
   > Se si sceglie di non creare l'account RunAs selezionando l'opzione **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**.  L'account viene creato nel portale di Azure, ma non ha un'identità di autenticazione corrispondente nel servizio directory della sottoscrizione classica o Resource Manager e, di conseguenza, non avrà accesso alle risorse nella sottoscrizione.  Questo impedirà ai runbook che fanno riferimento a questo account di completare l'autenticazione ed eseguire attività sulle risorse in tali modelli di distribuzione.
   > 
   > ![Aggiungi account di Automazione, avviso](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Se non viene creata l'entità servizio, il ruolo Collaboratore non viene assegnato.
   > 

7. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="resources-included"></a>Risorse incluse
Quando la creazione dell'account di Automazione viene completata, vengono create automaticamente diverse risorse.  La tabella seguente offre un riepilogo delle risorse per l'account RunAs.<br>

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureAutomationTutorial |Runbook grafico di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialScript |Runbook di PowerShell di esempio che illustra come eseguire l'autenticazione con l'account RunAs e ottiene tutte le risorse di Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Runbook di Python di esempio che illustra come eseguire l'autenticazione usando l'account RunAs e quindi elenca i gruppi di risorse presenti nella sottoscrizione specificata. |
| AzureRunAsCertificate |Asset di certificato creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente.  Consente di eseguire l'autenticazione con Azure per poter gestire le risorse di Azure Resource Manager con i runbook.  La durata di questo certificato è di un anno. |
| AzureRunAsConnection |Asset di connessione creato automaticamente durante la creazione dell'account di Automazione oppure usando lo script di PowerShell riportato più avanti per un account esistente. |

La tabella seguente offre un riepilogo delle risorse per l'account RunAs classico.<br>

| Risorsa | Descrizione |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Runbook grafico di esempio che ottiene tutte le VM in modalità classica in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM. |
| Runbook di script AzureClassicAutomationTutorial |Runbook PowerShell di esempio che ottiene tutte le VM in modalità classica in una sottoscrizione usando l'account RunAs classico (certificato) e quindi restituisce il nome e lo stato delle VM. |
| AzureClassicRunAsCertificate |Asset di certificato creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook.  La durata di questo certificato è di un anno. |
| AzureClassicRunAsConnection |Asset di connessione creato automaticamente e usato per eseguire l'autenticazione con Azure per poter gestire le risorse classiche di Azure con i runbook. |


## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a usare i runbook di Python2, vedere [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Primo runbook di Python2).
