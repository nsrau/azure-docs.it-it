---
title: Gestire le istanze di avviso in Monitoraggio di AzureManage alert instances in Azure Monitor
description: Gestione delle istanze di avviso in Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667619"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gestire le istanze degli avvisi con avvisi unificatiManage alert instances with unified alerts

Con l'esperienza degli [avvisi unificati](https://aka.ms/azure-alerts-overview) in Monitoraggio di Azure, è possibile visualizzare tutti i diversi tipi di avvisi in Azure.With the unified alerts experience in Azure Monitor, you can see all your different types of alerts across Azure. Si estende su più sottoscrizioni in un singolo riquadro. In questo articolo viene illustrato come visualizzare le istanze di avviso e come trovare istanze di avviso specifiche per la risoluzione dei problemi.

> [!NOTE]
> È possibile accedere agli avvisi generati solo negli ultimi 30 giorni.

## <a name="go-to-the-alerts-page"></a>Vai alla pagina degli avvisi

È possibile passare alla pagina degli avvisi in uno dei modi seguenti:

- Nel [portale di Azure](https://portal.azure.com/)selezionare **Monitora** > **avvisi**.  

     ![Screenshot di Monitor Avvisi](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Utilizzare il contesto di una risorsa specifica. Aprire una risorsa, passare alla sezione **Monitoraggio** e scegliere **Avvisi**. La pagina di destinazione viene prefiltrata per gli avvisi relativi a tale risorsa specifica.

     ![Schermata degli avvisi di monitoraggio delle risorse](media/alerts-managing-alert-instances/alert-resource.JPG)

- Usare il contesto di un gruppo di risorse specifico. Aprire un gruppo di risorse, passare alla sezione **Monitoraggio** e scegliere **Avvisi**. La pagina di destinazione viene prefiltrata per gli avvisi relativi a tale gruppo di risorse specifico.    

     ![Schermata del gruppo di risorse Monitoraggio avvisi](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Trovare istanze di avviso

La pagina Riepilogo avvisi offre una panoramica di tutte le istanze di avviso in Azure.The **Alerts Summary** page gives you an overview of all your alert instances across Azure. È possibile modificare la visualizzazione di riepilogo selezionando **più sottoscrizioni** (fino a un massimo di 5) oppure filtrando tra gruppi di **risorse,** **risorse**specifiche o **intervalli di tempo**. Selezionare **Avvisi totali**o una delle bande di gravità per passare alla visualizzazione elenco degli avvisi.     

![Screenshot della pagina Riepilogo avvisi](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Nella pagina **Tutti gli avvisi** sono elencate tutte le istanze di avviso in Azure.On the All Alerts page, all the alert instances across Azure are listed. Se si arriva al portale da una notifica di avviso, è possibile usare i filtri disponibili per limitare l'ambito su tale istanza di avviso specifico.

> [!NOTE]
> Se si è venuti alla pagina selezionando una delle bande di gravità, l'elenco viene pre-filtrato per tale gravità.

Oltre ai filtri disponibili nella pagina precedente, è anche possibile filtrare in base al servizio di monitoraggio (ad esempio, piattaforma per le metriche), condizione di monitoraggio (attivata o risolta), gravità, stato di avviso (nuovo/riconosciuto/chiuso) o l'ID del gruppo intelligente.

![Screenshot della pagina Tutti gli avvisi](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Se si è venuti alla pagina selezionando una delle bande di gravità, l'elenco viene pre-filtrato per tale gravità.

Se si seleziona un'istanza di avviso, viene aperta la pagina **Dettagli avviso,** che consente di visualizzare ulteriori dettagli sull'istanza di avviso specifica.   

![Schermata della pagina Dettagli avviso](media/alerts-managing-alert-instances/alert-details.jpg)  

