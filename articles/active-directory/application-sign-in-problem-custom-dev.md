---
title: Problemi di accesso a un'applicazione personalizzata | Microsoft Docs
description: Errori comuni che potrebbero impedire l'accesso a un'applicazione sviluppata con Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 51fac8adc2fe11aac005b4c0c0e9b67bbca8fa6e
ms.contentlocale: it-it
ms.lasthandoff: 04/17/2017

---

# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problemi di accesso a un'applicazione personalizzata

Ci sono diversi errori che potrebbero impedire l'accesso a un'app. Il motivo più frequente che causa questo problema è l'errata configurazione delle app.

## <a name="errors-related-to--misconfigured-apps"></a>Errori correlati alle app configurate in modo errato

* Verificare che le configurazioni nel portale corrispondano a quelle nell'app. In particolare, confrontare ID applicazione/client, URL di risposta, chiavi/segreti client e URI ID app.

* Confrontare la risorsa a cui si richiede l'accesso nel codice con le autorizzazioni configurate nella scheda **Risorse necessarie** per assicurarsi di richiedere solo le risorse configurate.

* Vedere [Azure AD in StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory) per errori o problemi simili.

## <a name="next-steps"></a>Passaggi successivi

[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD in Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)

