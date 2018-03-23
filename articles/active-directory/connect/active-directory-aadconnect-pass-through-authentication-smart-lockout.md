---
title: 'Azure AD Connect: autenticazione pass-through - Blocco smart | Microsoft Docs'
description: Questo articolo descrive come l'autenticazione pass-through di Azure Active Directory (Azure AD) consente di proteggere gli account locali da attacchi di forza bruta alla password nel cloud
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 9477d47824213d7ea15bcf6c6b615a220bae2e48
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticazione pass-through di Azure Active Directory: blocco smart

## <a name="overview"></a>Panoramica

Azure Active Directory (Azure AD) consente di proteggersi dagli attacchi di forza bruta alle password e impedisce il blocco delle applicazioni Office 365 e SaaS degli utenti originali. Questa funzionalità, denominata *blocco smart*, è supportata quando si usa l'autenticazione pass-through come metodo di accesso. La funzionalità per il blocco smart è abilitata per impostazione predefinita per tutti i tenant, non solo per quelli che usano l'autenticazione pass-through, e protegge continuamente gli account utente.

Il blocco smart tiene traccia dei tentativi di accesso non riusciti. Dopo una determinata *soglia di blocco*, viene avviata una *durata di blocco*. Il blocco smart rifiuta eventuali tentativi di accesso da parte di un utente malintenzionato durante la durata del blocco. Se l'attacco continua, i tentativi successivi di accesso al termine della durata del blocco non riescono e la durata del blocco viene prolungata.

>[!NOTE]
>La soglia di blocco predefinita corrisponde a 10 tentativi non riusciti. La durata di blocco predefinita è di 60 secondi.

Il blocco smart consente di distinguere anche tra l'accesso effettuato da utenti originali e accessi da utenti malintenzionati. Nella maggior parte dei casi vengono bloccati solo gli utenti malintenzionati. Questa funzionalità impedisce agli utenti malintenzionati di bloccare gli utenti veri. Il blocco smart usa il comportamento di accesso precedente, i dispositivi e i browser degli utenti e altri segnali per distinguere tra utenti originali e utenti malintenzionati. Gli algoritmi vengono migliorati costantemente.

