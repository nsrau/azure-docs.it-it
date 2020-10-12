---
title: Risoluzione dei problemi relativi alle informazioni dettagliate basate sulla cartella di lavoro di monitoraggio di Azure
description: Fornisce indicazioni per la risoluzione dei problemi relativi alle informazioni dettagliate basate su cartelle di lavoro di monitoraggio di Azure per servizi quali Azure Key Vault, Azure CosmosDB, archiviazione di Azure e cache di Azure per Redis.
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 90743a213092aacfa10139c12f47bc11faf0d86d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498713"
---
# <a name="troubleshooting-workbook-based-insights"></a>Risoluzione dei problemi relativi alle informazioni dettagliate basate sulla cartella di lavoro

Questo articolo illustra la diagnosi e la risoluzione dei problemi comuni che possono verificarsi quando si usano le informazioni dettagliate basate su cartelle di lavoro di monitoraggio di Azure.


## <a name="why-can-i-only-see-200-resources"></a>Perché è possibile visualizzare solo le risorse 200

Il numero di risorse selezionate ha un limite di 200, indipendentemente dal numero di sottoscrizioni selezionate.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Cosa accade quando si fa clic su un riquadro aggiunto di recente nel dashboard

* Se si fa clic in un punto qualsiasi del riquadro, verrà visualizzata la scheda da cui è stato aggiunto il riquadro. Se, ad esempio, si aggiunge un grafico nella scheda "panoramica", quando si fa clic su tale riquadro nel dashboard verrà aperta la visualizzazione predefinita, tuttavia se si aggiunge un grafo dalla propria copia salvata, verrà aperta la visualizzazione della copia salvata.
* L'icona del filtro nella parte superiore sinistra del titolo apre la scheda "Configura le impostazioni del riquadro".
* L'icona con i puntini di sospensione in alto a destra offre le opzioni per personalizzare i dati del titolo, personalizzare, aggiornare, e rimuovere dal dashboard.

## <a name="what-happens-when-i-save-a-workbook"></a>Cosa accade quando si salva una cartella di lavoro

* Quando si salva una cartella di lavoro, è possibile creare una nuova copia della cartella di lavoro con le modifiche e modificare il titolo. Salvando, la cartella di lavoro non viene sovrascritta. La cartella di lavoro corrente sarà sempre la visualizzazione predefinita.
* Una cartella di lavoro **non salvata** è semplicemente la visualizzazione predefinita.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Perché non vengono visualizzate tutte le sottoscrizioni nel portale

All'avvio il portale mostrerà i dati solo per le sottoscrizioni selezionate. Per modificare le sottoscrizioni selezionate, andare in alto a destra e fare clic sul blocco appunti con l'icona di un filtro. Questa opzione consente di visualizzare la scheda **directory + sottoscrizioni** .

![Directory e sottoscrizione](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Che cos'è l'intervallo di tempo

L'intervallo di tempo mostra i dati di un determinato periodo. Se, ad esempio, l'intervallo di tempo è di 24 ore, vengono visualizzati i dati delle ultime 24 ore.

## <a name="what-is-time-granularity-time-grain"></a>Che cos'è la granularità temporale (granularità temporale)

La granularità temporale è la differenza di tempo tra due punti dati. Se, ad esempio, l'intervallo di tempo è impostato su 1 secondo, le metriche vengono raccolte ogni secondo.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Qual è la granularità temporale dopo che è stata aggiunta una parte delle cartelle di lavoro a un dashboard

La granularità temporale predefinita è impostata su automatica, attualmente non può essere modificata.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Ricerca per categorie modificare l'intervallo di tempo e TimeSpan del passaggio della cartella di lavoro nel dashboard

Per impostazione predefinita, l'intervallo di tempo nel riquadro del dashboard è impostato su 24 ore. Per modificarlo, fare clic sui puntini di sospensione in alto a destra, selezionare **Personalizza dati riquadro**, selezionare "Eseguire l'override delle impostazioni di ora del dashboard a livello del riquadro" e quindi selezionare un intervallo di tempo usando il menu a discesa.  

![Selezionare i puntini di sospensione nell'angolo destro del riquadro e scegliere Personalizza dati riquadro](./media/storage-insights-overview/fqa-data-settings.png)

![Nelle impostazioni del riquadro Configura selezionare l'elenco a discesa TimeSpan per modificare l'intervallo di tempo](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Ricerca per categorie modificare il titolo della cartella di lavoro o un passaggio di cartella di lavoro aggiunto a un dashboard

Il titolo della cartella di lavoro o del passaggio della cartella di lavoro aggiunto a un dashboard mantiene lo stesso nome che aveva nella cartella di lavoro. Per modificare il titolo è necessario salvare una copia della cartella di lavoro. Sarà quindi possibile assegnare un nome alla cartella di lavoro prima di fare clic su Salva.

![Selezionare Salva nella parte superiore per salvare una copia della cartella di lavoro e modificarne il nome](./media/storage-insights-overview/fqa-change-workbook-name.png)

Per modificare il nome di un passaggio nella cartella di lavoro salvata, selezionare modifica sotto il passaggio e quindi selezionare l'ingranaggio nella parte inferiore delle impostazioni.

![Selezionare Modifica nella parte inferiore di un passaggio della cartella di lavoro per aprire le impostazioni](./media/storage-insights-overview/fqa-edit.png)
![Nelle impostazioni selezionare l'ingranaggio in fondo per poter modificare il nome del passaggio](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle cartelle di lavoro degli scenari, vedere [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../platform/workbooks-overview.md), come creare nuovi report esistenti e personalizzarli.
