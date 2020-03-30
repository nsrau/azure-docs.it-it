---
title: Cartelle di lavoro di Monitoraggio di Azure per i report Documenti Microsoft
description: Informazioni su come usare le cartelle di lavoro di Monitoraggio di Azure per i report di Azure Active Directory.Learn how to use Azure Monitor workbooks for Azure Active Directory reports.
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
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014373"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Come usare le cartelle di lavoro di Monitoraggio di Azure per i report di Azure Active DirectoryHow to use Azure Monitor workbooks for Azure Active Directory reports

> [!IMPORTANT]
> Per ottimizzare le query sottostanti in questa cartella di lavoro, fare clic su "Modifica", fare clic sull'icona Impostazioni e selezionare l'area di lavoro in cui si desidera eseguire queste query. Le cartelle di lavoro per impostazione predefinita selezionano tutte le aree di lavoro in cui si instradano i log di Azure AD. 

Vuoi:

- Comprendere l'effetto dei criteri di [accesso condizionale](../conditional-access/overview.md) sull'esperienza di accesso degli utenti?

- Risolvere gli errori di accesso per ottenere una visualizzazione migliore dell'integrità dell'accesso dell'organizzazione e risolvere rapidamente i problemi?

- Sai chi usa le autenticazioni legacy per accedere al tuo ambiente? Bloccando [l'autenticazione legacy,](../conditional-access/block-legacy-authentication.md)è possibile migliorare la protezione del tenant.

- È necessario comprendere l'impatto dei criteri di accesso condizionale nel tenant?

- Si desidera la possibilità di esaminare: query di log di accesso, la cartella di lavoro segnala quanti utenti hanno concesso o negato l'accesso, nonché quanti utenti hanno ignorato i criteri di accesso condizionale quando accedono alle risorse?

- Interessato a sviluppare una comprensione più approfondita di: i dettagli della cartella di lavoro per condizione in modo che l'impatto di un criterio possa essere contestualizzato per condizione, tra cui piattaforma del dispositivo, stato del dispositivo, app client, rischio di accesso, posizione e applicazione?

- Ottenere informazioni più approfondite nelle query di log di accesso, la cartella di lavoro segnala il numero di utenti a cui è stato concesso o negato l'accesso, nonché il numero di utenti che hanno ignorato i criteri di accesso condizionale quando accedono alle risorse.

- Per facilitare l'aggiornamento di queste domande, Active Directory fornisce cartelle di lavoro per il monitoraggio. [Le cartelle di lavoro](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) di Monitoraggio di Azure combinano query di testo, analisi, metriche e parametri in report interattivi avanzati.



Questo articolo:

- Si presuppone che si abbia familiarità con la creazione di [report interattivi utilizzando Le cartelle di lavoro Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Viene illustrato come utilizzare le cartelle di lavoro di monitoraggio per comprendere l'effetto dei criteri di accesso condizionale, per risolvere gli errori di accesso e per identificare le autenticazioni legacy.
 


## <a name="prerequisites"></a>Prerequisiti

Per utilizzare le cartelle di lavoro Monitor, è necessario:

- Un tenant di Active Directory con una licenza Premium (P1 o P2). Scopri come [ottenere una licenza premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Un'area di lavoro di Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Accesso](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) all'area di lavoro di log analytics
- Ruoli seguenti in Azure Active Directory (se si accede a Log Analytics tramite il portale di Azure Active Directory)Following roles in Azure Active Directory (if you are accessing Log Analytics through Azure Active Directory portal)
    - Amministratore della sicurezza
    - Ruolo con autorizzazioni di lettura per la sicurezza
    - Lettore di report
    - Amministratore globale

## <a name="roles"></a>Ruoli
Per gestire le cartelle di lavoro, è necessario essere in uno dei ruoli seguenti e avere accesso all'area di lavoro [di Log Analytics sottostante:](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)
-   Amministratore globale
-   Amministratore della sicurezza
-   Ruolo con autorizzazioni di lettura per la sicurezza
-   Lettore di report
-   Amministratore di applicazioni

## <a name="workbook-access"></a>Accesso alla cartella di lavoro 

Per accedere alle cartelle di lavoro:

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Passare a**Cartelle**di lavoro di**monitoraggio** > di **Azure Active Directory** > . 

1. Selezionare un report o un modello oppure sulla barra degli strumenti selezionare **Apri**. 

![Trovare le cartelle di lavoro di Monitoraggio di Azure in Azure ADFind the Azure Monitor workbooks in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analisi dell'accesso

Per accedere alla cartella di lavoro per l'analisi di accesso, nella sezione **Utilizzo** selezionare **Accessi**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:This workbook shows the following sign-in trends:

- Tutti gli invii

- Operazione completata

- Azione utente in sospeso

- Operazioni non riuscite

È possibile filtrare ogni tendenza in base alle seguenti categorie:

- Intervallo di ore

- App

- Utenti

![Analisi dell'accesso](./media/howto-use-azure-monitor-workbooks/43.png)


Per ogni tendenza, si ottiene una suddivisione per le seguenti categorie:

- Location

    ![Accedi in base alla posizione](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Accedi dal dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Accesso con autenticazione legacy 


Per accedere alla cartella di lavoro per gli accessi che utilizzano [l'autenticazione legacy](../conditional-access/block-legacy-authentication.md), nella sezione **Utilizzo** selezionare **Accessi utilizzando l'autenticazione legacy**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:This workbook shows the following sign-in trends:

- Tutti gli invii

- Operazione completata


È possibile filtrare ogni tendenza in base alle seguenti categorie:

- Intervallo di ore

- App

- Utenti

- Protocolli

![Accesso tramite autenticazione legacy](./media/howto-use-azure-monitor-workbooks/47.png)


Per ogni tendenza, si ottiene una ripartizione per app e protocollo.

![Gli indirizzi con autenticazione legacy per app e protocollo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Accessi con accesso condizionale 


Per accedere alla cartella di lavoro per gli accessi in base ai [criteri di accesso condizionale](../conditional-access/overview.md), nella sezione Accesso **condizionale** selezionare **Accessi con accesso condizionale**. 

Questa cartella di lavoro mostra le tendenze per gli edicazione disabilitati. È possibile filtrare ogni tendenza in base alle seguenti categorie:

- Intervallo di ore

- App

- Utenti

![Accessi con accesso condizionale](./media/howto-use-azure-monitor-workbooks/49.png)


Per gli accessi disabilitati, si ottiene una suddivisione in base allo stato Accesso condizionale.

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Informazioni dettagliate su accesso condizionale

### <a name="overview"></a>Panoramica

Le cartelle di lavoro contengono query di log di accesso che consentono agli amministratori IT di monitorare l'impatto dei criteri di accesso condizionale nel tenant. È possibile creare report sul numero di utenti a cui sarebbe stato concesso o negato l'accesso. La cartella di lavoro contiene informazioni dettagliate sul numero di utenti che avrebbero ignorato i criteri di accesso condizionale in base agli attributi di tali utenti al momento dell'accesso. Contiene dettagli per condizione in modo che l'impatto di un criterio possa essere contestualizzato per condizione, tra cui piattaforma del dispositivo, stato del dispositivo, app client, rischio di accesso, posizione e applicazione.

### <a name="instructions"></a>Istruzioni 
Per accedere alla cartella di lavoro per Informazioni dettagliate sull'accesso condizionale, selezionare la cartella di lavoro **Informazioni di accesso condizionale** nella sezione Accesso condizionale. Questa cartella di lavoro mostra l'impatto previsto di ogni criterio di accesso condizionale nel tenant. Selezionare uno o più criteri di accesso condizionale dall'elenco a discesa e restringere l'ambito della cartella di lavoro applicando i filtri seguenti: 

- **Intervallo di tempo**

- **Utente**

- **App**

- **Visualizzazione dati**

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Il riepilogo degli effetti mostra il numero di utenti o di accesso per i quali i criteri selezionati hanno avuto un risultato specifico. Totale è il numero di utenti o di accesso per cui sono stati valutati i criteri selezionati nell'intervallo di tempo selezionato. Fare clic su un riquadro per filtrare i dati nella cartella di lavoro in base al tipo di risultato. 

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Questa cartella di lavoro mostra anche l'impatto dei criteri selezionati suddivisi per ognuna delle sei condizioni: 
- **Stato del dispositivo**
- **Piattaforma del dispositivo**
- **App client**
- **Rischio di accesso**
- **Percorso**
- **Applicazioni**

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/device-platform.png)

È inoltre possibile analizzare i singoli invii, filtrati in base ai parametri selezionati nella cartella di lavoro. Cercare singoli utenti, ordinati in base alla frequenza di accesso, e visualizzare gli eventi di accesso corrispondenti. 

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Accesso tramite controlli di concessione

Per accedere alla cartella di lavoro per gli accessi con i controlli di [concessione](../conditional-access/controls.md), nella sezione **Accesso condizionale** selezionare **Accessi con concedi controlli**. 

Questa cartella di lavoro mostra le seguenti tendenze di accesso disabilitate:This workbook shows the following disabled sign-in trends:

- Richiedere l'autenticazione MFA
 
- Richiedere le condizioni per l'utilizzo

- Richiedi informativa sulla privacy

- Altri


È possibile filtrare ogni tendenza in base alle seguenti categorie:

- Intervallo di ore

- App

- Utenti

![Accesso tramite controlli di concessione](./media/howto-use-azure-monitor-workbooks/50.png)


Per ogni tendenza, si ottiene una ripartizione per app e protocollo.

![Suddivisione degli invii recenti](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analisi degli errori di accesso

Usare la cartella di lavoro Di analisi degli errori di accesso per risolvere gli errori con:Use the **Sign-ins failure analysis** workbook to troubleshoot errors with:

- Accessi
- Criteri di accesso condizionale
- Autenticazione legacy 


Per accedere agli accessi tramite dati di accesso condizionale, nella sezione **Risoluzione dei problemi** selezionare **Accessi utilizzando l'autenticazione legacy**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:This workbook shows the following sign-in trends:

- Tutti gli invii

- Operazione completata

- Azione in sospeso

- Operazioni non riuscite


È possibile filtrare ogni tendenza in base alle seguenti categorie:

- Intervallo di ore

- App

- Utenti

![Risoluzione dei problemi di accesso](./media/howto-use-azure-monitor-workbooks/52.png)


Per facilitare la risoluzione dei problemi relativi agli accessi, Monitoraggio di Azure offre una suddivisione in base alle categorie seguenti:To help you troubleshoot sign-ins, Azure Monitor gives you a breakdown by the following categories:

- Errori principali

    ![Riepilogo degli errori principali](./media/howto-use-azure-monitor-workbooks/53.png)

- Accesso in attesa di azioni utente

    ![Riepilogo degli inattesa di accesso all'azione dell'utente](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passaggi successivi

[Creare report interattivi utilizzando Controlla cartelle di lavoro](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
