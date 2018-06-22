---
title: Gestione delle minacce in Azure Active Directory B2C | Microsoft Docs
description: Vengono illustrate le tecniche di rilevamento e mitigazione di attacchi Denial of Service e attacchi alle password in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b2686b4bb2b98d3f79d8087f6857c149cfdeb553
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711264"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C: Gestione delle minacce

Gestione delle minacce include la pianificazione per la protezione da attacchi contro il sistema e le reti. Gli attacchi Denial of Service potrebbero rendere le risorse non disponibili agli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse. Azure Active Directory B2C (Azure AD B2C) offre funzionalità incorporate che aiutano a proteggere i dati da queste minacce in diversi modi.

## <a name="denial-of-service-attacks"></a>Attacchi Denial of Service

Azure AD B2C usa tecniche di rilevamento e mitigazione, ad esempio cookie SYN e limiti di velocità e connessione, per proteggere le risorse sottostanti dagli attacchi Denial of Service.

## <a name="password-attacks"></a>Attacchi alle password

Azure AD B2C dispone inoltre di tecniche di mitigazione per gli attacchi alle password. Questa mitigazione include sia attacchi di forza bruta che attacchi con dizionario alle password. Le password impostate dagli utenti devono essere ragionevolmente complesse. Tramite segnali diversi, Azure AD B2C analizza l'integrità delle richieste. Azure Active Directory B2C è progettato per distinguere in modo intelligente gli utenti previsti da hacker e botnet. Azure AD B2C fornisce una sofisticata strategia di blocco account in base alle password immesse rispetto alla probabilità di un attacco.

Per maggiori informazioni, visitare il [Centro protezione Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement).
