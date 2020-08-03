---
title: Versioni del flusso utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sulle versioni dei flussi utente disponibili in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67949c31c710d88a05e1e110860fe703caf66d04
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481326"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versioni del flusso utente in Azure Active Directory B2C

I flussi utente in Azure Active Directory B2C (Azure AD B2C) consentono di configurare [criteri](user-flow-overview.md) comuni che descrivono in modo completo le esperienze di identità dei clienti. Queste esperienze includono iscrizione, accesso, reimpostazione della password o modifica del profilo. Le tabelle seguenti descrivono i flussi utente disponibili in Azure AD B2C.

> [!IMPORTANT]
> È stato modificato il modo in cui si fa riferimento alle versioni del flusso utente. In precedenza, abbiamo offerto versioni V1 (pronte per l'ambiente di produzione) e versioni V 1.1 e V2 (anteprima). A questo punto, i flussi utente sono stati consolidati in due versioni:
>
>- I flussi utente **consigliati** sono le nuove versioni di anteprima dei flussi utente. Sono testati accuratamente e combinano tutte le funzionalità delle versioni **v2** e **v 1.1** legacy. In futuro, i nuovi flussi utente consigliati verranno mantenuti e aggiornati. Quando si passa a questi nuovi flussi utente consigliati, sarà possibile accedere alle nuove funzionalità non appena vengono rilasciate.
>- I flussi utente standard, precedentemente noti come **V1**, sono disponibili a **livello** generale, flussi utente pronti per la produzione. Se i flussi utente sono cruciali e dipendono da versioni altamente stabili, è possibile continuare a usare i flussi utente standard, rendendo conto che tali versioni non verranno gestite e aggiornate.
>
>Tutti i flussi utente di anteprima legacy (V 1.1 e v2) si trovano in un percorso deprecato entro **il 1 agosto 2021**. Laddove possibile, è consigliabile [passare alle nuove versioni **consigliate** ](#how-to-switch-to-a-new-recommended-user-flow) appena possibile, in modo da poter sfruttare sempre le funzionalità e gli aggiornamenti più recenti. *Queste modifiche si applicano solo al cloud pubblico di Azure. Altri ambienti continueranno a usare il [controllo delle versioni legacy del flusso utente](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Flussi utente consigliati

I flussi utente consigliati sono versioni di anteprima che combinano nuove funzionalità con funzionalità legacy V2 e V 1.1. In futuro, i flussi utente consigliati verranno mantenuti e aggiornati.

| Flusso utente | Descrizione |
| --------- | ----------- |
| Reimpostazione della password (anteprima) | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Modifica del profilo (anteprima) | Consente a un utente di configurare i rispettivi attributi utente. Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li></ul> |
| Accedi (anteprima) | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>Personalizzazione della pagina di accesso</li></ul> |
| Iscrizione (anteprima) | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Iscrizione e accesso (anteprima) | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Flussi utente standard

I flussi utente standard (indicati in precedenza come V1) sono disponibili a livello generale, ovvero i flussi utente pronti per la produzione. I flussi utente standard non verranno aggiornati in futuro.

| Flusso utente | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione della password | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Modifica del profilo | Consente a un utente di configurare i rispettivi attributi utente. Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li></ul> |
| Accedi | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>Blocco dell'accesso</li><li>Forzatura della reimpostazione della password</li><li>Opzione Mantieni l'accesso</ul><br>Non è possibile personalizzare l'interfaccia utente con questo flusso utente. |
| Iscrizione | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Eseguire l'iscrizione e l'accesso | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Come passare a un nuovo flusso utente consigliato

Per passare da una versione legacy di un flusso utente alla nuova versione di anteprima **consigliata** , seguire questa procedura:

1. Creare un nuovo criterio di flusso utente attenendosi alla procedura descritta in [esercitazione: creare flussi utente in Azure Active Directory](tutorial-create-user-flows.md). Quando si crea il flusso utente, selezionare la versione **consigliata** .

3. Configurare il nuovo flusso utente con le stesse impostazioni configurate nei criteri legacy.

4. Aggiornare l'URL di accesso dell'applicazione al criterio appena creato.

5. Dopo aver testato il flusso utente e averne verificato il funzionamento, eliminare il flusso utente legacy attenendosi alla procedura seguente:
   1. Nel menu Panoramica del tenant Azure AD B2C selezionare **flussi utente**.
   2. Trovare il flusso utente che si vuole eliminare.
   3. Nell'ultima colonna selezionare il menu di scelta rapida (**...**) e quindi selezionare **Elimina**.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>È ancora possibile creare flussi utente legacy V2 e V 1.1?

Non sarà possibile creare nuovi flussi utente basati sulle versioni V2 e V 1.1 Legacy, ma è possibile continuare a leggere, aggiornare ed eliminare qualsiasi flusso utente V2 e V 1.1 Legacy attualmente in uso.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Esistono motivi per continuare a usare i flussi utente legacy V2 e V 1.1?

Risposta errata. Le nuove versioni di anteprima **consigliate** contengono la stessa funzionalità delle versioni V2 e v 1.1 Legacy. Nulla è stato rimosso e in effetti includono funzionalità aggiuntive.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Se non si passa da criteri legacy V2 e V 1.1, in che modo influirà sull'applicazione?

Se si usa un flusso utente legacy v2 o V 1.1, l'applicazione non sarà interessata da questa modifica del controllo delle versioni. Tuttavia, per ottenere l'accesso a nuove funzionalità o modifiche ai criteri in futuro, sarà necessario passare alle nuove versioni **consigliate** .

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft supporterà comunque i criteri del flusso utente v2 o V 1.1 Legacy?

Le versioni legacy V2 e V 1.1 dei flussi utente continueranno a essere completamente supportate.
