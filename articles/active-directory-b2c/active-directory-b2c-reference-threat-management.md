---
title: Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C | Microsoft Docs
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 65c74b7451c5a605ca8c2e866296c87c0320b730
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316902"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gestire le minacce alle risorse e ai dati in Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C offre funzionalità incorporate che aiutano a proteggere le risorse e i dati dalle minacce. Queste minacce includono gli attacchi Denial of Service e gli attacchi alle password. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse. 

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C protegge dagli attacchi flood SYN usando un cookie SYN. Azure AD B2C protegge anche dagli attacchi Denial of Service usando i limiti per velocità e connessioni.

## <a name="password-attacks"></a>Attacchi alle password

Le password impostate dagli utenti devono essere ragionevolmente complesse. Azure AD B2C ha tecniche di mitigazione per gli attacchi alle password. Questa mitigazione include sia attacchi di forza bruta che attacchi con dizionario alle password. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet. 

Azure AD B2C usa una strategia sofisticata per bloccare gli account. Gli account vengono bloccati in base all'indirizzo IP della richiesta e alle password immesse. La durata del blocco aumenta anche in base alla probabilità che sia in corso un attacco. Dopo che una password viene usata 10 volte con esito negativo, si verifica un blocco di un minuto. La volta successiva che un accesso ha esito negativo dopo che l'account è stato sbloccato, si verifica un altro blocco di un minuto e lo stesso accade per ogni accesso non riuscito. L'immissione ripetuta della stessa password non viene considerata come una serie di accessi non riusciti. 

I primi 10 periodi di blocco durano un minuto. I 10 periodi di blocco successivi sono leggermente più lunghi e la durata aumenta dopo ogni 10 periodi di blocco. Il contatore di blocchi viene reimpostato su zero dopo un accesso riuscito quando l'account non è bloccato. I periodi di blocco possono durare fino a cinque ore. 

Attualmente, non è possibile:

- Attivare un blocco con meno di 10 accessi non riusciti
- Recuperare un elenco di account bloccati
- Configurare i criteri di blocco

Per maggiori informazioni, visitare il [Centro protezione Microsoft](https://www.microsoft.com/trustcenter/default.aspx).
