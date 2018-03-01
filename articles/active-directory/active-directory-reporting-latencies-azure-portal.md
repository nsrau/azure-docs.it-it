---
title: Latenze dei report di Azure Active Directory | Microsoft Docs
description: Informazioni sul tempo necessario per la visualizzazione nel portale di Azure degli eventi dei report
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Latenze dei report di Azure Active Directory

I [report](active-directory-preview-explainer.md) di Azure Active Directory offrono tutte le informazioni necessarie per determinare lo stato dell'ambiente. Il tempo necessario per la visualizzazione dei dati di report nel portale di Azure è nota anche come latenza. 

Questo argomento elenca le informazioni sulla latenza per tutte le categorie di report nel portale di Azure. 


## <a name="activity-reports"></a>Report sull’attività

Esistono due aree di reporting sulle attività:

- **Attività di accesso** : informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso utente
- **Log di controllo** : informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory

Nella tabella seguente sono elencate le informazioni sulla latenza per i report di attività.

| Report | Minima | Media | Osservazioni |
| :-- | --- | --- | :-- |
| Log di controllo | 30 minuti  | 1 ora  |In alcuni casi, possono essere necessarie fino a 2 ore per poter visualizzare i dati delle attività di controllo.|
| Accessi | 15 minuti  | 2 ore |In alcuni casi, possono essere necessarie fino a 24 ore per poter visualizzare i dati delle attività di accesso, inclusi quelli provenienti da applicazioni legacy di office. |







## <a name="security-reports"></a>Report sulla sicurezza

Esistono due aree di reporting sulla sicurezza:

- **Accessi a rischio**. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 
- **Utenti contrassegnati per il rischio**. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

Nella tabella seguente sono elencate le informazioni sulla latenza per i report di sicurezza.

| Report | Minima | Media | Massima |
| :-- | --- | --- | --- |
| Utenti a rischio.          | 5 minuti   | 15 minuti  | 2 ore  |
| Accessi a rischio         | 5 minuti   | 15 minuti  | 2 ore  |

## <a name="risk-events"></a>Eventi di rischio

Azure Active Directory usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato evento di rischio.

Nella tabella seguente sono elencate le informazioni sulla latenza per gli eventi di rischio.

| Report | Minima | Media | Massima |
| :-- | --- | --- | --- |
| Accessi da indirizzi IP anonimi |5 minuti |15 minuti |2 ore |
| Accessi da posizioni non note |5 minuti |15 minuti |2 ore |
| Utenti con credenziali perse |2 ore |4 ore |8 ore |
| Trasferimento impossibile a posizioni atipiche |5 minuti |1 ora |8 ore  |
| Accessi da dispositivi infetti |2 ore |4 ore |8 ore  |
| Accessi da indirizzi IP con attività sospette |2 ore |4 ore |8 ore  |



## <a name="next-steps"></a>Passaggi successivi

Se si desidera ottenere maggiori informazioni sui report dell’attività nel portale di Azure, vedere:

- [Report delle attività di accesso nel portale di Azure Active Directory](active-directory-reporting-activity-sign-ins.md)
- [Report delle attività di controllo nel portale di Azure Active Directory](active-directory-reporting-activity-audit-logs.md)

Se si desidera ottenere maggiori informazioni sui report della sicurezza nel portale di Azure, vedere:

- [Report della sicurezza per gli utenti a rischio nel portale di Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Report degli accessi a rischio nel portale di Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)

Se si desidera ottenere maggiori informazioni sugli eventi di rischio, vedere [Eventi di rischio di Azure Active Directory](active-directory-reporting-risk-events.md).
