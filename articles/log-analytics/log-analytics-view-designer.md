---
title: Creare visualizzazioni per analizzare i dati in Azure Log Analytics | Microsoft Docs
description: "Progettazione viste di Log Analytics consente di creare visualizzazioni personalizzate che vengono visualizzate nel portale di Azure e contengono visualizzazioni dei dati diverse rispetto all'area di lavoro di Log Analytics. In questo articolo è presentata una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate."
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
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Usare Progettazione viste per creare viste personalizzate in Log Analytics
Progettazione viste di [Log Analytics](log-analytics-overview.md) consente di creare viste personalizzate nel portale di Azure che contengono visualizzazioni diverse dei dati rispetto all'area di lavoro di Log Analytics. In questo articolo è presentata una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate.

Altri articoli disponibili su Progettazione viste sono:

* [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate.
* [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate.

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), le query in tutte le viste devono essere scritte nel [nuovo linguaggio di query](https://go.microsoft.com/fwlink/?linkid=856078).  Le viste create prima dell'aggiornamento dell'area di lavoro verranno automaticamente convertite.

## <a name="concepts"></a>Concetti
Le visualizzazioni vengono mostrate nella pagina **Panoramica** dell'area di lavoro Log Analytics nel portale di Azure.  Il riquadro per ogni visualizzazione personalizzata verrà mostrato in ordine alfabetico assieme i riquadri per le soluzioni installate nella stessa area di lavoro.

![Pagina di panoramica](media/log-analytics-view-designer/overview-page.png)

Le viste create con Progettazione viste contengono gli elementi riportati nella tabella seguente.

| Parte: | DESCRIZIONE |
|:--- |:--- |
| Riquadro |Visualizzato nella pagina Panoramica dell'area di lavoro Log Analytics.  Include un riepilogo visivo delle informazioni contenute nella vista personalizzata.  Diversi tipi di riquadri forniscono diverse visualizzazioni di record.  Fare clic sul riquadro per aprire la vista personalizzata. |
| Vista personalizzata |Viene visualizzato quando l'utente fa clic sul riquadro.  Contiene una o più parti della visualizzazione. |
| Parti della visualizzazione |Visualizzazione dei dati nell'area di lavoro di Log Analytics in base a una o più [ricerche nei log](log-analytics-log-searches.md).  Le parti includeranno perlopiù un'intestazione che offre una visualizzazione di alto livello e un elenco dei risultati principali.  Diversi tipi di parti forniscono diverse visualizzazioni di record nell'area di lavoro di Log Analytics.  Fare clic sugli elementi della parte per eseguire una ricerca nei log che restituisca record dettagliati. |


## <a name="work-with-an-existing-view"></a>Usare una visualizzazione esistente
Quando si apre una visualizzazione creata con Progettazione viste, questa disporrà di un menu con le opzioni contenute nella tabella seguente.

![Menu della panoramica](media/log-analytics-view-designer/overview-menu.png)


| Opzione | DESCRIZIONE |
|:--|:--|
| Aggiorna   | Aggiornare la visualizzazione con i dati più recenti. | 
| Analytics | Aprire il [portale Advanced Analytics](log-analytics-log-search-portals.md#advanced-analytics-portal) per analizzare i dati con ricerche nei log (log-analytics-log-search-portals.md#advanced-analytics-portal). |
| Filtro    | Impostare un filtro temporale per i dati inclusi nella visualizzazione. |
| Modificare      | Aprire la visualizzazione in Progettazione viste per modificarne il contenuto e la configurazione.   |
| Clone     | Creare una nuova visualizzazione e aprirla in Progettazione viste.  La nuova visualizzazione avrà lo stesso nome dell'originale, con l'aggiunta di "Copia" alla fine. |


## <a name="create-a-new-view"></a>Creazione di una nuova vista
Creare una nuova visualizzazione in **Progettazione viste** facendo clic sul riquadro Progettazione viste nella pagina Panoramica dell'area di lavoro Log Analytics nel portale di Azure.

![Riquadro Progettazione viste](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Uso di Progettazione viste
Si userà Progettazione viste per creare una nuova visualizzazione o modificarne una esistente.  

Progettazione viste presenta tre riquadri.  Il riquadro **Progettazione** contiene la visualizzazione personalizzata che si sta creando o modificando.  Riquadri e parti vengono aggiunti dal riquadro **Controllo** al riquadro **Progettazione**.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per il riquadro o la parte selezionati.

![Progettazione viste](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurazione del riquadro di vista
Una vista personalizzata può avere solo un singolo riquadro.  Selezionare la scheda **Riquadro** in **Controllo** per visualizzare il riquadro corrente o selezionarne uno alternativo.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per il riquadro corrente.  Configurare le proprietà del riquadro in base alle informazioni dettagliate in [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md) e fare clic su **Applica** per salvare le modifiche.

### <a name="configure-visualization-parts"></a>Configurazione delle parti della visualizzazione
Una vista può includere qualsiasi numero di parti della visualizzazione.  Selezionare il riquadro **Vista**, quindi una parte della visualizzazione da aggiungere alla vista.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per la parte selezionata.  Configurare le proprietà della vista in base alla informazioni dettagliate in [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md) e fare clic su **Applica** per salvare le modifiche.

Le viste hanno solo di una riga di parti della visualizzazione.  Ridisporre le parti esistenti in una vista facendo clic e trascinandole in una nuova posizione.

È possibile rimuovere una parte della visualizzazione dalla vista facendo clic sul pulsante **X** nell'angolo superiore destro della parte.


### <a name="menu-options"></a>Opzioni del menu
Quando si lavora con una visualizzazione in modalità di modifica, sono disponibili le opzioni di menu descritte nella tabella seguente.

![Menu Modifica](media/log-analytics-view-designer/edit-menu.png)

| Opzione | DESCRIZIONE |
|:--|:--|
| Salva        | Salvare le modifiche e chiudere la visualizzazione. |
| Annulla      | Rimuovere le modifiche e chiudere la visualizzazione. |
| Eliminare la visualizzazione | Eliminare la visualizzazione. |
| Esportazione      | Esportare la visualizzazione in un [modello di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) da importare in un'altra area di lavoro.  Il nome del file corrisponderà al nome della vista con estensione *omsview*. |
| Importa      | Importare un file *omsview* precedentemente esportato da un'altra area di lavoro.  La configurazione della visualizzazione esistente verrà sovrascritta. |
| Clone       | Creare una nuova visualizzazione e aprirla in Progettazione viste.  La nuova visualizzazione avrà lo stesso nome dell'originale, con l'aggiunta di "Copia" alla fine. |
| Pubblica     | Esportare la visualizzazione in un file JSON da inserire in una [soluzione di gestione](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  Il nome del file corrisponderà al nome della visualizzazione con estensione *json*. Verrà creato un secondo file con estensione *resjson* che include i valori delle risorse definite nel file JSON.

## <a name="next-steps"></a>Passaggi successivi
* Aggiungere [Riquadri](log-analytics-view-designer-tiles.md) alla vista personalizzata.
* Aggiungere [parti della visualizzazione](log-analytics-view-designer-parts.md) alla vista personalizzata.
