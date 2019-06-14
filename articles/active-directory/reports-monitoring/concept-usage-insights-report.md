---
title: Informazioni sull'utilizzo e insights report nel portale di Azure Active Directory | Microsoft Docs
description: Introduzione ai report di utilizzo e insights nel portale di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806357"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Report di utilizzo e insights nel portale di Azure Active Directory

Con il report sull'utilizzo e informazioni dettagliate, è possibile ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso. È possibile trovare risposte alle domande seguenti:

*   Che cosa sono la parte superiore delle applicazioni usate all'interno dell'organizzazione?
*   Quali applicazioni hanno gli accessi non riusciti più? 
*   Quali sono gli errori principali Accedi per ogni applicazione?

## <a name="prerequisites"></a>Prerequisiti 

Per accedere ai dati dal report di utilizzo e informazioni dettagliate, è necessario:

* Un tenant di Azure AD
* Una licenza Azure AD premium (P1 o P2) per visualizzare i dati di accesso
* Un utente amministratore globale, amministratore della sicurezza, lettura per la sicurezza o ai ruoli di lettore di report. Inoltre, tutti gli utenti (utenti non amministratori) possono accedere i propri accessi. 

## <a name="access-the-usage-and-insights-report"></a>Accedere al report sull'utilizzo e informazioni dettagliate

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory corretta, quindi selezionare **Azure Active Directory** e scegliere **applicazioni aziendali**.
3. Dal **impegno** sezione, selezionare **utilizzo & insights** per aprire il report. 

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Usare il report

Il report sull'utilizzo e informazioni dettagliate Mostra l'elenco delle applicazioni con uno o più di accesso nel tentativo e consente di ordinare il numero di accessi riusciti, accessi non riusciti e la percentuale di successo.

Facendo clic su Carica più nella parte inferiore dell'elenco consente di visualizzare le applicazioni aggiuntive nella pagina. È possibile selezionare l'intervallo di date per visualizzare tutte le applicazioni che sono state utilizzate all'interno dell'intervallo.

È anche possibile impostare lo stato attivo in un'applicazione specifica. Selezionare **visualizzare attività di accesso** per verificare l'accesso nell'attività nel tempo per l'applicazione, nonché gli errori principali.  

Quando si seleziona un giorno nel grafico di utilizzo dell'applicazione, si ottiene un elenco dettagliato delle attività di accesso per l'applicazione.  

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report degli accessi](concept-sign-ins.md)