---
title: Ruoli di amministratore personalizzati in Azure Active Directory | Microsoft Docs
description: Informazioni sui ruoli personalizzati di Azure AD in Azure Active Directory (Azure AD), con il controllo degli accessi in base al ruolo e gli ambiti delle risorse.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0785d8070a60ae7594ea0b182a0238bf6b4b6a58
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95899463"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Ruoli di amministratore personalizzati in Azure Active Directory (anteprima)

Questo articolo descrive i ruoli personalizzati di Azure AD in Azure Active Directory (Azure AD) con il controllo degli accessi in base al ruolo e gli ambiti delle risorse. I ruoli di Azure AD personalizzati evidenziano le autorizzazioni sottostanti dei [ruoli predefiniti](permissions-reference.md), per poter creare e organizzare ruoli personalizzati. Questo approccio permette di concedere l'accesso in modo più granulare rispetto ai ruoli predefiniti, ogni volta che sono necessari. Questa prima versione dei ruoli personalizzati di Azure AD include la possibilità di creare un ruolo per assegnare le autorizzazioni per la gestione delle registrazioni app. In futuro verranno aggiunte ulteriori autorizzazioni per le risorse dell'organizzazione, ad esempio applicazioni aziendali, utenti e dispositivi.  

Inoltre, i ruoli personalizzati di Azure AD supportano le assegnazioni in base alle singole risorse, oltre alle assegnazioni più tradizionali a livello di organizzazione. Questo approccio offre la possibilità di concedere l'accesso per gestire alcune risorse, ad esempio, una registrazione app, senza concedere l'accesso a tutte le risorse (tutte le registrazioni app).

Il controllo degli accessi in base al ruolo di Azure AD è una funzionalità in anteprima pubblica di Azure AD ed è disponibile con qualsiasi piano di licenza a pagamento di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Informazioni sul controllo degli accessi in base al ruolo di Azure AD

La concessione di autorizzazioni tramite ruoli di Azure AD personalizzati è un processo in due passaggi che prevede la creazione di una definizione del ruolo personalizzata e la successiva assegnazione tramite un'assegnazione di ruolo. Una definizione del ruolo personalizzata è una raccolta di autorizzazioni aggiunte da un elenco preimpostato. Queste autorizzazioni corrispondono a quelle usate nei ruoli predefiniti.  

Una volta creata la definizione del ruolo, è possibile assegnarla a un utente creando un'assegnazione di ruolo. Un'assegnazione di ruolo concede all'utente le autorizzazioni in una definizione del ruolo in un ambito specificato. Questo processo in due passaggi consente di creare una singola definizione del ruolo e di assegnarla più volte in ambiti diversi. Un ambito definisce il set di risorse di Azure AD a cui il membro del ruolo può accedere. L'ambito più comune è l'ambito a livello di organizzazione. Un ruolo personalizzato può essere assegnato a livello di organizzazione, per concedere così al membro del ruolo le autorizzazioni del ruolo per tutte le risorse nell'organizzazione. È anche possibile assegnare un ruolo personalizzato a un ambito degli oggetti. Un ambito degli oggetti è ad esempio una singola applicazione. Lo stesso ruolo può essere assegnato a un utente per tutte le applicazioni dell'organizzazione e quindi a un altro utente con un ambito limitato all'app Contoso Expense Reports.  

I ruoli predefiniti e personalizzati di Azure AD operano in base a concetti simili al [Controllo degli accessi in base al ruolo di Azure](../../active-directory-b2c/overview.md). La [differenza tra questi due sistemi di controllo degli accessi in base al ruolo](../../role-based-access-control/rbac-and-directory-admin-roles.md) sta nel fatto che il controllo degli accessi in base al ruolo di Azure controlla l'accesso alle risorse di Azure, ad esempio le macchine virtuali o lo spazio di archiviazione, tramite Gestione delle risorse di Azure e i ruoli personalizzati di Azure AD controllano l'accesso alle risorse di Azure AD tramite l'API Graph. Entrambi i sistemi sfruttano il concetto di definizioni del ruolo e assegnazioni di ruolo. Le autorizzazioni del controllo degli accessi in base al ruolo di Azure AD non possono essere incluse nei ruoli di Azure e viceversa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>In che modo Azure AD determina se un utente può accedere a una risorsa

