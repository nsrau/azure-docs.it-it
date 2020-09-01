---
title: Cartelle di lavoro di Monitoraggio di Azure per i report | Microsoft Docs
description: Informazioni su come usare le cartelle di lavoro di Monitoraggio di Azure per i report Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 510991c1afba9a5ffbfd77e855ee25d8b641bdd0
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226982"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Come usare le cartelle di lavoro di Monitoraggio di Azure per i report Azure Active Directory

> [!IMPORTANT]
> Per ottimizzare le query sottostanti in questa cartella di lavoro, fare clic su "Modifica", quindi sull'icona Impostazioni e selezionare l'area di lavoro in cui si intende eseguire queste query. Per impostazione predefinita, le cartelle di lavoro selezionano tutte le aree di lavoro in cui si esegue il routing dei log Azure AD. 

Si vuole:

- Comprendere l'effetto dei [criteri di accesso condizionale](../conditional-access/overview.md) sull'esperienza di accesso degli utenti?

- Risolvere errori di accesso per ottenere una visuale più chiara sull'integrità di accesso dell'organizzazione e risolvere rapidamente i problemi?

- Sapere chi sta usando autenticazioni legacy per accedere all'ambiente? [Bloccando l'autenticazione legacy](../conditional-access/block-legacy-authentication.md), è possibile migliorare la protezione del tenant.

- Comprendere l'impatto dei criteri di accesso condizionale nel tenant?

- Avere la possibilità di esaminare le query dei log di accesso? La cartella di lavoro indica il numero di utenti a cui è stato concesso o negato l'accesso, nonché il numero di utenti che hanno ignorato i criteri di accesso condizionale durante l'accesso alle risorse.

- Sviluppare una conoscenza più approfondita dei dettagli della cartella di lavoro per ogni condizione, in modo che l'impatto di un criterio possa essere contestualizzato in base alla condizione, tra cui la piattaforma del dispositivo, lo stato del dispositivo, l'app client, il rischio di accesso, la posizione e l'applicazione?

- Ottenere maggiori dettagli sulle query dei log di accesso? La cartella di lavoro indica il numero di utenti a cui è stato concesso o negato l'accesso, nonché il numero di utenti che hanno ignorato i criteri di accesso condizionale durante l'accesso alle risorse.

- Per rispondere a queste domande, Azure Active Directory fornisce cartelle di lavoro per il monitoraggio. Le cartelle di lavoro di [Monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md) combinano testo, query di analisi, metriche e parametri all'interno di report interattivi avanzati.



Questo articolo:

- Presuppone che l'utente abbia familiarità con la procedura per [creare report interattivi usando le cartelle di lavoro di Monitoraggio](../../azure-monitor/platform/workbooks-overview.md).

- Spiega come usare le cartelle di lavoro di Monitoraggio per comprendere l'effetto dei criteri di accesso condizionale, per risolvere gli errori di accesso e per identificare le autenticazioni legacy.
 


## <a name="prerequisites"></a>Prerequisiti

Per usare le cartelle di lavoro di Monitoraggio, è necessario disporre degli elementi riportati di seguito:

- Un tenant di Azure Active Directory con una licenza Premium (P1 o P2). Sono disponibili informazioni su [come ottenere una licenza Premium](../fundamentals/active-directory-get-started-premium.md).

- Un'[area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md).

- [Accesso](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) all'area di lavoro Log Analytics
- I ruoli seguenti in Azure Active Directory se si accede a Log Analytics tramite il portale di Azure Active Directory
    - Amministratore della sicurezza
    - Ruolo con autorizzazioni di lettura per la sicurezza
    - Lettore di report
    - Amministratore globale

## <a name="roles"></a>Ruoli
Per gestire le cartelle di lavoro, è necessario disporre di uno dei ruoli seguenti, nonché dell'[accesso all'area di lavoro Log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) sottostante:
-   Amministratore globale
-   Amministratore della sicurezza
-   Ruolo con autorizzazioni di lettura per la sicurezza
-   Lettore di report
-   Amministratore di applicazioni

## <a name="workbook-access"></a>Accesso alle cartelle di lavoro 

Per accedere alle cartelle di lavoro:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare ad **Azure Active Directory** > **Monitoraggio** > **Cartelle di lavoro**. 

1. Selezionare un report o un modello oppure, sulla barra degli strumenti, selezionare **Apri**. 

