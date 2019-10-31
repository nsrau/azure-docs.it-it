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
ms.date: 10/30/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: b88648ce68a7359a842ec975d8633d6200b1a507
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200120"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Come usare le cartelle di lavoro di monitoraggio di Azure per i report Azure Active Directory

> [!IMPORTANT]
> Per ottimizzare le query sottostanti in questa cartella di lavoro, fare clic su "modifica", fare clic sull'icona impostazioni e selezionare l'area di lavoro in cui si desidera eseguire tali query. Per impostazione predefinita, le cartelle di lavoro selezionano tutte le aree di lavoro in cui si esegue il routing dei log Azure AD. 

Si desidera:

- Per comprendere l'effetto dei [criteri di accesso condizionale](../conditional-access/overview.md) sull'esperienza di accesso degli utenti,

- Risolvere gli errori di accesso per ottenere una visualizzazione migliore dell'integrità di accesso dell'organizzazione e per risolvere rapidamente i problemi?

- Sai chi sta usando le autenticazioni legacy per accedere al tuo ambiente? [Bloccando l'autenticazione legacy](../conditional-access/block-legacy-authentication.md), è possibile migliorare la protezione del tenant.

- È necessario comprendere l'effetto dei criteri di accesso condizionale nel tenant?

- La possibilità di esaminare le query del log di accesso, la cartella di lavoro indica il numero di utenti a cui è stato concesso o negato l'accesso, nonché il numero di utenti che hanno ignorato i criteri di accesso condizionale durante l'accesso alle risorse?

- Si è interessati a sviluppare una conoscenza più approfondita di: i dettagli della cartella di lavoro per ogni condizione in modo che l'effetto di un criterio possa essere contestuale per condizione, tra cui la piattaforma del dispositivo, lo stato del dispositivo, l'app client, il rischio di accesso, la posizione e l'applicazione?

- Ottenere informazioni più approfondite sulle query del log di accesso, la cartella di lavoro indica il numero di utenti a cui è stato concesso o negato l'accesso, nonché il numero di utenti che hanno ignorato i criteri di accesso condizionale durante l'accesso alle risorse.

- Per rispondere a queste domande, Active Directory fornisce cartelle di lavoro per il monitoraggio. Le [cartelle di lavoro di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinano testo, query di analisi, metriche e parametri in report interattivi avanzati.



Questo articolo:

- Presuppone che si abbia familiarità con le modalità di [creazione di report interattivi tramite le cartelle di lavoro di monitoraggio](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Viene illustrato come utilizzare le cartelle di lavoro di monitoraggio per comprendere l'effetto dei criteri di accesso condizionale, per risolvere gli errori di accesso e per identificare le autenticazioni legacy.
 


## <a name="prerequisites"></a>Prerequisiti

Per utilizzare le cartelle di lavoro di monitoraggio, è necessario:

- Un tenant Active Directory con una licenza Premium (P1 o P2). Informazioni su come [ottenere una licenza Premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Accesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) all'area di lavoro di log Analytics
- Ruoli seguenti in Azure Active Directory (se si accede a Log Analytics tramite il portale di Azure Active Directory)
    - Amministratore della sicurezza
    - Ruolo con autorizzazioni di lettura per la sicurezza
    - Lettore di report
    - Amministratore globale

## <a name="roles"></a>Ruoli
Per gestire le cartelle di lavoro di, è necessario disporre di uno dei ruoli seguenti, nonché accedere all'area di lavoro [log Analytics sottostante](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) :
-   Amministratore globale
-   Amministratore della sicurezza
-   Ruolo con autorizzazioni di lettura per la sicurezza
-   Lettore di report
-   Amministratore di applicazioni

## <a name="workbook-access"></a>Accesso alla cartella di lavoro 

Per accedere alle cartelle di lavoro:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare a **Azure Active Directory** >  > **cartelle di lavoro**di **monitoraggio** . 

1. Selezionare un report o un modello oppure fare clic su **Apri**nella barra degli strumenti. 

![Trovare le cartelle di lavoro di monitoraggio di Azure in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

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


## <a name="conditional-access-insights"></a>Informazioni dettagliate su accesso condizionale

### <a name="overview"></a>Panoramica

Le cartelle di lavoro contengono query di log di accesso che consentono agli amministratori IT di monitorare l'effetto dei criteri di accesso condizionale nel tenant. Si ha la possibilità di segnalare il numero di utenti a cui è stato concesso o negato l'accesso. La cartella di lavoro contiene informazioni dettagliate sul numero di utenti che hanno ignorato i criteri di accesso condizionale in base agli attributi degli utenti al momento dell'accesso. Contiene i dettagli per ogni condizione, in modo che l'effetto di un criterio possa essere contestuale per condizione, tra cui la piattaforma del dispositivo, lo stato del dispositivo, l'app client, il rischio di accesso, la posizione e l'applicazione.

### <a name="instructions"></a>Istruzioni 
Per accedere alla cartella di lavoro per informazioni dettagliate sull'accesso condizionale, selezionare la cartella di lavoro delle **informazioni dettagliate** sull'accesso condizionale nella sezione accesso condizionale. Questa cartella di lavoro Mostra l'effetto previsto di ogni criterio di accesso condizionale nel tenant. Selezionare uno o più criteri di accesso condizionale dall'elenco a discesa e limitare l'ambito della cartella di lavoro applicando i filtri seguenti: 

- **Intervallo di tempo**

- **Utente**

- **App**

- **Visualizzazione dati**

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Il riepilogo dell'effetto Mostra il numero di utenti o accessi per i quali i criteri selezionati hanno un determinato risultato. Total è il numero di utenti o accessi per i quali i criteri selezionati sono stati valutati nell'intervallo di tempo selezionato. Fare clic su un riquadro per filtrare i dati nella cartella di lavoro in base al tipo di risultato. 

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Questa cartella di lavoro mostra anche l'effetto dei criteri selezionati suddivisi in base a ognuna delle sei condizioni: 
- **Stato del dispositivo**
- **Piattaforma del dispositivo**
- **App client**
- **Rischio di accesso**
- **Località**
- **Applicazioni**

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/device-platform.png)

È anche possibile esaminare gli accessi singoli, filtrati in base ai parametri selezionati nella cartella di lavoro. Cercare singoli utenti, ordinati in base alla frequenza di accesso e visualizzare gli eventi di accesso corrispondenti. 

![Stato di accesso condizionale](./media/howto-use-azure-monitor-workbooks/filtered.png)





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

Usare la cartella di lavoro analisi degli errori di **accesso** per risolvere gli errori con:

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
