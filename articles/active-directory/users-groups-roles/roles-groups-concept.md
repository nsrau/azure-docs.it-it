---
title: Usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory | Microsoft Docs
description: Anteprima dei ruoli di Azure AD personalizzati per la delega della gestione delle identità. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65aed14ec7f644f2792aaecde5c8bccfffdd8081
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078445"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)

Azure Active Directory (Azure AD) introduce un'anteprima pubblica in cui è possibile assegnare un gruppo cloud per Azure AD ruoli predefiniti. Con questa funzionalità, è possibile usare i gruppi per concedere l'accesso di amministratore in Azure AD con il minimo sforzo da parte degli amministratori del ruolo globale e con privilegi.

Si consideri questo esempio: Contoso ha assunto persone tra le aree geografiche per gestire e reimpostare le password per i dipendenti nella propria organizzazione Azure AD. Anziché chiedere a un amministratore del ruolo con privilegi o a un amministratore globale di assegnare il ruolo di amministratore del supporto tecnico a ogni persona individualmente, è possibile creare un gruppo di Contoso_Helpdesk_Administrators e assegnarlo al ruolo. Quando gli utenti si uniscono al gruppo, viene assegnato il ruolo indirettamente. Il flusso di lavoro di governance esistente può quindi gestire il processo di approvazione e il controllo dell'appartenenza del gruppo per garantire che solo gli utenti legittimi siano membri del gruppo e siano quindi assegnati al ruolo di amministratore del supporto tecnico.

## <a name="how-this-feature-works"></a>Funzionamento della funzionalità

Creare un nuovo gruppo di sicurezza o di Office 365 con la proprietà' isAssignableToRole ' impostata su' true '. È anche possibile abilitare questa proprietà quando si crea un gruppo nella portale di Azure attivando **Azure ad i ruoli possono essere assegnati al gruppo**. In entrambi i casi, è possibile assegnare il gruppo a uno o più ruoli di Azure AD nello stesso modo in cui si assegnano i ruoli agli utenti. È possibile creare un massimo di 200 gruppi assegnabili al ruolo in un'unica organizzazione Azure AD (tenant).

Se non si desidera che i membri del gruppo dispongano dell'accesso al ruolo, è possibile utilizzare Azure AD Privileged Identity Management. Assegnare un gruppo come membro idoneo di un ruolo Azure AD. Ogni membro del gruppo è quindi idoneo a fare in modo che l'assegnazione venga attivata per il ruolo a cui è assegnato il gruppo. Possono quindi attivare l'assegnazione di ruolo per un periodo di tempo fisso.

> [!Note]
> È necessario avere una versione aggiornata di Privileged Identity Management per poter assegnare un gruppo al ruolo Azure AD tramite PIM. È possibile che si usi la versione precedente di PIM perché l'organizzazione Azure AD utilizza l'API Privileged Identity Management. Rivolgersi all'alias pim_preview@microsoft.com per spostare l'organizzazione e aggiornare l'API. Per altre informazioni [, vedere Azure ad Roles and features in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-roles-features).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Perché viene applicata la creazione di un gruppo speciale per assegnarlo a un ruolo

Se a un gruppo viene assegnato un ruolo, qualsiasi amministratore IT che può gestire l'appartenenza al gruppo potrebbe anche gestire indirettamente l'appartenenza a tale ruolo. Si supponga, ad esempio, che un gruppo Contoso_User_Administrators sia assegnato al ruolo di amministratore dell'account utente. Un amministratore di Exchange che può modificare l'appartenenza al gruppo può aggiungere se stesso al gruppo Contoso_User_Administrators e in questo modo diventa un amministratore dell'account utente. Come si può notare, un amministratore potrebbe elevare i propri privilegi in modo non inteso.

Azure AD consente di proteggere un gruppo assegnato a un ruolo utilizzando una nuova proprietà denominata isAssignableToRole per i gruppi. Solo i gruppi cloud con la proprietà isAssignableToRole impostata su' true ' al momento della creazione possono essere assegnati a un ruolo. Questa proprietà non è modificabile. una volta creato un gruppo con questa proprietà impostata su "true", non è possibile modificarla. Non è possibile impostare la proprietà su un gruppo esistente.
È stato progettato il modo in cui i gruppi vengono assegnati ai ruoli per impedire che si verifichino potenziali violazioni:

- Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono creare un gruppo assegnabile al ruolo (con la proprietà "isAssignableToRole" abilitata).
- Non può essere un gruppo dinamico Azure AD; ovvero deve avere un tipo di appartenenza "assegnato". Il popolamento automatico dei gruppi dinamici può causare l'aggiunta di un account indesiderato al gruppo e quindi l'assegnazione al ruolo.
- Per impostazione predefinita, solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono gestire l'appartenenza di un gruppo assegnabile al ruolo, ma è possibile delegare la gestione dei gruppi assegnabili al ruolo aggiungendo i proprietari del gruppo.
- Per evitare l'elevazione dei privilegi, le credenziali dei membri e dei proprietari di un gruppo assegnabile al ruolo possono essere modificate solo da un amministratore di autenticazione con privilegi o da un amministratore globale.
- Nessun annidamento. Non è possibile aggiungere un gruppo come membro di un gruppo assegnabile al ruolo.

## <a name="limitations"></a>Limitazioni

Gli scenari seguenti non sono supportati in questo momento:  

- Assegnare gruppi di cloud a Azure AD ruoli personalizzati
- Assegnare i gruppi di cloud ai ruoli Azure AD (incorporati o personalizzati) su un'unità amministrativa o un ambito di applicazione.
- Assegnare gruppi locali a ruoli di Azure AD (incorporati o personalizzati)

## <a name="known-issues"></a>Problemi noti

- Non è possibile creare o modificare un gruppo dinamico quando il ruolo viene assegnato tramite un gruppo.
- L' **Abilitazione dell'implementazione temporanea per la funzionalità di accesso utente gestito** non supporta l'assegnazione tramite gruppo.
- *Solo per i clienti con licenza Azure ad P2*: non assegnare un gruppo come attivo a un ruolo tramite Azure AD e Privileged Identity Management. In questo modo, si verificheranno problemi per cui gli utenti non possono visualizzare le relative assegnazioni di ruolo attive in PIM, oltre all'impossibilità di rimuovere tale assegnazione PIM. Le assegnazioni idonee non sono interessate in questo scenario. Se si tenta di eseguire questa assegnazione, è possibile che venga visualizzato un comportamento imprevisto, ad esempio:
  - L'ora di fine per l'assegnazione di ruolo potrebbe non essere visualizzata correttamente.
  - Nel portale di PIM, i **ruoli** possono visualizzare solo un'assegnazione di ruolo indipendentemente dal numero di metodi per cui viene concessa l'assegnazione (tramite uno o più gruppi e direttamente).
- *Solo per i clienti con licenza Azure ad P2* Anche dopo l'eliminazione del gruppo, viene comunque visualizzato un membro idoneo del ruolo nell'interfaccia utente di PIM. Dal punto di vista funzionale non è presente alcun problema. si tratta semplicemente di un problema relativo alla cache nel portale di Azure.  
- L'interfaccia di amministrazione di Exchange non riconosce ancora l'appartenenza al ruolo tramite il gruppo, ma il cmdlet di PowerShell funzionerà.
- Azure Information Protection portale (il portale classico) non riconosce ancora l'appartenenza al ruolo tramite il gruppo. È possibile [eseguire la migrazione alla piattaforma Unified Sensitivity Labeling](https://docs.microsoft.com/azure/information-protection/configure-policy-migrate-labels) e quindi usare Office 365 Security & Compliance Center per usare le assegnazioni di gruppo per gestire i ruoli.

Stiamo risolvendo questi problemi.

## <a name="required-license-plan"></a>Piano di licenza necessario

Per usare questa funzionalità, è necessario disporre di una licenza Azure AD Premium P1 disponibile nell'organizzazione Azure AD. Per utilizzare Privileged Identity Management anche per l'attivazione del ruolo JIT, è necessario disporre di una licenza Azure AD Premium P2 disponibile. Per trovare la licenza corretta per i propri requisiti, vedere [confronto tra le funzionalità disponibili a livello generale dei piani Premium e gratuito](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un gruppo assegnabile a un ruolo](roles-groups-create-eligible.md)
- [Assegnazione di un ruolo a un gruppo assegnabile al ruolo](roles-groups-assign-role.md)
