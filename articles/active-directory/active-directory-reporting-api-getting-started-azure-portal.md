---

title: Introduzione all'API di creazione report di Azure AD | Microsoft Docs
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fd750c1bf960678e0fecef61a67b73781d9777ef
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introduzione all'API di creazione report di Azure Active Directory

Azure Active Directory fornisce una serie di report. I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. L'API di creazione report di Azure AD fornisce l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

Questo articolo riporta le informazioni necessarie per iniziare con le API di creazione report di Azure AD.
Nella sezione successiva sono riportate ulteriori informazioni sull'utilizzo delle API di controllo e di accesso. Per tutte le altre API, vedere l'articolo relativo a [report ed eventi di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).

Per le domande frequenti, fare clic [qui](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq). In caso di problemi, [inviare un ticket di supporto](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>Percorso di formazione
1. **Preparazione** - Prima di poter usare gli esempi contenuti in questo argomento, è necessario completare i [prerequisiti di accesso all'API di creazione report di Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Esplorazione** - Ottenere una prima impressione delle API di creazione report:
   
   * [Utilizzo degli esempi dell'API di controllo](active-directory-reporting-api-audit-samples.md) 
   * [Utilizzo degli esempi dell'API di creazione report sull'attività di accesso](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalizzazione** - Creare una soluzione personalizzata: 
   
   * [Utilizzo delle informazioni di riferimento sull'API di controllo](active-directory-reporting-api-audit-reference.md) 
   * [Utilizzo delle informazioni di riferimento sull'API di creazione report sull'attività di accesso](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Passaggi successivi
Se si è curiosi di vedere tutti gli endpoint disponibili dell'API Graph di Azure AD, passare ad [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).


