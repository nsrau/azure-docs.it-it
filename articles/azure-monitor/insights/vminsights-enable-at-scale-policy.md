---
title: Abilitare Monitoraggio di Azure per le macchine virtuali usando criteri di Azure | Microsoft Docs
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando criteri di Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 4a89eb36c9aa7369d6145304b572b4245cef3483
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109346"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Abilitare Monitoraggio di Azure per le macchine virtuali (anteprima) usando criteri di Azure

Questo articolo illustra come abilitare la Monitoraggio di Azure per le macchine virtuali (anteprima) per le macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando criteri di Azure. Al termine di questo processo, sarà stata configurata l'abilitazione dell'Log Analytics e degli agenti di dipendenza e delle macchine virtuali identificate che non sono conformi.

Per individuare, gestire e abilitare Monitoraggio di Azure per le macchine virtuali per tutte le macchine virtuali di Azure o i set di scalabilità di macchine virtuali, è possibile usare criteri di Azure o Azure PowerShell. Criteri di Azure è il metodo consigliato perché consente di gestire le definizioni dei criteri per governare efficacemente le sottoscrizioni in modo da garantire conformità coerente e abilitazione automatica delle macchine virtuali di cui è stato effettuato il provisioning. Queste definizioni dei criteri:

* Distribuiscono l'agente di Log Analytics e Dependency Agent.
* Creano report con i risultati relativi alla conformità.
* Correggere le macchine virtuali non conformi.

