---
title: Latenze dei report di Azure Active Directory | Microsoft Docs
description: Informazioni sul tempo necessario per la visualizzazione nel portale di Azure degli eventi dei report
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007698"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latenze dei report di Azure Active Directory

La latenza è il tempo necessario per la visualizzazione dei dati di report di Azure Active Directory (Azure AD) nel [portale di Azure](https://portal.azure.com). Questo articolo elenca la latenza prevista per i diversi tipi di report. 

## <a name="activity-reports"></a>Report sull’attività

Esistono due tipi di report attività:

- [Accessi](concept-sign-ins.md): fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti
- [Log di controllo](concept-audit-logs.md): fornisce informazioni relative alle attività di sistema su utenti e gruppi, applicazioni gestite e attività di directory

Nella tabella seguente sono elencate le informazioni sulla latenza per i report di attività. 

> [!NOTE]
> **Latenza (95° percentile)** si riferisce al tempo entro il quale verrà segnalato il 95% dei log e **Latenza (99° percentile)** si riferisce al tempo entro il quale verrà segnalato il 99% dei log. 
>

| Report | Latenza (95° percentile) |Latenza (99° percentile)|
| :-- | --- | --- |
| Log di controllo | 2 min  | 5 min  |
| Accessi | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>In quanto tempo è possibile visualizzare i dati di attività dopo aver acquistato una licenza Premium?

Se si dispone già di dati di attività con la licenza gratuita, è possibile visualizzarli immediatamente dopo l'aggiornamento. Se non si dispone di dati, questi verranno visualizzati nei report dopo uno o due giorni dall'acquisto della licenza Premium.

## <a name="security-reports"></a>Report sulla sicurezza

Esistono due tipi di report sulla sicurezza:

- [Accessi a rischio](concept-risky-sign-ins.md). Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente. 
- [Utenti contrassegnati per il rischio](concept-user-at-risk.md). Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso. 

Nella tabella seguente sono elencate le informazioni sulla latenza per i report di sicurezza.

| Report | Minima | Media | Massima |
| :-- | --- | --- | --- |
| Utenti a rischio.          | 5 minuti   | 15 minuti  | 2 ore  |
| Accessi a rischio         | 5 minuti   | 15 minuti  | 2 ore  |

## <a name="risk-detections"></a>Rilevamenti dei rischi

Azure AD usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato **rilevamento del rischio**.

Nella tabella seguente sono elencate le informazioni sulla latenza per i rilevamenti dei rischi.

| Report | Minima | Media | Massima |
| :-- | --- | --- | --- |
| Accessi da indirizzi IP anonimi |5 minuti |15 minuti |2 ore |
| Accessi da posizioni non note |5 minuti |15 minuti |2 ore |
| Utenti con credenziali perse |2 ore |4 ore |8 ore |
| Trasferimento impossibile a posizioni atipiche |5 minuti |1 ora |8 ore  |
| Accessi da dispositivi infetti |2 ore |4 ore |8 ore  |
| Accessi da indirizzi IP con attività sospette |2 ore |4 ore |8 ore  |


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei report di Azure AD](overview-reports.md)
* [Accesso programmatico ai report di Azure AD](concept-reporting-api.md)
* [Rilevamento del rischio Azure Active Directory](concept-risk-events.md)
