---
title: Abilitare il monitoraggio di Azure per le macchine virtuali usando criteri di Azure | Microsoft Docs
description: Questo articolo descrive come si abilita il monitoraggio di Azure per le macchine virtuali per più macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando criteri di Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: cf06004c70609dbea59a47b207e3568299260a82
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594438"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) con i criteri di Azure

Questo articolo illustra come abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando criteri di Azure. Alla fine di questo processo, verrà avere configurato che consente al Log Analitica e degli agenti di dipendenza e identificate le macchine virtuali che non sono conformi.

Per individuare, gestire e abilitare il monitoraggio di Azure per le macchine virtuali per tutte le macchine virtuali di Azure o un set di scalabilità di macchine virtuali, è possibile usare criteri di Azure o Azure PowerShell. Criteri di Azure è il metodo consigliato perché è possibile gestire le definizioni dei criteri per gestire efficacemente le sottoscrizioni per garantire il rispetto coerente e l'abilitazione automatica di appena effettuato il provisioning di macchine virtuali. Queste definizioni di criteri:

* Distribuiscono l'agente di Log Analytics e Dependency Agent.
* Creano report con i risultati relativi alla conformità.
* La correzione per le macchine virtuali non conformi.

Se è interessati a eseguire queste attività con Azure PowerShell o un modello Azure Resource Manager, vedere [abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) usando i modelli di Azure PowerShell o Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Panoramica delle funzionalità di Code Coverage dei criteri di gestione

Originariamente, l'esperienza con criteri di Azure per gestire e distribuire le definizioni dei criteri di monitoraggio di Azure per le macchine virtuali è stata eseguita in modo esclusivo da criteri di Azure. La funzionalità Gestione criteri Coverage rende più semplice e facile da individuare, gestire e abilitare su larga scala il **abilitare il monitoraggio di Azure per le macchine virtuali** iniziativa, che include le definizioni dei criteri indicate in precedenza. È possibile accedere a questa nuova funzionalità dal **iniziare a usare** scheda in Monitoraggio di Azure per le macchine virtuali. Selezionare **gestire i criteri di copertura** per aprire il **monitoraggio di Azure per la copertura dei criteri di macchine virtuali** pagina.

