---
title: Risoluzione dei problemi relativi alle unità amministrative e alle domande frequenti - Azure Active Directory Documenti Microsoft
description: Esaminare le unità amministrative alla delega delle autorizzazioni con ambito limitato in Azure Active DirectoryInvestigate administrative units to delegation of permissions with restricted scope in Azure Active Directory
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
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428147"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Risoluzione dei problemi e domande frequenti per le unità amministrative in Azure Active DirectoryTroubleshooting and FAQ for administrative units in Azure Active Directory

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare gli utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative.For more granular administrative control in Azure Active Directory (Azure AD), you can assign users to an Azure AD role with a scope limited to one or more administrative units (AUs). È possibile trovare script di PowerShell di esempio per le attività comuni in https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0.

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Non sono in grado di creare un'unità amministrativa**

**A:** Solo un amministratore globale o un amministratore del ruolo con privilegi può creare un'unità amministrativa in Azure AD. Verificare che all'utente che tenta di creare l'unità amministrativa sia assegnato il ruolo Amministratore globale o Amministratore ruolo con privilegi.

**D: Ho aggiunto un gruppo all'unità amministrativa, ma i membri del gruppo non vengono ancora visualizzati nell'unità amministrativa**

**A:** Quando si aggiunge un gruppo all'unità amministrativa, ciò non comporta l'aggiunta di tutti i membri del gruppo all'unità amministrativa. Gli utenti devono essere assegnati direttamente all'unità amministrativa.

**D: Ho appena aggiunto / rimosso un membro dell'unità amministrativa e viene ancora visualizzato nell'interfaccia utente**

**A:** Talvolta l'elaborazione dell'aggiunta o della rimozione di uno o più membri dell'unità amministrativa potrebbe richiedere alcuni minuti per essere riportata nella pagina **Unità amministrative.** È possibile scegliere di attendere alcuni minuti affinché venga riflessa sotto le unità amministrative. In alternativa, è possibile passare direttamente alle proprietà della risorsa associata e verificare se l'azione è stata completata. Per ulteriori informazioni su utenti e gruppi in unità di gestione delle unità di guida, vedere [Elencare le unità amministrative per un utente](roles-admin-units-add-manage-users.md) ed [Elencare le unità amministrative per un gruppo.](roles-admin-units-add-manage-groups.md)

**D: In qualità di amministratore delegato di password in un'unità amministrativa, non riesco a reimpostare la password di un utente specifico**

**A:** Un amministratore assegnato su un'unità amministrativa è possibile reimpostare la password solo per gli utenti assegnati all'unità amministrativa. Assicurarsi che l'utente per il quale la reimpostazione della password non riesce appartiene alle unità amministrative a cui è stato assegnato il ruolo. Se l'utente appartiene alla stessa unità amministrativa e non è ancora possibile reimpostare la password dell'utente, controllare i ruoli assegnati all'utente. Per impedire un'elevazione dei privilegi, un amministratore con ambito di unità amministrativa non può reimpostare la password di un utente assegnato a un ruolo con un ambito a livello di organizzazione.

**D: Perché sono necessarie unità amministrative? Non è stato possibile utilizzare i gruppi di sicurezza come modo per definire un ambito?**

**A:** I gruppi di sicurezza dispongono di uno scopo e di un modello di autorizzazione esistenti. A User administrator, for example, can manage membership of all security groups in the Azure AD organization, such as to use groups to manage access to applications like Salesforce. Un amministratore utente non deve avere la possibilità di gestire il modello di delega stesso, che sarebbe il risultato se i gruppi di sicurezza venissero estesi per supportare scenari di "raggruppamento di risorse". Le unità amministrative, ad esempio le unità organizzative in Active Directory di Windows Server, hanno lo scopo di fornire un modo per definire l'ambito di amministrazione di un'ampia gamma di oggetti directory. I gruppi di sicurezza stessi possono essere membri degli ambiti delle risorse. L'utilizzo di gruppi di sicurezza per definire il set di gruppi di sicurezza che un amministratore può gestire potrebbe creare confusione.

**D: Cosa significa aggiungere un gruppo a un'unità amministrativa?**

**A:** L'aggiunta di un gruppo a un'unità amministrativa porta il gruppo stesso nell'ambito di gestione di qualsiasi amministratore utente che ha anche ambito tale unità amministratore. Gli amministratori utente per l'unità amministrativa possono gestire il nome e l'appartenenza del gruppo stesso. Non concede all'amministratore dell'utente alcuna autorizzazione per gestire gli utenti del gruppo (ad esempio, reimpostare le password). Per concedere all'amministratore utente la possibilità di gestire gli utenti, gli utenti devono essere membri diretti dell'unità amministrativa.

**D: Una risorsa (utente o gruppo) può essere membro di più unità amministrative?**

**A:** Sì, una risorsa può essere membro di più unità amministrative. La risorsa può essere gestita da tutti gli amministratori con ambito unità a livello di organizzazione e amministrativo che dispongono delle autorizzazioni sulla risorsa.

**D: Le unità amministrative sono disponibili nelle organizzazioni B2C?**

**A:** No, le unità amministrative non sono disponibili per le organizzazioni B2C.

**D: Sono supportate unità amministrative annidate?**

**A:** Le unità amministrative annidate non sono supportate.

**D: Le unità amministrative sono supportate in PowerShell e nell'API Graph?**

**A:** Sì. Il supporto per le unità amministrative è disponibile nella [documentazione del cmdlet PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e [negli script di esempio](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)e il supporto è in Microsoft Graph per il tipo di risorsa [administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Passaggi successivi

- [Unità amministrative per limitare l'ambito per la panoramica dei ruoli](directory-administrative-units.md)
- [Gestire le unità amministrative](roles-admin-units-manage.md)