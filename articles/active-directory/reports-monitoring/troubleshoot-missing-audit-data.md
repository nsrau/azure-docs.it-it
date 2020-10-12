---
title: Risolvere i problemi relativi ai dati mancanti nei log attività | Microsoft Docs
description: Offre una risoluzione per i dati mancanti nei log attività di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e1ac0033b7ed2de90ece481cd02d64970ff5f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85608110"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Risolvere i problemi relativi ai dati mancanti nei log attività di Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Non è possibile trovare i log di controllo per le azioni recenti nel portale di Azure

### <a name="symptoms"></a>Sintomi

Sono state eseguite alcune azioni nel portale di Azure e si prevedeva la visualizzazione dei log di controllo per tali azioni nel pannello `Activity logs > Audit Logs`, ma non è possibile trovarli.

 ![Reporting](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Causa

Le azioni non vengono visualizzate immediatamente nei log attività. La tabella seguente elenca i numeri di latenza per i log attività. 

| Report | Latenza (P95) | Latenza (P99) |
|--------|---------------|---------------|
| Directory Audit (Controllo directory) | 2 min | 5 min |
| Attività di accesso | 2 min | 5 min |

### <a name="resolution"></a>Soluzione

Attendere tra 15 minuti e due ore e verificare se le azioni vengono visualizzate nel log. Se i log non vengono visualizzati nemmeno dopo due ore, [inviare un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) che verrà preso in esame.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Impossibile trovare gli accessi utente recenti nel log attività degli accessi di Azure Active Directory

### <a name="symptoms"></a>Sintomi

Di recente si è eseguito l'accesso al portale di Azure e si prevedeva la visualizzazione dei log di accesso per tali azioni nel pannello `Activity logs > Sign-ins`, ma non è possibile trovarli.

 ![Reporting](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Causa

Le azioni non vengono visualizzate immediatamente nei log attività. La tabella seguente elenca i numeri di latenza per i log attività. 

| Report | Latenza (P95) | Latenza (P99) |
|--------|---------------|---------------|
| Directory Audit (Controllo directory) | 2 min | 5 min |
| Attività di accesso 2 minuti | 5 min |

### <a name="resolution"></a>Soluzione

Attendere tra 15 minuti e due ore e verificare se le azioni vengono visualizzate nel log. Se i log non vengono visualizzati nemmeno dopo due ore, [inviare un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) che verrà preso in esame.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Non è possibile visualizzare più di 30 giorni di dati di report nel portale di Azure

### <a name="symptoms"></a>Sintomi

Non è possibile visualizzare più di 30 giorni di dati di accesso e controllo nel portale di Azure. Perché? 

 ![Reporting](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Causa

In base alla licenza, Azioni di Azure Active Directory archivia i report delle attività per le durate seguenti:

| Report           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| Directory Audit (Controllo directory)  |  7 giorni       | 30 giorni             | 30 giorni             |
| Attività di accesso | Non disponibile. È possibile accedere alle informazioni di accesso per 7 giorni dal pannello del singolo profilo utente | 30 giorni | 30 giorni             |

Per altre informazioni, vedere [Criteri di conservazione dei report di Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Soluzione

Sono disponibili due opzioni per conservare i dati per più di 30 giorni. È possibile usare le [API creazione report di Azure AD](concept-reporting-api.md) per recuperare i dati a livello di codice e archiviarli in un database. In alternativa, è possibile integrare i log di controllo in un sistema SIEM di terze parti, ad esempio Splunk o SumoLogic.

## <a name="next-steps"></a>Passaggi successivi

* [Conservazione dei report di Azure AD](reference-reports-data-retention.md).
* [Latenza Azure Active Directory Reporting](reference-reports-latencies.md).
* [Domande frequenti sulla creazione di report Azure Active Directory](reports-faq.md).

