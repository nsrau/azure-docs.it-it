---
title: Gestione delle minacce per Azure B2C | Microsoft Docs
description: Tecniche di prevenzione e rilevamento degli attacchi DOS e alle password in Azure B2C.
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C: Gestione delle minacce
Gestione delle minacce include la protezione da attacchi contro il sistema e le reti. Denial of service (DOS) può incidere sulla disponibilità e impedire alle risorse di essere disponibili per gli utenti previsti. Gli attacchi alle password comportano un rischio di accesso non autorizzato alle risorse. Microsoft Azure Active Directory B2C offre funzionalità incorporate per proteggere i dati da queste minacce in diversi modi. 

## <a name="denial-of-service-attack"></a>Attacco Denial of Service

Azure AD B2C usa tecniche di prevenzione e rilevamento, ad esempio cookie SYN, limiti di velocità e connessione, per proteggere le risorse sottostanti da tali attacchi.  

## <a name="password-attacks"></a>Attacchi alle password

Azure AD B2C inoltre dispone inoltre di misure preventive per gli attacchi alle password.  Questa tecnica include sia gli attacchi di forza bruta che attacchi con dizionario alle password.  Le password impostate dagli utenti devono essere ragionevolmente complesse.  Azure AD B2C analizza l'integrità delle richieste per distinguere in modo intelligente gli utenti previsti dai pirati informatici e botnet, usando diversi segnali. B2C fornisce una sofisticata strategia di blocco account in base alle password immesse rispetto alla probabilità di un attacco.

[Ulteriori informazioni su Gestione delle minacce di Microsoft](https://www.microsoft.com/trustcenter/security/threatmanagement)
