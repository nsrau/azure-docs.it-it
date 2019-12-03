---
title: Controllare il traffico con gestione traffico
description: Trovare le procedure consigliate per la configurazione di gestione traffico di Azure quando si integra con app Azure servizio.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 200effab70b369d69b4e89b1901578ecfe1a1b87
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684095"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Controllo del traffico del servizio app di Azure con Gestione traffico di Azure
> [!NOTE]
> In questo articolo vengono fornite informazioni di riepilogo per Gestione traffico di Azure di Microsoft in relazione al servizio app di Azure. Per ulteriori informazioni su Gestione traffico di Azure, visitare i collegamenti forniti alla fine di questo articolo.
> 
> 

## <a name="introduction"></a>Introduzione
È possibile usare Gestione traffico di Azure per controllare il modo in cui le richieste dai client Web vengono distribuite alle app nel servizio app di Azure. Quando si aggiungono endpoint del servizio app a un profilo di Gestione traffico di Azure, quest'ultimo tiene traccia dello stato delle app del servizio app (in esecuzione, arrestate o eliminate) in modo da poter decidere quali di questi endpoint devono ricevere il traffico.

## <a name="routing-methods"></a>Metodi di routing
Gestione traffico di Azure usa quattro metodi di routing diversi. Questi metodi vengono descritti nell'elenco seguente, in quanto pertinenti al servizio app di Azure.

* **[Priorità](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** per usare un'app principale per tutto il traffico e fornire dei backup se l'app principale o quelle di backup non sono disponibili.
* **[Ponderato](../traffic-manager/traffic-manager-routing-methods.md#weighted):** per distribuire il traffico in un set di app in modo uniforme o in base alle ponderazioni definite.
* **[Prestazioni](../traffic-manager/traffic-manager-routing-methods.md#performance):** quando si dispone di app in posizioni geografiche diverse, usare l'app "più vicina" in termini di latenza di rete più bassa.
* **[Geografico](../traffic-manager/traffic-manager-routing-methods.md#geographic):** per indirizzare gli utenti ad app specifiche in base alla posizione geografica da cui ha origine la query DNS. 

Per altre informazioni, vedere [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Servizio app e profili di Gestione traffico
Per configurare il controllo del traffico delle app del servizio app, creare un profilo in gestione traffico di Azure che usa uno dei quattro metodi di bilanciamento del carico descritti in precedenza e quindi aggiungere gli endpoint (in questo caso, il servizio app) per i quali si vuole controllare il traffico al profilo. Lo stato dell'app (in esecuzione, interrotta o eliminata) viene comunicato regolarmente al profilo in modo che Gestione traffico di Azure possa instradare il traffico di conseguenza.

Quando si utilizza Gestione traffico con Azure, è opportuno tenere presenti i fattori seguenti:

* Per le distribuzioni di sole app all'interno della stessa area, il servizio app fornisce già la funzionalità di failover e round-robin che non tengono conto della modalità delle app.
* Per distribuzioni nella stessa area in cui il servizio app viene utilizzato insieme a un altro servizio cloud di Azure, è possibile combinare entrambi i tipi di endpoint per abilitare scenari ibridi.
* È possibile specificare solo un endpoint del servizio app per area in un profilo. Quando si seleziona un'app come endpoint per un'area, le app rimanenti in quell'area non saranno più disponibili per la selezione per quel profilo.
* Gli endpoint del servizio app che si specificano in un profilo di Gestione traffico di Azure vengono visualizzati nella sezione **Nomi di dominio** della pagina Configura per le app nel profilo, ma non sono configurabili in quel contesto.
* Dopo avere aggiunto un'app a un profilo, nel campo **URL del sito** nel dashboard della pagina del portale dell'app viene visualizzato l'URL del dominio personalizzato dell'app, se ne è stato configurato uno. In caso contrario, viene visualizzato l'URL del profilo di Gestione traffico, ad esempio `contoso.trafficmanager.net`. Sia il nome di dominio diretto dell'app sia l'URL di Gestione traffico sono visibili nella pagina Configura dell'app nella sezione **Nomi di dominio**.
* I nomi di dominio personalizzato funzionano come previsto, ma oltre ad aggiungerli alle app, è necessario configurare la mappa DNS in modo che punti all'URL di Gestione traffico. Per informazioni sulla configurazione di un dominio personalizzato per un'app del servizio app, vedere [Eseguire il mapping di un nome DNS personalizzato esistente con il Servizio app di Azure](app-service-web-tutorial-custom-domain.md).
* È possibile aggiungere a un profilo di Gestione traffico di Azure solo app in modalità Standard o Premium.

## <a name="next-steps"></a>Fasi successive
Per una panoramica concettuale e tecnica di Gestione traffico di Azure, vedere [Panoramica di Gestione traffico](../traffic-manager/traffic-manager-overview.md).


