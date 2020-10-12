---
title: Versioni del flusso utente legacy in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sulle versioni legacy dei flussi utente disponibili in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3471b9988b154ccb38dc56545e769a7179a4026f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258728"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Versioni del flusso utente legacy in Azure Active Directory B2C

> [!IMPORTANT]
> Questo articolo descrive il metodo di controllo delle versioni legacy per i flussi utente, che offre versioni V1 (pronte per la produzione) e versioni V 1.1 e V2 (anteprima) dei flussi utente. Gli ambienti diversi dal cloud pubblico di Azure continueranno a usare questo metodo di controllo delle versioni legacy. Nel cloud pubblico di Azure questo metodo viene sostituito dalle [nuove versioni **consigliate** e di **Anteprima** ](user-flow-versions.md).
> 
I flussi utente in Azure Active Directory B2C (Azure AD B2C) consentono di configurare [criteri](user-flow-overview.md) comuni che descrivono in modo completo le esperienze di identità dei clienti. Queste esperienze includono iscrizione, accesso, reimpostazione della password o modifica del profilo.

Nella tabella seguente, a meno che non venga identificato un flusso utente come **consigliato**, questo viene considerato in *Anteprima*. È consigliabile usare solo i flussi utente consigliati per le applicazioni di produzione.

## <a name="v1"></a>V1

| Flusso utente | Consigliato | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione della password | Sì | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Modifica del profilo | Sì | Consente a un utente di configurare i rispettivi attributi utente. Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li></ul> |
| Accedere con ROPC | No | Consente a un utente con un account locale di accedere direttamente nelle applicazioni native (senza aver bisogno del browser). Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li></ul> |
| Accedi | No | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>Blocco dell'accesso</li><li>Forzatura della reimpostazione della password</li><li>Opzione Mantieni l'accesso</ul><br>Non è possibile personalizzare l'interfaccia utente con questo flusso utente. |
| Iscrizione | No | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Eseguire l'iscrizione e l'accesso | Sì | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| Flusso utente | Consigliato | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione della password v 1.1 | No | Consente all'utente di scegliere una nuova password dopo la verifica della posta elettronica (nuovo layout di pagina disponibile). Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| Flusso utente | Consigliato | Descrizione |
| --------- | ----------- | ----------- |
| Reimpostazione della password v2 | No | Consente a un utente di scegliere una nuova password dopo la verifica del rispettivo indirizzo di posta elettronica. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>Impostazioni di compatibilità dei token</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Modifica del profilo v2 | Sì | Consente a un utente di configurare i rispettivi attributi utente. Con questo flusso utente, è possibile configurare: <ul><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li></ul> |
| Accesso v2 | No | Consente a un utente di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>Personalizzazione della pagina di accesso</li></ul> |
| Iscrizione v2 | No | Consente a un utente di creare un account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Durata del token](tokens-overview.md)</li><li>Impostazioni di compatibilità dei token</li><li>Comportamento della sessione</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |
| Iscrizione e accesso v2 | No | Consente a un utente di creare un account o di accedere al proprio account. Con questo flusso utente, è possibile configurare: <ul><li>[Autenticazione a più fattori](custom-policy-multi-factor-authentication.md)</li><li>[Controllo dell'accesso in base all'età](basic-age-gating.md)</li><li>[Requisiti di complessità delle password](user-flow-password-complexity.md)</li></ul> |