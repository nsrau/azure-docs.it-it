---
title: La metrica del disco è deprecata nel portale di Azure | Microsoft Docs
description: Informazioni sulle metriche del disco deprecate e su come aggiornare gli avvisi delle metriche per usare le nuove metriche.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79299803"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>La metrica del disco è deprecata nel portale di Azure

Le metriche correlate al disco deprecate verranno presto rimosse dal portale di Azure. È disponibile una nuova versione di ogni metrica deprecata da usare. Questo articolo illustra le metriche nuove e come aggiornare gli avvisi delle metriche per usarli.

## <a name="list-of-new-metrics"></a>Elenco di nuove metriche

Questa tabella esegue il mapping di ogni metrica deprecata alla nuova metrica corrispondente. 

|Metrica deprecata|Metrica nuova (sostituzione)|
|----|----|
|Profondità coda per un disco dati (deprecato)|Profondità coda per un disco dati (anteprima)|
|Byte letti da disco/sec per un disco dati (deprecato)|Byte letti da disco/sec per un disco dati (anteprima)|
|Operazioni di lettura da disco/sec per un disco dati (deprecato)|Operazioni di lettura da disco/sec per un disco dati (anteprima)|
|Byte scritti su disco/sec per un disco dati (deprecato)|Byte scritti su disco/sec per un disco dati (anteprima)|
|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|
|Sistema operativo QD (deprecato)|Profondità della coda del sistema operativo (anteprima)|
|Byte letti dal sistema operativo/sec (deprecato)|Byte letti dal sistema operativo/sec (anteprima)|
|Operazioni di lettura del sistema operativo/sec (deprecate)|Operazioni di lettura del sistema operativo/sec (anteprima)|
|Byte scritti sistema operativo/sec (deprecato)|Byte scritti sistema operativo/sec (anteprima)|
|Operazioni di scrittura del sistema operativo/sec (deprecate)|Operazioni di scrittura del sistema operativo/sec (anteprima)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Eseguire la migrazione delle metriche negli avvisi delle metriche

Aggiornare gli avvisi delle metriche per usare le nuove metriche.

1. Nella portale di Azure cercare gli **avvisi**. Quindi, nella sezione **Servizi** scegliere **avvisi**.

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Nella pagina **avvisi** scegliere il pulsante **Gestisci regole di avviso** . 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Nell'elenco a discesa **gruppo di risorse** selezionare la casella di controllo **macchine virtuali** e nell'elenco a discesa **tipo di segnale** selezionare la casella di controllo **metrica** . 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Nell'elenco delle metriche identificare le condizioni correlate ai dischi. Fare clic sul nome della regola. 

   Il nome viene visualizzato come collegamento ipertestuale nella colonna **nome** della tabella.

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Nella sezione **condizioni** della pagina **Gestione regole** fare clic sulla condizione dell'avviso. 

   La condizione viene visualizzata come collegamento ipertestuale.  

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Viene visualizzata la pagina **Configura logica di segnalazione** e le impostazioni della condizione vengono visualizzate nella sezione relativa alla **logica di avviso** di tale pagina.

6. Creare un record di queste impostazioni che scompariranno quando si rimuove la metrica deprecata.

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Prendere in considerazione l'acquisizione di queste impostazioni in una schermata o in un file di testo. 

7. Fare clic sul collegamento **Torna alla selezione del segnale** .

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Nella pagina **Configura logica di segnalazione** scegliere la metrica di sostituzione appropriata (nuova metrica). Usare la [tabella](#update-metrics) riportata in precedenza in questo articolo per identificare il nome della nuova metrica.

   > [!TIP] 
   > Iniziare a digitare nella barra di ricerca per restringere l'elenco dei nomi delle metriche. 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Scegliere il pulsante **fine** . 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Per eseguire il commit delle modifiche, scegliere il pulsante **Salva** . 

    > [!div class="mx-imgBorder"]
    > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/save-new-metric.png)






