---
title: Riepilogo e accesso alla conversione delle cartelle di lavoro di Progettazione viste di monitoraggio di Azure
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7614dd1d60bad5c124269ae2af02d30a5aacfe3e
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564033"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Riepilogo e accesso alla conversione di Progettazione viste a cartelle di lavoro
[Progettazione](view-designer.md) viste è una funzionalità di monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro log Analytics, con grafici, elenchi e sequenze temporali. Vengono eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo viene illustrato in dettaglio come creare un riepilogo della panoramica e le autorizzazioni necessarie per accedere alle cartelle di lavoro di.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Creazione del riepilogo dell'area di lavoro dal dashboard di Azure
Gli utenti di Progettazione viste possono avere familiarità con un riquadro Panoramica per rappresentare un set di visualizzazioni. Per gestire una panoramica visiva come il riepilogo dell'area di lavoro di Progettazione viste, le cartelle di lavoro offrono passaggi aggiunti, che possono essere aggiunti al [dashboard portale di Azure](../../azure-portal/azure-portal-dashboards.md). Analogamente ai riquadri panoramica nel riepilogo dell'area di lavoro, gli elementi della cartella di lavoro aggiunti vengono collegati direttamente alla visualizzazione della cartella di lavoro.

È possibile sfruttare il livello elevato di funzionalità di personalizzazione fornite con i dashboard di Azure, che consente l'aggiornamento automatico, lo stato di ridimensionamento e l'applicazione di filtri aggiuntivi per gli elementi e le visualizzazioni aggiunti. 

![Screenshot mostra un dashboard di Azure personalizzato con il titolo riepilogo area di lavoro.](media/view-designer-conversion-access/dashboard.png)

Creare un nuovo dashboard di Azure o selezionare un dashboard esistente per iniziare a bloccare gli elementi delle cartelle di lavoro.

Per aggiungere un singolo elemento, è necessario abilitare l'icona del PIN per il passaggio specifico. A tale scopo, selezionare il pulsante **modifica** corrispondente per il passaggio, quindi selezionare l'icona a forma di ingranaggio per aprire **Impostazioni avanzate**. Selezionare l'opzione per **visualizzare sempre l'icona del PIN in questo passaggio**. nell'angolo superiore destro del passaggio verrà visualizzata un'icona di blocco. Questo pin consente di aggiungere visualizzazioni specifiche al dashboard, come i riquadri di panoramica.

![Aggiungi passaggio](media/view-designer-conversion-access/pin-step.png)


È anche possibile aggiungere più visualizzazioni dalla cartella di lavoro o l'intero contenuto della cartella di lavoro a un dashboard. Per aggiungere l'intera cartella di lavoro, selezionare **modifica** nella barra degli strumenti superiore per impostare la **modalità di modifica**. Verrà visualizzata un'icona a forma di puntina, che consente di aggiungere l'intero elemento della cartella di lavoro o tutti i singoli passaggi e visualizzazioni nella cartella di lavoro.

![Aggiungi tutto](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Condivisione e visualizzazione delle autorizzazioni 
Le cartelle di lavoro di hanno il vantaggio di essere un documento privato o condiviso. Per impostazione predefinita, le cartelle di lavoro salvate verranno salvate in **report personali**, vale a dire che solo l'autore può visualizzare questa cartella di lavoro.

È possibile condividere le cartelle di lavoro selezionando l'icona **Condividi** nella barra degli strumenti superiore in **modalità di modifica**. Verrà richiesto di spostare la cartella di lavoro in **report condivisi**, in modo da generare un collegamento che consente di accedere direttamente alla cartella di lavoro.

Per consentire a un utente di visualizzare una cartella di lavoro condivisa, è necessario avere accesso alla sottoscrizione e al gruppo di risorse in cui viene salvata la cartella di lavoro.

![Accesso basato su sottoscrizione](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Passaggi successivi

- [Attività comuni](view-designer-conversion-tasks.md)