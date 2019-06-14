---
title: Versioni del flusso utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sulle versioni dei flussi utente disponibili in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed57a9fa3b041961ce220e8f10d9aed5e7bef60e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511930"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versioni del flusso utente in Azure Active Directory B2C

>[!IMPORTANT]
> Qualsiasi flusso utente elencato nell'articolo è considerato disponibile in anteprima pubblica a meno che non sia identificato come **Consigliato**. Si dovrebbero usare solo flussi utente consigliati per le applicazioni di produzione.

I flussi utente in Azure Active Directory (Azure AD) B2C consentono di configurare [criteri](active-directory-b2c-reference-policies.md) comuni che descrivono in modo esauriente le esperienze di identità dei clienti. Queste esperienze includono iscrizione, accesso, reimpostazione della password o modifica del profilo. In Azure AD B2C è possibile effettuare una selezione da una raccolta di flussi utente consigliati e flussi utente in anteprima. 

I nuovi flussi utente vengono aggiunti sotto forma di nuove versioni. Man mano che diventano stabili, verranno consigliati per l'uso. I flussi utente sono contrassegnati come **Consigliati** dopo essere stati testati completamente. I flussi utente saranno considerati disponibili in anteprima finché non verranno contrassegnati come consigliati. Usare un flusso utente consigliato per tutte le applicazioni di produzione, ma scegliere tra altre versioni per testare nuove funzionalità appena diventano disponibili. Non è consigliabile usare versioni precedenti dei flussi utente consigliati.

## <a name="v1"></a>V1

| Flusso utente | Consigliato | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione delle password | Yes | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Modifica del profilo | Yes | Consente a un utente di configurare i rispettivi attributi utente. Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li></ul> |
| Proprietario della risorsa | No | Consente a un utente con un account locale di accedere direttamente nelle applicazioni native (senza aver bisogno del browser). Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li></ul> |
| Accesso | No | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>Blocco dell'accesso</li><li>Forzatura della reimpostazione della password</li><li>Opzione Mantieni l'accesso</ul><br>Non è possibile personalizzare l'interfaccia utente con questo flusso utente. |
| Iscrizione | No | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Eseguire l'iscrizione e l'accesso | Yes | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| Flusso utente | Consigliato | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione della password v2 | No | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Accesso v2 | No | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>Personalizzazione della pagina di accesso</li></ul> |
| Iscrizione v2 | No | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Durata del token](active-directory-b2c-reference-tokens.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| Iscrizione e accesso v2 | No | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](active-directory-b2c-reference-mfa.md)</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](active-directory-b2c-reference-password-complexity.md)</li></ul> |
