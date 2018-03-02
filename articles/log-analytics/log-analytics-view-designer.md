---
title: Creare visualizzazioni per analizzare i dati in Azure Log Analytics | Microsoft Docs
description: Progettazione visualizzazioni in Log Analytics consente di creare visualizzazioni personalizzate, disponibili nel portale di Azure, che includono una serie di visualizzazioni dati nell'area di lavoro di Log Analytics. Questo articolo include una panoramica su Progettazione visualizzazioni e sulle procedure per la creazione e la modifica delle visualizzazioni personalizzate.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: 08d0e557f03f771901c9ac92fb080e74e5966452
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Creare visualizzazioni personalizzate usando Progettazione visualizzazioni in Log Analytics
Tramite Progettazione visualizzazioni in [Azure Log Analytics](log-analytics-overview.md) è possibile creare nel portale di Azure una serie di visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics. Questo articolo presenta una panoramica su Progettazione visualizzazioni e sulle procedure per la creazione e la modifica delle visualizzazioni personalizzate.

Per altre informazioni su Progettazione visualizzazioni, vedere:

* [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md): offre una guida di riferimento sulle impostazioni relative a ogni riquadro disponibile nelle visualizzazioni personalizzate.
* [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md): offre una guida di riferimento sulle impostazioni relative alle parti della visualizzazione disponibili nelle visualizzazioni personalizzate.

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), le query in tutte le visualizzazioni devono essere scritte nel [nuovo linguaggio di query](https://go.microsoft.com/fwlink/?linkid=856078). Le visualizzazioni create prima dell'aggiornamento dell'area di lavoro vengono convertite automaticamente.

## <a name="concepts"></a>Concetti
Le visualizzazioni vengono mostrate nella pagina **Panoramica** dell'area di lavoro Log Analytics nel portale di Azure. I riquadri in ogni visualizzazione personalizzata vengono mostrati in ordine alfabetico e i riquadri per le soluzioni vengono installati nella stessa area di lavoro.

![Pagina di panoramica](media/log-analytics-view-designer/overview-page.png)

Le visualizzazioni create con Progettazione visualizzazioni contengono gli elementi descritti nella tabella seguente:

| Parte: | DESCRIZIONE |
|:--- |:--- |
| Riquadri | Sono disponibili nella pagina **Panoramica** dell'area di lavoro di Log Analytics. Ogni riquadro mostra un riepilogo visivo della visualizzazione personalizzata che rappresenta. Ciascun tipo di riquadro offre una visualizzazione diversa dei record. Selezionare un riquadro per mostrare una visualizzazione personalizzata. |
| Visualizzazione personalizzata | Viene mostrata quando si seleziona un riquadro. Ciascuna visualizzazione contiene una o più parti della visualizzazione. |
| Parti della visualizzazione | Presentano una visualizzazione dei dati nell'area di lavoro di Log Analytics in base a una o più [ricerche log](log-analytics-log-searches.md). La maggior parte delle parti include un'intestazione, che offre una visualizzazione di alto livello, e un elenco che mostra i risultati principali. Ciascun tipo di parte offre una visualizzazione diversa dei record nell'area di lavoro di Log Analytics. Selezionare gli elementi della parte per eseguire una ricerca log che restituisca record dettagliati. |


## <a name="work-with-an-existing-view"></a>Usare una visualizzazione esistente
Le visualizzazioni create con Progettazione visualizzazioni mostrano le opzioni seguenti:

![Menu della panoramica](media/log-analytics-view-designer/overview-menu.png)

Le opzioni sono descritte nella tabella seguente:

| Opzione | DESCRIZIONE |
|:--|:--|
| Aggiorna   | Aggiorna la visualizzazione con i dati più recenti. | 
| Analytics | Apre il [portale Advanced Analytics](log-analytics-log-search-portals.md#advanced-analytics-portal) per analizzare i dati con ricerche log. |
| Filtro    | Imposta un filtro temporale per i dati inclusi nella visualizzazione. |
| Modificare      | Apre la visualizzazione in Progettazione visualizzazioni per modificarne il contenuto e la configurazione.  |
| Clone     | Crea una nuova visualizzazione e la apre in Progettazione visualizzazioni. La nuova visualizzazione ha lo stesso nome dell'originale, con l'aggiunta di *Copia*. |


## <a name="create-a-new-view"></a>Creazione di una nuova vista
È possibile creare una nuova visualizzazione in Progettazione visualizzazioni selezionando il riquadro **Progettazione visualizzazioni** nella pagina **Panoramica** dell'area di lavoro di Log Analytics.

![Riquadro Progettazione viste](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Uso di Progettazione visualizzazioni
Progettazione visualizzazioni consente di creare nuove visualizzazioni o di modificare quelle esistenti. 

Progettazione visualizzazioni presenta tre sezioni: 
* **Progettazione**: contiene la visualizzazione personalizzata che si sta creando o modificando. 
* **Controlli**: contiene le parti e i riquadri aggiunti alla sezione **Progettazione**. 
* **Proprietà**: mostra le proprietà dei riquadri o delle parti selezionate.

![Progettazione viste](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurare il riquadro della visualizzazione
Una vista personalizzata può avere solo un singolo riquadro. Per visualizzare il riquadro corrente o selezionarne uno alternativo, selezionare la scheda **Riquadro** nella sezione **Controllo**. La sezione **Proprietà** mostra le proprietà del riquadro corrente. 

È possibile configurare le proprietà del riquadro in base alle informazioni contenute in [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md) e quindi fare clic su **Applica** per salvare le modifiche.

### <a name="configure-the-visualization-parts"></a>Configurare le parti della visualizzazione
Una vista può includere qualsiasi numero di parti della visualizzazione. Per aggiungere parti a una visualizzazione, selezionare la scheda **Visualizzazione** e quindi una parte della visualizzazione. La sezione **Proprietà** mostra le proprietà della parte selezionata. 

È possibile configurare le proprietà della visualizzazione in base alle informazioni contenute in [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md) e quindi fare clic su **Applica** per salvare le modifiche.

Le visualizzazioni hanno solo una riga per le parti della visualizzazione. È possibile ridisporre le parti esistenti trascinandole in una nuova posizione.

È possibile rimuovere una parte della visualizzazione selezionando **X** nell'angolo superiore destro della parte.


### <a name="menu-options"></a>Opzioni del menu
Le opzioni per l'uso delle visualizzazioni in modalità di modifica sono descritte nella tabella seguente.

![Menu Modifica](media/log-analytics-view-designer/edit-menu.png)

| Opzione | DESCRIZIONE |
|:--|:--|
| Salva        | Salva le modifiche e chiude la visualizzazione. |
| Annulla      | Annulla le modifiche e chiude la visualizzazione. |
| Eliminare la visualizzazione | Elimina la visualizzazione. |
| Esportazione      | Esporta la visualizzazione in un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) da importare in un'altra area di lavoro. Il nome del file corrisponde al nome della visualizzazione e ha estensione *omsview*. |
| Importa      | Importa il file *omsview* esportato da un'altra area di lavoro. Questa azione sovrascrive la configurazione della visualizzazione esistente. |
| Clone       | Crea una nuova visualizzazione e la apre in Progettazione visualizzazioni. La nuova visualizzazione ha lo stesso nome dell'originale, con l'aggiunta di *Copia*. |
| Pubblica     | Esporta la visualizzazione in un file con estensione json da inserire in una [soluzione di gestione](../operations-management-suite/operations-management-suite-solutions-resources-views.md). Il file ha lo stesso nome della visualizzazione, ma ha estensione *json*. Un secondo file con estensione *resjson* include i valori delle risorse definite nel file con estensione json.

## <a name="next-steps"></a>Passaggi successivi
* Aggiungere [Riquadri](log-analytics-view-designer-tiles.md) alla vista personalizzata.
* Aggiungere [parti della visualizzazione](log-analytics-view-designer-parts.md) alla visualizzazione personalizzata.
