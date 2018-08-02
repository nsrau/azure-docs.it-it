---
title: Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori | Microsoft Docs
description: Come individuare il motivo per cui il provisioning per l'applicazione in uso può richiedere più tempo del previsto
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 5be6933c4cd9efa4b8decc4cba6298f18610266a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364698"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori

Quando si abilita il provisioning automatico per un'applicazione, la sincronizzazione iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. 

Le sincronizzazioni successive a quella iniziale risultano più veloci, poiché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive.

## <a name="how-to-improve-provisioning-performance"></a>Come migliorare le prestazioni del provisioning

Se la sincronizzazione iniziale richiede più di qualche ora, è possibile eseguire un'operazione per migliorare le prestazioni:

-   **Filtri di ambito utente** I filtri di ambito consentono di ottimizzare i dati che il servizio di provisioning estrae da Azure AD, filtrando gli utenti in base a valori di attributo specifici. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

