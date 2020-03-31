---
title: Operazioni di Microsoft Graph supportate
titleSuffix: Azure AD B2C
description: Indice delle operazioni di Microsoft Graph supportate per la gestione delle risorse B2C di Azure AD, inclusi utenti, flussi utente, provider di identità, criteri personalizzati, chiavi dei criteri e altro ancora.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184249"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Operazioni di Microsoft Graph disponibili per Azure AD B2C

Le seguenti operazioni API di Microsoft Graph sono supportate per la gestione delle risorse B2C di Azure AD, inclusi utenti, provider di identità, flussi utente, criteri personalizzati e chiavi di criteri.

Ogni collegamento nelle sezioni seguenti è destinato alla pagina corrispondente all'interno del riferimento all'API Microsoft Graph per tale operazione.

## <a name="user-management"></a>Gestione utente

- [Elencare gli utenti](https://docs.microsoft.com/graph/api/user-list)
- [Creare un utente consumerCreate a consumer user](https://docs.microsoft.com/graph/api/user-post-users)
- [Ottenere un utenteGet a user](https://docs.microsoft.com/graph/api/user-get)
- [Aggiornare un utente](https://docs.microsoft.com/graph/api/user-update)
- [Eliminare un utente](https://docs.microsoft.com/graph/api/user-delete)

Per altre informazioni sulla gestione degli account utente di Azure AD B2C con l'API Microsoft Graph, vedere [Gestire gli account utente B2C](manage-user-accounts-graph-api.md)di Azure AD con Microsoft Graph.

## <a name="identity-providers-user-flow"></a>Provider di identità (flusso utente)

Gestire i provider di identità disponibili per i flussi utente nel tenant B2C di Azure AD.

- [Provider di identità di elenco registrati nel tenant B2C di Azure ADList identity providers registered in the Azure AD B2C tenant](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Creare un provider di identitàCreate an identity provider](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Ottenere un provider di identitàGet an identity provider](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Aggiornare il provider di identitàUpdate identity provider](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Eliminare un provider di identitàDelete an identity provider](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Flusso utente

Configurare criteri predefiniti per l'iscrizione, l'accesso, l'accesso combinato e l'accesso, la reimpostazione della password e l'aggiornamento del profilo.

- [Elencare i flussi utente](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Creare un flusso utenteCreate a user flow](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Ottenere un flusso utenteGet a user flow](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Eliminare un flusso utenteDelete a user flow](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Criteri personalizzati

Le operazioni seguenti consentono di gestire i criteri di Azure AD B2C Trust Framework, noti come [criteri personalizzati.](custom-policy-overview.md)

- [Elencare tutti i criteri del framework di attendibilità configurati in un tenantList all trust framework policies configured in a tenant](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Creare criteri del framework di attendibilitàCreate trust framework policy](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Leggere le proprietà di un criterio del framework di attendibilità esistenteRead properties of an existing trust framework policy](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Aggiornare o creare criteri del framework di attendibilità.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Eliminare un criterio del framework di attendibilità esistenteDelete an existing trust framework policy](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Chiavi dei criteri

Identity Experience Framework archivia i segreti a cui si fa riferimento in un criterio personalizzato per stabilire l'attendibilità tra i componenti. Questi segreti possono essere chiavi/valori simmetrici o asimmetrici. Nel portale di Azure queste entità vengono visualizzate come **Chiavi dei criteri**.

La risorsa di primo livello per le chiavi dei criteri nell'API Microsoft Graph è l'insieme di [chiavi Trusted Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Ogni **Keyset** contiene almeno una **chiave**. Per creare una chiave, creare innanzitutto un set di chiavi vuoto, quindi generare una chiave nel keyset. È possibile creare un segreto manuale, caricare un certificato o una chiave PKCS12. La chiave può essere un segreto generato, una stringa definita dall'utente (ad esempio il segreto dell'applicazione Facebook) o un certificato caricato. Se un keyset dispone di più chiavi, solo uno dei tasti è attivo.

### <a name="trust-framework-policy-keyset"></a>Keyset dei criteri di Framework di attendibilità

- [Elencare i set di chiavi del framework di attendibilitàList the trust framework keysets](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Creare un set di chiavi del framework di attendibilitàCreate a trust framework keysets](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Ottenere un set di tastiGet a keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Aggiornare un set di chiavi del framework di attendibilitàUpdate a trust framework keysets](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Eliminare un set di chiavi del framework di attendibilitàDelete a trust framework keysets](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Chiave dei criteri del framework di attendibilità

- [Ottenere la chiave attualmente attiva nel keysetGet currently active key in the keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Generare una chiave nel keyset](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Caricare un segreto basato su stringaUpload a string based secret](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Caricare un certificato X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Caricare un certificato in formato PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>APPLICAZIONI

- [Elencare le applicazioni](https://docs.microsoft.com/graph/api/application-list)
- [Creare un'applicazioneCreate an application](https://docs.microsoft.com/graph/api/resources/application)
- [Aggiorna applicazione](https://docs.microsoft.com/graph/api/application-update)
- [Creare servicePrincipalCreate servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Crea concessione oauth2Permission](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Eliminare l'applicazione](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Proprietà dell'estensione dell'applicazione

- [Elencare le proprietà dell'estensioneList extension properties](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C fornisce una directory che può contenere 100 attributi personalizzati per utente. Per i flussi utente, queste proprietà di estensione vengono [gestite tramite il portale](custom-policy-custom-attributes.md)di Azure.For user flows, these extension properties are managed by using the Azure portal . Per i criteri personalizzati, Azure AD B2C crea automaticamente la proprietà la prima volta che il criterio scrive un valore nella proprietà di estensione.

## <a name="audit-logs"></a>Log di controllo

- [Elencare i registri di controllo](https://docs.microsoft.com/graph/api/directoryaudit-list)

Per altre informazioni sull'accesso ai log di controllo B2C di Azure AD con l'API Microsoft Graph, vedere Accesso ai log di [controllo B2C](view-audit-logs.md)di Azure AD.
