---
title: Ruoli di amministratore personalizzati in Azure Active Directory Documenti Microsoft
description: Visualizzare in anteprima i ruoli personalizzati di Azure AD per la delega della gestione delle identità. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.Manage Azure roles in the Azure portal, PowerShell, or Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085111"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Ruoli di amministratore personalizzati in Azure Active Directory (anteprima)Custom administrator roles in Azure Active Directory (preview)

Questo articolo descrive come comprendere i ruoli personalizzati di Azure AD in Azure Active Directory (Azure AD) con il controllo degli accessi in base ai ruoli e gli ambiti delle risorse. I ruoli personalizzati di Azure AD determinano le autorizzazioni sottostanti dei [ruoli predefiniti,](directory-assign-admin-roles.md)in modo che sia possibile creare e organizzare ruoli personalizzati. Questo approccio consente di concedere l'accesso in modo più granulare rispetto ai ruoli predefiniti, ogni volta che sono necessari. Questa prima versione dei ruoli personalizzati di Azure AD include la possibilità di creare un ruolo per assegnare le autorizzazioni per la gestione delle registrazioni delle app. Nel corso del tempo, verranno aggiunte autorizzazioni aggiuntive per le risorse dell'organizzazione, ad esempio applicazioni aziendali, utenti e dispositivi.  

Inoltre, i ruoli personalizzati di Azure AD supportano le assegnazioni in base alle risorse, oltre alle assegnazioni più tradizionali a livello di organizzazione. Questo approccio consente di concedere l'accesso per gestire alcune risorse (ad esempio, una registrazione dell'app) senza concedere l'accesso a tutte le risorse (tutte le registrazioni dell'app).

Il controllo degli accessi in base al ruolo di Azure AD è una funzionalità di anteprima pubblica di Azure AD ed è disponibile con qualsiasi piano di licenza di Azure AD a pagamento. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Informazioni sul controllo degli accessi in base al ruolo di Azure ADUnderstand Azure AD role-based access control

La concessione dell'autorizzazione tramite ruoli di Azure AD personalizzati è un processo in due passaggi che prevede la creazione di una definizione di ruolo personalizzata e quindi l'assegnazione tramite un'assegnazione di ruolo. Una definizione di ruolo personalizzata è una raccolta di autorizzazioni aggiunte da un elenco preimpostato. Queste autorizzazioni sono le stesse utilizzate nei ruoli predefiniti.  

Dopo aver creato la definizione di ruolo, è possibile assegnarla a un utente creando un'assegnazione di ruolo. Un'assegnazione di ruolo concede all'utente le autorizzazioni in una definizione di ruolo in un ambito specificato. Questo processo in due passaggi consente di creare una singola definizione di ruolo e assegnarla più volte in ambiti diversi. Un ambito definisce il set di risorse di Azure AD a cui il membro del ruolo ha accesso. L'ambito più comune è l'ambito a livello di organizzazione (a livello di organizzazione). Un ruolo personalizzato può essere assegnato nell'ambito a livello di organizzazione, ovvero il membro del ruolo dispone delle autorizzazioni del ruolo su tutte le risorse dell'organizzazione. Un ruolo personalizzato può essere assegnato anche in un ambito oggetto. Un esempio di ambito di oggetto potrebbe essere una singola applicazione. Lo stesso ruolo può essere assegnato a un utente su tutte le applicazioni dell'organizzazione e quindi a un altro utente con un ambito solo dell'app Contoso Expense Reports.  

I ruoli predefiniti e personalizzati di Azure AD operano su concetti simili al controllo degli accessi basato sui ruoli di Azure.Azure AD built-in and custom roles operate on concepts similar to [Azure role-based access control](../../role-based-access-control/overview.md). La differenza tra questi due sistemi di controllo degli accessi in base al ruolo consiste nel fatto che il controllo degli accessi in base al ruolo di Azure controlla l'accesso alle risorse di Azure, ad esempio le macchine virtuali o l'archiviazione tramite Gestione risorse di Azure e i ruoli personalizzati di Azure AD controllano l'accesso alle risorse di Azure AD usando l'API Graph.The difference between these two [role-based access control](../../role-based-access-control/rbac-and-directory-admin-roles.md) systems is that Azure RBAC controls access to Azure resources such as virtual machines or storage using Azure Resource Management, and Azure AD custom roles control access to Azure AD resources using Graph API. Entrambi i sistemi sfruttano il concetto di definizioni di ruolo e assegnazioni di ruolo.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Come Azure AD determina se un utente ha accesso a una risorsaHow Azure AD determines if a user has access to a resource