L'autenticazione pass-through inoltra le richieste di convalida della password all'istanza locale di Active Directory, quindi è necessario impedire ai pirati informatici di bloccare gli account Active Directory degli utenti. Active Directory offre criteri di blocco degli account specifici, in particolare i criteri [Soglia di blocchi dell'account](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [Reimposta contatore blocco account dopo](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx). Configurare la soglia di blocco di Azure AD e i valori della durata di blocco in modo appropriato per escludere tramite filtro gli attacchi sul cloud prima che raggiungano l'istanza locale di Active Directory.

>[!NOTE]
>>La funzionalità Smart Lockout è gratuita e _attiva_ per impostazione predefinita per tutti i clienti. Per modificare i valori relativi alla soglia di blocco e alla durata del blocco di Azure AD usando l'API Graph, è tuttavia necessaria l'attivazione del tenant per Azure AD Premium P2. 

Per garantire che gli account Active Directory locali degli utenti siano protetti, è necessario assicurarsi che:

   * La soglia di blocco di Azure AD è _inferiore_ alla soglia di blocco dell'account Active Directory. Configurare i valori in modo che la soglia di blocco dell'account Active Directory sia almeno due o tre volte superiore rispetto alla soglia di blocco di Azure AD.
   * La durata di blocco di Azure AD, rappresentata in secondi, è _superiore_ alla reimpostazione del contatore di blocco dell'account Active Directory dopo la durata specificata, rappresentata in minuti.

>[!IMPORTANT]
>Attualmente un amministratore non può sbloccare gli account cloud degli utenti se questi sono stati bloccati dalla funzionalità di blocco smart. L'amministratore deve attendere la scadenza della durata del blocco.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Verificare i criteri di blocco dell'account Active Directory

Usare le istruzioni seguenti per verificare i criteri di blocco dell'account Active Directory:

1.  Aprire lo strumento Gestione criteri di gruppo.
2.  Modificare i criteri di gruppo applicati a tutti gli utenti, ad esempio il **Criterio dominio predefinito**.
3.  Passare a **Configurazione computer** > **Criteri** > **Impostazioni di Windows** > **Impostazioni di sicurezza** > **Criteri account** > **Criterio di blocco account**.
4.  Verificare i valori di **Soglia di blocchi dell'account** e **Reimposta contatore blocco account dopo**.

![Criteri di blocco dell'account Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Usare l'API Graph per gestire i valori di blocco smart del tenant (necessaria una licenza Premium)

>[!IMPORTANT]
>La modifica della soglia di blocco e dei valori della durata di blocco di Azure AD tramite l'API Graph è una funzionalità di Azure AD Premium P2. L'utente deve anche essere amministratore globale del tenant.

È possibile usare [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) per leggere, impostare e aggiornare i valori di blocco smart per Azure AD. È anche possibile eseguire queste operazioni a livello di codice.

### <a name="view-smart-lockout-values"></a>Visualizzare i valori di blocco smart

Seguire questa procedura per visualizzare i valori di blocco smart del tenant:

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Selezionare **Autorizzazioni di modifica** e quindi selezionare l'autorizzazione **Directory.ReadWrite.All**.
3. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su **BETA**, il tipo di richiesta su **GET** e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Selezionare **Esegui query** per visualizzare i valori di blocco smart del tenant. Se i valori del tenant non sono stati mai impostati, viene visualizzato un insieme vuoto.

### <a name="set-smart-lockout-values"></a>Impostare i valori di blocco smart

Attenersi alla procedura seguente per impostare i valori di blocco smart del tenant. Questa procedura è necessaria solo la prima volta:

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Selezionare **Autorizzazioni di modifica** e quindi selezionare l'autorizzazione **Directory.ReadWrite.All**.
3. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su **BETA**, il tipo di richiesta su **POST** e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copiare e incollare la richiesta JSON seguente nel campo **Corpo della richiesta**.
5. Selezionare **Esegui query** per impostare i valori di blocco smart del tenant.

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
>Se non si usano questi valori, è possibile lasciare i valori **BannedPasswordList** e **EnableBannedPasswordCheck** impostati su vuoto (**""**) e **false** rispettivamente.

Verificare di aver impostato i valori di blocco smart del tenant correttamente tramite la procedura illustrata in [Visualizzare i valori di blocco smart](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Aggiornare i valori di blocco smart

Seguire questa procedura per aggiornare i valori di blocco smart del tenant, se sono stati impostati in precedenza:

1. Accedere a Graph explorer come amministratore globale del tenant. Se richiesto, concedere l'accesso per le autorizzazioni richieste.
2. Selezionare **Autorizzazioni di modifica** e quindi selezionare l'autorizzazione **Directory.ReadWrite.All**.
3. [Seguire questa procedura per visualizzare i valori di blocco smart del tenant](#view-smart-lockout-values). Copiare il valore `id` (un GUID) dell'elemento con **displayName** come **PasswordRuleSettings**.
4. Configurare la richiesta dell'API Graph nel modo seguente: impostare la versione su **BETA**, il tipo di richiesta su **PATCH** e l'URL su `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Usare il GUID dal Passaggio 3 per `<id>`.
5. Copiare e incollare la richiesta JSON seguente nel campo **Corpo della richiesta**. Modificare i valori di blocco smart in base alle necessità.
6. Selezionare **Esegui query** per aggiornare i valori del blocco smart del tenant.

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

Verificare di aver aggiornato i valori di blocco smart del tenant correttamente tramite la procedura illustrata in [Visualizzare i valori di blocco smart](#view-smart-lockout-values).

## <a name="next-steps"></a>Passaggi successivi
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
