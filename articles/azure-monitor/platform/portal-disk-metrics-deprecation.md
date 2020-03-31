---
title: Metriche del disco deprecate nel portale di Azure - Documenti Microsoft
description: Scopri quali metriche del disco sono state deprecate e come aggiornare gli avvisi delle metriche per usare le nuove metriche.
services: azure-monitor
ms.subservice: metrics
ms.topic: conceptual
author: albecker1
ms.author: albecker
ms.date: 03/12/2020
ms.openlocfilehash: f2b960c2198800e04da77ad6b5be78d7b4762354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299803"
---
# <a name="disk-metrics-deprecation-in-the-azure-portal"></a>Metriche del disco deprecate nel portale di AzureDisk metrics deprecation in the Azure portal

Le metriche deprecate relative al disco verranno presto rimosse dal portale di Azure.Deprecated disk-related metrics will soon be removed from the Azure portal. È disponibile una nuova versione di ogni metrica deprecata. Questo articolo mostra quali metriche sono nuove e come aggiornare gli avvisi relativi alle metriche per usarli.

## <a name="list-of-new-metrics"></a>Elenco delle nuove metriche

Questa tabella esegue il mapping di ogni metrica deprecata alla nuova metrica corrispondente. 

|Metrica deprecata|Nuova metrica (sostituzione)|
|----|----|
|QD del disco dati (obsoleto)|Profondità coda del disco dati (anteprima)Data Disk Queue Depth (Preview)|
|Byte letti su disco dati/sec (obsoleto)|Byte letti da disco/sec per un disco dati (anteprima)|
|Operazioni di lettura su disco dati/sec (obsoleto)|Operazioni di lettura da disco/sec per un disco dati (anteprima)|
|Byte scritti su disco dati/sec (obsoleto)|Byte scritti su disco/sec per un disco dati (anteprima)|
|Operazioni di scrittura su disco dati al secondo (obsoleto)|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|
|QD del sistema operativo (obsoleto)|Profondità coda del sistema operativo (anteprima)OS Queue Depth (Preview)|
|Byte letti del sistema operativo/sec (obsoleto)|Byte letti sistema operativo/sec (anteprima)OS Read Bytes/Sec (Preview)|
|Operazioni di lettura del sistema operativo al secondo (obsoleto)|Operazioni di lettura del sistema operativo/sec (anteprima)OS Read Operations/Sec (Preview)|
|Byte scritti del sistema operativo/sec (obsoleto)|Byte scritti sistema operativo/sec (anteprima)OS Write Bytes/Sec (Preview)|
|Operazioni di scrittura del sistema operativo al secondo (obsoleto)|Operazioni di scrittura del sistema operativo/sec (anteprima)OS Write Operations/Sec (Preview)|

<a id="update-metrics" />

## <a name="migrate-metrics-in-your-metric-alerts"></a>Eseguire la migrazione delle metriche negli avvisi di metricaMigrate metrics in your metric alerts

Aggiornare gli avvisi delle metriche per usare nuove metriche.

1. Nel portale di Azure cercare **Avvisi**. Nella sezione **Servizi** scegliere **Avvisi**quindi nella sezione Servizi .

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/alert-service-azure-portal.png)

2. Nella pagina **Avvisi** scegliere il pulsante **Gestisci regole di avviso.** 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/manage-alert-rules-button.png)

3. Nell'elenco a discesa Gruppo di risorse selezionare la casella di controllo Macchine virtuali e nell'elenco a discesa Tipo di segnale selezionare la casella di controllo Metriche.In the **Resource group** drop-down list, select the **Virtual Machines** checkbox, and in the Signal **type** drop-down list, select the **Metrics checkbox.** 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/filter-alerts.png)

4. Nell'elenco delle metriche, identificare le condizioni relative ai dischi. Fare clic sul nome della regola. 

   Il nome viene visualizzato come collegamento ipertestuale nella colonna **Nome** della tabella.

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/find-disk-conditions.png)

5. Nella sezione **Condizioni** della pagina **Gestione regole** fare clic sulla condizione dell'avviso. 

   La condizione viene visualizzata come collegamento ipertestuale.  

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/adjust-condition.png)

   Viene visualizzata la pagina **Configura logica del segnale** e le impostazioni della condizione vengono visualizzate nella sezione Logica di **avviso** della pagina.

6. Fai un record di queste impostazioni man mano che scompariranno quando rimuovi la metrica deprecata.

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/condition-rules.png)

   > [!TIP] 
   > Prendi in considerazione l'acquisizione di queste impostazioni in uno screenshot o in un file di testo. 

7. Fare clic sul collegamento **Indietro per la selezione del segnale.**

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/back-to-signal-selection.png)

8. Nella pagina **Configura logica del segnale** scegliere la metrica di sostituzione appropriata (nuova metrica). Usare la [tabella](#update-metrics) visualizzata in precedenza in questo articolo per identificare il nome della nuova metrica.

   > [!TIP] 
   > Iniziare a digitare nella barra di ricerca per restringere l'elenco dei nomi delle metriche. 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/choose-new-metric.png)

9. Scegliere il pulsante **Fine.** 

   > [!div class="mx-imgBorder"]
   > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/set-new-metric.png)

10. Eseguire il commit delle modifiche scegliendo il pulsante **Salva.** 

    > [!div class="mx-imgBorder"]
    > ![Descrizione immagine](./media/portal-disk-metrics-deprecation/save-new-metric.png)






