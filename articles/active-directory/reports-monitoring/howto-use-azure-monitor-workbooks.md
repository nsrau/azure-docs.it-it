---
title: Cartelle di lavoro di monitoraggio di Azure per i report | Microsoft Docs
description: Informazioni su come usare le cartelle di lavoro di monitoraggio di Azure per i report Azure Active Directory.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 5e498dcb39f62fc870bc7efa989b91caeac0dedc
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819710"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Come usare le cartelle di lavoro di monitoraggio di Azure per i report Azure Active Directory

Si desidera:

- Per comprendere l'effetto dei [criteri di accesso condizionale](../conditional-access/overview.md) sull'esperienza di accesso degli utenti,

- Risolvere gli errori di accesso per ottenere una visualizzazione migliore dell'integrità di accesso dell'organizzazione e per risolvere rapidamente i problemi?

- Sai chi sta usando le autenticazioni legacy per accedere al tuo ambiente? [Bloccando l'autenticazione legacy](../conditional-access/block-legacy-authentication.md), è possibile migliorare la protezione del tenant.

Per rispondere a queste domande, Active Directory fornisce cartelle di lavoro per il monitoraggio. Le [cartelle di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinano testo, query di analisi, metriche e parametri in report interattivi avanzati. 

Questo articolo:

- Presuppone che si abbia familiarità con le modalità di [creazione di report interattivi tramite le cartelle di lavoro di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Viene illustrato come utilizzare le cartelle di lavoro di monitoraggio per comprendere l'effetto dei criteri di accesso condizionale, per risolvere gli errori di accesso e per identificare le autenticazioni legacy.
 


## <a name="prerequisites"></a>Prerequisiti

Per utilizzare le cartelle di lavoro di monitoraggio, è necessario:

- Un tenant Active Directory con una licenza Premium (P1 o P2). Informazioni su come [ottenere una licenza Premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Accesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) all'area di lavoro di log Analytics
- Ruoli seguenti in Azure Active Directory (se si accede a Log Analytics tramite il portale di Azure Active Directory)
    - Amministrazione della protezione
    - Ruolo con autorizzazioni di lettura per la sicurezza
    - Lettore di report
    - Amministratore globale

## <a name="roles"></a>Ruoli
Per gestire le cartelle di lavoro di, è necessario disporre di uno dei ruoli seguenti, nonché accedere all'area di lavoro [log Analytics sottostante](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) :
-   Amministratore globale
-   Amministrazione della protezione
-   Ruolo con autorizzazioni di lettura per la sicurezza
-   Lettore di report
-   Amministratore applicazione


## <a name="workbook-access"></a>Accesso alla cartella di lavoro 

Per accedere alle cartelle di lavoro:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.

3. Nella sezione **monitoraggio** selezionare cartelle di **lavoro**. 

    ![Selezionare informazioni dettagliate](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selezionare un report o un modello oppure fare clic su **Apri**nella barra degli strumenti. 

    ![Selezionare Apri](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analisi di accesso

Per accedere alla cartella di lavoro di analisi di accesso, nella sezione **Usage (utilizzo** ) selezionare **accessi**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success

- Azione utente in sospeso

- Esito negativo

È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Analisi di accesso](./media/howto-use-azure-monitor-workbooks/43.png)


Per ogni tendenza si ottiene una suddivisione in base alle categorie seguenti:

- Località

    ![Accessi per località](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Accessi per dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Accessi con autenticazione legacy 


Per accedere alla cartella di lavoro per gli accessi che usano [l'autenticazione legacy](../conditional-access/block-legacy-authentication.md), nella sezione **utilizzo** Selezionare **accessi usando l'autenticazione legacy**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di tempo

- App

- Utenti

- Protocolli

![Accessi tramite autenticazione legacy](./media/howto-use-azure-monitor-workbooks/47.png)


Per ogni tendenza si ottiene una suddivisione in base all'app e al protocollo.

![Accessi con autenticazione legacy per app e protocollo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Accessi per accesso condizionale 


Per accedere alla cartella di lavoro per gli accessi in base ai [criteri di accesso](../conditional-access/overview.md)condizionale, nella sezione **accesso condizionale** Selezionare accessi per **accesso condizionale**. 

Questa cartella di lavoro Mostra le tendenze per gli accessi disabilitati. È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Accessi con accesso condizionale](./media/howto-use-azure-monitor-workbooks/49.png)


Per gli accessi disabilitati, si ottiene una suddivisione in base allo stato di accesso condizionale.

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Accessi tramite i controlli Grant

Per accedere alla cartella di lavoro per gli accessi tramite i [controlli di concessione](../conditional-access/controls.md), nella sezione **accesso condizionale selezionare accessi** **tramite Concedi controlli**. 

Questa cartella di lavoro Mostra le tendenze di accesso disabilitate seguenti:

- Richiedere l'autenticazione MFA
 
- Richiedere le condizioni per l'utilizzo

- Richiedi informativa sulla privacy

- Altro


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Accessi tramite i controlli Grant](./media/howto-use-azure-monitor-workbooks/50.png)


Per ogni tendenza si ottiene una suddivisione in base all'app e al protocollo.

![Suddivisione degli accessi recenti](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analisi degli errori di accesso

Utilizzare la cartella di lavoro analisi degli errori di **accesso** per risolvere gli errori con i seguenti elementi:

- Accessi
- Criteri di accesso condizionale
- Autenticazione legacy 


Per accedere agli accessi tramite i dati di accesso condizionale, nella sezione **risoluzione dei problemi** selezionare accessi **usando l'autenticazione legacy**. 

Questa cartella di lavoro Mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Success

- Azione in sospeso

- Esito negativo


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di tempo

- App

- Utenti

![Risoluzione dei problemi relativi agli accessi](./media/howto-use-azure-monitor-workbooks/52.png)


Per semplificare la risoluzione dei problemi di accesso, monitoraggio di Azure fornisce una suddivisione in base alle categorie seguenti:

- Errori principali

    ![Riepilogo degli errori principali](./media/howto-use-azure-monitor-workbooks/53.png)

- Accessi in attesa sull'azione dell'utente

    ![Riepilogo degli accessi in attesa dell'azione dell'utente](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passaggi successivi

[Creare report interattivi usando le cartelle di lavoro di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
