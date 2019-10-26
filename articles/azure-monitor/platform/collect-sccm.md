---
title: Connettersi Configuration Manager a monitoraggio di Azure | Microsoft Docs
description: Questo articolo illustra i passaggi per connettere Configuration Manager all'area di lavoro in monitoraggio di Azure e iniziare ad analizzare i dati.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 08/28/2019
ms.openlocfilehash: fee6f09ba8e290ae6599f07d4ed831fb89427f76
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932647"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Connetti Configuration Manager a monitoraggio di Azure
È possibile connettere l'ambiente System Center Configuration Manager a monitoraggio di Azure per sincronizzare i dati della raccolta dispositivi e fare riferimento a tali raccolte in monitoraggio di Azure e automazione di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Monitoraggio di Azure supporta System Center Configuration Manager Current Branch, versione 1606 e successive.

>[!NOTE]
>La funzionalità per la connessione Configuration Manager con un'area di lavoro Log Analytics è facoltativa e non è abilitata per impostazione predefinita. È necessario abilitare questa funzionalità prima di utilizzarla. Per altre informazioni, vedere [abilitare le funzionalità facoltative dagli aggiornamenti](https://docs.microsoft.com/sccm/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Panoramica della configurazione

I passaggi seguenti riepilogano i passaggi per configurare Configuration Manager l'integrazione con monitoraggio di Azure.  

1. In Azure Active Directory registrare Configuration Manager come applicazione Web e/o app per le API Web e assicurarsi di avere l'ID client e la chiave privata client dalla registrazione da Azure Active Directory. Vedere [Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse](../../active-directory/develop/howto-create-service-principal-portal.md) per informazioni dettagliate su come eseguire questo passaggio.

