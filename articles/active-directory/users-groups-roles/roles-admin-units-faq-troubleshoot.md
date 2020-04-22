---
title: Risoluzione dei problemi relativi alle unità amministrative e domande frequenti - Azure Active Directory Documenti Microsoft
description: Esaminare le unità amministrative per concedere autorizzazioni con ambito limitato in Azure Active Directory.Investigate administrative units to grant permissions with restricted scope in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684848"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD administrative units: Troubleshooting and FAQ

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare gli utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative. Per script di PowerShell di esempio per le attività comuni, vedere [Usare le unità amministrative.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Perché non è possibile creare un'unità amministrativa?**

**A:** Solo un *amministratore globale* o un amministratore del ruolo con privilegi può creare un'unità *amministrativa* in Azure AD. Verificare che all'utente che sta tentando di creare l'unità amministrativa venga assegnato il ruolo *Amministratore globale* o Amministratore ruolo *con privilegi.*

**D: Ho aggiunto un gruppo all'unità amministrativa. Perché i membri del gruppo non vengono ancora visualizzati lassù?**

**A:** Quando si aggiunge un gruppo all'unità amministrativa, non tutti i membri del gruppo vengono aggiunti ad esso. Gli utenti devono essere assegnati direttamente all'unità amministrativa.

**D: Ho appena aggiunto (o rimosso) un membro dell'unità amministrativa. Perché il membro non viene visualizzato (o continua a essere visualizzato) nell'interfaccia utente?**

**A:** In alcuni modi, l'elaborazione dell'aggiunta o della rimozione di uno o più membri dell'unità amministrativa potrebbe richiedere alcuni minuti per essere riflessa nella pagina **Unità amministrative.** In alternativa, è possibile passare direttamente alle proprietà della risorsa associata e verificare se l'azione è stata completata. Per ulteriori informazioni su utenti e gruppi in unità di gestione delle unità di guida, vedere [Elencare le unità amministrative per un utente](roles-admin-units-add-manage-users.md) ed [Elencare le unità amministrative per un gruppo.](roles-admin-units-add-manage-groups.md)

**D: Sono un amministratore delegato di password in un'unità amministrativa. Perché non riesco a reimpostare la password di un utente specifico?**

**A:** In qualità di amministratore di un'unità amministrativa, è possibile reimpostare le password solo per gli utenti assegnati all'unità amministrativa. Assicurarsi che l'utente la cui reimpostazione della password non riesce appartenga all'unità amministrativa a cui si è stati assegnati. Se l'utente appartiene alla stessa unità amministrativa ma non è ancora possibile reimpostare la password, controllare i ruoli assegnati all'utente. 

Per impedire un'elevazione dei privilegi, un amministratore con ambito di unità amministrativa non può reimpostare la password di un utente assegnato a un ruolo con un ambito a livello di organizzazione.

**D: Perché sono necessarie unità amministrative? Non è stato possibile utilizzare i gruppi di sicurezza come modo per definire un ambito?**

**A:** I gruppi di sicurezza dispongono di uno scopo e di un modello di autorizzazione esistenti. Un *amministratore utente,* ad esempio, può gestire l'appartenenza a tutti i gruppi di sicurezza nell'organizzazione di Azure AD. Il ruolo può utilizzare i gruppi per gestire l'accesso ad applicazioni come Salesforce. Un *amministratore utenti* non dovrebbe essere in grado di gestire il modello di delega stesso, che sarebbe il risultato se i gruppi di sicurezza venissero estesi per supportare scenari di "raggruppamento delle risorse". Le unità amministrative, ad esempio le unità organizzative in Active Directory di Windows Server, hanno lo scopo di fornire un modo per definire l'ambito di amministrazione di un'ampia gamma di oggetti directory. I gruppi di sicurezza stessi possono essere membri degli ambiti delle risorse. L'utilizzo di gruppi di sicurezza per definire il set di gruppi di sicurezza che un amministratore può gestire potrebbe creare confusione.

**D: Cosa significa aggiungere un gruppo a un'unità amministrativa?**

**A:** L'aggiunta di un gruppo a un'unità amministrativa porta il gruppo stesso nell'ambito di gestione di qualsiasi *amministratore utente* che ha anche ambito tale unità amministrativa. Gli amministratori utente dell'unità amministrativa possono gestire il nome e l'appartenenza del gruppo stesso. Non concede *all'amministratore utente* le autorizzazioni per l'unità amministrativa per gestire gli utenti del gruppo (ad esempio, per reimpostare le password). Per concedere *all'amministratore* utente la possibilità di gestire gli utenti, gli utenti devono essere membri diretti dell'unità amministrativa.

**D: Una risorsa (utente o gruppo) può essere membro di più unità amministrative?**

**A:** Sì, una risorsa può essere membro di più unità amministrative. La risorsa può essere gestita da tutti gli amministratori amministrativi a livello di unità e a livello di organizzazione che dispongono delle autorizzazioni sulla risorsa.

**D: Le unità amministrative sono disponibili nelle organizzazioni B2C?**

**A:** No, le unità amministrative non sono disponibili per le organizzazioni B2C.

**D: Sono supportate unità amministrative annidate?**

**A:** No, le unità amministrative annidate non sono supportate.

**D: Le unità amministrative sono supportate in PowerShell e nell'API Graph?**

**A:** Sì. Il supporto per le unità amministrative è disponibili nella [documentazione dei cmdlet](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) di PowerShell e negli [script di esempio.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview) 

Trovare il supporto per il [tipo di risorsa administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) in Microsoft Graph.Find support for the administrativeUnit resource type in Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

- [Limitare l'ambito per i ruoli utilizzando le unità amministrative](directory-administrative-units.md)
- [Gestire le unità amministrative](roles-admin-units-manage.md)
