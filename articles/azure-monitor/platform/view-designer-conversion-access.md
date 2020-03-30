---
title: Progettazione visualizzazione di Monitoraggio di Azure per il riepilogo delle conversioni e l'accesso alle cartelle di lavoroAzure Monitor view designer to workbooks conversion summary and access
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658847"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Visualizzare il riepilogo e l'accesso alla conversione da finestra di progettazione alle cartelle di lavoroView designer to workbooks conversion summary and access
[Progettazione viste](view-designer.md) è una funzionalità di Monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro di Log Analytics, con grafici, elenchi e sequenze temporali. Essi vengono gradualmente eliminati e sostituiti con cartelle di lavoro, che forniscono funzionalità aggiuntive. In questo articolo viene descritto in dettaglio come creare un riepilogo generale e le autorizzazioni necessarie per accedere alle cartelle di lavoro.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Creazione del riepilogo dell'area di lavoro dal dashboard di AzureCreating your Workspace Summary from Azure Dashboard
Gli utenti della finestra di progettazione di visualizzazioni potrebbero avere familiarità con la disponibilità di un riquadro Panoramica per rappresentare un set di visualizzazioni. Per mantenere una panoramica visiva come il riepilogo dell'area di lavoro della finestra di progettazione visualizzazioni, le cartelle di lavoro offrono passaggi aggiunti, che possono essere aggiunti al dashboard del portale di [Azure.](../../azure-portal/azure-portal-dashboards.md) Analogamente ai riquadri della panoramica in Riepilogo area di lavoro, gli elementi della cartella di lavoro aggiunti verranno collegati direttamente alla visualizzazione della cartella di lavoro.

È possibile sfruttare l'elevato livello di funzionalità di personalizzazione fornito con i dashboard di Azure, che consente l'aggiornamento automatico, lo spostamento, il ridimensionamento e il filtro aggiuntivo per gli elementi e le visualizzazioni aggiunti. 

![Dashboard](media/view-designer-conversion-access/dashboard.png)

Creare un nuovo dashboard di Azure o selezionare un dashboard esistente per iniziare ad aggiungere elementi cartella di lavoro.

Per aggiungere un singolo elemento, è necessario abilitare l'icona a forma di puntina per il passaggio specifico. A tale scopo, selezionare il pulsante **Modifica** corrispondente per il passaggio, quindi selezionare l'icona a forma di ingranaggio per aprire **Impostazioni avanzate**. Seleziona l'opzione **Mostra sempre l'icona a forma di puntina su questo passaggio**e apparirà un'icona a forma di puntina nell'angolo in alto a destra del passaggio. Questo segnaposto consente di aggiungere visualizzazioni specifiche alla dashboard, ad esempio i riquadri panoramica.

![Passaggio del pin](media/view-designer-conversion-access/pin-step.png)


È inoltre possibile aggiungere più visualizzazioni dalla cartella di lavoro o l'intero contenuto della cartella di lavoro a un dashboard. Per bloccare l'intera cartella di lavoro, selezionare **Modifica** nella barra degli strumenti superiore per attivare o disattivare **La modalità**di modifica . Apparirà un'icona a forma di puntina, che consente di bloccare l'intero elemento della cartella di lavoro o tutti i singoli passaggi e visualizzazioni nella cartella di lavoro.

![Blocca tutto](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Condivisione e visualizzazione delle autorizzazioni 
Le cartelle di lavoro hanno il vantaggio di essere un documento privato o condiviso. Per impostazione predefinita, le cartelle di lavoro salvate verranno salvate in **Report**personali, ovvero solo l'autore può visualizzare la cartella di lavoro.

È possibile condividere le cartelle di lavoro selezionando l'icona **Condividi** dalla barra degli strumenti superiore in **modalità di modifica**. Verrà richiesto di spostare la cartella di lavoro in **Report condivisi**, che genererà un collegamento che fornisce l'accesso diretto alla cartella di lavoro.

Affinché un utente visualizzi una cartella di lavoro condivisa, deve avere accesso sia alla sottoscrizione che al gruppo di risorse in cui viene salvata la cartella di lavoro.

![Accesso basato su abbonamento](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Passaggi successivi

- [Attività comuni](view-designer-conversion-tasks.md)