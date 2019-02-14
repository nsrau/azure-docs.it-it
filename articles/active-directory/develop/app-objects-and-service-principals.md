---
title: Oggetti applicazione e oggetti entità servizio in Azure Active Directory
description: Informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio in Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41eeb4c45e4ef2f04a5e53fde081a2c46093d379
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186081"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Oggetti applicazione e oggetti entità servizio in Azure Active Directory

Spesso il significato del termine "applicazione" può essere frainteso quando usato nel contesto di Azure Active Directory (Azure AD). Questo articolo illustra gli aspetti concettuali e concreti dell'integrazione delle applicazioni di Azure AD, con un esempio di registrazione e consenso per un'[applicazione multi-tenant](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Panoramica

Un'applicazione che è stata integrata con Azure AD ha delle implicazioni che vanno oltre l'aspetto del software. "Applicazione" viene spesso usato come termine concettuale, che fa riferimento non solo al software applicativo, ma anche alla sua registrazione in Azure AD e al suo ruolo nelle "conversazioni" di autenticazione/autorizzazione in fase di runtime.

Per definizione, un'applicazione può funzionare in questi ruoli:

- Ruolo [client](developer-glossary.md#client-application), che utilizza una risorsa
- Ruolo [server di risorse](developer-glossary.md#resource-server), che espone le API ai client
- Ruolo sia client che server di risorse

Un [flusso di concessione di autorizzazione OAuth 2.0](developer-glossary.md#authorization-grant) definisce il protocollo di conversazione, che permette al client e alla risorsa rispettivamente di accedere ai dati della risorsa e di proteggerli.

Nelle sezioni seguenti verrà illustrato come il modello applicativo di Azure AD rappresenta un'applicazione in fase di progettazione e in fase di esecuzione.

## <a name="application-registration"></a>Registrazione dell'applicazione

Quando si registra un'applicazione di Azure AD nel [portale di Azure][AZURE-Portal], vengono creati due oggetti nel tenant di Azure AD:

- Un oggetto applicazione
- Un oggetto entità servizio

### <a name="application-object"></a>Oggetto applicazione

Un'applicazione di Azure AD è definita da un solo oggetto applicazione che risiede nel tenant di Azure AD in cui l'applicazione è stata registrata, noto come tenant "home" dell'applicazione. L'[entità applicativa][AAD-Graph-App-Entity] di Azure AD Graph definisce lo schema per le proprietà di un oggetto applicazione.

### <a name="service-principal-object"></a>Oggetto entità servizio

Per accedere alle risorse protette da un tenant di Azure AD, l'entità che richiede l'accesso deve essere rappresentata da un'entità di sicurezza. Questo vale sia per gli utenti (entità utente) che per le applicazioni (entità servizio).

L'entità di sicurezza definisce i criteri di accesso e le autorizzazioni per l'utente/applicazione nel tenant di Azure AD. Ciò abilita le funzionalità di base, ad esempio l'autenticazione dell'utente/applicazione durante l'accesso e l'autorizzazione durante l'accesso alle risorse.

Quando a un'applicazione viene concesso di accedere alle risorse in un tenant (al momento della registrazione o del [consenso](developer-glossary.md#consent)), viene creato un oggetto entità servizio. L'[entità ServicePrincipal][AAD-Graph-Sp-Entity] di Azure AD Graph definisce lo schema delle proprietà di un oggetto entità servizio.

### <a name="application-and-service-principal-relationship"></a>Relazione tra applicazione e entità servizio

Si può considerare l'oggetto applicazione come la rappresentazione *globale* dell'applicazione usata in tutti i tenant, e l'entità servizio come la rappresentazione *locale* usata in uno specifico tenant.

L'oggetto applicazione funge da modello da cui *derivano* le proprietà comuni e predefinite per l'uso nella creazione di oggetti entità servizio corrispondenti. Un oggetto applicazione ha quindi una relazione 1:1 con l'applicazione software e relazioni 1:molti con gli oggetti entità servizio corrispondenti.

In ogni tenant in cui viene usata l'applicazione è necessario creare un'entità servizio per poter stabilire un'identità per l'iscrizione e/o l'accesso alle risorse che venga protetta da un tenant. Un'applicazione single-tenant ha una sola entità servizio (nel relativo tenant principale), creata e autorizzata per essere usata durante la registrazione dell'applicazione. Un'applicazione Web/API multi-tenant ha anche un'entità servizio creata in ogni tenant in cui l'utente ha dato il consenso all'uso. 

> [!NOTE]
> Qualsiasi modifica apportata all'oggetto applicazione verrà riflessa solo nell'oggetto entità servizio nel tenant home dell'applicazione, ovvero nel tenant in cui è stata registrata. Per le applicazioni multi-tenant, le modifiche apportate all'oggetto applicazione non vengono riflesse negli oggetti entità servizio dei tenant consumer fino a quando non viene rimosso l'accesso tramite il [Pannello di accesso all'applicazione](https://myapps.microsoft.com) e poi concesso nuovamente.
>
> Si noti anche che le applicazioni native sono registrate come multi-tenant per impostazione predefinita.

## <a name="example"></a>Esempio

Il diagramma seguente illustra la relazione tra l'oggetto applicazione e i corrispondenti oggetti entità servizio di un'applicazione, nel contesto di un'applicazione multi-tenant di esempio denominata **app HR**. In questo scenario di esempio sono presenti tre tenant di Azure AD:

- **Adatum**, il tenant usato dalla società che ha sviluppato l'**app HR**
- **Contoso**, il tenant usato dall'organizzazione Contoso, che utilizza l'**app HR**
- **Fabrikam**, il tenant usato dall'organizzazione Fabrikam, che utilizza anche in questo caso l'**app HR**

![Relazione tra un oggetto applicazione e un oggetto entità servizio](./media/app-objects-and-service-principals/application-objects-relationship.png)

In questo scenario di esempio:

| Passaggio | DESCRIZIONE |
|------|-------------|
| 1    | È il processo di creazione degli oggetti applicazione ed entità servizio nel tenant principale dell'applicazione. |
| 2    | Quando gli amministratori di Contoso e Fabrikam completano il consenso, nel tenant di Azure AD della rispettiva società viene creato un oggetto entità servizio a cui vengono assegnate le autorizzazioni concesse dall'amministratore. Si noti anche che l'app HR potrebbe essere configurata/progettata per permettere il consenso da parte di utenti per l'uso individuale. |
| 3    | I tenant consumer dell'applicazione HR (Contoso e Fabrikam) dispongono ognuno del proprio oggetto entità servizio. Ognuno rappresenta l'uso di un'istanza dell'applicazione in fase di runtime, gestito tramite le autorizzazioni concesse dall'amministratore. |

## <a name="next-steps"></a>Passaggi successivi

- È possibile usare [Graph explorer di Azure AD](https://graphexplorer.azurewebsites.net/) per eseguire query sugli oggetti applicazione ed entità servizio.
- È possibile accedere all'oggetto applicazione di un'applicazione tramite l'API Graph di Azure AD, l'editor del manifesto dell'applicazione del [portale di Azure][AZURE-Portal] o i [cmdlet di Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), come rappresentato dall'[entità Application][AAD-Graph-App-Entity] OData.
- È possibile accedere all'oggetto entità servizio di un'applicazione tramite l'API Graph di Azure AD o i [cmdlet di Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), come rappresentato dall'[entità ServicePrincipal][AAD-Graph-Sp-Entity] OData.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
