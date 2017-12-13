---
title: Monitorare il cluster DC/OS di Azure - Operations Management
description: Monitorare un cluster DC/OS del servizio contenitore di Azure con Microsoft Operations Management Suite.
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: a675f0b57ed9e5d515cfa79a3a841e0f133fff6f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-operations-management-suite"></a>Monitorare un cluster DC/OS del servizio contenitore di Azure con Operations Management Suite

Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. La soluzione contenitore è una soluzione di Log Analytics di OMS che consente di visualizzare l'inventario, le prestazioni e i log del contenitore in un'unica posizione. È possibile controllare, risolvere i problemi relativi ai contenitori visualizzando i log in una posizione centralizzata e trovare un contenitore con un consumo eccessivo in un host.

![](media/container-service-monitoring-oms/image1.png)

Per altre informazioni sulla soluzione contenitore, consultare [Log Analytics della soluzione contenitore](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-oms-from-the-dcos-universe"></a>Impostazione di OMS dall'universo DC/OS


In questo articolo si presume di aver configurato un DC/OSe di aver distribuito applicazioni contenitore Web semplici nel cluster.

### <a name="pre-requisite"></a>Prerequisito.
- [Sottoscrizione di Microsoft Azure](https://azure.microsoft.com/free/): è gratuita.  
- Configurazione dell'area di lavoro di Microsoft OMS (vedere il "Passaggio 3" sotto)
- [Interfaccia della riga di comando di DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) installata.

1. Nel dashboard di DC/OS fare clic su Universo e cercare "OMS" come illustrato di seguito.

![](media/container-service-monitoring-oms/image2.png)

2. Fare clic su **Installa**. Si aprirà una finestra popup con le informazioni sulla versione di OMS e un pulsante **Installa pacchetto** o **Installazione avanzata**. Fare clic su **Installazione avanzata** per accedere alla pagina dedicata alle **proprietà di configurazione specifiche di OMS**.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Qui verrà chiesto di immettere `wsid` (l'ID dell'area di lavoro di OMS) e `wskey` (la chiave primaria di OMS per l'ID dell'area di lavoro). Per ottenere `wsid` e `wskey` è necessario creare un account OMS sul sito <https://mms.microsoft.com>. Attenersi alla procedura di creazione di un account. Dopo aver creato l'account, è necessario ottenere `wsid` e `wskey` facendo clic su **Impostazioni**, **Origini connesse** e **Server Linux**, come indicato di seguito.

 ![](media/container-service-monitoring-oms/image5.png)

4. Selezionare il numero di istanze OMS desiderato e fare clic sul pulsante "Verifica e installa". In genere, è opportuno scegliere un numero di istanze OMS uguale al numero di macchine virtuali presenti nel cluster dell'agente. L'agente OMS per Linux viene installato come singoli contenitori in ciascuna macchina virtuale di cui si vuole raccogliere informazioni per il monitoraggio e la registrazione.

## <a name="setting-up-a-simple-oms-dashboard"></a>Configurazione di un dashboard OMS semplice

Dopo aver installato l'agente OMS per Linux nelle macchine virtuali, passare alla configurazione del dashboard OMS. Ci sono due modi per eseguire questa operazione: tramite il portale OMS o con portale di Azure.

### <a name="oms-portal"></a>Portale di OMS 

Accedere al portale di OMS (<https://mms.microsoft.com>) e andare in **Raccolta soluzioni**.

![](media/container-service-monitoring-oms/image6.png)

In **Raccolta soluzioni** selezionare **Contenitori**.

![](media/container-service-monitoring-oms/image7.png)

Dopo aver selezionato la soluzione contenitore, verrà visualizzato il riquadro nella pagina Dashboard generale di OMS. Dopo aver indicizzato i dati contenitore inseriti verrà visualizzato il riquadro popolato con le informazioni nei riquadri di visualizzazione della soluzione.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>portale di Azure 

Accedere al portale di Azure all'indirizzo <https://portal.microsoft.com/>. Passare a **Marketplace**, selezionare **Monitoraggio + gestione** e fare clic su **Visualizza tutto**. Dopodiché, digitare `containers` nella ricerca per visualizzare "contenitori" nei risultati. Selezionare **Contenitori** e fare clic su **Crea**.

![](media/container-service-monitoring-oms/image9.png)

Dopo avere fatto clic su **Crea**, verrà chiesto di indicare l'area di lavoro. Selezionarne una esistente, oppure creare una nuova area di lavoro.

![](media/container-service-monitoring-oms/image10.PNG)

Dopo averla selezionata, fare clic su **Crea**.

![](media/container-service-monitoring-oms/image11.png)

Per altre informazioni sulla soluzione contenitore OMS, consultare [Log Analytics della soluzione contenitore](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Come applicare la scalabilità all'agente OMS con DC/OS del servizio contenitore di Azure 

Nel caso sia necessario aver installato l'agente OMS oltre il conteggio dei nodi effettivi o qualora si applichi la scalabilità VMSS aggiungendo più macchine virtuali, è possibile farlo scalando il servizio `msoms`.

È possibile passare a Marathon o alla scheda Servizi di interfaccia utente DC/OS e aumentare il numero di nodi.

![](media/container-service-monitoring-oms/image12.PNG)

Questo verrà distribuito agli altri nodi che non hanno ancora distribuito l'agente OMS.

## <a name="uninstall-ms-oms"></a>Disinstallare OMS di Microsoft

Per disinstallare OMS di Microsoft immettere il comando seguente:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Facci sapere
Cosa funziona? Cosa manca? Cos'altro può servire? Scrivere a <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Passaggi successivi

 Dopo aver configurato OMS per monitorare i contenitori,[visualizzare il dashboard dei contenitore](../../log-analytics/log-analytics-containers.md).
