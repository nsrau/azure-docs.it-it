---
title: Monitoraggio della sicurezza dei contenitori nel centro sicurezza di Azure
description: Informazioni su come controllare il comportamento di sicurezza dei contenitori dal centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 76bf38c9d15e977b39922fdfbc7d30f533707cda
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801377"
---
# <a name="monitor-the-security-of-your-containers"></a>Monitorare la sicurezza dei contenitori

Questa pagina illustra come usare le funzionalità di sicurezza del contenitore descritte nell' [articolo sulla sicurezza dei contenitori](container-security.md) nella sezione concetti.


## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Analisi dei registri di contenitori basati su ARM per le vulnerabilità 

1. Per abilitare le analisi delle vulnerabilità delle immagini del Container Registry di Azure:

    1. Assicurarsi di trovarsi nel piano tariffario standard del Centro sicurezza di Azure.

    1. Dalla pagina **impostazioni & dei prezzi** abilitare il bundle facoltativo dei registri contenitori per la sottoscrizione: ![ Abilitazione del bundle registri contenitori](media/monitor-container-security/enabling-container-registries-bundle.png)

        Il Centro sicurezza è ora pronto per analizzare le immagini che vengono inserite nel registro di sistema. 

        >[!NOTE]
        >Questa funzionalità viene addebitata per ogni immagine.


1. Per attivare l'analisi di un'immagine, eseguirne il push nel registro. 

    Al termine dell'analisi, in genere dopo circa 10 minuti, i risultati sono disponibili nelle raccomandazioni del Centro sicurezza.
    

1. Per visualizzare i risultati, passare alla pagina **raccomandazioni** . Se sono stati rilevati problemi, verrà visualizzata la raccomandazione seguente:

    ![Suggerimenti per correggere i problemi ](media/monitor-container-security/acr-finding.png)


1. Selezionare la raccomandazione. 
    Verrà visualizzata la pagina Dettagli raccomandazione con ulteriori informazioni. Queste informazioni includono l'elenco dei registri con le immagini vulnerabili ("risorse interessate") e i passaggi correttivi. 

1. Selezionare un registro specifico per visualizzare i repository al suo interno con repository vulnerabili.

    ![Selezionare un registro](media/monitor-container-security/acr-finding-select-registry.png)

    Verrà visualizzata la pagina Dettagli registro di sistema con l'elenco dei repository interessati.

1. Selezionare un repository specifico per visualizzare i repository al suo interno contenenti immagini vulnerabili.

    ![Selezionare un repository](media/monitor-container-security/acr-finding-select-repository.png)

    Verrà visualizzata la pagina dei dettagli del repository. Elenca le immagini vulnerabili insieme a una valutazione della gravità dei risultati.

1. Selezionare un'immagine specifica per visualizzare le vulnerabilità.

    ![Seleziona immagini](media/monitor-container-security/acr-finding-select-image.png)

    Verrà visualizzato l'elenco dei risultati per l'immagine selezionata.

    ![Elenco di risultati](media/monitor-container-security/acr-findings.png)

1. Per ulteriori informazioni su una ricerca, selezionare la ricerca. 

    Viene visualizzato il riquadro Dettagli risultati.

    [![Riquadro Dettagli risultati](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Questo riquadro include una descrizione dettagliata del problema e i collegamenti a risorse esterne per attenuare le minacce.

1. Attenersi alla procedura descritta nella sezione monitoraggio e aggiornamento di questo riquadro.

1. Dopo aver eseguito i passaggi necessari per correggere il problema di sicurezza, sostituire l'immagine nel registro di sistema:

    1. Eseguire il push dell'immagine aggiornata. Verrà avviata un'analisi. 
    
    1. Vedere la pagina raccomandazioni per la raccomandazione "vulnerabilità in Azure Container Registry immagini da correggere". 
    
        Se la raccomandazione viene ancora visualizzata e l'immagine gestita è ancora visualizzata nell'elenco delle immagini vulnerabili, controllare di nuovo la procedura di correzione.

    1. Quando si è certi che l'immagine aggiornata è stata sottoposta a push, analizzata e non viene più visualizzata nella raccomandazione, eliminare l'immagine vulnerabile "obsoleta" dal registro di sistema.


## <a name="hardening-your-containers-docker-hosts"></a>Protezione avanzata degli host Docker dei contenitori

Il Centro sicurezza monitora costantemente la configurazione degli host Docker e genera raccomandazioni sulla sicurezza che riflettono gli standard di settore.

Per visualizzare le raccomandazioni sulla sicurezza del Centro sicurezza di Azure per gli host Docker dei contenitori:

1. Dalla barra di spostamento del Centro sicurezza aprire **calcolo & app** e selezionare la scheda **contenitori** .

1. Facoltativamente, filtrare l'elenco delle risorse del contenitore negli host host del contenitore.

    ![Filtro risorse contenitore](media/monitor-container-security/container-resources-filter.png)

1. Dall'elenco dei computer host del contenitore selezionare uno per approfondire l'analisi.

    ![Indicazioni sull'host contenitore](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Verrà visualizzata la **pagina informazioni host contenitore** con i dettagli dell'host e un elenco di raccomandazioni.

1. Dall'elenco raccomandazioni selezionare un Consiglio per eseguire ulteriori indagini.

    ![Elenco di raccomandazioni host contenitore](media/monitor-container-security/container-host-rec.png)

1. Facoltativamente, leggere la descrizione, le informazioni, le minacce e le procedure correttive. 

1. Selezionare Esegui **azione** nella parte inferiore della pagina.

    [![Pulsante azione](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics si apre con un'operazione personalizzata pronta per l'esecuzione. La query personalizzata predefinita include un elenco di tutte le regole non riuscite valutate, insieme alle linee guida che consentono di risolvere i problemi.

    [![Azione Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Se necessario, modificare i parametri della query.

1. Quando si è certi che il comando sia appropriato e pronto per l'host, selezionare **Esegui**.



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare le funzionalità di sicurezza del contenitore del Centro sicurezza. 

Per altri materiali correlati, vedere le pagine seguenti: 

- [Raccomandazioni del Centro sicurezza per i contenitori](recommendations-reference.md#recs-containers)
- [Avvisi per il livello del cluster AKS](alerts-reference.md#alerts-akscluster)
- [Avvisi per il livello host del contenitore](alerts-reference.md#alerts-containerhost)
