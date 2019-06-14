---
title: Usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory | Microsoft Docs
description: Informazioni su come usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory.
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
ms.openlocfilehash: 6d461725d0d4c86ee7aa5b450d42c01ca77ffb16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107669"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Come usare le cartelle di lavoro di monitoraggio di Azure per i report di Azure Active Directory

Si vuole:

- Comprendere l'effetto delle [criteri di accesso condizionale](../conditional-access/overview.md) sull'esperienza di accesso degli utenti?

- Risolvere gli errori di accesso per ottenere una migliore visualizzazione dell'integrità di accesso dell'organizzazione e per risolvere rapidamente i problemi?

- Sapere chi sta usando le autenticazioni legacy per accedere all'ambiente? (Da [blocco autenticazione legacy](../conditional-access/block-legacy-authentication.md), è possibile migliorare la protezione del tenant in uso.)

Per aiutarti a risolvere questi dubbi, Active Directory fornisce le cartelle di lavoro per il monitoraggio. [Le cartelle di lavoro di Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinare testo, le query di analitica, metriche e i parametri in report interattivi avanzati. 

Questo articolo:

- Si presuppone che si ha familiarità con le modalità [Crea report interattivi con cartelle di lavoro di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Viene illustrato come usare le cartelle di lavoro di monitoraggio per capire l'effetto dei criteri di accesso condizionale, per risolvere gli errori di accesso e per identificare le autenticazioni legacy.
 


## <a name="prerequisites"></a>Prerequisiti

Per usare cartelle di lavoro di monitoraggio, è necessario:

- Un tenant di Active Directory con una licenza premium (P1 o P2). Informazioni su come [ottenere una licenza premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Oggetto [dell'area di lavoro di Log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Accesso della cartella di lavoro 

Per accedere alle cartelle di lavoro:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

3. Nel **Monitoring** sezione, selezionare **Insights**. 

    ![Selezionare Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selezionare un report o un modello o sulla barra degli strumenti selezionare **aperto**. 

    ![Selezionare Open](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analisi di accesso

Per accedere a cartella di lavoro di analisi di accesso nel **utilizzo** sezione, selezionare **accessi**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Riuscito

- Azione dell'utente in sospeso

- Esito negativo

È possibile filtrare ogni tendenza per le categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Analisi di accesso](./media/howto-use-azure-monitor-workbooks/43.png)


Per ogni tendenza, ottenere una suddivisione per le categorie seguenti:

- Località

    ![Accessi da località](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Accessi da dispositivi](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Accessi tramite autenticazione legacy 


Per accedere la cartella di lavoro per gli accessi che usano [autenticazione legacy](../conditional-access/block-legacy-authentication.md), nella **utilizzo** sezione, selezionare **accessi usando l'autenticazione Legacy**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Riuscito


È possibile filtrare ogni tendenza per le categorie seguenti:

- Intervallo di tempo

- App

- Utenti

- Protocolli

![Accessi da autenticazione legacy](./media/howto-use-azure-monitor-workbooks/47.png)


Per ogni tendenza, ottenere una suddivisione dall'app e il protocollo.

![Accessi l'autenticazione legacy dall'app e il protocollo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Accessi da accesso condizionale 


Per accedere la cartella di lavoro per gli accessi da [criteri di accesso condizionale](../conditional-access/overview.md), nella **accesso condizionale** sezione, selezionare **accessi dall'accesso condizionale**. 

Questa cartella di lavoro vengono visualizzate le tendenze per gli accessi disabilitati. È possibile filtrare ogni tendenza per le categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Accessi con accesso condizionale](./media/howto-use-azure-monitor-workbooks/49.png)


Per gli accessi disabilitati, per ottenere una scomposizione, lo stato di accesso condizionale.

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Accessi da controlli di concessione

Per accedere la cartella di lavoro per gli accessi da [concedere i controlli](../conditional-access/controls.md), nella **accesso condizionale** sezione, selezionare **accessi dai controlli di concessione**. 

Questa cartella di lavoro vengono visualizzate le seguenti disabilitate Accedi tendenze:

- Richiedere l'autenticazione MFA
 
- Richiedere le condizioni per l'utilizzo

- Richiedere l'informativa sulla privacy

- Altri


È possibile filtrare ogni tendenza per le categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Accessi da controlli di concessione](./media/howto-use-azure-monitor-workbooks/50.png)


Per ogni tendenza, ottenere una suddivisione dall'app e il protocollo.

![Suddivisione di accessi recenti](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analisi degli errori di accessi

Usare la **analisi degli errori di accessi** cartella di lavoro per risolvere gli errori con il codice seguente:

- Accessi
- Criteri di accesso condizionale
- Autenticazione legacy 


Per accedere agli accessi da dati di accesso condizionale, nella **risoluzione dei problemi** sezione, selezionare **accessi usando l'autenticazione Legacy**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Riuscito

- Azione in sospeso

- Esito negativo


È possibile filtrare ogni tendenza per le categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Risoluzione dei problemi di accessi](./media/howto-use-azure-monitor-workbooks/52.png)


Per aiutarti a risolvere i problemi di accesso, monitoraggio di Azure ti offre una suddivisione per le categorie seguenti:

- Errori più frequenti

    ![Riepilogo di errori più frequenti](./media/howto-use-azure-monitor-workbooks/53.png)

- In attesa di intervento dell'utente gli accessi

    ![Riepilogo di accessi in attesa di intervento dell'utente](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passaggi successivi

[Creare report interattivi con cartelle di lavoro di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).