---
title: 'Azure Active Directory B2C: Gestione delle minacce | Documentazione Microsoft'
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
ms.openlocfilehash: a88a04dcc83482813b8ada0e7dbff985e9f49aba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Gestione delle minacce

Gestione delle minacce include la pianificazione per la protezione da attacchi contro il sistema e le reti. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse. Azure Active Directory B2C (Azure AD B2C) offre funzionalità incorporate che aiutano a proteggere i dati da queste minacce in diversi modi.

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C usa tecniche di rilevamento e mitigazione, ad esempio cookie SYN e limiti di velocità e connessione, per proteggere le risorse sottostanti dagli attacchi Denial of Service.

## <a name="password-attacks"></a>Attacchi alle password

Azure AD B2C dispone inoltre di tecniche di mitigazione per gli attacchi alle password. Questa mitigazione include sia attacchi di forza bruta che attacchi con dizionario alle password. Le password impostate dagli utenti devono essere ragionevolmente complesse. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet. Azure AD B2C fornisce una sofisticata strategia di blocco account in base alle password immesse rispetto alla probabilità di un attacco.

Per maggiori informazioni, visitare il [Centro protezione Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement).
