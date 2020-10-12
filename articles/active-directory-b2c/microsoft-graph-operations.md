---
title: Operazioni di Microsoft Graph supportate
titleSuffix: Azure AD B2C
description: Indice delle operazioni di Microsoft Graph supportate per la gestione di risorse Azure AD B2C, inclusi utenti, flussi utente, provider di identità, criteri personalizzati, chiavi dei criteri e altro ancora.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78184249"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph operazioni disponibili per Azure AD B2C

Per la gestione delle risorse Azure AD B2C, inclusi gli utenti, i provider di identità, i flussi utente, i criteri personalizzati e le chiavi dei criteri, sono supportate le seguenti operazioni di Microsoft Graph API.

Ogni collegamento nelle sezioni seguenti è destinato alla pagina corrispondente all'interno del Microsoft Graph riferimento API per l'operazione.

## <a name="user-management"></a>Gestione utente

- [Elencare gli utenti](https://docs.microsoft.com/graph/api/user-list)
- [Creazione di un utente consumer](https://docs.microsoft.com/graph/api/user-post-users)
- [Ottenere un utente](https://docs.microsoft.com/graph/api/user-get)
- [Aggiornare un utente](https://docs.microsoft.com/graph/api/user-update)
- [Eliminare un utente](https://docs.microsoft.com/graph/api/user-delete)

Per altre informazioni sulla gestione degli account utente di Azure AD B2C con l'API Microsoft Graph, vedere [gestire gli account utente di Azure ad B2C con Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Provider di identità (flusso utente)

Gestire i provider di identità disponibili per i flussi utente nel tenant del Azure AD B2C.

- [Elencare i provider di identità registrati nel tenant di Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Creazione di un provider di identità](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Ottenere un provider di identità](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aggiorna provider di identità](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Eliminare un provider di identità](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Flusso utente

Configurare criteri predefiniti per l'iscrizione, l'accesso, l'iscrizione e l'accesso combinati, la reimpostazione della password e l'aggiornamento del profilo.

- [Elencare i flussi utente](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Creare un flusso utente](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Ottenere un flusso utente](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Eliminare un flusso utente](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Criteri personalizzati

Le operazioni seguenti consentono di gestire i criteri di Azure AD B2C Trust Framework, noti come [criteri personalizzati](custom-policy-overview.md).

- [Elencare tutti i criteri di Framework attendibilità configurati in un tenant](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Creare criteri di Framework attendibilità](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Leggere le proprietà di un criterio di Framework attendibilità esistente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aggiornare o creare criteri di Framework attendibilità.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminare un criterio di Framework attendibilità esistente](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chiavi dei criteri

Il Framework dell'esperienza di identità Archivia i segreti a cui si fa riferimento in un criterio personalizzato per stabilire una relazione di trust tra i componenti. Questi segreti possono essere chiavi/valori simmetriche o asimmetriche. Nel portale di Azure queste entità vengono visualizzate come chiavi dei **criteri**.

La risorsa di primo livello per le chiavi dei criteri nell'API Microsoft Graph è il [keyset del Framework attendibile](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Ogni **Keyset** contiene almeno una **chiave**. Per creare una chiave, creare prima un keyset vuoto e quindi generare una chiave nel keyset. È possibile creare un segreto manuale, caricare un certificato o una chiave PKCS12. La chiave può essere un segreto generato, una stringa definita dall'utente, ad esempio il segreto dell'applicazione Facebook, oppure un certificato caricato. Se un keyset dispone di più chiavi, solo una delle chiavi è attiva.

### <a name="trust-framework-policy-keyset"></a>Keyset criteri di Framework attendibilità

- [Elencare i set di impostazioni del Framework di attendibilità](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Creare set di impostazioni di un Framework attendibilità](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Ottenere un keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aggiornare i set di impostazioni di un Framework attendibilità](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Eliminare i set di impostazioni di un Framework attendibilità](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chiave dei criteri di Framework attendibilità

- [Ottenere la chiave attualmente attiva nel keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generare una chiave in keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Carica un segreto basato su stringa](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Caricare un certificato X. 509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Carica un certificato in formato PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>APPLICAZIONI

- [Elenco applicazioni](https://docs.microsoft.com/graph/api/application-list)
- [Creare un'applicazione](https://docs.microsoft.com/graph/api/resources/application)
- [Update application](https://docs.microsoft.com/graph/api/application-update)
- [Crea servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Crea concessione oauth2Permission](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Eliminare l'applicazione](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Proprietà dell'estensione dell'applicazione

- [Elencare le proprietà dell'estensione](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C fornisce una directory che può contenere 100 attributi personalizzati per utente. Per i flussi utente, queste proprietà di estensione vengono [gestite tramite il portale di Azure](custom-policy-custom-attributes.md). Per i criteri personalizzati, Azure AD B2C crea la proprietà per la prima volta che il criterio scrive un valore nella proprietà di estensione.

## <a name="audit-logs"></a>Log di controllo

- [Elencare i log di controllo](https://docs.microsoft.com/graph/api/directoryaudit-list)

Per ulteriori informazioni sull'accesso ai log di controllo di Azure AD B2C con l'API Microsoft Graph, vedere [accesso ai log di controllo di Azure ad B2C](view-audit-logs.md).