Se si è interessati a eseguire queste attività con Azure PowerShell o un modello di Azure Resource Manager, vedere [abilitare monitoraggio di Azure per le macchine virtuali (anteprima) usando i modelli di Azure PowerShell o Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Panoramica sulla gestione della copertura dei criteri

In origine, l'esperienza con criteri di Azure per la gestione e la distribuzione delle definizioni dei criteri per Monitoraggio di Azure per le macchine virtuali è stata eseguita esclusivamente da criteri di Azure. La funzionalità di gestione del code coverage dei criteri semplifica e semplifica l'individuazione, la gestione e l'abilitazione su larga scala dell' **Abilitazione di monitoraggio di Azure per le macchine virtuali** Initiative, che include le definizioni dei criteri citate in precedenza. Per accedere a questa nuova funzionalità, è possibile usare la scheda attività **iniziali** in monitoraggio di Azure per le macchine virtuali. Selezionare **Gestisci copertura criteri** per aprire la pagina **copertura criteri monitoraggio di Azure per le macchine virtuali** .

![Scheda Introduzione a monitoraggio di Azure dalla macchina virtuale](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Da qui è possibile controllare e gestire la copertura per l'iniziativa tra i gruppi di gestione e le sottoscrizioni. È possibile comprendere il numero di macchine virtuali presenti in ognuno dei gruppi di gestione e delle sottoscrizioni e il relativo stato di conformità.

![Pagina di gestione dei criteri di Monitoraggio di Azure per le macchine virtuali](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Queste informazioni sono utili per pianificare ed eseguire lo scenario di governance per Monitoraggio di Azure per le macchine virtuali da un'unica posizione centrale. Mentre i criteri di Azure forniscono una visualizzazione della conformità quando un criterio o un'iniziativa viene assegnato a un ambito, con questa nuova pagina è possibile individuare il punto in cui il criterio o l'iniziativa non è stata assegnata e assegnarla sul posto. Tutte le azioni come assegnare, visualizzare e modificare il reindirizzamento direttamente ai criteri di Azure. La pagina **code coverage monitoraggio di Azure per le macchine virtuali** è un'esperienza espansa e integrata solo per l'iniziativa **Enable monitoraggio di Azure per le macchine virtuali**.

Da questa pagina è anche possibile configurare l'area di lavoro di Log Analytics per Monitoraggio di Azure per le macchine virtuali, che:

- Installa la soluzione Mapping dei servizi.
- Abilita i contatori delle prestazioni del sistema operativo utilizzati dai grafici delle prestazioni, dalle cartelle di lavoro e dagli avvisi e dalle query di log personalizzati.

![Monitoraggio di Azure per le macchine virtuali configurare l'area di lavoro](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Questa opzione non è correlata ad alcuna azione del criterio. È disponibile per fornire un modo semplice per soddisfare i [prerequisiti](vminsights-enable-overview.md) necessari per l'abilitazione di monitoraggio di Azure per le macchine virtuali.  

### <a name="what-information-is-available-on-this-page"></a>Quali sono le informazioni disponibili in questa pagina?

La tabella seguente fornisce una suddivisione delle informazioni presentate nella pagina relativa alla copertura dei criteri e come interpretarla.

| Funzione | DESCRIZIONE | 
|----------|-------------| 
| **Ambito** | Gruppo di gestione e sottoscrizioni a cui è stato o ereditato l'accesso con la possibilità di eseguire il drill-down attraverso la gerarchia del gruppo di gestione.|
| **Ruolo** | Ruolo per l'ambito, che può essere Reader, Owner o collaboratore. In alcuni casi, potrebbe sembrare vuoto per indicare che è possibile accedere alla sottoscrizione ma non al gruppo di gestione a cui appartiene. Le informazioni in altre colonne variano a seconda del ruolo. Il ruolo è fondamentale per determinare i dati che è possibile visualizzare e le azioni che è possibile eseguire in termini di assegnazione di criteri o iniziative (proprietario), modifica o visualizzazione della conformità. |
| **Totale macchine virtuali** | Numero di macchine virtuali in tale ambito. Per un gruppo di gestione, si tratta di una somma di VM annidate sotto le sottoscrizioni o il gruppo di gestione figlio. |
| **Code coverage assegnazione** | Percentuale di macchine virtuali coperte dal criterio o dall'iniziativa. |
| **Stato di assegnazione** | Informazioni sullo stato dell'assegnazione dell'iniziativa o del criterio. |
| **Macchine virtuali conformi** | Numero di macchine virtuali conformi ai criteri o all'iniziativa. Per l'iniziativa **Enable monitoraggio di Azure per le macchine virtuali**, questo è il numero di macchine virtuali con log Analytics Agent e Dependency Agent. In alcuni casi, potrebbe sembrare vuoto a causa di nessuna assegnazione, nessuna macchina virtuale o autorizzazioni insufficienti. Le informazioni vengono fornite in **stato di conformità**. |
| **Conformità** | Il numero di conformità complessivo è la somma di risorse distinte conformi, divise per la somma di tutte le risorse distinte. |
| **Stato di conformità** | Informazioni sullo stato di conformità per l'assegnazione di criteri o iniziative.|

Quando si assegna il criterio o l'iniziativa, l'ambito selezionato nell'assegnazione potrebbe essere l'ambito elencato o un sottoinsieme. Ad esempio, è possibile che sia stata creata un'assegnazione per una sottoscrizione (ambito dei criteri) e non un gruppo di gestione (ambito di code coverage). In questo caso, il valore di **code coverage assegnazione** indica le VM nell'ambito Policy o Initiative divise dalle macchine virtuali nell'ambito di code coverage. In un altro caso, è possibile che siano state escluse alcune macchine virtuali, gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se il valore è vuoto, significa che il criterio o l'iniziativa non esiste oppure non si dispone dell'autorizzazione. Le informazioni vengono fornite in **stato di assegnazione**.

## <a name="enable-by-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure

Per abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di Azure nel tenant:

- Assegnare l'iniziativa a un ambito: gruppo di gestione, sottoscrizione o gruppo di risorse.
- Esaminare e correggere i risultati di conformità.

Per altre informazioni sull'assegnazione di Criteri di Azure, vedere [Panoramica di Criteri di Azure](../../governance/policy/overview.md#policy-assignment) ed esaminare la [panoramica dei gruppi di gestione](../../governance/management-groups/overview.md) prima di continuare.

### <a name="policies-for-azure-vms"></a>Criteri per le macchine virtuali di Azure

Le definizioni dei criteri per una macchina virtuale di Azure sono elencate nella tabella seguente.

|Nome |DESCRIZIONE |digitare |
|-----|------------|-----|
|\[Anteprima\]: Abilita Monitoraggio di Azure per le macchine virtuali |Abilitare monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |Iniziativa |
|\[Preview\]: audit Dependency Agent Deployment-VM Image (OS) Unlisted |Segnala le macchine virtuali come non conformi se l'immagine di macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|\[Preview\]: Audit Log Analytics Agent Deployment-VM Image (OS) Unlisted |Segnala le macchine virtuali come non conformi se l'immagine di macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|\[\]Preview: distribuire l'agente di dipendenza per le macchine virtuali Linux |Distribuire Dependency Agent per macchine virtuali Linux se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\[\]Preview: distribuire l'agente di dipendenza per le macchine virtuali Windows |Distribuire Dependency Agent per VM Windows se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\]di \[Preview: distribuire l'agente di Log Analytics per le macchine virtuali Linux |Distribuire Log Analytics Agent per VM Linux se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\[anteprima\]: distribuire l'agente di Log Analytics per le macchine virtuali Windows |Distribuire Log Analytics Agent per VM Windows se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Criteri per i set di scalabilità di macchine virtuali di Azure

Le definizioni dei criteri per un set di scalabilità di macchine virtuali di Azure sono elencate nella tabella seguente.

|Nome |DESCRIZIONE |digitare |
|-----|------------|-----|
|\[anteprima\]: abilitare monitoraggio di Azure per i set di scalabilità di macchine virtuali |Abilitare monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se i criteri di aggiornamento del set di scalabilità sono impostati su manuale, applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento su di essi. Nell'interfaccia della riga di comando è `az vmss update-instances`. |Iniziativa |
|\[Preview\]: controllare la distribuzione dell'agente di dipendenza nei set di scalabilità di macchine virtuali-immagine VM (sistema operativo) non in elenco |Segnala il set di scalabilità di macchine virtuali come non conforme se l'immagine di macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|\[Preview\]: controllare la distribuzione dell'agente Log Analytics nei set di scalabilità di macchine virtuali-immagine VM (sistema operativo) non in elenco |Segnala il set di scalabilità di macchine virtuali come non conforme se l'immagine di macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|\[anteprima\]: distribuire l'agente di dipendenza per i set di scalabilità di macchine virtuali Linux |Distribuire Dependency Agent per i set di scalabilità di macchine virtuali Linux se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\[anteprima\]: distribuire l'agente di dipendenza per i set di scalabilità di macchine virtuali Windows |Distribuire Dependency Agent per i set di scalabilità di macchine virtuali Windows se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\[Preview\]: distribuire Log Analytics Agent per i set di scalabilità di macchine virtuali Linux |Distribuire Log Analytics Agent per i set di scalabilità di macchine virtuali Linux se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|\[Preview\]: distribuire Log Analytics Agent per i set di scalabilità di macchine virtuali Windows |Distribuire Log Analytics Agent per i set di scalabilità di macchine virtuali Windows se l'immagine di macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |

Il criterio autonomo (non incluso nell'iniziativa) è descritto qui:

|Nome |DESCRIZIONE |digitare |
|-----|------------|-----|
|\[Preview\]: Audit Log Analytics area di lavoro per macchina virtuale-segnala mancata corrispondenza |Segnala le macchine virtuali come non conformi se non eseguono la registrazione nell'area di lavoro Log Analytics specificata nell'assegnazione di criteri o di iniziativa. |Criteri |

### <a name="assign-the-azure-monitor-initiative"></a>Assegnare l'iniziativa di Monitoraggio di Azure

Per creare l'assegnazione dei criteri dalla pagina **monitoraggio di Azure per le macchine virtuali code coverage** , attenersi alla seguente procedura. Per informazioni su come completare questi passaggi, vedere  [Creare un'assegnazione di criteri dal portale di Azure](../../governance/policy/assign-policy-portal.md).

Quando si assegna il criterio o l'iniziativa, l'ambito selezionato nell'assegnazione potrebbe essere l'ambito elencato qui o un sottoinsieme. Ad esempio, è possibile che sia stata creata un'assegnazione per la sottoscrizione (ambito dei criteri) e non il gruppo di gestione (ambito di code coverage). In questo caso, la percentuale di code coverage indicherà le VM nell'ambito Policy o Initiative divise dalle macchine virtuali nell'ambito di code coverage. In un altro caso, è possibile che siano state escluse alcune macchine virtuali o gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se è vuota, indica che il criterio o l'iniziativa non esiste oppure non si dispone delle autorizzazioni necessarie. Le informazioni vengono fornite in **stato di assegnazione**.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure selezionare **Monitoraggio**. 

3. Scegliere **Macchine virtuali (anteprima)** nella sezione **Informazioni dettagliate**.
 
4. Selezionare la scheda attività **iniziali** . Nella pagina selezionare **Gestisci copertura criteri**.

5. Selezionare un gruppo di gestione o una sottoscrizione dalla tabella. Selezionare l' **ambito** selezionando i puntini di sospensione (...). Nell'esempio, un ambito limita l'assegnazione dei criteri a un raggruppamento di macchine virtuali per l'imposizione.

6. Nella pagina **assegnazione criteri di Azure** viene pre-popolato con l'iniziativa **Abilita monitoraggio di Azure per le macchine virtuali**. 
    La casella **nome assegnazione** viene popolata automaticamente con il nome dell'iniziativa, ma è possibile modificarla. È anche possibile aggiungere una descrizione facoltativa. La casella **assegnato da** viene popolata automaticamente in base a chi ha eseguito l'accesso. Questo valore è facoltativo.

7. (Facoltativo) Per rimuovere una o più risorse dall'ambito, selezionare **Esclusioni**.

8. Nell'elenco a discesa **area di lavoro Log Analytics** per l'area supportata selezionare un'area di lavoro.

   > [!NOTE]
   > Se l'area di lavoro non rientra nell'ambito dell'assegnazione, concedere le autorizzazioni di *Collaboratore di Log Analytics* all'ID entità di sicurezza dell'assegnazione dei criteri. Se non si esegue questa operazione, è possibile che venga visualizzato un errore di distribuzione, ad esempio `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` per concedere l'accesso, esaminare [come configurare manualmente l'identità gestita](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  La casella di controllo **identità gestita** è selezionata perché l'iniziativa da assegnare include un criterio con l'effetto *deployIfNotExists* .
    
9. Nell'elenco a discesa **Percorso identità gestita** selezionare l'area appropriata.

10. Selezionare **Assegna**.

Dopo aver creato l'assegnazione, la pagina **code coverage di monitoraggio di Azure per le macchine virtuali** aggiorna la **copertura**dell'assegnazione, **lo stato di assegnazione**, le **macchine virtuali conformi**e **lo stato di conformità** per riflettere le modifiche. 

Nella matrice seguente viene eseguito il mapping di ogni possibile stato di conformità per l'iniziativa.  

| Stato di conformità | DESCRIZIONE | 
|------------------|-------------|
| **Conforme** | A tutte le macchine virtuali nell'ambito sono distribuiti gli agenti di Log Analytics e dipendenza.|
| **Non conforme** | Non tutte le macchine virtuali nell'ambito dispongono della Log Analytics e degli agenti di dipendenza distribuiti e potrebbero richiedere la correzione.|
| **Non avviata** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si dispone di privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | Nessun criterio assegnato. | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario di fornire l'accesso. In alternativa, visualizzare le assegnazioni di conformità e gestione tramite le sottoscrizioni o i gruppi di gestione figlio. 

Nella tabella seguente viene eseguito il mapping di ogni possibile stato di assegnazione per l'iniziativa.

| Stato di assegnazione | DESCRIZIONE | 
|------------------|-------------|
| **Success** | A tutte le macchine virtuali nell'ambito sono distribuiti gli agenti di Log Analytics e dipendenza.|
| **Warning** | La sottoscrizione non è inclusa in un gruppo di gestione.|
| **Non avviata** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si dispone di privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | Non esiste alcuna macchina virtuale o non è stato assegnato alcun criterio. | 
| **Azione** | Assegnare un criterio o modificare un'assegnazione. | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario di fornire l'accesso. In alternativa, visualizzare le assegnazioni di conformità e gestione tramite le sottoscrizioni o i gruppi di gestione figlio.

## <a name="review-and-remediate-the-compliance-results"></a>Esaminare i risultati di conformità e risolvere eventuali problemi

L'esempio seguente è relativo a una macchina virtuale di Azure, ma si applica anche ai set di scalabilità di macchine virtuali. Per informazioni su come esaminare i risultati di conformità, vedere [identificare i risultati](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)della non conformità. Nella pagina **copertura criteri monitoraggio di Azure per le macchine virtuali** selezionare un gruppo di gestione o una sottoscrizione della tabella. Selezionare **Visualizza conformità** selezionando i puntini di sospensione (...).   

![Conformità dei criteri per le macchine virtuali di Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

In base ai risultati dei criteri inclusi nell'iniziativa, le macchine virtuali vengono segnalate come non conformi negli scenari seguenti:

* Log Analytics Agent o Dependency Agent non è stato distribuito.  
    Questo scenario è tipico per un ambito con macchine virtuali esistenti. Per attenuarlo, distribuire gli agenti necessari [creando attività di correzione](../../governance/policy/how-to/remediate-resources.md) su criteri non conformi.  
    - \[\]Preview: distribuire l'agente di dipendenza per le macchine virtuali Linux
    - \[\]Preview: distribuire l'agente di dipendenza per le macchine virtuali Windows
    - \]di \[Preview: distribuire l'agente di Log Analytics per le macchine virtuali Linux
    - \[anteprima\]: distribuire l'agente di Log Analytics per le macchine virtuali Windows

* L'immagine di macchina virtuale (sistema operativo) non è identificata nella definizione dei criteri.  
    I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato. Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per rendere conforme l'immagine.  
    - \[Preview\]: audit Dependency Agent Deployment-VM Image (OS) Unlisted
    - \[Preview\]: Audit Log Analytics Agent Deployment-VM Image (OS) Unlisted

* Le macchine virtuali non si connettono all'area di lavoro Log Analytics specificata.  
    È possibile che alcune macchine virtuali nell'ambito dell'iniziativa si connettano a un'area di lavoro Log Analytics diversa da quella specificata nell'assegnazione dei criteri. Questo criterio è uno strumento che consente di identificare le macchine virtuali che fanno riferimento a un'area di lavoro non conforme.  
    - \[Preview\]: Audit Log Analytics area di lavoro per macchina virtuale-segnala mancata corrispondenza

## <a name="edit-an-initiative-assignment"></a>Modificare un'assegnazione di iniziativa

In qualsiasi momento dopo l'assegnazione di un'iniziativa a un gruppo di gestione o una sottoscrizione, è possibile modificarla per modificare le proprietà seguenti:

- Nome assegnazione
- DESCRIZIONE
- Assegnato da
- Area di lavoro Log Analytics
- Eccezioni

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali. 

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 

- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md). 