![Individuare le cartelle di lavoro di Monitoraggio di Azure in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analisi degli accessi

Per accedere alla cartella di lavoro dell'analisi degli accessi, nella sezione **Utilizzo** selezionare **Accessi**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Operazione completata

- In attesa dell'azione dell'utente

- Operazioni non riuscite

È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di ore

- App

- Utenti

![Analisi degli accessi](./media/howto-use-azure-monitor-workbooks/43.png)


Per ogni tendenza viene visualizzato un dettaglio in base alle categorie seguenti:

- Location

    ![Accessi per località](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Accessi per dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Accessi con autenticazione legacy 


Per accedere alla cartella di lavoro per gli accessi che usano l'[autenticazione legacy](../conditional-access/block-legacy-authentication.md), nella sezione **Utilizzo** selezionare **Accessi con autenticazione legacy**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Operazione completata


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di ore

- App

- Utenti

- Protocolli

![Accessi con autenticazione legacy](./media/howto-use-azure-monitor-workbooks/47.png)


Per ogni tendenza viene visualizzato un dettaglio in base ad app e protocollo.

![Accessi con autenticazione legacy per app e protocollo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Accessi con accesso condizionale 


Per accedere alla cartella di lavoro per gli accessi in base ai [criteri di accesso condizionale](../conditional-access/overview.md), nella sezione **Accesso condizionale** selezionare **Accessi con accesso condizionale**. 

Questa cartella di lavoro mostra le tendenze per gli accessi disabilitati. È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di ore

- App

- Utenti

![Accessi con accesso condizionale](./media/howto-use-azure-monitor-workbooks/49.png)


Per gli accessi disabilitati viene visualizzato un dettaglio in base allo stato di accesso condizionale.

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Informazioni dettagliate su accesso condizionale

### <a name="overview"></a>Panoramica

Le cartelle di lavoro contengono query di log di accesso che consentono agli amministratori IT di monitorare l'impatto dei criteri di accesso condizionale nel tenant. È possibile segnalare il numero di utenti a cui è stato concesso o negato l'accesso. La cartella di lavoro contiene informazioni dettagliate sul numero di utenti che hanno ignorato i criteri di accesso condizionale in base agli attributi degli utenti al momento dell'accesso. Contiene dettagli per ogni condizione, in modo che l'impatto di un criterio possa essere contestualizzato in base alla condizione, tra cui la piattaforma del dispositivo, lo stato del dispositivo, l'app client, il rischio di accesso, la posizione e l'applicazione.

### <a name="instructions"></a>Istruzioni 
Per accedere alla cartella di lavoro **Informazioni dettagliate su accesso condizionale**, selezionarla nella sezione Accesso condizionale. Questa cartella di lavoro mostra l'impatto previsto di ogni criterio di accesso condizionale nel tenant. Selezionare uno o più criteri di accesso condizionale dall'elenco a discesa e restringere l'ambito della cartella di lavoro applicando i filtri seguenti: 

- **Intervallo di ore**

- **Utente**

- **App**

- **Vista dati**

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Il riepilogo dell'impatto mostra il numero di utenti o accessi per i quali i criteri selezionati hanno ricevuto un determinato risultato. Totale è il numero di utenti o accessi per i quali i criteri selezionati sono stati valutati nell'intervallo di ore selezionato. Fare clic su un riquadro per filtrare i dati nella cartella di lavoro in base al tipo di risultato. 

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Anche questa cartella di lavoro mostra l'impatto dei criteri selezionati suddivisi in base a ognuna delle sei condizioni: 
- **Stato del dispositivo**
- **Piattaforma del dispositivo**
- **App client**
- **Rischio di accesso**
- **Posizione**
- **Applicazioni**

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/device-platform.png)

È anche possibile analizzare i singoli accessi, filtrati in base ai parametri selezionati nella cartella di lavoro. Cercare singoli utenti ordinati in base alla frequenza di accesso e visualizzare gli eventi di accesso corrispondenti. 

![Stato accesso condizionale](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Accessi in base ai controlli di concessione

Per accedere alla cartella di lavoro per gli accessi in base ai [controlli di concessione](../conditional-access/controls.md), nella sezione **Accesso condizionale** selezionare **Accessi in base ai controlli di concessione**. 

Questa cartella di lavoro mostra le tendenze degli accessi disabilitati seguenti:

- Richiedere l'autenticazione MFA
 
- Richiedere le condizioni per l'utilizzo

- Richiedere l'Informativa sulla privacy

- Altri


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di ore

- App

- Utenti

![Accessi in base ai controlli di concessione](./media/howto-use-azure-monitor-workbooks/50.png)


Per ogni tendenza viene visualizzato un dettaglio in base ad app e protocollo.

![Dettaglio degli accessi recenti](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analisi degli errori di accesso

Usare la cartella di lavoro **Analisi degli errori di accesso** per risolvere gli errori relativi a:

- Accessi
- Criteri di accesso condizionale
- Autenticazione legacy 


Per accedere agli accessi tramite i dati di accesso condizionale, nella sezione **Risoluzione dei problemi** selezionare **Accessi con autenticazione legacy**. 

Questa cartella di lavoro mostra le tendenze di accesso seguenti:

- Tutti gli accessi

- Operazione completata

- Azione in sospeso

- Operazioni non riuscite


È possibile filtrare ogni tendenza in base alle categorie seguenti:

- Intervallo di ore

- App

- Utenti

![Risoluzione dei problemi degli accessi](./media/howto-use-azure-monitor-workbooks/52.png)


Per semplificare la risoluzione dei problemi degli accessi, Monitoraggio di Azure fornisce un dettaglio in base alle categorie seguenti:

- Errori più frequenti

    ![Riepilogo degli errori principali](./media/howto-use-azure-monitor-workbooks/53.png)

- Accessi in attesa di azione utente

    ![Riepilogo degli accessi in attesa di azione utente](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Passaggi successivi

[Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md).