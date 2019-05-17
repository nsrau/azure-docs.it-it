---
title: Abilitare il monitoraggio di Azure per le macchine virtuali usando criteri di Azure | Microsoft Docs
description: Questo articolo descrive la procedura per attivare il monitoraggio di Azure per le macchine virtuali per più macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando criteri di Azure.
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
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524146"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) usando criteri di Azure

Questo articolo illustra come abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per macchine virtuali di Azure o set di scalabilità di macchine virtuali usando criteri di Azure. Alla fine di questo processo, verrà hanno completata configurate abilitando il Log Analitica e degli agenti di dipendenza e identificate le macchine virtuali che non sono conformi.

Per individuare, gestire e abilitare il monitoraggio di Azure per le macchine virtuali per tutte le macchine virtuali di Azure o un set di scalabilità di macchine virtuali, è possibile usare criteri di Azure o Azure PowerShell. Criteri di Azure è il metodo consigliato perché è possibile gestire le definizioni dei criteri per gestire efficacemente le sottoscrizioni per garantire il rispetto coerente e l'abilitazione automatica di appena effettuato il provisioning di macchine virtuali. Queste definizioni di criteri:

* Distribuiscono l'agente di Log Analytics e Dependency Agent.
* Creano report con i risultati relativi alla conformità.
* Risolvono i problemi di macchine virtuali non conformi.

