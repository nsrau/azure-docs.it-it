---
title: App & entità servizio in Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 453efd7735c6843ccdaf8dfd86b18d0b2ef8b06d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604625"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Oggetti applicazione e oggetti entità servizio in Azure Active Directory

Questo articolo descrive la registrazione dell'applicazione, gli oggetti applicazione e le entità servizio in Azure Active Directory: quali sono, come vengono usati e come sono correlati tra loro. Viene inoltre presentato uno scenario di esempio multi-tenant per illustrare la relazione tra l'oggetto applicazione di un'applicazione e gli oggetti entità servizio corrispondenti.

## <a name="application-registration"></a>Registrazione dell'applicazione
Per delegare le funzioni di gestione delle identità e degli accessi ai Azure AD, un'applicazione deve essere registrata con un [tenant](developer-glossary.md#tenant)di Azure ad. Quando si registra l'applicazione con Azure AD, si crea una configurazione di identità per l'applicazione che consente l'integrazione con Azure AD. Quando si registra un'app nella [portale di Azure][AZURE-Portal], è possibile scegliere se si tratta di un singolo tenant (accessibile solo nel tenant) o multi-tenant (accessibile ad altri tenant) e facoltativamente impostare un URI di reindirizzamento (a cui viene inviato il token di accesso).

Al termine della registrazione dell'app, si ha un'istanza univoca globale dell'app (oggetto applicazione) che risiede all'interno del tenant o della directory principale.  È anche presente un ID univoco globale per l'app (l'ID app o client).  Nel portale è quindi possibile aggiungere segreti o certificati e ambiti per eseguire il lavoro dell'app, personalizzare la personalizzazione dell'app nella finestra di dialogo di accesso e altro ancora.

Se si registra un'applicazione nel portale, vengono creati automaticamente un oggetto applicazione e un oggetto entità servizio nel tenant principale.  Se si registra o si crea un'applicazione usando le API di Microsoft Graph, la creazione dell'oggetto entità servizio è un passaggio separato.

## <a name="application-object"></a>Oggetto applicazione
Un'applicazione Azure AD è definita da un solo oggetto applicazione che risiede nel tenant di Azure AD in cui l'applicazione è stata registrata, noto come tenant "Home" dell'applicazione.  Un oggetto applicazione viene usato come modello o progetto per creare uno o più oggetti entità servizio.  Viene creata un'entità servizio in ogni tenant in cui viene usata l'applicazione. Analogamente a una classe nella programmazione orientata a oggetti, l'oggetto applicazione dispone di alcune proprietà statiche che vengono applicate a tutte le entità servizio (o istanze dell'applicazione) create. 

L'oggetto applicazione descrive tre aspetti di un'applicazione: il modo in cui il servizio può emettere token per accedere all'applicazione, le risorse a cui l'applicazione potrebbe dover accedere e le azioni che l'applicazione può eseguire. 

Il pannello **registrazioni app** nel [portale di Azure][AZURE-Portal] viene usato per elencare e gestire gli oggetti applicazione nel tenant principale.

L' [entità dell'applicazione][MS-Graph-App-Entity] Microsoft Graph definisce lo schema per le proprietà di un oggetto applicazione.

## <a name="service-principal-object"></a>Oggetto entità servizio
Per accedere alle risorse protette da un tenant di Azure AD, l'entità che richiede l'accesso deve essere rappresentata da un'entità di sicurezza. Questo vale sia per gli utenti (entità utente) che per le applicazioni (entità servizio). L'entità di sicurezza definisce i criteri di accesso e le autorizzazioni per l'utente/applicazione nel tenant di Azure AD. Ciò abilita le funzionalità di base, ad esempio l'autenticazione dell'utente/applicazione durante l'accesso e l'autorizzazione durante l'accesso alle risorse.

Un'entità servizio è la rappresentazione locale o l'istanza dell'applicazione di un oggetto applicazione globale in un singolo tenant o directory. Un'entità servizio è un'istanza concreta creata dall'oggetto applicazione ed eredita determinate proprietà dall'oggetto applicazione.  Viene creata un'entità servizio in ogni tenant in cui viene usata l'applicazione e fa riferimento all'oggetto app univoco globale.  L'oggetto entità servizio definisce le operazioni che l'app può effettivamente eseguire nel tenant specifico, chi può accedere all'app e le risorse a cui l'app può accedere. 

Quando a un'applicazione viene concesso di accedere alle risorse in un tenant (al momento della registrazione o del [consenso](developer-glossary.md#consent)), viene creato un oggetto entità servizio. È anche possibile creare un oggetto entità servizio in un tenant usando [Azure PowerShell](howto-authenticate-service-principal-powershell.md), l'interfaccia della riga di comando di Azure, [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http), il [portale di Azure][AZURE-Portal]e altri strumenti.  Quando si usa il portale, un'entità servizio viene creata automaticamente quando si registra un'applicazione.

Il pannello **applicazioni aziendali** nel portale viene usato per elencare e gestire le entità servizio in un tenant. È possibile visualizzare le autorizzazioni di un'entità servizio, le autorizzazioni concesse dall'utente, gli utenti che hanno eseguito tale consenso, le informazioni di accesso e altro ancora.

L' [entità Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] definisce lo schema per le proprietà di un oggetto entità servizio.

## <a name="relationship-between-application-objects-and-service-principals"></a>Relazione tra gli oggetti applicazione e le entità servizio

L'oggetto applicazione è la rappresentazione *globale* dell'applicazione da usare in tutti i tenant e l'entità servizio è la rappresentazione *locale* da usare in un tenant specifico.

L'oggetto applicazione funge da modello da cui *derivano* le proprietà comuni e predefinite per l'uso nella creazione di oggetti entità servizio corrispondenti. Un oggetto applicazione ha quindi una relazione 1:1 con l'applicazione software e una relazione 1:molti con gli oggetti entità servizio corrispondenti.

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

![Relazione tra l'oggetto app e l'oggetto entità servizio](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In questo scenario di esempio:

| Passaggio | Descrizione |
|------|-------------|
| 1    | È il processo di creazione degli oggetti applicazione ed entità servizio nel tenant principale dell'applicazione. |
| 2    | Quando gli amministratori di Contoso e Fabrikam completano il consenso, nel tenant di Azure AD della rispettiva società viene creato un oggetto entità servizio a cui vengono assegnate le autorizzazioni concesse dall'amministratore. Si noti anche che l'app HR potrebbe essere configurata/progettata per permettere il consenso da parte di utenti per l'uso individuale. |
| 3    | I tenant consumer dell'applicazione HR (Contoso e Fabrikam) dispongono ognuno del proprio oggetto entità servizio. Ognuno rappresenta l'uso di un'istanza dell'applicazione in fase di runtime, gestito tramite le autorizzazioni concesse dall'amministratore. |

## <a name="next-steps"></a>Passaggi successivi

- È possibile utilizzare [esplora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) per eseguire query sugli oggetti dell'applicazione e dell'entità servizio.
- È possibile accedere all'oggetto applicazione di un'applicazione usando l'API Microsoft Graph, l'editor del manifesto dell'applicazione [portale di Azure][AZURE-Portal] o [Azure ad i cmdlet di PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), come rappresentato dall' [entità dell'applicazione][MS-Graph-App-Entity]OData.
- È possibile accedere all'oggetto entità servizio di un'applicazione tramite l'API Microsoft Graph o [Azure ad cmdlet di PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), come rappresentato dall' [entità ServicePrincipal][MS-Graph-Sp-Entity]di OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
