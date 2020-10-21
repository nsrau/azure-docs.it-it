---
title: Come usare Azure Defender per registri contenitori
description: Informazioni sull'uso di Azure Defender per registri contenitori per l'analisi delle immagini nei registri
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 57e8b6f47c4166c4f8b9f5de0f3e03a7d757e100
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342077"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Usare Azure Defender per registri contenitori per analizzare le vulnerabilità delle immagini

Questa pagina illustra come usare lo scanner di vulnerabilità integrato per analizzare le immagini del contenitore archiviate nel Container Registry Azure basato su Azure Resource Manager.

Quando **Azure Defender per i registri contenitori** è abilitato, tutte le immagini sottoposte a push nel registro verranno analizzate immediatamente. Inoltre, viene eseguita l'analisi di qualsiasi immagine pull negli ultimi 30 giorni. 

Quando lo scanner segnala le vulnerabilità al centro sicurezza, il Centro sicurezza presenta i risultati e le informazioni correlate come raccomandazioni. Inoltre, i risultati includono informazioni correlate, ad esempio passaggi di correzione, CVEs rilevanti, punteggi CVSS e altro ancora. È possibile visualizzare le vulnerabilità identificate per una o più sottoscrizioni o per uno specifico registro di sistema.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per i registri contenitori** viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Registri e immagini supportati:|![Sì ](./media/icons/yes-icon.png) registri ACR ospitati da Linux accessibili dalla rete Internet pubblica e offrono l'accesso alla Shell.<br>![Nessun ](./media/icons/no-icon.png) Registro ACR ospitato da Windows.<br>![Nessun ](./media/icons/no-icon.png) registro ' privato '-il Centro sicurezza richiede che i registri siano accessibili dalla rete Internet pubblica. Il Centro sicurezza non è attualmente in grado di connettersi o analizzare i registri con accesso limitato con un firewall, un endpoint del servizio o endpoint privati come il collegamento privato di Azure.<br>![Non ci sono ](./media/icons/no-icon.png) Immagini con minimalista, ad esempio immagini [Scratch di Docker](https://hub.docker.com/_/scratch/) o immagini "senza distribuzione" che contengono solo un'applicazione e le relative dipendenze di runtime senza gestione pacchetti, Shell o sistema operativo.|
|Autorizzazioni e ruoli obbligatori:|Ruolo lettore di **sicurezza** e [lettore di container Registry di Azure](../container-registry/container-registry-roles.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identificare le vulnerabilità nelle immagini dei registri contenitori di Azure 

1. Per abilitare le analisi delle vulnerabilità delle immagini archiviate nel Container Registry Azure basato su Azure Resource Manager:

    1. Abilitare **Azure Defender per i registri contenitori** per la sottoscrizione.

        Il Centro sicurezza è ora pronto per l'analisi delle immagini nei registri.

        >[!NOTE]
        > Questa funzionalità prevede un addebito per ogni immagine.

1. Le analisi delle immagini vengono attivate a ogni push o importazione e se l'immagine è stata ritirata negli ultimi 30 giorni. 

    Al termine dell'analisi (in genere dopo circa 2 minuti, ma può essere fino a 15 minuti), i risultati sono disponibili come raccomandazioni del Centro sicurezza.

1. [Visualizzare e correggere i risultati come illustrato di seguito](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identificare le vulnerabilità nelle immagini in altri registri contenitori 

1. Usare gli strumenti ACR per portare le immagini nel registro da Docker Hub o Microsoft Container Registry.  Al termine dell'importazione, le immagini importate verranno analizzate da Azure Defender. 

    Per altre informazioni, vedere [importare le immagini del contenitore in un registro contenitori](../container-registry/container-registry-import-images.md)

    Al termine dell'analisi (in genere dopo circa 2 minuti, ma può essere fino a 15 minuti), i risultati sono disponibili come raccomandazioni del Centro sicurezza.

1. [Visualizzare e correggere i risultati come illustrato di seguito](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Visualizzare e correggere i risultati

1. Per visualizzare i risultati, passare alla pagina **raccomandazioni** . Se sono stati rilevati problemi, verranno visualizzate le vulnerabilità delle raccomandazioni **in Azure container Registry immagini da correggere**

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


## <a name="disable-specific-findings-preview"></a>Disabilita risultati specifici (anteprima)

Se l'organizzazione deve ignorare un risultato invece di correggerlo, è possibile disabilitarlo facoltativamente. I risultati disabilitati non influiscono sul punteggio di sicurezza e non generano elementi non significativi.

Quando un risultato corrisponde ai criteri definiti nelle regole di disabilitazione, non verrà visualizzato nell'elenco di risultati. Gli scenari tipici includono:

- Disabilitare i risultati con gravità inferiore al medio
- Disabilitare i risultati non patchable
- Disabilitare i risultati con il punteggio CVSS sotto 6,5
- Disabilitare i risultati con testo specifico nel controllo di sicurezza o nella categoria (ad esempio, "RedHat", "aggiornamento della sicurezza CentOS per sudo")

> [!IMPORTANT]
> Per creare una regola, è necessario disporre delle autorizzazioni per modificare i criteri in criteri di Azure.
>
> Per altre informazioni, vedere [autorizzazioni RBAC di Azure in criteri di Azure](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).

È possibile usare uno dei criteri seguenti: 

- Ricerca ID 
- Category
- Controllo di sicurezza 
- Punteggi CVSS V3
- Gravità 
- Stato patchable 

Per creare una regola:

1. Dalla pagina dei dettagli delle raccomandazioni per le **vulnerabilità in Azure container Registry le immagini devono essere corrette**, selezionare **Disabilita regola**.
1. Selezionare l'ambito pertinente.
1. Definire i criteri.
1. Selezionare **Applica regola**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Creare una regola di disabilitazione per i risultati di VA nel registro di sistema":::

1. Per visualizzare, eseguire l'override o eliminare una regola: 
    1. Selezionare **Disabilita regola**.
    1. Dall'elenco ambito, le sottoscrizioni con regole attive vengono visualizzate come **regole applicate**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Creare una regola di disabilitazione per i risultati di VA nel registro di sistema":::
    1. Per visualizzare o eliminare la regola, selezionare il menu con i puntini di sospensione ("...").


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su Azure Defender](azure-defender.md)