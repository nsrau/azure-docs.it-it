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
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 47594d1d435ee92d57150a01a99765bab0e87846
ms.openlocfilehash: 2ac62c2c04975167b243b49bf2c536900e77df73


---
# <a name="azure-active-directory-report-retention-policies"></a>Criteri di conservazione dei report di Azure Active Directory
*Questo documento fa parte della [guida alla creazione di report in Azure Active Directory](active-directory-reporting-guide.md).*


Questo argomento offre le risposte alle domande più comuni sulla conservazione dei dati per i diversi report attività in Azure Active Directory. 

Come è possibile avviare la raccolta dei dati dell'attività?

| Edizione di Azure AD | Avvio della raccolta |
| :--              | :--   |
| Azure AD P1 Premium <br /> Azure AD P2 Premium | Quando ci si iscrive a una sottoscrizione |
| Azure AD Free | La prima volta che si apre il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o si usano le [API di creazione report](https://aka.ms/aadreports)  |


Quando i dati dell'attività sono disponibili nel portale di Azure?

- **Immediatamente**: se si usano già i report nel portale di Azure classico
- **Entro 2 ore**: se non si è attivata la creazione di report nel portale di Azure classico

Come è possibile avviare la raccolta dei segnali di sicurezza?  
Per i segnali di sicurezza, il processo di raccolta viene avviato quando si acconsente esplicitamente all'uso di Identity Protection Center. 

Per quanto tempo rimangono archiviati i dati raccolti?

**Report attività**    

| Report | Azure AD Free | Azure AD P1 Premium | Azure AD P2 Premium |
| :--    | :--           | :--                | :--                |
| Directory Audit (Controllo directory) | 7 giorni | 30 giorni | 30 giorni |
| Attività di accesso |    7 giorni | 30 giorni | 30 giorni |

**Segnali di sicurezza**

| Report | Azure AD Free | Azure AD P1 Premium | Azure AD P2 Premium |
| :--    | :--           | :--                | :--                |
| Accessi a rischio | 7 giorni | 30 giorni | 90 giorni |





<!--HONumber=Feb17_HO1-->


