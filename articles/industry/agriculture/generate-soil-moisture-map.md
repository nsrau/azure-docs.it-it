---
title: Genera mappa termica di umidità del suolo
description: Viene descritto come generare mappa termica di umidità del suolo in Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e11f560c2b101d86d41feb3f6c93cffe7c1a748d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851452"
---
# <a name="generate-soil-moisture-heatmap"></a>Genera mappa termica di umidità del suolo

L'umidità del suolo è l'acqua che viene mantenuta negli spazi tra le particelle del suolo. Il mappa termica di umidità del suolo ti aiuta a comprendere i dati di umidità a qualsiasi profondità e a risoluzione elevata nelle farm. Per generare un mappa termica di umidità del suolo accurato e utilizzabile, è necessaria una distribuzione uniforme dei sensori dallo stesso provider. Diversi provider avranno differenze nel modo in cui viene misurata l'umidità del suolo insieme alle differenze nella calibrazione. Il mappa termica viene generato per una particolare profondità usando i sensori distribuiti a tale profondità.

Questo articolo descrive il processo di generazione di un mappa termica di umidità del suolo per la farm, usando Azure FarmBeats Accelerator. In questo articolo verrà spiegato come:

- [Creazione di farm](#create-a-farm)
- [Assegnare sensori alle farm](#get-soil-moisture-sensor-data-from-partner)
- [Genera mappa termica di umidità del suolo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare quanto segue:  

- Una sottoscrizione di Azure.
- Istanza in esecuzione di Azure FarmBeats.
- Per la farm sono disponibili almeno tre sensori di umidità del suolo.

## <a name="create-a-farm"></a>Creazione di una farm

Una farm è un'area geografica di interesse per la quale si vuole creare un mappa termica di umidità del suolo. È possibile creare una farm usando l' [API Farm](https://aka.ms/FarmBeatsDatahubSwagger) o nell' [interfaccia utente di FarmsBeats Accelerator](manage-farms.md#create-farms)

## <a name="deploy-sensors"></a>Distribuire sensori

È necessario distribuire fisicamente sensori di umidità del suolo nella farm. È possibile acquistare sensori di umidità del suolo da qualsiasi partner approvato, [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). È necessario coordinarsi con il provider di sensori per eseguire la configurazione fisica nella farm.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Ottenere i dati del sensore di umidità del suolo dal partner

Man mano che i sensori avviano lo streaming, i dati nel dashboard dei dati dei partner abilitano i dati in Azure FarmBeats. Questa operazione può essere eseguita dall'applicazione partner.

Se, ad esempio, sono stati acquistati sensori Davis, sarà possibile accedere all'account di collegamento meteo e fornire le credenziali necessarie per abilitare lo streaming dei dati in Azure FarmBeats. Per ottenere le credenziali necessarie, seguire le istruzioni [riportate in ottenere i dati del sensore](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners).

Dopo aver immesso le credenziali e selezionato **Invia** nell'applicazione partner, è possibile che il flusso di dati venga eseguito in Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Assegnare sensori di umidità del suolo alla farm

Dopo aver collegato l'account del sensore in Azure FarmBeats, è necessario assegnare i sensori di umidità del suolo alla farm di interesse.

1.  Nella home page selezionare **Farm** dal menu. viene visualizzata la pagina dell'elenco **Farm** .
2.  Selezionare **farm** > **Aggiungi dispositivi**.
3.  Viene visualizzata la finestra **Aggiungi dispositivi** . Selezionare un dispositivo collegato ai sensori di umidità del suolo per la farm.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selezionare **Aggiungi dispositivi**.     

## <a name="generate-soil-moisture-heatmap"></a>Genera mappa termica di umidità del suolo

Questo passaggio consente di creare un processo o un'operazione a esecuzione prolungata che genera mappa termica di umidità del suolo per la farm.

1.  Nella home page passare a **Farm** dal menu di spostamento a sinistra per visualizzare la pagina Farm.
2.  Selezionare **Farm**.
3.  Nella pagina **Dettagli Farm** selezionare **Genera mappa di precisione**.
4.  Dal menu a discesa selezionare **Soil umidità**.
5.  Nella finestra **umidità del terreno** selezionare **questa settimana**.
6.  Nella misura **Seleziona** **sensore**di umidità del suolo immettere la misura che si vuole usare per la mappa.
    Per trovare la misura del sensore, in **sensori**selezionare qualsiasi sensore di umidità del suolo. In **Proprietà sensore**usare il valore **Nome misura** .

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selezionare **genera mappe**.
    Viene visualizzato un messaggio di conferma con i dettagli del processo. Per ulteriori informazioni, vedere stato del processo nei processi.

    >[!NOTE]
    > Il completamento del processo richiede circa tre-quattro ore.

### <a name="download-the-soil-moisture-heatmap"></a>Scaricare la mappa termica di umidità del suolo

Seguire questa procedura:

1. Nella pagina **processi** controllare lo stato del **processo** creato nell'ultima procedura.
2. Quando lo stato del processo è indicato *succeeded*, fare clic su **Maps** nel menu.
3. Cercare la mappa in base al giorno in cui è stata creata nel formato < > Soil-moisture_MyFarm_YYYY-MM-GG.
4. Selezionare una mappa nella colonna **nome** . verrà visualizzata una finestra popup con l'anteprima della mappa selezionata.
5. Selezionare **Download**. La mappa viene scaricata e archiviata nella cartella locale del computer.

    ![Beat Farm progetto](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato generato un mappa termica di umidità del suolo, viene illustrato come [generare il posizionamento dei sensori](generate-maps.md#sensor-placement-map) e inserire [i dati cronologici di telemetria](ingest-historical-telemetry-data.md). 
