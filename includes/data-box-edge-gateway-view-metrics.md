---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779914"
---
È anche possibile visualizzare le metriche per monitorare le prestazioni del dispositivo e in alcuni casi per la risoluzione dei problemi del dispositivo.

Eseguire i passaggi seguenti nel portale di Azure per creare un grafico per le metriche selezionate del dispositivo.

1. Per la risorsa nel portale di Azure, passare a **Monitoraggio > Metrica** e selezionare **Aggiungi metrica**.

    ![Aggiungere una metrica](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. La risorsa viene inserita automaticamente.  

    ![Risorsa corrente](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Per specificare un'altra risorsa, selezionare quella desiderata. Nel pannello **Seleziona una risorsa** selezionare la sottoscrizione, il gruppo di risorse, il tipo di risorsa e la risorsa specifica per cui si vogliono visualizzare le metriche e selezionare **Applica**.

    ![Scegliere un'altra risorsa](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. Nell'elenco a discesa selezionare una metrica in base a cui monitorare il dispositivo. Per un elenco completo di queste metriche, vedere [Metrica del dispositivo](#metrics-on-your-device).

4. Quando si seleziona una metrica nell'elenco a discesa, è anche possibile definire l'aggregazione. Per aggregazione si intende il valore effettivo aggregato nell'intervallo di tempo specificato. I valori aggregati possono essere medi, minimi o massimi. Selezionare Medio, Minimo o Massimo per l'aggregazione.

    ![Visualizzare il grafico](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Se la metrica selezionata ha più istanze, è disponibile l'opzione di suddivisione. Selezionare **Applica suddivisione** e quindi selezionare il valore in base a cui visualizzare la suddivisione.

    ![Applicare la suddivisione](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Se ora si vuole visualizzare la suddivisione solo per alcune istanze, è possibile filtrare i dati. Ad esempio, in questo caso, se si vuole visualizzare la velocità effettiva di rete solo per le due interfacce di rete connesse nel dispositivo, è possibile filtrare tali interfacce. Selezionare **Aggiungi filtro** e specificare il nome dell'interfaccia di rete per il filtro.

    ![Aggiungi filtro](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. È anche possibile aggiungere il grafico al dashboard per accedervi facilmente.

    ![Aggiungi al dashboard](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Per esportare i dati del grafico in un foglio di calcolo di Excel o ottenere un collegamento al grafico che è possibile condividere, selezionare l'opzione di condivisione dalla barra dei comandi.

    ![Esportazione dei dati](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)
