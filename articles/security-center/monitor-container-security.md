---
title: Monitoraggio della sicurezza dei contenitori nel centro sicurezza di Azure
description: Informazioni su come controllare il comportamento di sicurezza dei contenitori dal centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: d208629ee29bb9102f16fcb1d541677bd4a4d5fa
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431566"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitoraggio della sicurezza dei contenitori

Questa pagina illustra come usare le funzionalità di sicurezza del contenitore descritte nell' [articolo sulla sicurezza dei contenitori](container-security.md) nella sezione concetti.

Il Centro sicurezza di Azure illustra i tre aspetti seguenti della sicurezza del contenitore:

- **Gestione delle vulnerabilità** : se si usa il piano tariffario standard del Centro sicurezza (vedere i [prezzi](/azure/security-center/security-center-pricing)), è possibile analizzare la container Registry di Azure basata su ARM ogni volta che viene effettuato il push di una nuova immagine. Lo scanner (alimentato da Qualys) presenta i risultati come raccomandazioni del Centro sicurezza.
    Per istruzioni dettagliate, vedere [analisi dei registri dei contenitori per le vulnerabilità](#scanning-your-arm-based-container-registries-for-vulnerabilities) seguenti.

- Protezione **avanzata degli host Docker dei contenitori** : il Centro sicurezza trova i contenitori non gestiti ospitati in macchine virtuali Linux IaaS o altri computer Linux che eseguono Docker e confronta continuamente le configurazioni dei contenitori con il benchmark Docker di Center for Internet Security (CIS). Il Centro sicurezza avvisa l'utente se i contenitori non soddisfano i controlli. Il monitoraggio continuo dei rischi per la sicurezza a causa di problemi di configurazione è un componente fondamentale di qualsiasi programma di sicurezza. 
    Per istruzioni dettagliate, vedere [protezione avanzata degli host Docker per i contenitori](#hardening-your-containers-docker-hosts) .

- Protezione **avanzata dei cluster di servizi Kubernetes di Azure** : il Centro sicurezza fornisce consigli quando rileva vulnerabilità nella configurazione dei cluster di servizi Kubernetes di Azure. Per informazioni dettagliate sulle raccomandazioni specifiche che possono essere visualizzate, vedere le raccomandazioni per il [servizio Kubernetes](recommendations-reference.md#recs-containers).

- **Protezione in fase di esecuzione** : se si usa il piano tariffario standard del Centro sicurezza, si otterrà il rilevamento delle minacce in tempo reale per gli ambienti in contenitori. Il Centro sicurezza genera avvisi per le attività sospette a livello di host e del cluster AKS. Per informazioni dettagliate sugli avvisi di sicurezza pertinenti che potrebbero essere visualizzati, vedere gli avvisi [per i cluster del servizio Kubernetes di Azure](alerts-reference.md#alerts-akscluster) e gli [avvisi per i contenitori-](alerts-reference.md#alerts-containerhost) sezioni a livello di host della tabella di riferimento degli avvisi.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Analisi dei registri di contenitori basati su ARM per le vulnerabilità 

1. Per abilitare le analisi delle vulnerabilità delle immagini del Container Registry di Azure:

    1. Assicurarsi di trovarsi nel piano tariffario standard del Centro sicurezza di Azure.

    1. Dalla pagina **impostazioni & prezzi** abilitare il bundle facoltativo dei registri contenitori per la sottoscrizione: ![abilitazione del bundle registri contenitori](media/monitor-container-security/enabling-container-registries-bundle.png)

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

    [riquadro Dettagli risultati ![](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

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

    [pulsante ![azione Take](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics si apre con un'operazione personalizzata pronta per l'esecuzione. La query personalizzata predefinita include un elenco di tutte le regole non riuscite valutate, insieme alle linee guida che consentono di risolvere i problemi.

    [![Log Analytics azione](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Modificare i parametri di query e selezionare **Esegui** quando si è certi che sia pronto per l'host. 



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare le funzionalità di sicurezza del contenitore del Centro sicurezza. 

Per altri materiali correlati, vedere le pagine seguenti: 

- [Raccomandazioni del Centro sicurezza per i contenitori](recommendations-reference.md#recs-containers)
- [Avvisi per il livello del cluster AKS](alerts-reference.md#alerts-akscluster)
- [Avvisi per il livello host del contenitore](alerts-reference.md#alerts-containerhost)
