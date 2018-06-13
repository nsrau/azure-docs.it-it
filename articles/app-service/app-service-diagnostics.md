---
title: Panoramica della diagnostica del servizio app di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi dell'app Web con la diagnostica del servizio app.
keywords: servizio app, servizio app di azure, diagnostica, supporto, app web, risoluzione dei problemi, self-help
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 50e0e9f5edc18aac42ee80e232f70e09736124bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33761888"
---
# <a name="azure-app-service-diagnostics-overview"></a>Panoramica della diagnostica del servizio app di Azure 

Quando si esegue un'applicazione Web, si desidera essere pronti ad affrontare gli eventuali problemi che possono verificarsi, ad esempio gli errori di tipo 500 che indicano agli utenti che il sito è inattivo. La funzionalità di diagnostica del servizio app offre un'esperienza dinamica e interattiva che consente di risolvere i problemi delle app Web senza ricorrere ad alcun intervento di configurazione. Quando vengono rilevati problemi nell'app Web, la funzionalità di diagnostica del servizio app ne evidenzia la natura in modo da velocizzare l'individuazione e la risoluzione del problema. 
 
Sebbene ciò risulti particolarmente utile quando si verificano problemi nell'app Web nelle ultime 24 ore, tutti i grafici di diagnostica saranno disponibili per l'analisi in qualsiasi momento. Altri strumenti di risoluzione dei problemi e collegamenti a documentazione e forum utili sono disponibili nella colonna a destra.

La diagnostica del servizio app funziona non solo per le app in Windows, ma anche per le app in [Linux e contenitori](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro), nell'[ambiente del servizio app](https://docs.microsoft.com/en-us/azure/app-service/environment/intro) e di [Funzioni di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Aprire la diagnostica del servizio app

Per accedere alla diagnostica del servizio app, passare alla app del servizio app o all'ambiente del servizio app nel [portale di Azure](https://portal.azure.com). Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**. 

Per Funzioni di Azure, passare all'app di funzione e nel riquadro di spostamento superiore, fare clic su **Funzionalità della piattaforma** e selezionare **Diagnosticare e risolvi i problemi** nella sezione **Monitoraggio**. 

![Home page](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Controllo di integrità

Se non si conosce la causa del problema dell'app Web oppure se non si ha familiarità con le procedure di risoluzione dei problemi, è consigliabile iniziare con un controllo di integrità. Il controllo di integrità analizza le applicazioni Web e visualizza una panoramica interattiva in cui sono evidenziati i problemi riscontrati, indicando anche le aree in cui intervenire. L'interfaccia intelligente e interattiva fornisce tutti i passaggi necessari per eseguire il processo di risoluzione dei problemi.  

![Controllo di integrità](./media/app-service-diagnostics/HealthCheckup2.png)

Se nelle ultime 24 ore viene rilevato un problema associato a una categoria specifica, è possibile visualizzare il report di diagnostica completo. La diagnostica del servizio app consente tuttavia di visualizzare altri suggerimenti di risoluzione e procedure successive per guidare l'utente nel corso dell'intero processo.

![Risoluzione dei problemi e procedure successive](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Riquadro dei collegamenti

Se si conosce con esattezza il tipo di informazioni da cercare per la risoluzione di un problema specifico, il riquadro dei collegamenti consente di passare direttamente al report di diagnostica completo relativo alla categoria desiderata. Rispetto al controllo di integrità, il riquadro dei collegamenti è un modo più diretto, ma meno dettagliato per accedere alle metriche di diagnostica. Nell'ambito dei tasti di scelta rapida del riquadro, in questo punto sono disponibili anche gli **strumenti di diagnostica**, ovvero strumenti più avanzati che consentono di esaminare i problemi relativi a codice dell'applicazione, lentezza, stringhe di connessione e altro ancora. 

![Riquadro dei collegamenti](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Report di diagnostica

Se sono state richieste altre informazioni dopo aver eseguito un [controllo di integrità](#health-checkup) oppure è stato fatto clic su uno dei collegamenti disponibili nel [riquadro dei collegamenti](#tile-shortcuts), nel report di diagnostica completo verranno visualizzate le metriche più rilevati, sotto forma di grafici, relative alle ultime 24 ore. L'eventuale tempo di inattività dell'app è rappresentato da una barra arancione sotto la sequenza temporale. È possibile selezionare una delle barre arancione per selezionare il tempo di inattività al fine di visualizzare osservazioni relative e le operazioni di risoluzione dei problemi suggerite. 

![Report di diagnostica](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Analisi dei problemi di codice dell'applicazione

Poiché molti problemi di app sono associati a problemi nel codice dell'applicazione, la diagnostica del servizio app si integra con [Application Insights](https://azure.microsoft.com/services/application-insights/) per evidenziare le eccezioni e i problemi di dipendenza da correlare con il tempo di inattività selezionato. Application Insights deve essere abilitato separatamente. 

Per visualizzare le eccezioni e le dipendenze di Application Insights, selezionare i collegamenti del riquadro **Web App Down** (App Web inattive) o **Web App Slow** (App Web lente). 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

