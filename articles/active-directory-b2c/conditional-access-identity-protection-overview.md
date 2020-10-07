---
title: Identity Protection e l'accesso condizionale in Azure AD B2C
description: Informazioni su come usare Identity Protection per visualizzare accessi a rischio e rilevamenti di rischi e su come usare l'accesso condizionale per applicare criteri dell'organizzazione basati su eventi di rischio nei tenant di Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 921d9e2138c8aa9c09535a673a7cd2d32e9cddad
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270699"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection e l'accesso condizionale per Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Per migliorare la sicurezza di Azure Active Directory B2C (Azure AD B2C)., è possibile usare Azure AD Identity Protection e l'accesso condizionale. Le funzionalità di rilevamento di rischi di Identity Protection, tra cui utenti a rischio e accessi a rischio, vengono automaticamente rilevate e visualizzate nel tenant di Azure AD B2C. È possibile creare criteri di accesso condizionale che usano questi rilevamenti di rischi per determinare le azioni da adottare e applicare i criteri dell'organizzazione. Grazie alla combinazione di queste funzionalità i proprietari di applicazioni Azure AD B2C possono usufruire di maggior controllo sulle autenticazioni rischiose e sui criteri di accesso.
  
Se si conoscono già le funzionalità [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) e l'[accesso condizionale](../active-directory/conditional-access/overview.md) in Azure AD, sarà più agevole usarle con Azure AD B2C, tenendo presenti le differenze minime descritte in questo articolo.

![Accesso condizionale in un tenant B2C](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Per usare l'accesso condizionale, è richiesto Azure AD B2C Premium P2.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Vantaggi dell'uso di Identity Protection e dell'accesso condizionale per Azure AD B2C  

Combinando i criteri di accesso condizionale con il rilevamento dei rischi di Identity Protection, è possibile rispondere alle autenticazioni rischiose con l'azione dei criteri appropriata.

- **Acquisire un nuovo livello di visibilità sui rischi di autenticazione per le app e la base di clienti**. Grazie ai segnali ottenuti da miliardi di autenticazioni mensili tra Azure AD e l'account Microsoft, gli algoritmi di rilevamento dei rischi ora contrassegnano le autenticazioni come a basso, a medio o ad alto rischio per le autenticazioni locali di consumer o cittadini.
- **Rispondere automaticamente ai rischi configurando un sistema personale di autenticazione adattiva**. Per applicazioni specificate, è possibile richiedere a un set specifico di utenti di fornire un secondo fattore di autenticazione, come nel caso dell'autenticazione a più fattori (MFA). In alternativa, è possibile bloccare l'accesso in base al livello di rischio rilevato. Come per le altre esperienze di Azure AD B2C, è possibile personalizzare l'esperienza dell'utente finale risultante con il messaggio, lo stile e il marchio dell'organizzazione. È anche possibile visualizzare le alternative di mitigazione se l'utente non riesce ad accedere.
- **Controllare l'accesso in base a località, gruppi e app**.  È possibile usare l'accesso condizionale anche per controllare situazioni non basate sul rischio. Ad esempio, è possibile richiedere l'autenticazione a più fattori per clienti che accedono a un'app specifica o bloccare l'accesso da aree geografiche specifiche.
- **Definire l'integrazione con i flussi utente di Azure AD B2C e i criteri personalizzati di Identity Experience Framework**. È possibile usare le esperienze personalizzate esistenti e aggiungere i controlli necessari per interfacciarsi con l'accesso condizionale. È anche possibile implementare scenari avanzati per concedere l'accesso, ad esempio per l'accesso basato sulle informazioni o con il provider di autenticazione a più fattori preferito.

## <a name="feature-differences-and-limitations"></a>Differenze e limitazioni delle funzionalità

Il funzionamento di Identity Protection e dell'accesso condizionale in Azure AD B2C è in genere identico a quello in Azure AD, con le eccezioni seguenti:

- Centro sicurezza non è disponibile in Azure AD B2C.

- Identity Protection e l'accesso condizionale non sono supportati per i flussi da server a server ROPC in tenant di Azure AD B2C.

- Nei tenant di Azure AD B2C i rilevamenti di rischi per Identity Protection sono disponibili solo per gli account B2C locali e non per le identità di social network come Google o Facebook.

- Nei tenant di Azure AD B2C è disponibile un subset dei rilevamenti di rischi di Identity Protection. Vedere [Configurare Identity Protection](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- La funzionalità Conformità del dispositivo dell'accesso condizionale non è disponibile nei tenant di Azure AD B2C.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrare l'accesso condizionale con flussi utente e criteri personalizzati

In Azure AD B2C è possibile attivare le condizioni di accesso condizionale da flussi utente predefiniti. È anche possibile incorporare l'accesso condizionale in criteri personalizzati. Come per gli altri aspetti del flusso utente B2C, la messaggistica dell'esperienza utente finale può essere personalizzata in base alle alternative per il messaggio, il marchio e la mitigazione della propria organizzazione. Vedere [Definire un profilo tecnico di accesso condizionale](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>API Microsoft Graph

È anche possibile gestire i criteri di accesso condizionale in Azure AD B2C con l'API Microsoft Graph. Per informazioni dettagliate, vedere la [documentazione relativa all'accesso condizionale](../active-directory/conditional-access/overview.md) e le [informazioni di riferimento su Microsoft Graph](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare Identity Protection e l'accesso condizionale per Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Informazioni su Identity Protection in Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
- [Informazioni sull'accesso condizionale](../active-directory/conditional-access/overview.md)