Se è interessati a questa operazione con il modello di Azure PowerShell o Azure Resource Manager, vedere [abilitare l'uso di modelli di Azure PowerShell o Resource Manager](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Panoramica delle funzionalità di Code Coverage dei criteri di gestione

L'esperienza con criteri di Azure per gestire e distribuire le definizioni dei criteri di monitoraggio di Azure per le macchine virtuali originariamente è stata eseguita in modo esclusivo da criteri di Azure. Con il **copertura dei criteri di gestione** funzionalità, rende più semplice e facile da individuare, gestire e abilitare su larga scala il **abilitare il monitoraggio di Azure per le macchine virtuali** iniziativa, che include le definizioni dei criteri indicato in precedenza. È possibile accedere a questa nuova funzionalità dal **iniziare** scheda monitoraggio di Azure per le macchine virtuali selezionando **copertura dei criteri di gestione**. Visualizzata la pagina di copertura dei criteri di macchine virtuali. 

![Monitoraggio di Azure dalla scheda macchine virtuali di iniziare](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Da qui è possibile controllare e gestire code coverage per l'iniziativa tra i gruppi di gestione e sottoscrizioni, nonché comprendere quante macchine virtuali presenti in ognuno dei gruppi di gestione e le sottoscrizioni e il relativo stato di conformità.   

![Monitoraggio di Azure per la pagina dei criteri di gestione di macchine virtuali](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Queste informazioni sono utili per pianificare ed eseguire lo scenario di governance per monitoraggio di Azure per le macchine virtuali da una posizione centrale. Anche se criteri di Azure offre una vista conformità quando una criterio/iniziativa viene assegnata a un ambito, con questa nuova pagina è possibile individuare in cui il criterio/iniziativa non è assegnato e assegnarla sul posto. Tutte le azioni (assegnare, visualizzare, modificare) reindirizza direttamente al criterio di Azure. Monitoraggio di Azure per la pagina di copertura dei criteri di macchine virtuali è un'esperienza espansa e integrata per solo l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali**. 

Da questa pagina è anche possibile configurare l'area di lavoro di Log Analitica per monitoraggio di Azure per le macchine virtuali, che esegue le operazioni seguenti:

- Installa le soluzioni di installazione di mapping dei servizi e informazioni dettagliate sull'infrastruttura
- Abilita i contatori delle prestazioni del sistema operativo usati dai grafici delle prestazioni, le cartelle di lavoro e le query di log personalizzato e gli avvisi.

![Monitoraggio di Azure per le macchine virtuali Configura area di lavoro](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Questa opzione non è correlata a eventuali azioni di criteri ed è disponibile per fornire un modo semplice per soddisfare le [prerequisiti](vminsights-enable-overview.md) necessaria per l'abilitazione di monitoraggio di Azure per le macchine virtuali.  

### <a name="what-information-is-available-on-this-page"></a>Quali informazioni sono disponibili in questa pagina?
Nella tabella seguente fornisce una suddivisione di quali informazioni sono disponibili nella pagina dei criteri di code coverage e sulla loro interpretazione.

| Funzione | Descrizione | 
|----------|-------------| 
| **Ambito** | Gruppo di gestione e le sottoscrizioni o ereditata di accesso per la gerarchia di gruppo con possibilità di eseguire il drill-down tramite la gestione.|
| **Ruolo** | Il ruolo all'ambito, potrebbe essere proprietario del lettore o collaboratore. In alcuni casi, potrebbe sembrare vuoto per indicare che si abbia accesso alla sottoscrizione ma non per il gruppo di gestione a cui appartiene. Le informazioni nelle altre colonne variano in base al ruolo come chiave per determinare quali dati è possibile visualizzare e azioni che è possibile eseguire in termini di assegnazione di iniziativa/criterio (proprietario), un'operazione di modifica o la visualizzazione di conformità. |
| **Totale macchine virtuali** | Numero di macchine virtuali in tale ambito. Per un gruppo di gestione è una somma delle macchine virtuali nidificate sotto le sottoscrizioni e/o il gruppo di gestione figlio. |
| **Code Coverage di assegnazione** | Percentuale di macchine virtuali che vengono analizzate iniziativa/criteri. |
| **Stato di assegnazione** | In questo articolo, è possibile trovare le informazioni sullo stato dei criteri / iniziativa assegnazione. |
| **VM compatibili** | Numero di macchine virtuali che sono conformi con il criterio/iniziativa. Per l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali** questo è il numero di macchine virtuali che hanno sia Log Analitica agente e l'agente di dipendenza. In alcuni casi, potrebbe sembrare vuoto perché nessuna assegnazione, o non macchine virtuali o autorizzazioni non sufficienti. Vengono fornite informazioni in stato di conformità. |
| **Conformità** | Il numero di conformità complessivo è la somma di risorse distinte che sono conformi diviso per la somma di tutte le risorse distinte. |
| **Stato di conformità** | Informazioni sullo stato di conformità per il criterio / iniziativa assegnazione.|

Quando si assegnano l'iniziativa/criteri, l'ambito selezionato nell'assegnazione può essere l'ambito elencati o un suo sottoinsieme. Ad esempio, si potrebbe aver creato un'assegnazione per una sottoscrizione (ambito dei criteri) e non un gruppo di gestione (ambito di code coverage). In questo caso, il valore di **Coverage assegnazione** indicherebbe ambito le macchine virtuali nel criterio (o un'iniziativa) diviso per le macchine virtuali nell'ambito di code coverage. In un altro caso, si può avere escluse alcune macchine virtuali, i gruppi di risorse o una sottoscrizione dall'ambito dei criteri. Se il valore è vuoto, indica che il criterio/iniziativa non esiste oppure non si è autorizzati (informazioni in stato di assegnazione).

## <a name="enable-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure

Per abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di Azure nel tenant:

- Assegnare l'iniziativa a un ambito: gruppo di gestione, sottoscrizione o gruppo di risorse
- Esaminare i risultati di conformità e risolvere eventuali problemi

Per altre informazioni sull'assegnazione di Criteri di Azure, vedere [Panoramica di Criteri di Azure](../../governance/policy/overview.md#policy-assignment) ed esaminare la [panoramica dei gruppi di gestione](../../governance/management-groups/index.md) prima di continuare.

### <a name="policies-for-azure-vms"></a>Criteri per le macchine virtuali di Azure

Le definizioni dei criteri per una macchina virtuale di Azure sono elencati nella tabella seguente:

|NOME |Descrizione |Type |
|-----|------------|-----|
|[Anteprima]: Abilita Monitoraggio di Azure per le macchine virtuali |Abilita Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |Iniziativa |
|[Anteprima]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale (sistema operativo) non è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuisci Dependency Agent per le macchine virtuali Linux |Distribuisce Dependency Agent per le macchine virtuali Linux se l'immagine della macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuisci Dependency Agent per le macchine virtuali WindowsMs |Distribuisce Dependency Agent per le macchine virtuali Windows se l'immagine della macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Distribuisce l'agente di Log Analytics per le macchine virtuali Linux se l'immagine della macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Distribuisce l'agente di Log Analytics per le macchine virtuali Windows se l'immagine della macchina virtuale (sistema operativo) è definita nell'elenco e l'agente non è installato. |Criteri |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Set di scalabilità i criteri per la macchina virtuale di Azure

Le definizioni dei criteri per un set di scalabilità di macchine virtuali di Azure sono elencati nella tabella seguente:

|NOME |Descrizione |Type |
|-----|------------|-----|
|[Anteprima]: Abilitare il monitoraggio di Azure per il set di scalabilità di macchine Virtuali (VMSS) |Abilitare il monitoraggio di Azure per il set di scalabilità di macchine virtuali nell'ambito specificato (gestione gruppo, una sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se upgradePolicy del set di scalabilità è impostata su manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set di chiamata di aggiornamento. Riga di comando in questo sarebbe az vmss update-instances. |Iniziativa |
|[Anteprima]: Distribuzione dell'agente di dipendenza nel VMSS: immagini (sistema operativo VM) rimossa dall'elenco di controllo |Scalabilità di macchine virtuali di report impostata come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuzione dell'agente di controllo Log Analitica nei set di scalabilità: immagine (sistema operativo VM) non in elenco |Scalabilità di macchine virtuali di report impostata come non conforme se l'immagine di macchina virtuale (OS) non è definita nell'elenco e l'agente non è installato. |Criteri |
|[Anteprima]: Distribuire Dependency Agent per il set di scalabilità di macchine Virtuali Linux (VMSS) |Distribuire Dependency Agent per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente Linux. |Criteri |
|[Anteprima]: Distribuire Dependency Agent per set di scalabilità di macchine Virtuali Windows (VMSS) |Distribuire Dependency Agent per Windows set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente. |Criteri |
|[Anteprima]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Linux |Distribuire l'agente di Log Analitica per i set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente Linux. |Criteri |
|[Anteprima]: Distribuisci l'agente di Log Analytics per i set di scalabilità di macchine virtuali Windows |Distribuisci Log Analitica dell'agente per Windows set di scalabilità di macchine virtuali se l'immagine di macchina virtuale (OS) è definita nell'elenco e non è installato l'agente. |Criteri |

Il criterio autonomo (non incluso nell'iniziativa) è descritto qui:

|NOME |Descrizione |Type |
|-----|------------|-----|
|[Anteprima]: Controlla area di lavoro Log Analytics per la macchina virtuale - Segnala mancata corrispondenza |Segnala le macchine virtuali come non conformi se non si connettono all'area di lavoro Log Analytics specificata nell'assegnazione dei criteri o dell'iniziativa. |Criteri |

### <a name="assign-the-azure-monitor-initiative"></a>Assegnare l'iniziativa di Monitoraggio di Azure
Per creare l'assegnazione dei criteri di monitoraggio di Azure per la pagina di copertura dei criteri di macchine virtuali, eseguire la procedura seguente. Per informazioni su come completare questi passaggi, vedere  [Creare un'assegnazione di criteri dal portale di Azure](../../governance/policy/assign-policy-portal.md).

Quando si assegnano l'iniziativa/criteri, l'ambito selezionato nell'assegnazione può essere l'ambito elencati di seguito o un suo sottoinsieme. Ad esempio, potrebbe aver creato assegnazione per la sottoscrizione (ambito dei criteri) e non il gruppo di gestione (ambito di code coverage) in cui una copertura case % indicherebbe ambito le macchine virtuali nel criterio (o un'iniziativa) diviso per le macchine virtuali nell'ambito di code coverage. In qualche altro caso, si può avere escluse alcune macchine virtuali o gruppi di risorse o sottoscrizione dall'ambito dei criteri.  Nel caso in cui è vuoto, indica che entrambi i criteri / iniziativa non esiste o non si autorizzati (informazioni fornite in stato di assegnazione)  

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure selezionare **Monitoraggio**. 

3. Scegliere **Macchine virtuali (anteprima)** nella sezione **Informazioni dettagliate**.
 
4. Selezionare il **iniziare** scheda e nella pagina selezionare **gestire criteri di Coverage**.

5. Selezionare una sottoscrizione o gruppo di gestione dalla tabella e selezionare **ambito** facendo clic sui puntini di sospensione (...).      Nell'esempio, un ambito limita l'assegnazione dei criteri a un raggruppamento di macchine virtuali per l'applicazione.

6. Nel **assegnazione di criteri di Azure** pagina viene prepopolata con l'iniziativa **abilitare il monitoraggio di Azure per le macchine virtuali**. 
    Il **nome dell'assegnazione** casella viene popolata automaticamente con il nome dell'iniziativa, ma è possibile modificarlo. È anche possibile aggiungere una descrizione facoltativa. La casella **Assegnato da** viene popolata automaticamente in base all'utente che ha eseguito l'accesso e il valore è facoltativo.

7. (Facoltativo) Per rimuovere una o più risorse dall'ambito, selezionare **Esclusioni**.

8. Nell'elenco a discesa **area di lavoro Log Analytics** per l'area supportata selezionare un'area di lavoro.

   > [!NOTE]
   > Se l'area di lavoro non rientra nell'ambito dell'assegnazione, concedere le autorizzazioni di *Collaboratore di Log Analytics* all'ID entità di sicurezza dell'assegnazione dei criteri. Se non si esegue questa operazione potrebbe essere visualizzato un errore di distribuzione, ad esempio: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Per concedere l'accesso, vedere [Configurare manualmente l'identità gestita](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  La casella di controllo **Identità gestita** è selezionata perché l'iniziativa che viene assegnata include un criterio con l'effetto *deployIfNotExists*.
    
9. Nell'elenco a discesa **Percorso identità gestita** selezionare l'area appropriata.

10. Selezionare **Assegna**.

Dopo aver creato l'assegnazione e il monitoraggio di Azure per Code Coverage dei criteri di macchine virtuali di pagina verrà aggiornata la copertura di assegnazione, stato di assegnazione, macchine virtuali di conformità e lo stato di conformità per riflettere le modifiche. 

La matrice seguente esegue il mapping di ogni stato di conformità possibili per l'iniziativa.  

| Stato di conformità | Descrizione | 
|------------------|-------------|
| **Conforme** | Tutte le macchine virtuali nell'ambito sono gli agenti di Log Analitica e dipendenza ad essi distribuiti.|
| **Non conforme** | Non tutte le macchine virtuali nell'ambito di Analitica di Log e Dependency Agent ad essi distribuito possono richiedere monitoraggio e aggiornamento.|
| **Non è stato avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non disporre di privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | Nessun criterio assegnato. | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario per fornire l'accesso o visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o delle sottoscrizioni. 

Nella tabella seguente esegue il mapping di ogni stato di assegnazione possibili per l'iniziativa.

| Stato dell'assegnazione | Descrizione | 
|------------------|-------------|
| **Success** | Tutte le macchine virtuali nell'ambito sono gli agenti di Log Analitica e dipendenza ad essi distribuiti.|
| **Warning** | La sottoscrizione non è presente in un gruppo di gestione.|
| **Non è stato avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non disporre di privilegi sufficienti per il gruppo di gestione. <sup>1</sup> | 
| **Vuoto** | Nessuna VM esiste o non assegnati criteri. | 
| **Azione** | Assegnare criteri o modifica assegnazione | 

<sup>1</sup> se non si ha accesso al gruppo di gestione, chiedere a un proprietario per fornire l'accesso o visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o delle sottoscrizioni. 

## <a name="review-and-remediate-the-compliance-results"></a>Esaminare i risultati di conformità e risolvere eventuali problemi

Nell'esempio seguente è per una macchina virtuale di Azure, ma si applica anche ai set di scalabilità di macchine virtuali. Per informazioni su come esaminare i risultati di conformità, vedere [Identificare le risorse non conformi](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In Monitoraggio di Azure per la pagina di copertura dei criteri di macchine virtuali, selezionare una sottoscrizione o gruppo di gestione della tabella e selezionare **visualizzare la conformità** facendo clic sui puntini di sospensione (...).   

![Conformità dei criteri per le macchine virtuali di Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

In base ai risultati dei criteri inclusi nell'iniziativa, le macchine virtuali vengono segnalate come non conformi negli scenari seguenti:

* L'agente di Log Analytics o Dependency Agent non è distribuito.  
    Questo scenario è tipico per un ambito con macchine virtuali esistenti. Per attenuare il problema, distribuire gli agenti necessari [creando attività di correzione](../../governance/policy/how-to/remediate-resources.md) nei criteri non conformi.  
    - [Anteprima]: Deploy Dependency Agent for Linux VMs
    - [Anteprima]: Deploy Dependency Agent for Windows VMs
    - [Anteprima]: Deploy Log Analytics Agent for Linux VMs
    - [Anteprima]: Deploy Log Analytics Agent for Windows VMs

* L'immagine della macchina virtuale (sistema operativo) non è identificata nella definizione dei criteri.  
    I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato. Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per rendere conforme l'immagine.  
    - [Anteprima]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco
    - [Anteprima]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco

* Le macchine virtuali non si connettono all'area di lavoro Log Analytics specificata.  
    È possibile che alcune macchine virtuali nell'ambito dell'iniziativa si connettano a un'area di lavoro Log Analytics diversa da quella specificata nell'assegnazione dei criteri. Questo criterio è uno strumento per identificare le macchine virtuali collegate a un'area di lavoro non conforme.  
    - [Anteprima]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Modifica assegnazione dell'iniziativa

In qualsiasi momento dopo l'assegnazione di un'iniziativa a una sottoscrizione o gruppo di gestione, è possibile modificarlo per modificare le proprietà seguenti:

- Nome dell'assegnazione
- Descrizione
- Assegnato da
- Area di lavoro Log Analytics
- Eccezioni

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è attivato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con monitoraggio di Azure per le macchine virtuali. Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). Per individuare i colli di bottiglia e l'utilizzo generale con le prestazioni delle macchine virtuali, vedere [Visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md); per visualizzare le dipendenze dell'applicazione individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).