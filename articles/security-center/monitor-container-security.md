---
title: Monitoraggio della sicurezza dei contenitori nel Centro sicurezza di AzureMonitoring the security of your containers in Azure Security Center
description: Informazioni su come verificare il livello di sicurezza dei contenitori dal Centro sicurezza di AzureLearn how to check the security posture of your containers from Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919533"
---
# <a name="monitoring-the-security-of-your-containers"></a>Monitoraggio della sicurezza dei contenitori

In questa pagina viene illustrato come utilizzare le funzionalità di sicurezza dei contenitori descritte [nell'articolo Sicurezza dei contenitori](container-security.md) nella sezione Concetti.

Il Centro sicurezza di Azure copre i tre aspetti seguenti della sicurezza dei contenitori:Azure Security Center covers the following three aspects of container security:

- **Gestione delle vulnerabilità:** se si fa parte del piano tariffario standard del Centro sicurezza (vedere [prezzi](/azure/security-center/security-center-pricing)), è possibile eseguire la scansione del Registro di sistema del contenitore di Azure basato su ARM ogni volta che viene inserita una nuova immagine. Lo scanner (alimentato da Qualys) presenta i risultati come consigli del Centro sicurezza.
    Per istruzioni dettagliate, vedere [Scansione dei registri dei contenitori per le vulnerabilità riportate](#scanning-your-arm-based-container-registries-for-vulnerabilities) di seguito.

- **Protezione avanzata degli host Docker dei contenitori:** il Centro sicurezza consente di trovare contenitori non gestiti ospitati in macchine virtuali Linux IaaS o in altre macchine Linux che eseguono Docker e confronta continuamente le configurazioni dei contenitori con il Center for Internet Security (CIS) Docker Benchmark. Il Centro sicurezza avvisa l'utente se i contenitori non soddisfano nessuno dei controlli. Il monitoraggio continuo dei rischi per la sicurezza dovuti a configurazioni errate è un componente fondamentale di qualsiasi programma di sicurezza. 
    Per istruzioni [dettagliate, vedere Hardening host Docker dei contenitori](#hardening-your-containers-docker-hosts) di seguito.

- Protezione avanzata dei cluster del servizio Azure Kubernetes - Centro sicurezza fornisce suggerimenti per la gestione delle vulnerabilità nella configurazione dei cluster del servizio Azure Kubernetes.Hardening **your Azure Kubernetes Service clusters** - Security Center provides recommendations when it finds vulnerabilities in the configuration of your Azure Kubernetes Service clusters. Per informazioni dettagliate sui consigli specifici che possono essere visualizzati, vedere le [raccomandazioni del servizio Kubernetes](recommendations-reference.md#recs-containers).

- **Protezione runtime:** se usi il piano tariffario standard del Centro sicurezza, otterrai una protezione dalle minacce in tempo reale per gli ambienti containerizzati. Il Centro sicurezza genera avvisi per attività sospette a livello di host e cluster AKS. Per informazioni dettagliate degli avvisi di sicurezza pertinenti che potrebbero essere visualizzati, vedere le sezioni Avvisi per i cluster del [servizio Kubernetes](alerts-reference.md#alerts-akscluster) di Azure e [Avvisi per i contenitori: livello host](alerts-reference.md#alerts-containerhost) della tabella di riferimento degli avvisi.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Scansione dei registri dei container basati su ARM alla ricerca di vulnerabilità 

1. Per abilitare le analisi delle vulnerabilità delle immagini del Registro di sistema del contenitore di Azure:

    1. Assicurati di essere nel piano tariffario standard del Centro sicurezza di Azure.Ensure you're on Azure Security Center's standard pricing tier.

    1. Dalla pagina **Impostazioni & prezzi,** abilitare il bundle ![Facoltativo Registri contenitori per l'abbonamento: Abilitazione del bundle dei registri contenitori](media/monitor-container-security/enabling-container-registries-bundle.png)

        Il Centro sicurezza è ora pronto per eseguire la scansione delle immagini che vengono inviate al Registro di sistema. 

        >[!NOTE]
        >Questa funzione viene caricata per ogni immagine.


1. Per attivare la scansione di un'immagine, eseguirne il push nel Registro di sistema. 

    Quando l'analisi viene completata (in genere dopo circa 10 minuti), i risultati sono disponibili nei consigli del Centro sicurezza.
    

1. Per visualizzare i risultati, passare alla pagina **Raccomandazioni.** Se sono stati rilevati problemi, verrà visualizzato il seguente consiglio:

    ![Raccomandazione per risolvere i problemi ](media/monitor-container-security/acr-finding.png)


1. Selezionare il suggerimento. 
    Viene visualizzata la pagina dei dettagli del suggerimento con informazioni aggiuntive. Queste informazioni includono l'elenco dei registri con immagini vulnerabili ("risorse interessate") e i passaggi di correzione. 

1. Selezionare un registro specifico per visualizzare i repository al suo interno che dispongono di repository vulnerabili.

    ![Selezionare un Registro di sistema](media/monitor-container-security/acr-finding-select-registry.png)

    Viene visualizzata la pagina dei dettagli del Registro di sistema con l'elenco dei repository interessati.

1. Selezionare un repository specifico per visualizzare i repository al suo interno che hanno immagini vulnerabili.

    ![Selezionare un repository](media/monitor-container-security/acr-finding-select-repository.png)

    Viene visualizzata la pagina dei dettagli del repository. Elenca le immagini vulnerabili insieme a una valutazione della gravità dei risultati.

1. Selezionare un'immagine specifica per visualizzare le vulnerabilità.

    ![Selezionare le immagini](media/monitor-container-security/acr-finding-select-image.png)

    Viene visualizzato l'elenco dei risultati per l'immagine selezionata.

    ![Elenco dei risultati](media/monitor-container-security/acr-findings.png)

1. Per ulteriori informazioni su una ricerca, selezionare la ricerca. 

    Viene visualizzato il riquadro dei dettagli dei risultati.

    [![Riquadro dei dettagli dei risultati](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Questo riquadro include una descrizione dettagliata del problema e collegamenti a risorse esterne per ridurre le minacce.

1. Seguire i passaggi nella sezione di correzione di questo riquadro.

1. Dopo aver eseguito la procedura necessaria per risolvere il problema di protezione, sostituire l'immagine nel Registro di sistema:

    1. Spingere l'immagine aggiornata. Questo attiverà una scansione. 
    
    1. Controllare la pagina dei suggerimenti per la raccomandazione "Le vulnerabilità nelle immagini del Registro di sistema del contenitore di Azure devono essere corrette". 
    
        Se il suggerimento viene ancora visualizzato e l'immagine gestita viene ancora visualizzata nell'elenco delle immagini vulnerabili, controllare di nuovo i passaggi di correzione.

    1. Quando si è certi che l'immagine aggiornata è stata inserita, scansionata e non viene più visualizzata nella raccomandazione, eliminare la "vecchia" immagine vulnerabile dal Registro di sistema.


## <a name="hardening-your-containers-docker-hosts"></a>Protezione avanzata degli host Docker dei contenitori

Il Centro sicurezza monitora costantemente la configurazione degli host Docker e genera consigli di sicurezza che riflettono gli standard del settore.

Per visualizzare i consigli di sicurezza del Centro sicurezza di Azure per gli host Docker dei contenitori:To view Azure Security Center's security recommendations for your containers' Docker hosts:

1. Nella barra di spostamento del Centro sicurezza aprire **Calcolo & app** e selezionare la scheda **Contenitori.From** the Security Center navigation bar, open Compute & apps and select the Containers tab.

1. Facoltativamente, filtrare l'elenco delle risorse del contenitore in un host host contenitore.

    ![Filtro risorse contenitore](media/monitor-container-security/container-resources-filter.png)

1. Nell'elenco dei computer host contenitore selezionarne uno per analizzare ulteriormente.

    ![Consigli per l'host di contenitoriContainer host recommendations](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Viene visualizzata la **pagina Informazioni sull'host contenitore** con i dettagli dell'host e un elenco di consigli.

1. Nell'elenco dei suggerimenti selezionare un suggerimento per analizzare ulteriormente.

    ![Elenco di suggerimenti per host contenitore](media/monitor-container-security/container-host-rec.png)

1. Facoltativamente, leggere la descrizione, le informazioni, le minacce e i passaggi di correzione. 

1. Selezionare **Azione** nella parte inferiore della pagina.

    [![Pulsante Azione](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    Log Analytics si apre con un'operazione personalizzata pronta per l'esecuzione. La query personalizzata predefinita include un elenco di tutte le regole non riuscite che sono state valutate, insieme a linee guida per risolvere i problemi.

    [![Azione di Log Analytics](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Modificare i parametri di query e selezionare **Esegui** quando si è certi che sia pronto per l'host. 



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come utilizzare le funzionalità di sicurezza dei contenitori del Centro sicurezza. 

Per altri materiali correlati, vedere le pagine seguenti: 

- [Consigli del Centro sicurezza per i contenitori](recommendations-reference.md#recs-containers)
- [Avvisi per il livello di cluster AKSAlerts for AKS cluster level](alerts-reference.md#alerts-akscluster)
- [Avvisi per il livello host Contenitore](alerts-reference.md#alerts-containerhost)
