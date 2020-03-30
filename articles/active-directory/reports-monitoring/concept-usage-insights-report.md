---
title: Rapporto sull'utilizzo e sulle informazioni dettagliate Documenti Microsoft
description: Introduzione al report di utilizzo e informazioni dettagliate nel portale di Azure Active DirectoryIntroduction to usage and insights report in the Azure Active Directory portal
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
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008258"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Report sull'utilizzo e le informazioni dettagliate nel portale di Azure Active Directory

Con il report di utilizzo e informazioni dettagliate, è possibile ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso. Puoi trovare le risposte alle seguenti domande:

*   Quali sono le principali applicazioni utilizzate nell'organizzazione?
*   Quali applicazioni hanno gli accessi più falliti? 
*   Quali sono i principali errori di accesso per ogni applicazione?

## <a name="prerequisites"></a>Prerequisiti 

Per accedere ai dati dal report di utilizzo e informazioni dettagliate, è necessario:To access the data from the usage and insights report, you need:

* Un tenant di Azure AD
* Una licenza premium di Azure AD (P1/P2) per visualizzare i dati di accesso
* Utente nei ruoli di amministratore globale, amministratore della sicurezza, lettore di sicurezza o lettore di report. Inoltre, qualsiasi utente (non amministratori) può accedere ai propri accessi. 

## <a name="access-the-usage-and-insights-report"></a>Accedere al report sull'utilizzo e le informazioni dettagliate

1. Passare al [portale di Azure](https://portal.azure.com).
2. Selezionare la directory corretta, quindi selezionare **Azure Active Directory** e scegliere Applicazioni **aziendali**.
3. Nella sezione **Attività** selezionare **Utilizzo & informazioni dettagliate** per aprire il report. 

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Utilizzare il report

Il report sull'utilizzo e le informazioni dettagliate mostra l'elenco delle applicazioni con uno o più tentativi di accesso e consente di ordinare in base al numero di accessi riusciti, agli accessi non riusciti e alla percentuale di operazioni riuscite.

Fare clic su Carica altro nella parte inferiore dell'elenco consente di visualizzare applicazioni aggiuntive nella pagina. È possibile selezionare l'intervallo di date per visualizzare tutte le applicazioni utilizzate all'interno dell'intervallo.

È inoltre possibile impostare lo stato attivo su un'applicazione specifica. Selezionare **Visualizza attività di accesso** per visualizzare l'attività di accesso nel tempo per l'applicazione e gli errori principali.  

Quando si seleziona un giorno nel grafico di utilizzo dell'applicazione, viene visualizzato un elenco dettagliato delle attività di accesso per l'applicazione.  

![Report su utilizzo e informazioni dettagliate](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Passaggi successivi

* [Report sugli accessi](concept-sign-ins.md)