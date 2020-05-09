---
title: Problemi di accesso a un'applicazione personalizzata | Microsoft Docs
description: Errori comuni che potrebbero non essere in grado di accedere a un'applicazione sviluppata con Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01a424129abf88c18500c96bd1889fc6dcce2ec6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890711"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemi di accesso a un'applicazione personalizzata

Ci sono diversi errori che potrebbero impedire l'accesso a un'app. Il motivo più frequente che causa questo problema è l'errata configurazione delle app.

## <a name="errors-related-to--misconfigured-apps"></a>Errori correlati alle app configurate in modo errato

* Verificare che le configurazioni nel portale corrispondano a quelle nell'app. In particolare, confrontare ID applicazione/client, URL di risposta, chiavi/segreti client e URI ID app.

* Confrontare la risorsa a cui si richiede l'accesso nel codice con le autorizzazioni configurate nella scheda **Risorse necessarie** per assicurarsi di richiedere solo le risorse configurate.

* Vedere [Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) per errori o problemi simili.

## <a name="next-steps"></a>Passaggi successivi

[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD in Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
