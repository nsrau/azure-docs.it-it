---
title: Usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory | Microsoft Docs
description: Informazioni su come usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287226"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Procedura: Utilizzare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory

Si vuole:

- Comprendere quale impatto avrà la [criteri di accesso condizionale](../conditional-access/overview.md) su esperienza di accesso degli utenti?

- Risolvere gli errori di accesso per ottenere una migliore visualizzazione dell'integrità della accesso dell'organizzazione, nonché di risolvere rapidamente i problemi?

- Sapere chi sta usando le autenticazioni legacy per accedere all'ambiente? Dal [blocco autenticazione legacy](../conditional-access/block-legacy-authentication.md), è possibile migliorare la protezione del tenant.


[Le cartelle di lavoro di Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinare testo, le query Analitica, le metriche di Azure e i parametri in report interattivi avanzati. Azure Active Directory offre le cartelle di lavoro per il monitoraggio che consentono di trovare le risposte alle domande precedenti.

Questo articolo:

- Si presuppone che si abbia familiarità con le modalità [Crea report interattivi con cartelle di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Illustra come è possibile usare le cartelle di lavoro di monitoraggio di Azure sul monitoraggio per rispondere alle domande precedenti.
 


## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:

- Un tenant di Azure Active Directory, con una licenza premium (P1 o P2). Informazioni su come [ottenere una licenza premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Oggetto [dell'area di lavoro di Log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Cartelle di lavoro di accesso 

Per accedere alle cartelle di lavoro:

1. Accedi per i [portale di Azure](https://portal.azure.com).

2. Sulla barra di spostamento a sinistra fare clic su **Azure Active Directory**.

3. Nel **Monitoring** fare clic su **Insights**. 

    ![Informazioni dettagliate](./media/howto-use-azure-monitor-workbooks/41.png)

4. Fare clic su un report o un modello, oppure fare clic su **aperto** sulla barra degli strumenti. 

    ![Gallery](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analisi di accesso

Per accedere alla cartella di lavoro di analisi di accesso, fare clic su **accessi** nel **utilizzo** sezione. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success

- In attesa dell'azione dell'utente

- Esito negativo

È possibile filtrare ogni tendenza per:

- Intervallo di tempo

- App

- Utenti

![Gallery](./media/howto-use-azure-monitor-workbooks/43.png)


Per ogni tendenza, ottenere una suddivisione:

- Location

    ![Gallery](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Gallery](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Accessi tramite autenticazione legacy 


Per l'accesso gli accessi usando [autenticazione legacy](../conditional-access/block-legacy-authentication.md) cartella di lavoro, fare clic su **accessi usando l'autenticazione Legacy** nel **utilizzo** sezione. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success


È possibile filtrare ogni tendenza per:

- Intervallo di tempo

- App

- Utenti

- Protocolli 

![Gallery](./media/howto-use-azure-monitor-workbooks/47.png)


Per ogni tendenza, ottenere una suddivisione dall'app e il protocollo.

![Gallery](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Accessi da accesso condizionale 


Per accedere l'accessi in base a [criteri di accesso condizionale](../conditional-access/overview.md) cartella di lavoro, fare clic su **accessi dall'accesso condizionale** nel **accesso condizionale** sezione. 

Questa cartella di lavoro Mostra la tendenza per gli accessi disabilitati.

È possibile filtrare ogni tendenza per:

- Intervallo di tempo

- App

- Utenti

![Gallery](./media/howto-use-azure-monitor-workbooks/49.png)


Per i disabilitato gli accessi, per ottenere una scomposizione, lo stato di accesso condizionale.

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Accessi da controlli di concessione

Per accedere gli accessi da [concedere controlli](../conditional-access/controls.md) cartella di lavoro, fare clic su **accessi dai controlli di concessione** nel **accesso condizionale** sezione. 

Questa cartella di lavoro vengono visualizzate le seguenti disabilitate Accedi tendenze:

- Richiedere l'autenticazione MFA
 
- Richiedere le condizioni d'uso

- Richiedere l'informativa sulla privacy

- Altri


È possibile filtrare ogni tendenza per:

- Intervallo di tempo

- App

- Utenti

![Gallery](./media/howto-use-azure-monitor-workbooks/50.png)


Per ogni tendenza, ottenere una suddivisione dall'app e il protocollo.

![Gallery](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analisi degli errori di accessi

Usare la **analisi degli errori di accessi** cartella di lavoro per risolvere gli errori con:

- Accessi
- Criteri di accesso condizionale
- Autenticazione legacy. 


Per accedere gli accessi dai dati di accesso condizionale, fare clic su **accessi usando l'autenticazione Legacy** nel **risoluzione dei problemi** sezione. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success

- In attesa di un'azione

- Esito negativo


È possibile filtrare ogni tendenza per:

- Intervallo di tempo

- App

- Utenti

![Gallery](./media/howto-use-azure-monitor-workbooks/52.png)


Per risolvere i problemi di accessi, ottenere una suddivisione:

- Errori più frequenti

    ![Gallery](./media/howto-use-azure-monitor-workbooks/53.png)

- In attesa di intervento dell'utente gli accessi

    ![Gallery](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passaggi successivi

* [Crea report interattivi con cartelle di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)