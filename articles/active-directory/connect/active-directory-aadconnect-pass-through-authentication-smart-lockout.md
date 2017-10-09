---
title: 'Azure AD Connect: autenticazione pass-through - Blocco smart | Microsoft Docs'
description: Questo articolo descrive come l'autenticazione pass-through di Azure Active Directory (Azure AD) consente di proteggere gli account locali da attacchi di forza bruta alla password nel cloud.
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 7e05c469260a445578c80cdf77fab2d5ffb48022
ms.contentlocale: it-it
ms.lasthandoff: 09/28/2017

---

# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticazione pass-through di Azure Active Directory: blocco smart

## <a name="overview"></a>Panoramica

Azure AD consente di proteggersi dagli attacchi di forza bruta alle password e impedisce il blocco delle applicazioni Office 365 e SaaS degli utenti originali. Questa funzionalità, denominata **blocco smart**, è supportata quando si usa l'autenticazione pass-through come metodo di accesso. Il blocco smart è abilitato per impostazione predefinita per tutti i tenant e protegge costantemente gli account utente. Non è necessario attivarlo.

Il blocco smart tiene traccia dei tentativi di accesso non riusciti e dopo una determinata **soglia di blocco**, avvia la **durata del blocco**. Eventuali tentativi di accesso da un utente malintenzionato durante la durata del blocco vengono rifiutati. Se l'attacco continua, i tentativi successivi di accesso al termine della durata del blocco non riescono e la durata del blocco viene prolungata.

>[!NOTE]
>Il valore predefinito della Soglia di blocco è 10 tentativi non riusciti, mentre la Durata del blocco predefinita è 60 secondi.

Il blocco smart consente di distinguere anche tra l'accesso effettuato da utenti originali e da utenti malintenzionati e nella maggior parte dei casi blocca solo gli utenti malintenzionati. Questa funzionalità impedisce agli utenti malintenzionati di bloccare gli utenti veri. Per distinguere tra utenti malintenzionati e utenti veri vengono analizzati il comportamento di accesso, i dispositivi e i browser degli utenti oltre ad altri segnali. Gli algoritmi vengono migliorati costantemente.

Poiché l'autenticazione pass-through inoltra le richieste di convalida della password in Active Directory (AD) locale, è necessario impedire ai pirati informatici di bloccare gli account di AD degli utenti. Poiché l'utente dispone di propri criteri di blocco degli account di AD, in particolare [**Soglia di blocchi dell'account**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [ **	Reimposta blocco account dopo**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx), è necessario configurare in modo appropriato i valori di durata del blocco e la soglia di blocco di Azure AD per filtrare gli attacchi nel cloud, prima che raggiungano AD locale.

>[!NOTE]
>La funzionalità Smart Lockout è gratuita e _attiva_ per impostazione predefinita per tutti i clienti. Tuttavia, per modificare i valori relativi alla soglia di blocco e alla durata del blocco di Azure AD usando l'API Graph, è necessario che il tenant abbia almeno una licenza di Azure AD Premium P2. Non è necessaria una licenza di Azure AD Premium P2 _per ogni utente_ per ottenere la funzionalità Smart Lockout con l'autenticazione pass-through.

Per garantire che gli account di AD locali degli utenti siano protetti, è necessario assicurarsi che:

1.  La Soglia di blocco di Azure AD sia _inferiore_ alla soglia di blocco dell'account di AD. È consigliabile impostare i valori in modo che è la soglia di blocco dell'account di AD sia almeno di due o tre volte superiore alla soglia di blocco di Azure AD.
2.  La durata del blocco di Azure AD, rappresentata in secondi, è _maggiore_ rispetto al valore di Reimposta blocco account dopo di AD, rappresentato in minuti.

## <a name="verify-your-ad-account-lockout-policies"></a>Verificare i criteri di blocco degli account di AD

Usare le istruzioni seguenti per verificare i criteri di blocco degli account di AD:

1.  Aprire lo strumento Gestione criteri di gruppo.
2.  Modificare i criteri di gruppo applicati a tutti gli utenti, ad esempio il Criterio dominio predefinito.
3.  Passare a Configurazione computer\Criteri\Impostazioni di Windows\Impostazioni di sicurezza\Criteri account\Criterio di blocco account.
4.  Verificare i valori di Soglia di blocchi dell'account e Reimposta blocco account dopo.

![Criteri di blocco degli account di AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Usare l'API Graph per gestire i valori di blocco smart del tenant (necessaria licenza Premium)

>[!IMPORTANT]
>La modifica dei valori relativi alla soglia di blocco e alla durata del blocco di Azure AD tramite l'API Graph di Azure AD sono funzionalità di Azure AD Premium P2. L'utente deve anche essere Amministratore globale del tenant.

È possibile usare [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) per leggere, impostare e aggiornare i valori di blocco smart per Azure AD. Ma è anche possibile eseguire queste operazioni a livello di programmazione.

### <a name="read-smart-lockout-values"></a>Leggere i valori di blocco smart

Seguire questa procedura per leggere i valori di blocco smart del tenant:

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Fare clic su "Autorizzazioni di modifica" e selezionare l'autorizzazione "Directory.ReadWrite.All".
3. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su "BETA", il tipo di richiesta su "GET" e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Fare clic su "Esegui query" per visualizzare i valori di blocco smart del tenant. Se i valori del tenant non sono stati mai impostati, viene visualizzato un insieme vuoto.

### <a name="set-smart-lockout-values"></a>Impostare i valori di blocco smart

Attenersi alla procedura seguente per impostare i valori di blocco smart del tenant. Eseguire questa procedura solo la prima volta.

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Fare clic su "Autorizzazioni di modifica" e selezionare l'autorizzazione "Directory.ReadWrite.All".
3. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su "BETA", il tipo di richiesta su "POST" e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copiare e incollare la richiesta JSON seguente nel campo "Corpo della richiesta".
5. Fare clic su "Esegui query" per impostare i valori di blocco smart del tenant.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Se l'utente non li sta usando, è possibile lasciare i valori di **BannedPasswordList** e **EnableBannedPasswordCheck** i vuoto ("") e "false" rispettivamente.

Verificare di aver impostato i valori di blocco smart del tenant correttamente tramite [questa procedura](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Aggiornare i valori di blocco smart

Seguire questa procedura per aggiornare i valori di blocco smart del tenant, se questi sono già stati impostati in precedenza:

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Fare clic su "Autorizzazioni di modifica" e selezionare l'autorizzazione "Directory.ReadWrite.All".
3. [Seguire questa procedura per leggere i valori di blocco smart del tenant](#read-smart-lockout-values). Copia il valore `id` , ovvero un GUID dell'elemento con "displayName" come "PasswordRuleSettings".
4. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su "BETA", il tipo di richiesta su "PATCH" e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Usare il GUID dal passaggio 3 per `<id>`.
5. Copiare e incollare la richiesta JSON seguente nel campo "Corpo della richiesta". Modificare i valori di blocco smart in base alle necessità.
6. Fare clic su "Esegui query" per aggiornare i valori di blocco smart del tenant.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Verificare di aver aggiornato i valori di blocco smart del tenant correttamente tramite [questa procedura](#read-smart-lockout-values).

## <a name="next-steps"></a>Passaggi successivi
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