Di seguito è indicata la procedura generale usata da Azure AD per determinare se l'utente è autorizzato ad accedere a una risorsa di gestione. Usare queste informazioni per risolvere i problemi di accesso.

1. Un utente (o un'entità servizio) acquisisce un token per l'endpoint Microsoft Graph o Azure AD Graph.

1. L'utente effettua una chiamata API ad Azure Active Directory (Azure AD) tramite Microsoft Graph o Azure AD Graph usando il token rilasciato.

1. A seconda delle circostanze, Azure AD esegue una delle operazioni seguenti:

    - Valuta le appartenenze ai ruoli dell'utente in base all'[attestazione wids](../../active-directory-b2c/access-tokens.md) nel token di accesso dell'utente.
    - Recupera tutte le assegnazioni di ruolo valide per l'utente, direttamente o tramite l'appartenenza a gruppi, per la risorsa in cui viene eseguita l'azione.

1. Azure AD determina se l'azione nella chiamata API è inclusa nei ruoli di cui l'utente dispone per questa risorsa.
1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, l'accesso viene concesso.

### <a name="role-assignments"></a>Assegnazioni di ruoli

Un'assegnazione di ruolo è l'oggetto che collega una definizione del ruolo a un utente in un ambito specifico per concedere l'accesso alla risorsa di Azure AD. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi principali:

- Utente: un soggetto che dispone di un profilo utente in Azure Active Directory
- Definizione di ruolo
- Ambito risorsa

È possibile [creare assegnazioni di ruolo](custom-create.md) usando il portale di Azure, l'interfaccia della riga di comando di Azure, Azure AD PowerShell o l'API Graph. È anche possibile [visualizzare le assegnazioni per un ruolo personalizzato](custom-view-assignments.md#view-the-assignments-of-a-role).

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio a Chris Green è stato assegnato il ruolo personalizzato di amministratore registrazioni app nell'ambito della registrazione app Contoso Widget Builder. L'assegnazione concede a Chris le autorizzazioni del ruolo di amministratore registrazioni app solo per la registrazione di questa app specifica.

![L'assegnazione di ruolo è il modo in cui vengono imposte le autorizzazioni e include tre parti](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entità di sicurezza

Un'entità di sicurezza rappresenta l'utente a cui deve essere assegnato l'accesso alle risorse di Azure AD. Un *utente* è un soggetto che dispone di un profilo utente in Azure Active Directory.

### <a name="role"></a>Ruolo

Una definizione del ruolo o ruolo è una raccolta di autorizzazioni ed elenca le operazioni che è possibile eseguire sulle risorse di Azure AD, ad esempio creazione, lettura, aggiornamento ed eliminazione. Sono disponibili due tipi di ruoli in Azure AD:

- Ruoli predefiniti creati da Microsoft che non possono essere modificati.
- Ruoli personalizzati creati e gestiti dall'organizzazione.

### <a name="scope"></a>Scope

Un ambito è la restrizione delle azioni consentite a una risorsa di Azure AD specifica come parte di un'assegnazione di ruolo. Quando si assegna un ruolo, è possibile specificare un ambito che limita l'accesso dell'amministratore a una risorsa specifica. Se ad esempio si desidera concedere a uno sviluppatore un ruolo personalizzato, ma solo per gestire una registrazione dell'applicazione specifica, è possibile includere la registrazione dell'applicazione specifica come ambito nell'assegnazione di ruolo.

  > [!Note]
  > I ruoli personalizzati possono essere assegnati nell'ambito della directory e nell'ambito della risorsa. Non possono ancora essere assegnati nell'ambito dell'unità amministrativa.
  > I ruoli predefiniti possono essere assegnati nell'ambito della directory e, in alcuni casi, nell'ambito dell'unità amministrativa. Non è ancora possibile assegnarli nell'ambito della risorsa di Azure AD.

## <a name="required-license-plan"></a>Piano di licenza necessario

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare assegnazioni di ruolo personalizzate usando il [portale di Azure, Azure AD PowerShell e l'API Graph](custom-create.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](custom-view-assignments.md)
