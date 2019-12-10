---
title: Gestire le minacce a risorse e dati
titleSuffix: Azure AD B2C
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b236dba9b682bc4d62ef8cfc8b95e67370b3e0ea
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947741"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) offre funzionalità predefinite che consentono di proteggersi dalle minacce per le risorse e i dati. Queste minacce includono gli attacchi Denial of Service e gli attacchi alle password. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse.

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C protegge dagli attacchi flood SYN usando un cookie SYN. Azure AD B2C protegge anche dagli attacchi Denial of Service usando i limiti per velocità e connessioni.

## <a name="password-attacks"></a>Attacchi alle password

Le password impostate dagli utenti devono essere ragionevolmente complesse. Azure AD B2C ha tecniche di mitigazione per gli attacchi alle password. La mitigazione include il rilevamento di attacchi di forza bruta e attacchi di password di dizionario. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet.

Azure AD B2C usa una strategia sofisticata per bloccare gli account. Gli account vengono bloccati in base all'indirizzo IP della richiesta e alle password immesse. La durata del blocco aumenta anche in base alla probabilità che sia in corso un attacco. Dopo che una password è stata tentata 10 volte senza esito positivo (soglia di tentativo predefinita), si verifica un blocco di un minuto. Alla successiva chiusura di un account di accesso dopo che l'account è stato sbloccato (ovvero dopo che l'account è stato sbloccato automaticamente dal servizio una volta scaduto il periodo di blocco), si verifica un altro blocco di un minuto e continua per ogni accesso non riuscito. L'immissione ripetuta della stessa password non viene considerata come una serie di accessi non riusciti.

I primi 10 periodi di blocco durano un minuto. I 10 periodi di blocco successivi sono leggermente più lunghi e la durata aumenta dopo ogni 10 periodi di blocco. Il contatore di blocchi viene reimpostato su zero dopo un accesso riuscito quando l'account non è bloccato. I periodi di blocco possono durare fino a cinque ore.

## <a name="manage-password-protection-settings"></a>Gestire le impostazioni di protezione delle password

Per gestire le impostazioni di protezione delle password, inclusa la soglia di blocco:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Usare il filtro **directory + sottoscrizione** nel menu in alto per selezionare la directory che contiene il tenant del Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **sicurezza**selezionare **metodi di autenticazione (anteprima)** , quindi selezionare **protezione con password**.
1. Immettere le impostazioni di protezione password desiderate e quindi selezionare **Salva**.

    ![portale di Azure pagina di protezione della password in impostazioni Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Impostazione della soglia di blocco su 5 nelle impostazioni di **protezione delle password*** .

## <a name="view-locked-out-accounts"></a>Visualizzare gli account bloccati

Per ottenere informazioni sugli account bloccati, è possibile controllare il [report sull'attività di accesso](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)Active Directory. In **stato**selezionare **errore**. Tentativi di accesso non riusciti con un **codice di errore di accesso** di `50053` indicare un account bloccato:

![Sezione di Azure AD report di accesso che mostra l'account bloccato](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Per informazioni sulla visualizzazione del report delle attività di accesso in Azure Active Directory, vedere [codici di errore del report delle attività di accesso](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
