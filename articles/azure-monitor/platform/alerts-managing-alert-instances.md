---
title: Gestire le istanze di avviso in monitoraggio di Azure
description: Gestione delle istanze di avviso in Azure
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 955ed75f8253798e6970c02b0abf5794298ae6da
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555632"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gestire le istanze di avviso con avvisi unificati
Con l' [esperienza degli avvisi unificati](https://aka.ms/azure-alerts-overview) in monitoraggio di Azure, è possibile visualizzare tutti i diversi tipi di avvisi in Azure. Questa operazione si estende su più sottoscrizioni in un unico riquadro. In questo articolo viene illustrato come visualizzare le istanze di avviso e come individuare specifiche istanze di avviso per la risoluzione dei problemi.

> [!NOTE]
   >  È possibile accedere solo agli avvisi generati negli ultimi 30 giorni.

## <a name="go-to-the-alerts-page"></a>Vai alla pagina degli avvisi

È possibile accedere alla pagina degli avvisi in uno dei modi seguenti:

   + Nella [portale di Azure](https://portal.azure.com/)selezionare **monitoraggio**  > **avvisi**.  
     ![Screenshot degli avvisi di monitoraggio ](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Usare il contesto di una risorsa specifica. Aprire una risorsa, passare alla sezione **monitoraggio** e scegliere **avvisi**. La pagina di destinazione viene pre-filtrata per gli avvisi relativi a tale risorsa specifica.
   
     ![Screenshot degli avvisi di monitoraggio delle risorse](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Usare il contesto di un gruppo di risorse specifico. Aprire un gruppo di risorse, passare alla sezione **monitoraggio** e scegliere **avvisi**. La pagina di destinazione è prefiltrata per gli avvisi relativi al gruppo di risorse specifico.    
   
     ![Screenshot degli avvisi di monitoraggio del gruppo di risorse](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Trova istanze di avviso

La pagina di **Riepilogo degli avvisi** offre una panoramica di tutte le istanze di avviso in Azure. È possibile modificare la visualizzazione di riepilogo selezionando **più sottoscrizioni** (fino a un massimo di 5) o filtrando tra **gruppi di risorse**, **risorse**specifiche o intervalli di **tempo**. Selezionare **avvisi totali**o una qualsiasi delle bande di gravità per passare alla visualizzazione elenco per gli avvisi.     
   ![Screenshot della pagina Riepilogo avvisi ](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Nella pagina **tutti gli avvisi** sono elencate tutte le istanze di avviso in Azure. Se si arriva al portale da una notifica di avviso, è possibile usare i filtri disponibili per limitare l'ambito su tale istanza di avviso specifico.

> [!NOTE]
>  Se si è arrivati alla pagina selezionando una delle bande di gravità, l'elenco viene pre-filtrato per tale gravità.

Oltre ai filtri disponibili nella pagina precedente, è anche possibile filtrare in base al servizio di monitoraggio (ad esempio, la piattaforma per le metriche), la condizione di monitoraggio (attivata o risolta), la gravità, lo stato di avviso (nuovo/riconosciuto/chiuso) o l'ID del gruppo intelligente.

   ![Screenshot della pagina tutti gli avvisi](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Se si è arrivati alla pagina selezionando una delle bande di gravità, l'elenco viene pre-filtrato per tale gravità.
 
Selezionando un'istanza di avviso, viene visualizzata la pagina **Dettagli avviso** che consente di visualizzare altri dettagli sull'istanza di avviso specifica.   
   ![Screenshot della pagina dei dettagli dell'avviso ](media/alerts-managing-alert-instances/alert-details.jpg)  

