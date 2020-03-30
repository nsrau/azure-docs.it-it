---
title: Integrazione della mappa di Monitoraggio di Azure per le macchine virtuali con Operations Manager . Documenti Microsoft
description: Monitoraggio di Azure per le macchine virtuali individua automaticamente i componenti dell'applicazione nei sistemi Windows e Linux ed esegue il mapping della comunicazione tra i servizi. In questo articolo viene illustrato l'utilizzo della funzionalità Mappa per creare automaticamente diagrammi applicazioni distribuiti in Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663454"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrare la funzionalità di mapping di System Center Operations Manager con Monitoraggio di Azure per macchine virtuali

In Monitoraggio di Azure per le macchine virtuali è possibile visualizzare i componenti dell'applicazione individuati nelle macchine virtuali (VM) Windows e Linux eseguite in Azure o nell'ambiente. Con questa integrazione tra la funzionalità Mappa e System Center Operations Manager, è possibile creare automaticamente diagrammi applicazioni distribuiti in Operations Manager basati sulle mappe delle dipendenze dinamiche in Monitoraggio di Azure per le macchine virtuali. In questo articolo viene descritto come configurare il gruppo di gestione di System Center Operations Manager per supportare questa funzionalità.

>[!NOTE]
>Se la mappa del servizio è già stata distribuita, è possibile visualizzare le mappe in Monitoraggio di Azure per le macchine virtuali, che include funzionalità aggiuntive per monitorare l'integrità e le prestazioni delle macchine virtuali. La funzionalità Mappa di Monitoraggio di Azure per le macchine virtuali deve sostituire la soluzione della mappa del servizio autonoma. Per altre informazioni, vedere [Descrizione di Monitoraggio di Azure per le macchine virtuali](vminsights-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di gestione di System Center Operations Manager (2012 R2 o versione successiva).
* Un'area di lavoro di Log Analytics configurata per supportare Monitoraggio di Azure per le macchine virtuali.
* Una o più macchine virtuali Windows e Linux o computer fisici monitorati da Operations Manager e che inviano dati all'area di lavoro di Log Analytics. Linux servers reporting to an Operations Manager management group need to be configured to directly connect to Azure Monitor. Per ulteriori informazioni, vedere la panoramica in Raccogliere i dati del [log con l'agente di Log Analytics](../platform/log-analytics-agent.md).
* Un'entità servizio con accesso alla sottoscrizione di Azure associata all'area di lavoro Log Analytics. Per altre informazioni, vedere [Creare un'entità servizio](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installare il management pack di Elenco dei servizi

Attivare l'integrazione tra Operations Manager e la funzionalità di mapping importando il pacchetto di Management Pack Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). È possibile scaricare il bundle del Management Pack da [Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). Il bundle contiene i management pack seguenti:

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>Configurare l'integrazione

Dopo aver installato il Management Pack della mappa del servizio, un nuovo nodo, **Mappa servizio**, viene visualizzato in **Operations Management Suite** nel riquadro **Amministrazione** della console operatore di Operations Manager.

>[!NOTE]
>[Operations Management Suite era una raccolta di servizi](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) che includeva Log Analytics, ora fa parte di Monitoraggio di [Azure.](../overview.md)

Per configurare l'integrazione della mappa di Monitoraggio di Azure per le macchine virtuali, eseguire le operazioni seguenti:To configure Azure Monitor for VMs Map integration, do the following:

1. Per aprire la configurazione guidata, fare clic su **Add workspace** (Aggiungi area di lavoro) nel riquadro **Service Map Overview** (Panoramica di Elenco dei servizi) .  

    ![Riquadro Service Map Overview (Panoramica di Elenco dei servizi)](media/service-map-scom/scom-configuration.png)

2. Nella finestra **Configurazione di connessione** immettere l'ID o il nome del tenant, l'ID applicazione (noto anche come nome utente o ClientID) e la password dell'entità servizio, quindi fare clic su **Avanti**. Per altre informazioni, consultare Creare un'entità servizio.

    ![Finestra di configurazione della connessione](media/service-map-scom/scom-config-spn.png)

3. Nella finestra **Selezione sottoscrizione**, selezionare la sottoscrizione di Azure, il gruppo di risorse di Azure contenente l'area di lavoro Log Analytics e infine l'area di lavoro stessa, quindi fare clic su **Avanti**.

    ![Area di lavoro di configurazione di Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. Nella finestra **Machine Group Selection** (Selezione gruppi di computer) è possibile scegliere i gruppi di computer di Mapping dei servizi da sincronizzare con Operations Manager. Fare clic su **Add/Remove Machine Groups** (Aggiungi/Rimuovi gruppi di computer), scegliere i gruppi nell'elenco **Available Machine Groups** (Gruppi di computer disponibili) e fare clic su **Aggiungi**.  Dopo avere completato la selezione dei gruppi, fare clic su **OK** per terminare.

    ![Gruppi di computer di configurazione di Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Nella finestra **Selezione server** è possibile configurare il gruppo di server di mappe del servizio con i server che si desidera sincronizzare tra Operations Manager e la funzionalità Mappa. Fare clic su **Aggiungi/Rimuovi server**.

    Perché l'integrazione crei un diagramma applicazioni distribuite per un server, quest'ultimo deve essere:

   * Monitorato da Operations Manager
   * Configurato per il report nell'area di lavoro di Log Analytics configurata con Monitoraggio di Azure per le macchine virtuali
   * Elencato nel gruppo di server di Mapping dei servizi

     ![Gruppo di configurazione di Operations Manager](media/service-map-scom/scom-config-group.png)

6. Facoltativo: selezionare il pool di risorse di tutti i server di gestione per comunicare con Log Analytics e quindi fare clic su **Aggiungi area di lavoro**.

    ![Pool di risorse di configurazione di Operations Manager](media/service-map-scom/scom-config-pool.png)

    Per configurare e registrare l'area di lavoro Log Analytics potrebbero essere necessari alcuni minuti. Dopo la configurazione, Operations Manager avvia la prima sincronizzazione della mappa.

    ![Pool di risorse di configurazione di Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorare l'integrazione

Dopo aver connesso l'area di lavoro di Log Analytics, una nuova cartella, Mapping del servizio, viene visualizzata nel riquadro **Monitoraggio** della console operatore di Operations Manager.

![Riquadro Monitoraggio di Operations Manager](media/service-map-scom/scom-monitoring.png)

La cartella Mapping dei servizi ha quattro nodi:

* **Avvisi attivi:** elenca tutti gli avvisi attivi sulla comunicazione tra Operations Manager e Monitoraggio di Azure.  

  >[!NOTE]
  >Questi avvisi non sono avvisi di Log Analytics sincronizzati con Operations Manager, vengono generati nel gruppo di gestione in base ai flussi di lavoro definiti nel Management Pack della mappa del servizio.

* **Server:** elenca i server monitorati configurati per la sincronizzazione dalla funzionalità Mappa di Monitoraggio di Azure per macchine virtuali.

    ![Riquadro Monitoraggio server di Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Viste dipendenze gruppo**di computer: elenca tutti i gruppi di computer sincronizzati dalla funzionalità Mappa. È possibile fare clic su un gruppo per visualizzarne il diagramma applicazioni distribuite.

    ![Diagramma applicazioni distribuite di Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Viste dipendenze server:** elenca tutti i server sincronizzati dalla funzionalità Mappa. È possibile fare clic su un server per visualizzarne il diagramma applicazioni distribuite.

    ![Diagramma applicazioni distribuite di Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Modificare o eliminare l'area di lavoro

È possibile modificare o eliminare l'area di lavoro configurata tramite il riquadro **Service Map Overview** (Panoramica di Elenco dei servizi): riquadro **Amministrazione** --> **Operations Management Suite** > **Elenco dei servizi**.

>[!NOTE]
>[Operations Management Suite era una raccolta di servizi](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) che includeva Log Analytics, ora incluso in [Monitoraggio di Azure](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

In questa versione corrente è possibile configurare una sola area di lavoro di Log Analytics.You can configure only one Log Analytics workspace in this current release.

![Riquadro Modifica area di lavoro di Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurare regole e override

Una regola, *Microsoft.SystemCenter.ServiceMapImport.Rule*, recupera periodicamente le informazioni dalla funzionalità mappa di Monitoraggio di Azure per macchine virtuali. Per modificare l'intervallo di sincronizzazione, è possibile ignorare la regola e modificare il valore per il parametro **IntervalMinutes**.

![Finestra delle proprietà di override di Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: abilita/disabilita gli aggiornamenti automatici.
* **IntervalMinutes**: Specifica l'ora tra gli aggiornamenti. L'intervallo predefinito è un'ora. Se si desidera sincronizzare le mappe più frequentemente, è possibile modificare il valore.
* **TimeoutSeconds**: Specifica il periodo di tempo prima del timeout della richiesta.
* **TimeWindowMinutes**: Specifica l'intervallo di tempo per l'esecuzione di query sui dati. Il valore predefinito è 60 minuti, ovvero l'intervallo massimo consentito.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

La progettazione attuale presenta i problemi e le limitazioni seguenti:

* È possibile connettersi a una sola area di lavoro Log Analytics.
* Anche se è possibile aggiungere manualmente server al gruppo di server di Mapping dei servizi tramite il riquadro **Creazione e modifica**, le mappe di tali server non vengono sincronizzate immediatamente. Verranno sincronizzati dalla funzionalità Mappa di Monitoraggio di Azure per le macchine virtuali durante il ciclo di sincronizzazione successivo.
* Se si apportano modifiche ai diagrammi di applicazione distribuita creati dal Management Pack, tali modifiche verranno probabilmente sovrascritte alla sincronizzazione successiva con Monitoraggio di Azure per le macchine virtuali.

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per la documentazione ufficiale di Azure sulla creazione di un'entità servizio, vedere:

* [Create a service principal by using PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) (Creare un'entità servizio usando PowerShell)
* [Create a service principal by using Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli) (Creare un'entità servizio usando Azure CLI)
* [Create a service principal by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Creare un'entità servizio usando il portale di Azure)

### <a name="feedback"></a>Commenti e suggerimenti
Sono disponibili commenti e suggerimenti sull'integrazione con la funzionalità di mapping di Monitoraggio di Azure per le macchine virtuali o su questa documentazione? Vedere la [pagina per i suggerimenti degli utenti](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), in cui è possibile suggerire funzionalità o votare i suggerimenti esistenti.