2. In Azure Active Directory [concedere Configuration Manager (l'app Web registrata) con l'autorizzazione per accedere a monitoraggio di Azure](#grant-configuration-manager-with-permissions-to-log-analytics).

3. In Configuration Manager aggiungere una connessione tramite la procedura guidata per i **servizi di Azure** .

4. [Scaricare e installare l'agente di log Analytics per Windows](#download-and-install-the-agent) nel computer in cui è in esecuzione il Configuration Manager ruolo del sistema del sito del punto di connessione del servizio. L'agente invia i dati Configuration Manager all'area di lavoro Log Analytics in monitoraggio di Azure.

5. In monitoraggio di Azure [importare le raccolte da Configuration Manager](#import-collections) come gruppi di computer.

6. In monitoraggio di Azure visualizzare i dati di Configuration Manager come [gruppi di computer](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Fornire a Configuration Manager le autorizzazioni per Log Analytics

Nella procedura seguente si concede il ruolo *Collaboratore* nell'area di lavoro Log Analytics all'applicazione AD e all'entità servizio creata in precedenza per Configuration Manager. Se non si ha ancora un'area di lavoro, vedere [creare un'area di lavoro in monitoraggio di Azure prima di](../../azure-monitor/learn/quick-create-workspace.md) procedere. In questo modo, si consente a Configuration Manager di eseguire l'autenticazione e connettersi all'area di lavoro Log Analytics.  

> [!NOTE]
> È necessario specificare le autorizzazioni nell'area di lavoro Log Analytics per Configuration Manager. In caso contrario, viene visualizzato un messaggio di errore quando si usa la procedura guidata di configurazione in Configuration Manager.
>

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.

2. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro da modificare.

3. Nel riquadro a sinistra selezionare **Controllo di accesso (IAM)** .

4. Nella pagina Controllo di accesso (IAM) fare clic su **Aggiungi un'assegnazione di ruolo**. Viene visualizzato il riquadro **Aggiungi un'assegnazione di ruolo**.

5. Nel riquadro **Aggiungi un'assegnazione di ruolo**, nell'elenco a discesa **Ruolo** selezionare il ruolo **Collaboratore**.  

6. Nell'elenco a discesa **Assegna accesso a** selezionare l'applicazione di Configuration Manager creata in precedenza in AD e quindi fare clic su **OK**.  

## <a name="download-and-install-the-agent"></a>Scaricare e installare l'agente

Vedere l'articolo [connettere computer Windows a monitoraggio di Azure in Azure](agent-windows.md) per informazioni sui metodi disponibili per l'installazione dell'agente di log Analytics per Windows nel computer che ospita il ruolo del sistema del sito del punto di connessione del servizio Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Connetti Configuration Manager all'area di lavoro Log Analytics

>[!NOTE]
> Per aggiungere una connessione Log Analytics, è necessario che l'ambiente Configuration Manager disponga di un [punto di connessione del servizio](https://docs.microsoft.com/sccm/core/servers/deploy/configure/about-the-service-connection-point) configurato per la modalità online.

> [!NOTE]
> È necessario connettere il sito di livello superiore della gerarchia a monitoraggio di Azure. Se si connette un sito primario autonomo a monitoraggio di Azure e quindi si aggiunge un sito di amministrazione centrale all'ambiente, è necessario eliminare e ricreare la connessione all'interno della nuova gerarchia.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager selezionare **servizi cloud** , quindi selezionare **servizi di Azure**. 

2. Fare clic con il pulsante destro del mouse su **servizi di Azure** e scegliere **Configura servizi di Azure**. Viene visualizzata la pagina **Configura servizi di Azure** . 
   
3. Nella schermata **Generale**, confermare di aver eseguito le azioni seguenti e di aver ottenuto i dettagli per ogni elemento, quindi selezionare **Avanti**.

4. Nella pagina servizi di Azure della procedura guidata per i servizi di Azure:

    1. Specificare un **nome** per l'oggetto in Configuration Manager.
    2. Specificare una **Descrizione** facoltativa che consenta di identificare il servizio.
    3. Selezionare il **connettore OMS**per i servizi di Azure.

    >[!NOTE]
    >OMS è ora definito Log Analytics che è una funzionalità di monitoraggio di Azure.

5. Selezionare **Avanti** per passare alla pagina delle proprietà dell'app Azure della procedura guidata per i servizi di Azure.

6. Nella pagina **app** della procedura guidata per i servizi di Azure selezionare innanzitutto l'ambiente Azure dall'elenco e quindi fare clic su **Importa**.

7. Nella pagina **Importa app** specificare le informazioni seguenti:

    1. Specificare il **nome del Tenant Azure ad** per l'app.

    2. Specificare per **Azure ad ID tenant** il tenant del Azure ad. Queste informazioni sono reperibili nella pagina **proprietà** Azure Active Directory. 

    3. Specificare per **nome applicazione** il nome dell'applicazione.

    4. Specificare per **ID client**, l'ID applicazione dell'app Azure ad creata creata in precedenza.

    5. Specificare per **chiave privata**, la chiave privata del client dell'app Azure ad creata.

    6. Specificare per la **scadenza della chiave privata**, la data di scadenza della chiave.

    7. Specificare per **URI ID app**, l'URI ID app dell'app creata Azure ad creata in precedenza.

    8. Selezionare **Verifica** e a destra i risultati dovrebbero essere visualizzati **correttamente**.

8. Nella pagina **configurazione** esaminare le informazioni per verificare che i campi **sottoscrizioni di Azure**, **gruppo di risorse di Azure**e area di lavoro di **Operations Management Suite** siano pre-popolati indicando che l'applicazione Azure ad ha autorizzazioni sufficienti nel gruppo di risorse. Se i campi sono vuoti, significa che l'applicazione non dispone dei diritti necessari. Selezionare le raccolte di dispositivi da raccogliere e trasmettere all'area di lavoro, quindi selezionare **Aggiungi**.

9. Esaminare le opzioni nella pagina **Conferma impostazioni** e selezionare **Avanti** per iniziare a creare e configurare la connessione.

10. Al termine della configurazione, viene visualizzata la pagina **completamento** . Selezionare **Chiudi**. 

Dopo aver collegato Configuration Manager a monitoraggio di Azure, è possibile aggiungere o rimuovere raccolte e visualizzare le proprietà della connessione.

## <a name="update-log-analytics-workspace-connection-properties"></a>Aggiornare le proprietà di connessione dell'area di lavoro Log Analytics

Se una password o una chiave privata del client scade o viene persa, sarà necessario aggiornare manualmente le proprietà di connessione Log Analytics.

1. Nell'area di lavoro **Amministrazione** di Configuration Manager selezionare **servizi cloud** e quindi selezionare **OMS Connector** per aprire la pagina delle **proprietà della connessione OMS** .
2. In questa pagina, fare clic sulla scheda **Azure Active Directory** per visualizzare il **tenant**, l'**ID client** e la **scadenza della chiave privata client**. **Verificare** se la **chiave privata client** è scaduta.

## <a name="import-collections"></a>Importare le raccolte

Dopo aver aggiunto una connessione Log Analytics a Configuration Manager e aver installato l'agente nel computer che esegue il ruolo del sistema del sito del punto di connessione del servizio Configuration Manager, il passaggio successivo consiste nell'importare le raccolte da Configuration Manager in Azure Monitorare come gruppi di computer.

Dopo aver completato la configurazione iniziale per importare le raccolte di dispositivi dalla gerarchia, le informazioni sulla raccolta vengono recuperate ogni 3 ore per mantenendo l'appartenenza corrente. È possibile scegliere di disabilitare questa opzione in qualsiasi momento.

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Aree di lavoro di Log Analytics**.
2. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro con cui è stato registrato Configuration Manager.  
3. Selezionare **Impostazioni avanzate**.
4. Selezionare **Gruppi di computer** e quindi **SCCM**.  
5. Selezionare **Importa appartenenze alla raccolta di Configuration Manager** e fare clic su **Salva**.  
   
    ![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visualizzare i dati da Configuration Manager

Dopo aver aggiunto una connessione Log Analytics a Configuration Manager e aver installato l'agente nel computer che esegue il ruolo del sistema del sito del punto di connessione del servizio Configuration Manager, i dati dell'agente vengono inviati all'area di lavoro di Log Analytics in monitoraggio di Azure. In monitoraggio di Azure le raccolte di Configuration Manager vengono visualizzate come [gruppi di computer](../../azure-monitor/platform/computer-groups.md). È possibile visualizzare i gruppi dalla pagina **Configuration Manager** in **Impostazioni\Gruppi di computer**.

Una volta importate le raccolte, è possibile vedere quanti computer con appartenenze delle raccolte sono stati rilevati. È anche possibile visualizzare il numero di raccolte importate.

![Gruppi di computer - Scheda SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando si fa clic su uno di essi, viene visualizzato l'editor di query di log con tutti i gruppi importati o tutti i computer appartenenti a ogni gruppo. Con la [Ricerca log](../../azure-monitor/log-query/log-query-overview.md)è possibile eseguire un'analisi approfondita dei dati di appartenenza della raccolta.

## <a name="next-steps"></a>Passaggi successivi

Usare [Ricerca Log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare le informazioni dettagliate sui dati di Configuration Manager.
