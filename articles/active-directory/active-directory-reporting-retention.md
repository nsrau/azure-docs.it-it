---
title: Criteri di conservazione dei report di Azure Active Directory | Documentazione Microsoft
description: Criteri di conservazione dei dati di report in Azure Active Directory
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory
*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*


Questo argomento offre le risposte alle domande più comuni sulla conservazione dei dati per i diversi report attività in Azure Active Directory. 

Come è possibile avviare la raccolta dei dati dell'attività?

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
|Premium e Premium 2 | Quando ci si iscrive a una licenza |
| Gratuito | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |


Quando i dati dell'attività sono disponibili nel portale di Azure?

- **Immediatamente**: se si usano già i report nel portale di Azure classico
- **Entro 2 ore**: se non si è attivata la creazione di report nel portale di Azure classico

Come è possibile avviare la raccolta dei segnali di sicurezza?  
Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di Identity Protection Center. 

Per quanto tempo rimangono archiviati i dati raccolti?

**Report attività**    

| Report | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Directory Audit (Controllo directory) | 7 giorni | 30 giorni | 30 giorni |
| Attività di accesso |    7 giorni | 30 giorni | 30 giorni |

**Segnali di sicurezza**

| Report | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Accessi a rischio | 7 giorni | 30 giorni | 90 giorni |





<!--HONumber=Dec16_HO4-->


