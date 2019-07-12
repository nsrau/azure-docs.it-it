---
title: Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798249"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C offre funzionalità incorporate che aiutano a proteggere le risorse e i dati dalle minacce. Queste minacce includono gli attacchi Denial of Service e gli attacchi alle password. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse.

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C protegge dagli attacchi flood SYN usando un cookie SYN. Azure AD B2C protegge anche dagli attacchi Denial of Service usando i limiti per velocità e connessioni.

## <a name="password-attacks"></a>Attacchi alle password

Le password impostate dagli utenti devono essere ragionevolmente complesse. Azure AD B2C ha tecniche di mitigazione per gli attacchi alle password. Questa mitigazione include il rilevamento di attacchi di forza bruta e attacchi con dizionario password. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet.

Azure AD B2C usa una strategia sofisticata per bloccare gli account. Gli account vengono bloccati in base all'indirizzo IP della richiesta e alle password immesse. La durata del blocco aumenta anche in base alla probabilità che sia in corso un attacco. Dopo che una password è ha tentata 10 volte (soglia di tentativo di impostazione predefinita), un blocco di un minuto si verifica. La volta successiva che un account di accesso ha esito negativo dopo che l'account è sbloccata (vale a dire, dopo che l'account è stato sbloccato automaticamente dal servizio dopo la scadenza del blocco), un altro blocco di un minuto si verifica e continua per ogni account di accesso non riuscito. L'immissione ripetuta della stessa password non viene considerata come una serie di accessi non riusciti.

I primi 10 periodi di blocco durano un minuto. I 10 periodi di blocco successivi sono leggermente più lunghi e la durata aumenta dopo ogni 10 periodi di blocco. Il contatore di blocchi viene reimpostato su zero dopo un accesso riuscito quando l'account non è bloccato. I periodi di blocco possono durare fino a cinque ore.

## <a name="manage-password-protection-settings"></a>Gestire le impostazioni di protezione delle password

Per gestire le impostazioni di protezione delle password, tra cui la soglia di blocco:

1. Passare al [portale di Azure](https://portal.azure.com).
1. Selezionare il **Directory e sottoscrizione** filtrare nel menu superiore destro del portale, quindi selezionare il tenant di Azure AD B2C.
1. Selezionare **Azure Active Directory** dal menu a sinistra (o selezionare **tutti i servizi** nella sezione superiore sinistro del portale, quindi cercare e selezionare *Azure Active Directory*).
1. Sotto **sicurezza**, selezionare **metodi di autenticazione**, quindi selezionare **protezione con Password**.
1. Immettere le impostazioni di protezione di password desiderata e quindi selezionare **salvare**.

    ![Password protection pagina del portale Azure nelle impostazioni di Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Impostazione della soglia di blocco a 5 in **la protezione con Password** impostazioni*.

## <a name="view-locked-out-accounts"></a>Visualizzare gli account bloccati

Per ottenere informazioni sugli account bloccato, è possibile controllare il servizio Active Directory [report attività di accesso](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Sotto **lo stato**, selezionare **errore**. Non è stato possibile tentativi di accesso con un **Accedi codice di errore** di `50053` indicare un account bloccato:

![Sezione di Azure AD report sugli accessi con account bloccato](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Per altre informazioni sulla visualizzazione del report attività di accesso in Azure Active Directory, vedere [Accedi codici errore report delle attività](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