![Monitoraggio di Azure dalla scheda macchine virtuali di iniziare](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

A questo punto, è possibile controllare e gestire code coverage per l'iniziativa tra gruppi di gestione e sottoscrizioni. È possibile comprendere quante macchine virtuali presenti in ognuno dei gruppi di gestione e le sottoscrizioni e il relativo stato di conformità.

![Monitoraggio di Azure per la pagina dei criteri di gestione di macchine virtuali](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Queste informazioni sono utili per pianificare ed eseguire lo scenario di governance per monitoraggio di Azure per le macchine virtuali da una posizione centrale. Anche se criteri di Azure offre una vista conformità quando un criterio o un'iniziativa viene assegnata a un ambito, con questa nuova pagina è possibile individuare in cui non è assegnato il criterio o un'iniziativa e assegnarla posto. Tutte le azioni, ad esempio assegnare, visualizzare e modificare direttamente reindirizzamento a criteri di Azure. Il **monitoraggio di Azure per macchine virtuali di criteri di copertura** pagina offre un'esperienza estesa e integrata per solo l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali**.

In questa pagina è anche possibile configurare l'area di lavoro di Log Analitica per monitoraggio di Azure per le macchine virtuali, che:

- Installa le soluzioni di installazione di mapping dei servizi e informazioni dettagliate sull'infrastruttura.
- Abilita i contatori delle prestazioni del sistema operativo usati dai grafici delle prestazioni, le cartelle di lavoro e le query di log personalizzato e gli avvisi.

![Monitoraggio di Azure per le macchine virtuali Configura area di lavoro](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Questa opzione non è correlata a eventuali azioni dei criteri. È disponibile per fornire un modo semplice per soddisfare le [prerequisiti](vminsights-enable-overview.md) necessaria per l'abilitazione di monitoraggio di Azure per le macchine virtuali.  

### <a name="what-information-is-available-on-this-page"></a>Quali informazioni sono disponibili in questa pagina?
Nella tabella seguente fornisce un riepilogo delle informazioni presentate nella pagina di code coverage dei criteri e sulla loro interpretazione.

| Funzione | Descrizione | 
|----------|-------------| 
| **Ambito** | Gruppo di gestione e le sottoscrizioni o ereditato l'accesso con possibilità di eseguire il drill-down attraverso la gerarchia dei gruppi di gestione.|
| **Ruolo** | Il ruolo all'ambito, che potrebbe essere lettore, proprietario o collaboratore. In alcuni casi, potrebbe sembrare vuoto per indicare che è possibile accedere alla sottoscrizione ma non per il gruppo di gestione a cui appartiene. Informazioni nelle altre colonne variano in base al ruolo. Il ruolo è una chiave per determinare quali dati è possibile visualizzare e azioni che è possibile eseguire in termini di assegnazione di criteri o le iniziative (proprietario), un'operazione di modifica o la visualizzazione di conformità. |
| **Totale macchine virtuali** | Numero di macchine virtuali in tale ambito. Per un gruppo di gestione è una somma delle macchine virtuali nidificate sotto le sottoscrizioni o un gruppo di gestione figlio. |
| **Code Coverage di assegnazione** | Percentuale di macchine virtuali che vengono analizzate il criterio o un'iniziativa. |
| **Stato di assegnazione** | Informazioni sullo stato di assegnazione di criterio o un'iniziativa. |
| **VM compatibili** | Numero di macchine virtuali che sono conformi con il criterio o un'iniziativa. Per l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali**, questo è il numero di macchine virtuali che hanno sia Log Analitica agente e l'agente di dipendenza. In alcuni casi, potrebbe sembrare vuoto Nessuna assegnazione, non le macchine virtuali o autorizzazioni non sufficienti. Vengono fornite informazioni sotto **lo stato di conformità**. |
| **Conformità** | Il numero di conformità complessivo è la somma di distinte risorse conformi divisa per la somma di tutte le risorse distinte. |
| **Stato di conformità** | Informazioni sullo stato di conformità per l'assegnazione di criterio o un'iniziativa.|

Quando si assegna il criterio o un'iniziativa, l'ambito selezionato nell'assegnazione può essere l'ambito elencati o un suo sottoinsieme. Ad esempio, si potrebbe aver creato un'assegnazione per una sottoscrizione (ambito dei criteri) e non un gruppo di gestione (ambito di code coverage). In questo caso, il valore di **Coverage assegnazione** indica le macchine virtuali in cui il criterio o l'ambito dell'iniziativa diviso per le macchine virtuali nell'ambito di code coverage. In un altro caso, si potrebbe sono escluse alcune macchine virtuali, i gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se il valore è vuoto, significa che il criterio o un'iniziativa non esiste o non si è autorizzati. Vengono fornite informazioni sotto **stato di assegnazione**.

## <a name="enable-by-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure

Per abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di Azure nel tenant:

- Assegna l'iniziativa a un ambito: gruppo di gestione, sottoscrizione o gruppo di risorse.
- Esaminare e correggere i risultati di conformità.

Per altre informazioni sull'assegnazione di Criteri di Azure, vedere [Panoramica di Criteri di Azure](../../governance/policy/overview.md#policy-assignment) ed esaminare la [panoramica dei gruppi di gestione](../../governance/management-groups/index.md) prima di continuare.

### <a name="policies-for-azure-vms"></a>Criteri per le macchine virtuali di Azure

Nella tabella seguente sono elencate le definizioni dei criteri per una macchina virtuale di Azure.

|Name |Descrizione |Type |
|-----|------------|-----|
|\[Anteprima\]: Abilita Monitoraggio di Azure per le macchine virtuali |Abilitare il monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |Iniziativa |
|\[Anteprima\]: Distribuzione dell'agente di dipendenza: immagine di macchina virtuale (OS) rimossa dall'elenco di controllo |Segnala le macchine virtuali come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e non è installato l'agente. |Policy |
|\[Anteprima\]: Distribuzione dell'agente di Log Analitica: immagine di macchina virtuale (OS) rimossa dall'elenco di controllo |Segnala le macchine virtuali come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e non è installato l'agente. |Policy |
|\[Anteprima\]: Distribuire Dependency agent per VM Linux |Se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente, distribuire Dependency agent per VM Linux. |Policy |
|\[Anteprima\]: Distribuire Dependency agent per le macchine virtuali Windows |Distribuire Dependency agent per le macchine virtuali Windows se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente. |Policy |
|\[Anteprima\]: Distribuire l'agente di Log Analitica per le macchine virtuali Linux |Distribuire l'agente Log Analitica per le macchine virtuali Linux se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente. |Policy |
|\[Anteprima\]: Distribuire l'agente di Log Analitica per le macchine virtuali Windows |Distribuire l'agente Log Analitica per le macchine virtuali Windows se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente. |Policy |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Set di scalabilità i criteri per la macchina virtuale di Azure

Nella tabella seguente sono elencate le definizioni dei criteri per un set di scalabilità di macchine virtuali di Azure.

|NOME |DESCRIZIONE |Type |
|-----|------------|-----|
|\[Anteprima\]: Abilitare il monitoraggio di Azure per il set di scalabilità di macchine virtuali |Abilitare il monitoraggio di Azure per il set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: Se i criteri di aggiornamento di set di scalabilità sono impostato su Manual, applicare l'estensione a tutte le macchine virtuali nel set di dalla chiamata di aggiornamento. Riga di comando, si tratta di az vmss update-instances. |Iniziativa |
|\[Anteprima\]: Controllo distribuzione di agente di dipendenza nel set di scalabilità di macchine virtuali: immagine di macchina virtuale (OS) non in elenco |Report impostata come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e non è installato l'agente di scalabilità di macchine virtuali. |Policy |
|\[Anteprima\]: Distribuzione dell'agente Log Analitica nei set di scalabilità di macchine virtuali: immagine di macchina virtuale (OS) rimossa dall'elenco di controllo |Report impostata come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e non è installato l'agente di scalabilità di macchine virtuali. |Policy |
|\[Anteprima\]: Distribuire Dependency agent per set di scalabilità di macchine virtuali Linux |Distribuire Dependency agent per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente Linux. |Policy |
|\[Anteprima\]: Distribuire Dependency agent per set di scalabilità di macchine virtuali Windows |Distribuire Dependency agent per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente di Windows. |Policy |
|\[Anteprima\]: Distribuire l'agente di Log Analitica per set di scalabilità di macchine virtuali Linux |Distribuire l'agente di Log Analitica per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente Linux. |Policy |
|\[Anteprima\]: Distribuire l'agente di Log Analitica per set di scalabilità di macchine virtuali Windows |Distribuire l'agente di Log Analitica per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente di Windows. |Policy |

Il criterio autonomo (non incluso nell'iniziativa) è descritto qui:

|Name |Descrizione |Type |
|-----|------------|-----|
|\[Anteprima\]: Controllo dell'area di lavoro di Log Analitica per la macchina virtuale: mancata corrispondenza del Report |Report delle macchine virtuali come non conforme se essi non sono l'accesso all'area di lavoro di Log Analitica specificata nell'assegnazione di criterio o un'iniziativa. |Policy |

### <a name="assign-the-azure-monitor-initiative"></a>Assegnare l'iniziativa di Monitoraggio di Azure
Per creare l'assegnazione di criteri dal **monitoraggio di Azure per macchine virtuali di criteri di Coverage** pagina, seguire questa procedura. Per informazioni su come completare questi passaggi, vedere  [Creare un'assegnazione di criteri dal portale di Azure](../../governance/policy/assign-policy-portal.md).

Quando si assegna il criterio o un'iniziativa, l'ambito selezionato nell'assegnazione può essere l'ambito elencati di seguito o un suo sottoinsieme. Ad esempio, si potrebbe aver creato un'assegnazione della sottoscrizione (ambito dei criteri) e non il gruppo di gestione (ambito di code coverage). In questo caso, indica la percentuale di copertura le macchine virtuali in cui il criterio o l'ambito dell'iniziativa diviso per le macchine virtuali nell'ambito del code coverage. In altri casi, potrebbe essere esclusi alcune macchine virtuali, o i gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se è vuoto, indica che il criterio o un'iniziativa non esiste o non si hanno autorizzazioni. Vengono fornite informazioni sotto **stato di assegnazione**.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure selezionare **Monitoraggio**. 

3. Scegliere **Macchine virtuali (anteprima)** nella sezione **Informazioni dettagliate**.
 
4. Selezionare il **iniziare a usare** scheda. Nella pagina, selezionare **gestire i criteri di copertura**.

5. Selezionare un gruppo di gestione o una sottoscrizione dalla tabella. Selezionare **ambito** selezionando i puntini di sospensione (...). Nell'esempio, un ambito limita l'assegnazione di criteri a un raggruppamento di macchine virtuali per l'imposizione.

6. Nel **assegnazione di criteri di Azure** pagina, è prepopolato con l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali**. 
    Il **nome dell'assegnazione** casella viene popolata automaticamente con il nome dell'iniziativa, ma è possibile modificarlo. È anche possibile aggiungere una descrizione facoltativa. Il **assegnato da** è popolato automaticamente basato su chi è collegato. Questo valore è facoltativo.

7. (Facoltativo) Per rimuovere una o più risorse dall'ambito, selezionare **Esclusioni**.

8. Nell'elenco a discesa **area di lavoro Log Analytics** per l'area supportata selezionare un'area di lavoro.

   > [!NOTE]
   > Se l'area di lavoro non rientra nell'ambito dell'assegnazione, concedere le autorizzazioni di *Collaboratore di Log Analytics* all'ID entità di sicurezza dell'assegnazione dei criteri. Se non si esegue questa operazione, si potrebbe riscontrare un errore di distribuzione, ad esempio `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` per concedere l'accesso, esaminare [come configurare manualmente l'identità gestita](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Il **identità gestito** casella di controllo è selezionata poiché l'iniziativa assegnato include un criterio con la *deployIfNotExists* effetto.
    
9. Nell'elenco a discesa **Percorso identità gestita** selezionare l'area appropriata.

10. Selezionare **Assegna**.

Dopo aver creato l'assegnazione e il **monitoraggio di Azure per la copertura dei criteri di macchine virtuali** pagina aggiornamenti **Coverage assegnazione**, **stato di assegnazione**, **conforme Le macchine virtuali**, e **lo stato di conformità** in modo da riflettere le modifiche. 

La matrice seguente esegue il mapping di ogni stato di conformità possibili per l'iniziativa.  

| Stato di conformità | Descrizione | 
|------------------|-------------|
| **Conforme** | Tutte le macchine virtuali nell'ambito sono gli agenti di Log Analitica e dipendenza ad essi distribuiti.|
| **Non conforme** | Non tutte le macchine virtuali nell'ambito sono il Log Analitica e Dependency Agent ad essi distribuito e potrebbero richiedere monitoraggio e aggiornamento.|
| **Non è stato avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si hanno privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | Non sono assegnati criteri. | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario per fornire l'accesso. In alternativa, visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o delle sottoscrizioni. 

Nella tabella seguente esegue il mapping di ogni stato di assegnazione possibili per l'iniziativa.

| Stato di assegnazione | Descrizione | 
|------------------|-------------|
| **Success** | Tutte le macchine virtuali nell'ambito sono gli agenti di Log Analitica e dipendenza ad essi distribuiti.|
| **Warning** | La sottoscrizione non è in un gruppo di gestione.|
| **Non è stato avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si hanno privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | VM non esiste o non è assegnato un criterio. | 
| **Azione** | Assegnare un criterio o modificare un'assegnazione. | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario per fornire l'accesso. In alternativa, visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o delle sottoscrizioni.

## <a name="review-and-remediate-the-compliance-results"></a>Esaminare i risultati di conformità e risolvere eventuali problemi

L'esempio seguente è per una macchina virtuale di Azure, ma si applica anche ai set di scalabilità di macchine virtuali. Per informazioni su come esaminare i risultati di conformità, vedere [identificare i risultati della non conformità](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Nel **monitoraggio di Azure per macchine virtuali di criteri di Coverage** pagina, selezionare un gruppo di gestione o una sottoscrizione dalla tabella. Selezionare **visualizzare la conformità** selezionando i puntini di sospensione (...).   

![Conformità dei criteri per le macchine virtuali di Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Le macchine virtuali in base ai risultati dei criteri inclusi con l'iniziativa, vengono segnalate come non conforme nei seguenti scenari:

* Agente di log Analitica o di dipendenza non è stato distribuito.  
    Questo scenario è tipico per un ambito con macchine virtuali esistenti. Per limitare il problema, distribuire gli agenti richiesti da [la creazione delle attività di correzione](../../governance/policy/how-to/remediate-resources.md) nei criteri non conformi.  
    - \[Anteprima\]: Distribuire Dependency agent per VM Linux
    - \[Anteprima\]: Distribuire Dependency agent per le macchine virtuali Windows
    - \[Anteprima\]: Distribuire l'agente di Log Analitica per le macchine virtuali Linux
    - \[Anteprima\]: Distribuire l'agente di Log Analitica per le macchine virtuali Windows

* Immagine di macchina virtuale (OS) non è identificato nella definizione dei criteri.  
    I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato. Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per rendere conforme l'immagine.  
    - \[Anteprima\]: Distribuzione dell'agente di dipendenza: immagine di macchina virtuale (OS) rimossa dall'elenco di controllo
    - \[Anteprima\]: Distribuzione dell'agente di Log Analitica: immagine di macchina virtuale (OS) rimossa dall'elenco di controllo

* Le macchine virtuali non si connettono all'area di lavoro Log Analytics specificata.  
    È possibile che alcune macchine virtuali nell'ambito dell'iniziativa si connettano a un'area di lavoro Log Analytics diversa da quella specificata nell'assegnazione dei criteri. Questo criterio è uno strumento per identificare le macchine virtuali riportano a un'area di lavoro non conforme.  
    - \[Anteprima\]: Controllo dell'area di lavoro di Log Analitica per la macchina virtuale: mancata corrispondenza del Report

## <a name="edit-an-initiative-assignment"></a>Modificare un'assegnazione di iniziativa

In qualsiasi momento dopo l'assegnazione di un'iniziativa a un gruppo di gestione o sottoscrizione, è possibile modificarlo per modificare le proprietà seguenti:

- Nome dell'assegnazione
- Descrizione
- Assegnato da
- Area di lavoro Log Analytics
- Eccezioni

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è attivato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con monitoraggio di Azure per le macchine virtuali. 

- Per informazioni su come usare la funzionalità di integrità, vedere [visualizzazione di monitoraggio di Azure per l'integrità di macchine virtuali](vminsights-health.md). 
- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 
- Per identificare i colli di bottiglia e utilizzo complessivo delle prestazioni della VM, vedere [prestazioni delle VM di Azure Visualizza](vminsights-performance.md). 
- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
