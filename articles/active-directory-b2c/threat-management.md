---
title: Gestire le minacce a risorse e dati
titleSuffix: Azure AD B2C
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183602"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) include funzionalità incorporate che consentono di proteggere le minacce alle risorse e ai dati. Queste minacce includono gli attacchi Denial of Service e gli attacchi alle password. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse.

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C protegge dagli attacchi flood SYN usando un cookie SYN. Azure AD B2C protegge anche dagli attacchi Denial of Service usando i limiti per velocità e connessioni.

## <a name="password-attacks"></a>Attacchi alle password

Le password impostate dagli utenti devono essere ragionevolmente complesse. Azure AD B2C ha tecniche di mitigazione per gli attacchi alle password. La mitigazione include il rilevamento di attacchi di password a forza bruta e attacchi di password del dizionario. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet.

Azure AD B2C usa una strategia sofisticata per bloccare gli account. Gli account vengono bloccati in base all'indirizzo IP della richiesta e alle password immesse. La durata del blocco aumenta anche in base alla probabilità che sia in corso un attacco. Dopo che una password viene tentata 10 volte senza successo (la soglia di tentativo predefinita), si verifica un blocco di un minuto. La volta successiva che un accesso avrà esito negativo dopo lo sblocco dell'account (ovvero, dopo che l'account è stato sbloccato automaticamente dal servizio alla scadenza del periodo di blocco), si verifica un altro blocco di un minuto e continua per ogni accesso non riuscito. L'immissione ripetuta della stessa password non viene considerata come una serie di accessi non riusciti.

I primi 10 periodi di blocco durano un minuto. I 10 periodi di blocco successivi sono leggermente più lunghi e la durata aumenta dopo ogni 10 periodi di blocco. Il contatore di blocchi viene reimpostato su zero dopo un accesso riuscito quando l'account non è bloccato. I periodi di blocco possono durare fino a cinque ore.

## <a name="manage-password-protection-settings"></a>Gestire le impostazioni di protezione con password

Per gestire le impostazioni di protezione con password, inclusa la soglia di blocco:

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Usare il filtro **Directory e sottoscrizione** nel menu superiore per selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Sicurezza**selezionare Metodi di **autenticazione (anteprima)**, quindi **Selezionare Protezione con password**.
1. Immettere le impostazioni di protezione con password desiderate, quindi selezionare **Salva**.

    ![Pagina Di protezione password del portale di Azure nelle impostazioni di Azure ADAzure portal Password protection page in Azure AD settings](./media/threat-management/portal-02-password-protection.png)
    <br />Impostazione della soglia di blocco su *5 in Impostazioni di protezione con **password** *.

## <a name="view-locked-out-accounts"></a>Visualizzare gli account bloccati

Per ottenere informazioni sugli account bloccati, è possibile controllare il report attività [di accesso](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)ad Active Directory. In **Stato**selezionare **Errore**. Tentativi di accesso non riusciti con `50053` un codice di errore di **accesso** che indica un account bloccato:

![Sezione del report di accesso di Azure AD che mostra l'account bloccato](./media/threat-management/portal-01-locked-account.png)

Per informazioni sulla visualizzazione del report attività di accesso in Azure Active Directory, vedere Codici di errore del [report attività di accesso.](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)
