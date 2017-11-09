---
title: Controllo del traffico delle app Web di Azure con Gestione traffico di Azure
description: Questo articolo fornisce informazioni di riepilogo per Gestione traffico di Azure in relazione alle app Web di Azure.
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 93645aa5765d533b45fe2266f061ad61c0bf45d7
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controllo del traffico delle app Web di Azure con Gestione traffico di Azure
> [!NOTE]
> Questo articolo offre informazioni di riepilogo per Gestione traffico di Microsoft Azure in relazione ad App Web di Azure. Per ulteriori informazioni su Gestione traffico di Azure, visitare i collegamenti forniti alla fine di questo articolo.
> 
> 

## <a name="introduction"></a>Introduzione
È possibile usare Gestione traffico di Azure per controllare il modo in cui le richieste dai client Web vengono distribuite alle app Web in Azure App Service. Quando si aggiungono endpoint di app Web a un profilo di Gestione traffico di Azure, quest'ultimo tiene traccia dello stato delle app Web (in esecuzione, arrestate o eliminate) in modo da poter decidere quali di questi endpoint devono ricevere il traffico.

## <a name="routing-methods"></a>Metodi di routing
Gestione traffico di Azure usa tre metodi di routing diversi. Questi metodi vengono descritti nell'elenco seguente, in quanto pertinenti ad App Web di Azure.

* **[Priorità](#priority):** per usare un'app Web primaria per tutto il traffico e fornire backup nel caso in cui l'app Web primaria o di backup non sia disponibile.
* **[Ponderato](#weighted):** per distribuire il traffico in un set di app Web in modo uniforme o in base alle ponderazioni definite.
* **[Prestazioni](#performance):** quando si dispone di app Web in posizioni geografiche diverse, usare l'app Web "più vicina" in termini di latenza di rete più bassa.
* **[Geografico](#geographic):** per indirizzare gli utenti ad app Web specifiche in base alla posizione geografica da cui ha origine la query DNS. 

Per altre informazioni, vedere [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="web-apps-and-traffic-manager-profiles"></a>App Web e profili di Gestione traffico
Per configurare il controllo del traffico delle app Web, è possibile creare un profilo in Gestione traffico di Azure che utilizzi uno dei tre metodi di bilanciamento del carico, descritti in precedenza, e quindi aggiungere gli endpoint (in questo caso, le app Web) per i quali si desidera controlla il traffico diretto al profilo. Lo stato dell'app Web (in esecuzione, interrotta o eliminata) viene comunicato regolarmente al profilo in modo che Gestione traffico di Azure possa instradare il traffico di conseguenza.

Quando si utilizza Gestione traffico con Azure, è opportuno tenere presenti i fattori seguenti:

* Per le distribuzioni di sole app We all'interno della stessa area, App Web fornisce già la funzionalità di failover e round-robin che non tengono conto della modalità delle app Web.
* Per distribuzioni nella stessa area in cui App Web viene utilizzato insieme a un altro servizio cloud di Azure, è possibile combinare entrambi i tipi di endpoint per abilitare scenari ibridi.
* È possibile specificare solo un endpoint dell'app Web per area in un profilo. Quando si seleziona un'app Web come endpoint per un'area, le app Web rimanenti in quell'area non saranno più disponibili per la selezione per quel profilo.
* Gli endpoint delle app Web che si specificano in un profilo di Gestione traffico di Azure vengono visualizzati nella sezione **Nomi di dominio** della pagina Configura per le app Web nel profilo, ma non sono configurabili in quel contesto.
* Dopo avere aggiunto un'app Web a un profilo, nel campo **URL del sito** nel dashboard della pagina del portale dell'app Web viene visualizzato l'URL del dominio personalizzato dell'app Web, se ne è stato configurato uno. In caso contrario, viene visualizzato l'URL del profilo di Gestione traffico, ad esempio `contoso.trafficmgr.com`. Sia il nome di dominio diretto dell'app Web sia l'URL di Gestione traffico sono visibili nella pagina Configura dell'app Web nella sezione **Nomi di dominio**.
* I nomi di dominio personalizzato funzionano come previsto, ma oltre ad aggiungerli alle app Web, è necessario configurare la mappa DNS in modo che punti all'URL di Gestione traffico. Per informazioni sulla configurazione di un dominio personalizzato per un'app Web di Azure, vedere [Configurazione di un dominio personalizzato per un sito Web di Azure](app-service-web-tutorial-custom-domain.md).
* È possibile aggiungere a un profilo di Gestione traffico di Azure solo app Web in modalità Standard o Premium.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica concettuale e tecnica di Gestione traffico di Azure, vedere [Panoramica di Gestione traffico](../traffic-manager/traffic-manager-overview.md).

Per altre informazioni sull'uso di Gestione traffico con App Web, vedere i post di blog relativi all'[uso di Gestione traffico di Azure con Siti Web di Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e [all'integrazione di Gestione traffico di Azure con Siti Web di Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

