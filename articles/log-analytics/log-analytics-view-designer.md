---
title: Creare viste per analizzare i dati in Log Analytics di OMS | Documentazione Microsoft
description: "Progettazione viste di Log Analytics consente di creare viste personalizzate che vengono visualizzate nel portale di OMS e di Azure e contengono visualizzazioni diverse dei dati nel repository di OMS. In questo articolo è presentata una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate."
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
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 4d6ea516717077ffb4b832caa9e8a064905fce09
ms.openlocfilehash: e101972777e45bcf3b49b113c6fd92d220ad43fc


---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Usare Progettazione viste per creare viste personalizzate in Log Analytics
Progettazione viste di [Log Analytics]() consente di creare viste personalizzate nella console di OMS contenenti visualizzazioni diverse dei dati nel repository di OMS. In questo articolo è presentata una panoramica su Progettazione viste e sulle procedure per creare e modificare viste personalizzate.

Altri articoli disponibili su Progettazione viste sono:

* [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate. 
* [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md): informazioni di riferimento sulle impostazioni relative a ciascun riquadro disponibile per l'uso nelle viste personalizzate. 

## <a name="concepts"></a>Concetti
Le viste create con Progettazione viste contengono gli elementi riportati nella tabella seguente.

| Parte: | Descrizione |
|:--- |:--- |
| Riquadro |Viene visualizzato nel dashboard principale di panoramica di Log Analytics.  Include un riepilogo visivo delle informazioni contenute nella vista personalizzata.  Diversi tipi di riquadri forniscono diverse visualizzazioni di record nel repository di OMS.  Fare clic sul riquadro per aprire la vista personalizzata. |
| Vista personalizzata |Viene visualizzato quando l'utente fa clic sul riquadro.  Contiene una o più parti della visualizzazione. |
| Parti della visualizzazione |La visualizzazione dei dati nel repository di OMS in base a una o più [ricerche nei log](log-analytics-log-searches.md).  Le parti includeranno perlopiù un'intestazione che offre una visualizzazione di alto livello e un elenco dei risultati principali.  Diversi tipi di parti forniscono diverse visualizzazioni di record nel repository di OMS.  Fare clic sugli elementi della parte per eseguire una ricerca nei log che restituisca record dettagliati. |

![Panoramica di Progettazione viste](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Aggiunta di Progettazione viste all'area di lavoro
Mentre Progettazione viste è in modalità di anteprima, è necessario aggiungerla all'area di lavoro selezionando le **funzionalità di anteprima** nella sezione **Impostazioni** del portale di OMS.

![Abilitazione dell'anteprima](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Creazione e modifica di viste
### <a name="create-a-new-view"></a>Creazione di una nuova vista
Aprire una nuova vista in **Progettazione viste** facendo clic sul riquadro Progettazione viste nel dashboard principale di OMS.

![Riquadro Progettazione viste](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Modifica di una vista esistente
Per modificare una vista esistente in Progettazione viste, aprirla facendo clic sul riquadro nel dashboard principale di OMS.  Fare clic sul pulsante **Modifica** per aprire la vista in Progettazione viste.

![Modifica di una vista](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonazione di una vista esistente
Quando si clona una vista, viene creata una nuova vista che viene aperta in Progettazione viste.  La nuova vista avrà lo stesso nome dell'originale, con l'aggiunta di "Copia" alla fine.  Per clonare una vista, aprire quella esistente facendo clic sul relativo riquadro nel dashboard principale di OMS.  Fare clic sul pulsante **Clona** per aprire la vista in Progettazione viste.

![Clonazione di una vista](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Eliminazione di una vista esistente
Per eliminare una vista esistente, aprirla facendo clic sul relativo riquadro nel dashboard principale di OMS.  Fare clic sul pulsante **Modifica** per aprire la vista in Progettazione viste, quindi selezionare **Elimina vista**.

![Eliminazione di una vista](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Esportazione di una vista esistente
È possibile esportare una vista in un file JSON che può essere importato in un'altra area di lavoro o usare in un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).  Per esportare una vista esistente, aprirla facendo clic sul relativo riquadro nel dashboard principale di OMS.  Fare clic sul pulsante **Esporta** per creare un file nella cartella di download del browser.  Il nome del file corrisponderà al nome della vista con estensione *omsview*.

![Esportazione di una vista](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importazione di una vista esistente
È possibile importare un file *omsview* precedentemente esportato da un altro gruppo di gestione.  Per importare una vista esistente, creare innanzitutto una nuova vista.  Fare clic sul pulsante **Importa** e selezionare il file *omsview*.  La configurazione nel file verrà copiata nella vista esistente.

![Esportazione di una vista](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Uso di Progettazione viste
Progettazione viste presenta tre riquadri.  Il riquadro **Progettazione** rappresenta la vista personalizzata.  Quando si aggiungono riquadri e parti da **Controllo** a **Progettazione**, vengono aggiunti anche alla vista.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per il riquadro o la parte selezionati.

![Progettazione viste](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configurazione del riquadro di vista
Una vista personalizzata può avere solo un singolo riquadro.  Selezionare la scheda **Riquadro** in **Controllo** per visualizzare il riquadro corrente o selezionarne uno alternativo.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per il riquadro corrente.  Configurare le proprietà del riquadro in base alle informazioni dettagliate in [Informazioni di riferimento sul riquadro](log-analytics-view-designer-tiles.md) e fare clic su **Applica** per salvare le modifiche.

### <a name="configure-visualization-parts"></a>Configurazione delle parti della visualizzazione
Una vista può includere qualsiasi numero di parti della visualizzazione.  Selezionare il riquadro **Vista**, quindi una parte della visualizzazione da aggiungere alla vista.  Nel riquadro **Proprietà** verranno visualizzate le proprietà per la parte selezionata.  Configurare le proprietà della vista in base alla informazioni dettagliate in [Informazioni di riferimento sulle parti della visualizzazione](log-analytics-view-designer-parts.md) e fare clic su **Applica** per salvare le modifiche.

### <a name="delete-a-visualization-part"></a>Eliminazione di una parte della visualizzazione
È possibile rimuovere una parte della visualizzazione dalla vista facendo clic sul pulsante **X** nell'angolo superiore destro della parte.

### <a name="rearrange-visualization-parts"></a>Ridisposizione delle parti della visualizzazione
Le viste hanno solo di una riga di parti della visualizzazione.  Ridisporre le parti esistenti in una vista facendo clic e trascinandole in una nuova posizione.

## <a name="next-steps"></a>Passaggi successivi
* Aggiungere [Riquadri](log-analytics-view-designer-tiles.md) alla vista personalizzata.
* Aggiungere [parti della visualizzazione](log-analytics-view-designer-parts.md) alla vista personalizzata.




<!--HONumber=Jan17_HO4-->


