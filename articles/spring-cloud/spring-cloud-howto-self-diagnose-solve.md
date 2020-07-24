---
title: Come diagnosticare e risolvere autonomamente i problemi nel cloud Spring di Azure
description: Informazioni su come diagnosticare e risolvere autonomamente i problemi nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1f48cd185f2aa25954627a9ff26f3e59b8e76e7a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037313"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Come diagnosticare e risolvere autonomamente i problemi nel cloud Spring di Azure
La diagnostica cloud Spring di Azure è un'esperienza interattiva che consente di risolvere i problemi dell'app. Non è richiesta alcuna configurazione. Quando si rilevano problemi, la diagnostica cloud Spring di Azure rileva gli errori e fornisce informazioni utili per risolvere il problema.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Istanza del servizio Azure Spring Cloud distribuita. Per iniziare, seguire la guida di [avvio rapido sulla distribuzione di un'app tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart-launch-app-cli.md).
* Almeno un'applicazione già creata nell'istanza del servizio.

## <a name="navigate-to-the-diagnostics-page"></a>Passare alla pagina di diagnostica
1. Accedere al portale di Azure.
2. Passare alla pagina **Panoramica** di Azure Spring Cloud.
3. Aprire **diagnosticare e risolvere i problemi** nel menu sul lato sinistro della pagina.

 ![Diagnostica, Risolvi finestra di dialogo](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Cerca problemi registrati
Per individuare un problema, è possibile eseguire una ricerca digitando una parola chiave o facendo clic su gruppo di soluzioni per esplorare tutti i valori di tale categoria.

 ![Problemi relativi alla ricerca](media/spring-cloud-diagnose/search-detectors.png)

Selezione del **controllo integrità**del server di configurazione, **stato di integrità del server di configurazione**o cronologia degli aggiornamenti del **server** di configurazione visualizzerà diversi risultati.

![Opzioni problemi](media/spring-cloud-diagnose/detectors-options.png)

Individuare il rilevatore di destinazione e fare clic su di esso per eseguire. Dopo l'esecuzione del detector verrà visualizzato un riepilogo della diagnostica. È possibile selezionare **Visualizza report completo** per controllare i dettagli di diagnostica oppure fare clic sul pulsante di **menu Mostra riquadro** per tornare all'elenco dei rilevatori.

 ![Diagnostica di riepilogo](media/spring-cloud-diagnose/summary-diagnostics.png)

Nella pagina dettagli di diagnostica è possibile modificare l'intervallo di tempo di diagnostica con il controller nell'angolo superiore destro. Per visualizzare più metriche o log, impostare ogni diagnostica. È possibile che si verifichi un ritardo di 15 minuti per le metriche e i log.

 ![Dettagli diagnostica](media/spring-cloud-diagnose/diagnostics-details.png)

Alcuni risultati contengono documentazione correlata.

 ![Dettagli correlati](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare le risorse di Spring Cloud tramite avvisi e gruppi di azioni](spring-cloud-tutorial-alerts-action-groups.md)
* [Controlli di sicurezza per il servizio Azure Spring Cloud](spring-cloud-concept-security-controls.md)