Di seguito sono riportati i passaggi di alto livello utilizzati da Azure AD per determinare se si ha accesso a una risorsa di gestione. Utilizzare queste informazioni per risolvere i problemi di accesso.

1. Un utente (o entità servizio) acquisisce un token per l'endpoint Microsoft Graph o Azure AD Graph.A user (or service principal) acquires a token to the Microsoft Graph or Azure AD Graph endpoint.

1. L'utente effettua una chiamata API ad Azure Active Directory (Azure AD) tramite Microsoft Graph o Azure AD Graph usando il token rilasciato.

1. A seconda delle circostanze, Azure AD esegue una delle azioni seguenti:

    - Valuta le appartenenze ai ruoli dell'utente in base [all'attestazione Wids](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) nel token di accesso dell'utente.
    - Recupera tutte le assegnazioni di ruolo che si applicano all'utente, direttamente o tramite l'appartenenza al gruppo, alla risorsa in cui viene intrapresa l'azione.

1. Azure AD determina se l'azione nella chiamata API è inclusa nei ruoli di cui l'utente dispone per questa risorsa.
1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, l'accesso viene concesso.

### <a name="role-assignments"></a>Assegnazioni di ruoli

Un'assegnazione di ruolo è l'oggetto che associa una definizione di ruolo a un utente in un ambito specifico per concedere l'accesso alle risorse di Azure AD. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo. Al suo interno, un'assegnazione di ruolo è costituita da tre elementi:At its core, a role assignment consists of three elements:

- User (an individual who has a user profile in Azure Active Directory)
- Definizione di ruolo
- Ambito della risorsa

È possibile creare assegnazioni di ruolo usando il portale di Azure, Azure AD PowerShell o l'API Graph.You can create [role assignments](roles-create-custom.md) using the Azure portal, Azure AD PowerShell, or Graph API. È inoltre possibile [visualizzare le assegnazioni per un ruolo personalizzato.](roles-view-assignments.md#view-the-assignments-of-a-role)

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio a Chris Green è stato assegnato il ruolo personalizzato Amministratore registrazione app nell'ambito della registrazione dell'app Contoso Widget Builder. L'assegnazione concede a Chris le autorizzazioni del ruolo di amministratore della registrazione app solo per la registrazione specifica dell'app.

![L'assegnazione dei ruoli è la modalità di applicazione delle autorizzazioni e delle tre parti](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entità di sicurezza

Un'entità di sicurezza rappresenta l'utente a cui deve essere assegnato l'accesso alle risorse di Azure AD. Un utente è una persona che dispone di un profilo utente in Azure Active Directory.A *user* is an individual who has a user profile in Azure Active Directory.

### <a name="role"></a>Ruolo

Una definizione di ruolo, o ruolo, è una raccolta di autorizzazioni. Una definizione di ruolo elenca le operazioni che possono essere eseguite sulle risorse di Azure AD, ad esempio creazione, lettura, aggiornamento ed eliminazione. Esistono due tipi di ruoli in Azure AD:There are two types of roles in Azure AD:

- Ruoli predefiniti creati da Microsoft che non possono essere modificati.
- Ruoli personalizzati creati e gestiti dall'organizzazione.

### <a name="scope"></a>Scope

Un ambito è la restrizione delle azioni consentite a una determinata risorsa di Azure AD come parte di un'assegnazione di ruolo. Quando si assegna un ruolo, è possibile specificare un ambito che limiti l'accesso dell'amministratore a una risorsa specifica. Ad esempio, se si desidera concedere a uno sviluppatore un ruolo personalizzato, ma solo per gestire una registrazione specifica dell'applicazione, è possibile includere la registrazione dell'applicazione specifica come ambito nell'assegnazione di ruolo.

  > [!Note]
  > I ruoli personalizzati possono essere assegnati nell'ambito della directory e nell'ambito della risorsa. Non possono ancora essere assegnati nell'ambito dell'unità amministrativa.
  > I ruoli predefiniti possono essere assegnati nell'ambito della directory e, in alcuni casi, nell'ambito delle unità amministrative. Non possono ancora essere assegnati nell'ambito delle risorse di Azure AD.

## <a name="required-license-plan"></a>Piano di licenza richiesto

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare assegnazioni di ruolo personalizzate usando il portale di [Azure, Azure AD PowerShell e l'API GraphCreate custom role assignments using the Azure portal, Azure AD PowerShell, and Graph API](roles-create-custom.md)
- [Visualizzare le assegnazioni per un ruolo personalizzatoView the assignments for a custom role](roles-view-assignments.md#view-assignments-of-single-application-scope)
