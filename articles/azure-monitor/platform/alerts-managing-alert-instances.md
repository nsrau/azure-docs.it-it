---
title: Gestire le istanze di avviso
description: Gestione delle istanze di avviso in Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034835"
---
# <a name="manage-alert-instances"></a>Gestire le istanze di avviso
Con l'[esperienza degli avvisi unificati](https://aka.ms/azure-alerts-overview) in Monitoraggio di Azure è ora possibile visualizzare tutti i diversi tipi di avvisi in Azure, estesi su più sottoscrizioni, in un'unica posizione centralizzata. Questo articolo illustra come visualizzare le istanze di avviso e come effettuare ricerche dettagliate nel portale per trovare istanze di avviso specifiche per la risoluzione dei problemi.

> [!NOTE]
   >  È possibile accedere solo agli avvisi generati negli ultimi 30 giorni nell'esperienza utente o tramite le API REST.

1. Esistono tre modi per visualizzare la pagina degli avvisi

   + Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione Monitoraggio scegliere **Avvisi**.  
     ![Monitoraggio](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + È possibile passare agli avvisi dal contesto di una specifica **risorsa**. Una volta che una risorsa è aperta, passare tramite il sommario alla sezione Monitoraggio e scegliere **Avvisi**, con la pagina di destinazione pre-filtrata per gli avvisi specifici della risorsa.
   
     ![Monitoraggio](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + È possibile passare agli avvisi dal contesto di uno specifico **gruppo di risorse**. Una volta che un gruppo di risorse è aperto, passare tramite il sommario alla sezione Monitoraggio e scegliere **Avvisi**, con la pagina di destinazione pre-filtrata per gli avvisi specifici del gruppo di risorse.    
   
     ![Monitoraggio](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Viene visualizzata la pagina **Riepilogo avvisi** che fornisce una panoramica di tutte le istanze degli avvisi in Azure. È possibile modificare la visualizzazione riepilogo, selezionando **più sottoscrizioni** (al massimo 5) o con l'applicazione di un filtro su **gruppi di risorse**, **risorse** specifiche o **intervalli di tempo**. Fare clic su Totale avvisi o su una qualsiasi delle fasce di gravità per passare alla visualizzazione elenco degli avvisi.     
   ![Riepilogo degli avvisi](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Viene visualizzata la pagina **Tutti gli avvisi** in cui è possibile visualizzare l'elenco di tutte le istanze di avviso in Azure. Se si arriva al portale da una notifica di avviso, è possibile usare i filtri disponibili per limitare l'ambito su tale istanza di avviso specifico. (**Nota**: se si arriva alla pagina facendo clic su una delle fasce di gravità, l'elenco sarà pre-filtrato per quel livello di gravità al momento della visualizzazione). Oltre ai filtri disponibili nella pagina precedente, è ora possibile anche filtrare in base al servizio di monitoraggio (ad esempio, la piattaforma per le metriche), condizione di monitoraggio (attivato o risolto), gravità, stato dell'avviso (nuovo/confermato/chiuso) oppure ID del gruppo intelligente.

   ![Tutti gli avvisi](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Se si arriva alla pagina facendo clic su una delle fasce di gravità, l'elenco sarà pre-filtrato per quel livello di gravità al momento della visualizzazione.
 
1. Facendo clic su qualsiasi istanza di avviso si apre la pagina **Dettagli avviso**, che consente di approfondire le informazioni sull'istanza di avviso specifica.   
   ![Dettagli avviso](media/alerts-managing-alert-instances/alert-details.jpg)  

