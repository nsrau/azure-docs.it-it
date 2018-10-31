---
title: Monitorare il cluster DC/OS di Azure - Operations Management
description: Monitorare un cluster DC/OS del servizio contenitore di Azure con Log Analytics.
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 4576d9decc6ba1e01ef39abdb8a3ef89461196e8
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407793"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Monitorare un cluster DC/OS del servizio contenitore di Azure con Log Analytics

Log Analytics è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. La soluzione di monitoraggio dei contenitori è una soluzione di Log Analytics che consente di visualizzare l'inventario, le prestazioni e i log dei contenitori in un'unica posizione. È possibile controllare, risolvere i problemi relativi ai contenitori visualizzando i log in una posizione centralizzata e trovare un contenitore con un consumo eccessivo in un host.

![](media/container-service-monitoring-oms/image1.png)

Per altre informazioni sulla soluzione contenitore, consultare [Log Analytics della soluzione contenitore](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Impostazione di Log Analytics dall'universo DC/OS


In questo articolo si presume di aver configurato un DC/OSe di aver distribuito applicazioni contenitore Web semplici nel cluster.

### <a name="pre-requisite"></a>Prerequisito.
- [Sottoscrizione di Microsoft Azure](https://azure.microsoft.com/free/): è possibile ottenerla gratuitamente.  
- Configurazione dell'area di lavoro di Log Analytics (vedere il "Passaggio 3" sotto)
- [Interfaccia della riga di comando di DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) installata.

1. Nel dashboard di DC/OS fare clic su Universo e cercare "OMS" come illustrato di seguito.

   >[!NOTE]
   >OMS viene a questo punto indicato come Log Analytics.

 ![](media/container-service-monitoring-oms/image2.png)

2. Fare clic su **Installa**. Si aprirà una finestra popup con le informazioni sulla versione e un pulsante **Installa pacchetto** o **Installazione avanzata**. Fare clic su **Installazione avanzata** per accedere alla pagina dedicata alle **proprietà di configurazione specifiche di OMS**.

 ![](media/container-service-monitoring-oms/image3.png)

 ![](media/container-service-monitoring-oms/image4.png)

3. Qui verrà chiesto di immettere `wsid`, ovvero l'ID dell'area di lavoro di Log Analytics, e `wskey`, la chiave primaria per l'ID dell'area di lavoro. Per ottenere sia `wsid` che `wskey` è necessario creare un account alla pagina <https://mms.microsoft.com>.
Attenersi alla procedura di creazione di un account. Dopo aver creato l'account, è necessario ottenere `wsid` e `wskey` facendo clic su **Impostazioni**, **Origini connesse** e **Server Linux**, come indicato di seguito.

 ![](media/container-service-monitoring-oms/image5.png)

4. Selezionare il numero di istanze desiderato e fare clic sul pulsante "Verifica e installa". In genere, è opportuno scegliere un numero di istanze uguale al numero di macchine virtuali presenti nel cluster dell'agente. L'agente di Log Analytics per Linux viene installato come singolo contenitore in ciascuna macchina virtuale di cui si vuole raccogliere informazioni per fini di monitoraggio e registrazione.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>Configurazione di una dashboard di Log Analytics semplice

Dopo aver installato l'agente di Log Analytics per Linux nelle macchine virtuali, passare alla configurazione della dashboard di Log Analytics. È possibile configurare la dashboard tramite il portale di Azure.

### <a name="azure-portal"></a>Portale di Azure 

Accedere al portale di Azure all'indirizzo <https://portal.microsoft.com/>. Passare a **Marketplace**, selezionare **Monitoraggio + gestione** e fare clic su **Visualizza tutto**. Dopodiché, digitare `containers` nella ricerca per visualizzare "contenitori" nei risultati. Selezionare **Contenitori** e fare clic su **Crea**.

![](media/container-service-monitoring-oms/image9.png)

Dopo avere fatto clic su **Crea**, verrà chiesto di indicare l'area di lavoro. Selezionarne una esistente, oppure creare una nuova area di lavoro.

![](media/container-service-monitoring-oms/image10.PNG)

Dopo averla selezionata, fare clic su **Crea**.

![](media/container-service-monitoring-oms/image11.png)

Per altre informazioni sulla Soluzione contenitore di Log Analytics, consultare [Soluzione Monitoraggio contenitori in Log Analytics](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>Come ridimensionare l'agente di Log Analytics con DC/OS di ACS 

Nel caso sia necessario aver installato l'agente di Log Analytics oltre al conteggio dei nodi effettivi o qualora si aumenti il set di scalabilità di macchine virtuali aggiungendo più macchine virtuali, è possibile farlo ridimensionando il servizio `msoms`.

È possibile passare a Marathon o alla scheda Servizi di interfaccia utente DC/OS e aumentare il numero di nodi.

![](media/container-service-monitoring-oms/image12.PNG)

Questo verrà distribuito agli altri nodi che non hanno ancora distribuito l'agente di Log Analytics.

## <a name="uninstall-ms-oms"></a>Disinstallare OMS di Microsoft

Per disinstallare OMS di Microsoft immettere il comando seguente:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Facci sapere
Cosa funziona? Cosa manca? Cos'altro può servire? Scrivere a <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passaggi successivi

 Dopo aver configurato Log Analytics per monitorare i contenitori, [visualizzare il dashboard dei contenitori](../../log-analytics/log-analytics-containers.md).
