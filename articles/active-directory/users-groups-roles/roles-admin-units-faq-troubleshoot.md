---
title: Risoluzione dei problemi e domande frequenti sulle unità amministrative-Azure Active Directory | Microsoft Docs
description: Esaminare le unità amministrative per concedere autorizzazioni con ambito limitato in Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684848"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD unità amministrative: risoluzione dei problemi e domande frequenti

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative (AUs). Per gli script di PowerShell di esempio per le attività comuni, vedere [usare unità amministrative](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: perché non è possibile creare un'unità amministrativa?**

**R:** Solo un amministratore *globale* o un *amministratore del ruolo con privilegi* può creare un'unità amministrativa in Azure ad. Verificare che all'utente che sta tentando di creare l'unità amministrativa sia assegnato il ruolo di amministratore *globale* o *amministratore del ruolo con privilegi* .

**D: è stato aggiunto un gruppo all'unità amministrativa. Perché i membri del gruppo non vengono ancora visualizzati?**

**R:** Quando si aggiunge un gruppo all'unità amministrativa, questo non comporta l'aggiunta di tutti i membri del gruppo. Gli utenti devono essere assegnati direttamente all'unità amministrativa.

**D: è stato appena aggiunto o rimosso un membro dell'unità amministrativa. Perché il membro non viene visualizzato (o ancora visualizzato) nell'interfaccia utente?**

**R:** In alcuni casi, l'elaborazione dell'aggiunta o della rimozione di uno o più membri dell'unità amministrativa potrebbe richiedere alcuni minuti per essere riflessa nella pagina **unità amministrative** . In alternativa, è possibile passare direttamente alle proprietà della risorsa associata e verificare se l'azione è stata completata. Per altre informazioni su utenti e gruppi in AUs, vedere [elencare le unità amministrative per un utente](roles-admin-units-add-manage-users.md) ed [elencare le unità amministrative per un gruppo](roles-admin-units-add-manage-groups.md).

**D: sono un amministratore di password delegata in un'unità amministrativa. Perché non è possibile reimpostare la password di un utente specifico?**

**R:** In qualità di amministratore di un'unità amministrativa, è possibile reimpostare le password solo per gli utenti assegnati all'unità amministrativa. Assicurarsi che l'utente la cui reimpostazione della password non riesca appartenga all'unità amministrativa a cui è stato assegnato. Se l'utente appartiene alla stessa unità amministrativa ma non è ancora possibile reimpostare la password, controllare i ruoli assegnati all'utente. 

Per evitare un'elevazione dei privilegi, un amministratore con ambito di unità amministrative non può reimpostare la password di un utente assegnato a un ruolo con ambito a livello di organizzazione.

**D: perché sono necessarie unità amministrative? Non è stato possibile usare i gruppi di sicurezza come metodo per definire un ambito?**

**R:** I gruppi di sicurezza hanno un modello di scopo e autorizzazione esistente. Un *amministratore utente*, ad esempio, può gestire l'appartenenza di tutti i gruppi di sicurezza nell'organizzazione Azure ad. Il ruolo può usare i gruppi per gestire l'accesso ad applicazioni come Salesforce. Un *amministratore utente* non deve essere in grado di gestire il modello di delega, come risultato se i gruppi di sicurezza sono stati estesi per supportare scenari di "raggruppamento di risorse". Le unità amministrative, ad esempio le unità organizzative in Windows Server Active Directory, consentono di definire l'ambito di amministrazione di una vasta gamma di oggetti directory. I gruppi di sicurezza possono essere membri degli ambiti delle risorse. L'uso di gruppi di sicurezza per definire il set di gruppi di sicurezza che un amministratore è in grado di gestire potrebbe creare confusione.

**D: cosa significa aggiungere un gruppo a un'unità amministrativa?**

**R:** Se si aggiunge un gruppo a un'unità amministrativa, il gruppo stesso viene inserito nell'ambito di gestione di qualsiasi *amministratore utente* che abbia anche l'ambito di tale unità amministrativa. Gli amministratori utente per l'unità amministrativa possono gestire il nome e l'appartenenza del gruppo stesso. Non concede all' *amministratore utente* le autorizzazioni per le unità amministrative per gestire gli utenti del gruppo, ad esempio per reimpostare le password. Per concedere all' *amministratore utente* la possibilità di gestire gli utenti, gli utenti devono essere membri diretti dell'unità amministrativa.

**D: una risorsa (utente o gruppo) può essere un membro di più di un'unità amministrativa?**

**R:** Sì, una risorsa può essere un membro di più di un'unità amministrativa. La risorsa può essere gestita da tutti gli amministratori con ambito di unità amministrative e a livello di organizzazione che dispongono delle autorizzazioni per la risorsa.

**D: le unità amministrative sono disponibili nelle organizzazioni B2C?**

**R:** No, le unità amministrative non sono disponibili per le organizzazioni B2C.

**D: sono supportate unità amministrative annidate?**

**R:** No, le unità amministrative annidate non sono supportate.

**D: le unità amministrative sono supportate in PowerShell e in API Graph?**

**R:** Sì. Il supporto per le unità amministrative è disponibile nella [documentazione dei cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) e negli script di [esempio](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview). 

Trovare il supporto per il [tipo di risorsa entità administrativeunit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) in Microsoft Graph.

## <a name="next-steps"></a>Passaggi successivi

- [Limitare l'ambito per i ruoli usando unità amministrative](directory-administrative-units.md)
- [Gestire le unità amministrative](roles-admin-units-manage.md)
