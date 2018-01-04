---
title: Sottoscrizione di Azure Active Directory Privileged Identity Management | Documentazione Microsoft
description: Illustra la sottoscrizione e i requisiti per le licenze per la gestione e l'utilizzo di Azure AD Privileged Identity Management nel tenant
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: b04544c6a1d288524783ed6d323146c0ef7bfe95
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Requisiti di sottoscrizione di Azure Active Directory Privileged Identity Management

Azure AD Privileged Identity Management è disponibile solo come parte di Azure AD Premium P2 Edition. Per maggiori informazioni su altre funzionalità di P2 e per un confronto con la versione Premium P1, vedere [Edizioni di Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Quando Azure Active Directory (Azure AD) Privileged Identity Management era disponibile nella versione di anteprima, per provare il servizio non erano necessarie le verifiche delle licenze nel tenant.  Azure AD Privileged Identity Management è ora disponibile a livello generale ed è quindi necessario che una sottoscrizione della versione di valutazione o a pagamento sia presente nel tenant per continuare a usare Privileged Identity Management dopo il mese di dicembre 2016.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Confermare la sottoscrizione di valutazione o a pagamento

Se non si è certi che l'organizzazione disponga di una sottoscrizione di valutazione o a pagamento, è possibile controllare la presenza di una sottoscrizione nel tenant utilizzando i comandi inclusi nel Modulo di Azure Active Directory per Windows PowerShell V1. 
1. Aprire una finestra di PowerShell.
2. Immettere `Connect-MsolService` per autenticare un utente nel tenant.
3. Immettere `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Questo comando Recupera un elenco delle sottoscrizioni nel tenant. Se non vengono restituite righe, è necessario ottenere una versione di valutazione di Azure AD Premium P2 oppure acquistare una sottoscrizione di Azure AD Premium P2 o EMS E5 per usare Azure AD Privileged Identity Management.  Per ottenere una versione di valutazione e iniziare a usare Azure AD Privileged Identity Management, consultare [Introduzione ad Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Se questo comando restituisce una riga in cui SkuPartNumber è "AAD_PREMIUM_P2" o "EMSPREMIUM" e IsTrial è "True", nel tenant è presente una versione di valutazione di Azure AD Premium P2.  Se lo stato della sottoscrizione non è abilitato e non si dispone di una sottoscrizione a pagamento ad Azure AD Premium P2 o EMS E5, è necessario acquistare una sottoscrizione di Azure AD Premium P2 o EMS E5 per continuare a usare Azure AD Privileged Identity Management.

Azure AD Premium P2 è disponibile tramite un [contratto Enterprise Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), il [programma di licenza Open Volume](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) e il programma [Cloud Solution Provider](https://partner.microsoft.com/en-US/cloud-solution-provider). I sottoscrittori di Azure e Office 365 possono acquistare Azure AD Premium P2 anche online.  Altre informazioni sui prezzi di Azure AD Premium e su come ordinarlo online sono disponibili sulla pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/en-us/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management non è disponibile nel tenant

Azure AD Privileged Identity Management non sarà più disponibile nel tenant se:
- L'organizzazione usava Azure AD Privileged Identity Management nella versione di anteprima e non è stata acquistata la sottoscrizione di Azure AD Premium P2 o a EMS E5.
- L'organizzazione era in possesso di una sottoscrizione di valutazione ad Azure AD Premium P2 o EMS E5 che è scaduta.
- L'organizzazione era in possesso di una sottoscrizione a pagamento che è scaduta.

Quando scade una sottoscrizione di Azure AD Premium P2 o EMS E5 oppure un'organizzazione che stava usando la versione di anteprima di Azure AD Privileged Identity Management non acquista una sottoscrizione di Azure AD Premium P2 o EMS E5:

- Le assegnazioni dei ruoli permanenti ad Azure AD non saranno interessate.
- Le estensioni di Azure AD Privileged Identity Management nel Portale di Azure, nonché i cmdlet dell'API Graph e le interfacce PowerShell di Azure AD Privileged Identity Management, non saranno più disponibili per gli utenti per l'attivazione di ruoli con privilegi, la gestione di accessi con privilegi o l'esecuzione di verifiche di accesso dei ruoli con privilegi.
- Le assegnazioni di ruoli idonei dei ruoli di Azure AD verranno rimosse poiché gli utenti non saranno più in grado di attivare ruoli con privilegi.
- Eventuali verifiche di accesso dei ruoli di Azure AD in corso termineranno e le impostazioni di configurazione di Azure AD Privileged Identity Management verranno rimosse.
- Azure AD Privileged Identity Management non invierà più messaggi di posta elettronica sulle modifiche di assegnazioni di ruoli.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)
- [Ruoli in Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-roles.md)
