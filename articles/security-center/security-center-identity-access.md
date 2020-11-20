---
title: Monitorare identità e accesso nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare le funzionalità per identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 12e5a6db7257678b3d4d14d7583a1a30da0d3a87
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967143"
---
# <a name="monitor-identity-and-access"></a>Monitorare identità e accesso

Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. Con questo sviluppo, la sicurezza è inferiore alla difesa della rete e altre informazioni sulla gestione della sicurezza di app, dati e utenti.

Monitorando le attività e le impostazioni di configurazione relative all'identità, è possibile intraprendere azioni proattive prima che avvenga un evento imprevisto oppure azioni reattive per arrestare i tentativi di attacco.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Quali misure di sicurezza di identità e accesso sono disponibili nel centro sicurezza? 

Il Centro sicurezza di Azure dispone di due controlli di sicurezza dedicati per garantire che i requisiti di sicurezza e identità dell'organizzazione siano soddisfatti: 

 - **Gestire l'accesso e le autorizzazioni** : si consiglia di adottare il [modello di accesso con privilegi minimi](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) e di garantire agli utenti solo l'accesso necessario per svolgere i propri processi. Questo controllo include anche suggerimenti per l'implementazione del [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) per controllare l'accesso alle risorse.
 
 - **Abilitare** l' [autenticazione a più fattori con l'](https://www.microsoft.com/security/business/identity/mfa) autenticazione a più fattori abilitata, gli account sono più sicuri e gli utenti possono ancora eseguire l'autenticazione a quasi tutte le applicazioni con Single Sign-on.

### <a name="example-recommendations-for-identity-and-access"></a>Suggerimenti di esempio per identità e accesso

Esempi di raccomandazioni che è possibile visualizzare in questi due controlli nella pagina delle **raccomandazioni** del Centro sicurezza:

- L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione
- Per la sottoscrizione devono essere designati al massimo 3 proprietari
- Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione
- Gli account deprecati devono essere rimossi dalla sottoscrizione (gli account deprecati sono account non più necessari e bloccati dall'accesso da Azure Active Directory)

> [!TIP]
> Per ulteriori informazioni su questi consigli e sugli altri elementi che possono essere visualizzati in questi controlli, vedere le indicazioni relative a [identità e accesso](recommendations-reference.md#recs-identity).

### <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni per le protezioni di identità e accesso del Centro sicurezza:

- Le raccomandazioni relative all'identità non sono disponibili per le sottoscrizioni con più di 600 account. In questi casi, queste raccomandazioni verranno elencate in "valutazioni non disponibili".
- Le raccomandazioni di identità non sono disponibili per gli agenti di amministrazione del partner Cloud Solution Provider (CSP).
- Le raccomandazioni di identità non identificano gli account gestiti con un sistema di gestione delle identità con privilegi (PIM). Se si usa uno strumento PIM, è possibile che vengano visualizzati risultati non accurati nel controllo **Gestisci accesso e autorizzazioni** .

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Multi-factor authentication (autenticazione a più fattori) e Azure Active Directory 

Per abilitare l'autenticazione a più fattori sono necessarie [autorizzazioni tenant di Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md).

- Se si ha un'edizione Premium di AD, abilitare l'autenticazione a più fattori usando l'[accesso condizionale](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Se si usa AD Free Edition, abilitare le **impostazioni predefinite di sicurezza** come descritto in [Azure Active Directory documentazione](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Identificare gli account senza multi-factor authentication (autenticazione a più fattori abilitata)

Per visualizzare gli account in cui non è abilitata l'autenticazione a più fattori, usare la query di Azure Resource Graph seguente. La query restituisce tutte le risorse non integre-account-della raccomandazione "l'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni proprietario per la sottoscrizione". 

1. Aprire **Esplora grafico risorse di Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio della pagina consigli di Azure Resource Graph Explorer * *" :::

1. Immettere la query seguente e selezionare **Esegui query**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. La `additionalData` Proprietà rivela l'elenco degli ID oggetto account per gli account che non hanno l'autenticazione a più fattori applicata. 

    > [!NOTE]
    > Gli account vengono visualizzati come ID oggetto anziché come nomi di account per proteggere la privacy dei titolari dell'account.

> [!TIP]
> In alternativa, è possibile usare le valutazioni dei metodi dell'API REST del Centro sicurezza [: Get](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere l'articolo seguente:

- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)