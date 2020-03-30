---
title: Abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di AzureEnable Azure Monitor for VMs by using Azure Policy
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando Criteri di Azure.This article describes how you enable Azure Monitor for VMs for multiple Azure virtual machines or virtual machine scale sets by using Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 7069f2cc96b8876f5514acfa4ba49274b61be46f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282937"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di AzureEnable Azure Monitor for VMs by using Azure Policy

Questo articolo illustra come abilitare Monitoraggio di Azure per le macchine virtuali per le macchine virtuali di Azure o i set di scalabilità di macchine virtuali usando Criteri di Azure.This article explains how to enable Azure Monitor for VMs for Azure virtual machines or virtual machine scale sets by using Azure Policy. Al termine di questo processo, sarà stata configurata correttamente l'abilitazione degli agenti Log Analytics e dependency e le macchine virtuali identificate che non sono conformi.

Per individuare, gestire e abilitare Monitoraggio di Azure per le macchine virtuali per tutte le macchine virtuali di Azure o i set di scalabilità di macchine virtuali, è possibile usare Criteri di Azure o Azure PowerShell.To discover, manage, and enable Azure Monitor for VMs for all of your Azure virtual machines or virtual machine scale sets, you can use either Azure Policy or Azure PowerShell. Criteri di Azure è il metodo consigliato perché è possibile gestire le definizioni dei criteri per gestire in modo efficace le sottoscrizioni per garantire la conformità coerente e l'abilitazione automatica delle macchine virtuali di cui è stato appena eseguito il provisioning. Queste definizioni di politica:

* Distribuiscono l'agente di Log Analytics e Dependency Agent.
* Creano report con i risultati relativi alla conformità.
* Correggere le macchine virtuali non conformi.

Se si è interessati a eseguire queste attività con Azure PowerShell o un modello di Azure Resource Manager, vedere Abilitare Monitoraggio di Azure per le macchine virtuali usando i modelli di Azure PowerShell o Azure Resource Manager.If you're interested in accomplishing these tasks with Azure PowerShell or an Azure Resource Manager template, [see Enable Azure Monitor for VMs using Azure PowerShell or Azure Resource Manager templates.](vminsights-enable-at-scale-powershell.md)

## <a name="prerequisites"></a>Prerequisiti
Prima di usare criteri per l'onboarding delle macchine virtuali di Azure e dei set di scalabilità delle macchine virtuali nel monitoraggio di Azure per le macchine virtuali, è necessario abilitare la soluzione VMInsights nell'area di lavoro che verrà usata per archiviare i dati di monitoraggio. Questa attività può essere completata dalla pagina **Introduzione** in Monitoraggio di Azure nella scheda **Altre opzioni di onboarding.**  Selezionare **Configura un'area di lavoro**per selezionare l'area di lavoro da configurare.

![Configurare l'area di lavoro](media/vminsights-enable-at-scale-policy/configure-workspace.png)

È inoltre possibile configurare l'area di lavoro scegliendo **Abilita tramite criteri** e quindi selezionare il pulsante Configura barra degli strumenti **dell'area di lavoro.**  Verrà installata la soluzione VMInsights nell'area di lavoro selezionata che consentirà all'area di lavoro di archiviare i dati di monitoraggio inviati dalle macchine virtuali e dai set di scalabilità delle macchine virtuali abilitati tramite criteri. 

![Abilitare l'utilizzo dei criteriEnable using policy](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Panoramica della funzionalità Gestione copertura criteri

Monitoraggio di Azure per la copertura dei criteri per le macchine virtuali semplifica l'individuazione, la gestione e l'abilitazione su larga scala dell'iniziativa **Abilita Monitoraggio di Azure per le macchine virtuali,** incluse le definizioni dei criteri menzionate in precedenza. Per accedere a questa funzionalità, selezionare Altre opzioni di **onboarding** nella scheda **Introduzione** in Monitoraggio di Azure per macchine virtuali. Selezionare **Gestisci copertura criteri** per aprire la pagina Copertura criteri di Monitoraggio di Azure per macchine **virtuali.**

![Scheda Guida di Monitoraggio di Azure dalle macchine virtualiAzure Monitor from VMs Get Started tab](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Da qui è possibile controllare e gestire la copertura per l'iniziativa tra i gruppi di gestione e le sottoscrizioni. È possibile comprendere il numero di macchine virtuali presenti in ognuno dei gruppi di gestione e delle sottoscrizioni e il relativo stato di conformità.

![Pagina Monitoraggio di Azure per macchine virtuali Gestisci criteriAzure Monitor for VMs Manage Policy page](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Queste informazioni sono utili per pianificare ed eseguire lo scenario di governance per Monitoraggio di Azure per le macchine virtuali da un'unica posizione centrale. Mentre Criteri di Azure offre una visualizzazione di conformità quando un criterio o un'iniziativa viene assegnata a un ambito, con questa nuova pagina è possibile individuare dove il criterio o l'iniziativa non è assegnata e assegnarla sul posto. Tutte le azioni come assegnare, visualizzare e modificare direttamente il reindirizzamento a Criteri di Azure.All actions like assign, view, and edit redirect to Azure Policy directly. La pagina **Copertura criteri di Monitoraggio di Azure per le macchine virtuali** è un'esperienza estesa e integrata solo per l'iniziativa Enable Azure Monitor for **VMs**.

Da questa pagina è anche possibile configurare l'area di lavoro Log Analytics per Monitoraggio di Azure per le macchine virtuali:

- Installa la soluzione Mappa del servizio.
- Abilita i contatori delle prestazioni del sistema operativo utilizzati dai grafici delle prestazioni, dalle cartelle di lavoro e dalle query e avvisi di log personalizzati.

![Monitoraggio di Azure per le macchine virtuali configurare l'area di lavoroAzure Monitor for VMs configure workspace](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Questa opzione non è correlata ad alcuna azione dei criteri. È disponibile per fornire un modo semplice per soddisfare i [prerequisiti](vminsights-enable-overview.md) necessari per abilitare Monitoraggio di Azure per le macchine virtuali.  

### <a name="what-information-is-available-on-this-page"></a>Quali informazioni sono disponibili in questa pagina?

Nella tabella seguente viene fornita una suddivisione delle informazioni presentate nella pagina di copertura dei criteri e viene illustrato come interpretarle.

| Funzione | Descrizione | 
|----------|-------------| 
| **Scope** | Gruppo di gestione e sottoscrizioni a cui è stato o a cui è stato ereditato l'accesso con la possibilità di eseguire il drill-down della gerarchia del gruppo di gestione.|
| **Ruolo** | Il ruolo per l'ambito, che può essere lettore, proprietario o collaboratore. In alcuni casi, potrebbe essere vuoto per indicare che è possibile avere accesso alla sottoscrizione ma non al gruppo di gestione a cui appartiene. Le informazioni in altre colonne variano a seconda del ruolo. Il ruolo è fondamentale per determinare quali dati è possibile visualizzare e azioni che è possibile eseguire in termini di assegnazione di criteri o iniziative (proprietario), modifica o visualizzazione della conformità. |
| **Totale macchine virtuali** | Numero di macchine virtuali in tale ambito. Per un gruppo di gestione, è una somma di macchine virtuali annidate sotto le sottoscrizioni o il gruppo di gestione figlio. |
| **Copertura delle assegnazioni** | Percentuale di macchine virtuali coperte dal criterio o dall'iniziativa. |
| **Stato assegnazione** | Informazioni sullo stato della polizza o dell'assegnazione dell'iniziativa. |
| **Macchine virtuali conformi** | Numero di macchine virtuali conformi ai criteri o all'iniziativa. Per l'iniziativa **Abilitare Monitoraggio di Azure per le macchine virtuali,** questo è il numero di macchine virtuali con agente Log Analytics e agente di dipendenza. In alcuni casi, potrebbe apparire vuoto a causa di nessuna assegnazione, nessuna macchine virtuali o autorizzazioni sufficienti. Le informazioni vengono fornite in **Stato di conformità**. |
| **Conformità** | Il numero di conformità complessivo è la somma delle risorse distinte conformi divise per la somma di tutte le risorse distinte. |
| **Stato conformità** | Informazioni sullo stato di conformità per la policy o l'assegnazione dell'iniziativa.|

Quando si assegna il criterio o l'iniziativa, l'ambito selezionato nell'assegnazione può essere l'ambito elencato o un sottoinsieme di esso. Ad esempio, è possibile che sia stata creata un'assegnazione per una sottoscrizione (ambito dei criteri) e non per un gruppo di gestione (ambito di copertura). In questo caso, il valore di **Copertura assegnazione** indica le macchine virtuali nell'ambito dei criteri o dell'iniziativa diviso per le macchine virtuali nell'ambito di code coverage. In un altro caso, è possibile che alcune macchine virtuali, gruppi di risorse o una sottoscrizione siano state escluse dall'ambito dei criteri. Se il valore è vuoto, indica che il criterio o l'iniziativa non esiste o che non si dispone dell'autorizzazione. Le informazioni vengono fornite in **Stato assegnazione**.

## <a name="enable-by-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure

Per abilitare Monitoraggio di Azure per le macchine virtuali usando Criteri di Azure nel tenant:

- Assegnare l'iniziativa a un ambito: gruppo di gestione, sottoscrizione o gruppo di risorse.
- Esaminare e correggere i risultati di conformità.

Per altre informazioni sull'assegnazione di Criteri di Azure, vedere [Panoramica di Criteri di Azure](../../governance/policy/overview.md#policy-assignment) ed esaminare la [panoramica dei gruppi di gestione](../../governance/management-groups/overview.md) prima di continuare.

### <a name="policies-for-azure-vms"></a>Criteri per le macchine virtuali di AzurePolicies for Azure VMs

Le definizioni dei criteri per una macchina virtuale di Azure sono elencate nella tabella seguente.

|Nome |Descrizione |Type |
|-----|------------|-----|
|Abilita Monitoraggio di Azure per le macchine virtuali |Abilitare Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |Iniziativa |
|Distribuzione dell'agente di dipendenza di controllo: immagine della macchina virtuale (OS) non in elencoAudit Dependency agent deployment – VM image (OS) unlisted |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale non è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuzione dell'agente di Audit Log Analytics - Immagine della macchina virtuale non in elencoAudit Log Analytics agent deployment – VM image (OS) unlisted |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale non è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente di dipendenza per le macchine virtuali LinuxDeploy Dependency agent for Linux VMs |Distribuire l'agente di dipendenza per le macchine virtuali Linux se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente di dipendenza per le macchine virtuali WindowsDeploy Dependency agent for Windows VMs |Distribuire l'agente di dipendenza per le macchine virtuali Windows se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente Log Analytics per macchine virtuali LinuxDeploy Log Analytics agent for Linux VMs |Distribuire l'agente log Analytics per le macchine virtuali Linux se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente Log Analytics per le macchine virtuali WindowsDeploy Log Analytics agent for Windows VMs |Distribuire l'agente Log Analytics per le macchine virtuali Windows se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Criteri per i set di scalabilità delle macchine virtuali di AzurePolicies for Azure virtual machine scale sets

Le definizioni dei criteri per un set di scalabilità di macchine virtuali di Azure sono elencate nella tabella seguente.

|Nome |Descrizione |Type |
|-----|------------|-----|
|Abilitare Monitoraggio di Azure per i set di scalabilità delle macchine virtualiEnable Azure Monitor for virtual machine scale sets |Abilitare Monitoraggio di Azure per i set di scalabilità delle macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se il criterio di aggiornamento del set di scalabilità è impostato su Manuale, applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento su di esse. Nell'interfaccia della `az vmss update-instances`riga di comando, si tratta di . |Iniziativa |
|Distribuzione dell'agente di dipendenza di controllo nei set di scalabilità delle macchine virtuali: immagine della macchina virtuale (OS) non in elencoAudit Dependency agent deployment in virtual machine scale sets – VM image (OS) unlisted |Segnala il set di scalabilità della macchina virtuale come non conforme se l'immagine della macchina virtuale non è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuzione dell'agente di Audit Log Analytics nei set di scalabilità delle macchine virtuali: immagine della macchina virtuale (OS) non in elencoAudit Log Analytics agent deployment in virtual machine scale sets – VM image (OS) unlisted |Segnala il set di scalabilità della macchina virtuale come non conforme se l'immagine della macchina virtuale non è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente di dipendenza per i set di scalabilità delle macchine virtuali LinuxDeploy Dependency agent for Linux virtual machine scale sets |Distribuire l'agente di dipendenza per i set di scalabilità delle macchine virtuali Linux se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente di dipendenza per i set di scalabilità delle macchine virtuali WindowsDeploy Dependency agent for Windows virtual machine scale sets |Distribuire l'agente di dipendenza per i set di scalabilità delle macchine virtuali Windows se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente Log Analytics per i set di scalabilità delle macchine virtuali LinuxDeploy Log Analytics agent for Linux virtual machine scale sets |Distribuire l'agente log Analytics per i set di scalabilità delle macchine virtuali Linux se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |
|Distribuire l'agente log Analytics per i set di scalabilità delle macchine virtuali WindowsDeploy Log Analytics agent for Windows virtual machine scale sets |Distribuire l'agente log Analytics per i set di scalabilità delle macchine virtuali Windows se l'immagine della macchina virtuale (OS) è definita nell'elenco e l'agente non è installato. |Policy |

Il criterio autonomo (non incluso nell'iniziativa) è descritto qui:

|Nome |Descrizione |Type |
|-----|------------|-----|
|Area di lavoro di Log Analytics di controllo per VM - Mancata corrispondenza del reportAudit Log Analytics workspace for VM – Report mismatch |Segnalare le macchine virtuali come non conformi se non effettuano la registrazione nell'area di lavoro di Log Analytics specificata nell'assegnazione di criteri o iniziative. |Policy |

### <a name="assign-the-azure-monitor-initiative"></a>Assegnare l'iniziativa di Monitoraggio di Azure

Per creare l'assegnazione dei criteri dalla pagina Copertura dei criteri di **Monitoraggio di Azure per le macchine virtuali,** eseguire la procedura seguente. Per informazioni su come completare questi passaggi, vedere  [Creare un'assegnazione di criteri dal portale di Azure](../../governance/policy/assign-policy-portal.md).

Quando si assegna il criterio o l'iniziativa, l'ambito selezionato nell'assegnazione potrebbe essere l'ambito elencato qui o un sottoinsieme di esso. Ad esempio, è possibile che sia stata creata un'assegnazione per la sottoscrizione (ambito dei criteri) e non per il gruppo di gestione (ambito di copertura). In questo caso, la percentuale di copertura indicherebbe le macchine virtuali nell'ambito dei criteri o dell'iniziativa diviso per le macchine virtuali nell'ambito di copertura. In un altro caso, è possibile che alcune macchine virtuali, i gruppi di risorse siano escluse o una sottoscrizione dall'ambito dei criteri. Se è vuoto, indica che il criterio o l'iniziativa non esiste o che non si dispone delle autorizzazioni. Le informazioni vengono fornite in **Stato assegnazione**.

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Nel portale di Azure selezionare **Monitor .** 

3. Scegliere **Macchine virtuali** nella sezione Informazioni **dettagliate.**
 
4. Selezionare la scheda **Inizia.** Nella pagina selezionare **Gestisci copertura criteri**.

5. Selezionare un gruppo di gestione o una sottoscrizione dalla tabella. Selezionare **Ambito** selezionando i conio ai lipsia (...). Nell'esempio, un ambito limita l'assegnazione dei criteri a un raggruppamento di macchine virtuali per l'applicazione.

6. Nella pagina **Assegnazione criteri** di Azure viene prepopolata con l'iniziativa **Abilitare Monitoraggio di Azure per le macchine virtuali.** 
    La casella **Nome assegnazione** viene compilata automaticamente con il nome dell'iniziativa, ma è possibile modificarla. È inoltre possibile aggiungere una descrizione facoltativa. La casella **Assegnato da** viene inserita automaticamente in base all'utente connesso. Questo valore è facoltativo.

7. (Facoltativo) Per rimuovere una o più risorse dall'ambito, selezionare **Esclusioni**.

8. Nell'elenco a discesa **area di lavoro Log Analytics** per l'area supportata selezionare un'area di lavoro.

   > [!NOTE]
   > Se l'area di lavoro non rientra nell'ambito dell'assegnazione, concedere le autorizzazioni di *Collaboratore di Log Analytics* all'ID entità di sicurezza dell'assegnazione dei criteri. Se non si riesce a eseguire questa `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` operazione, è possibile che venga visualizzato un errore di distribuzione, ad esempio Per concedere l'accesso, vedere [come configurare manualmente l'identità gestita.](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)
   > 
   >  La casella di controllo **Identità gestita** è selezionata perché l'iniziativa assegnata include un criterio con l'effetto *deployIfNotExists.*
    
9. Nell'elenco a discesa **Percorso identità gestita** selezionare l'area appropriata.

10. Selezionare **Assegna**.

Dopo aver creato l'assegnazione, la pagina **Monitoraggio di Azure per copertura criteri macchine virtuali** aggiorna **Copertura assegnazione**, **Stato assegnazione**, **Macchine virtuali conformi**e **Stato conformità** per riflettere le modifiche. 

La matrice seguente esegue il mapping di ogni possibile stato di conformità per l'iniziativa.  

| Stato di conformità | Descrizione | 
|------------------|-------------|
| **Conforme** | Per tutte le macchine virtuali nell'ambito sono distribuiti gli agenti Log Analytics e Dependency.|
| **Non conforme** | Non tutte le macchine virtuali nell'ambito dispongono degli agenti Log Analytics e Dependency distribuiti e potrebbero richiedere una correzione.|
| **Non avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si dispone di privilegi sufficienti per il gruppo di gestione. <sup>1 : il</sup> nome del | 
| **Vuoto** | Nessun criterio assegnato. | 

<sup>1</sup> Se non si ha accesso al gruppo di gestione, chiedere al proprietario di fornire l'accesso. In alternativa, visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o le sottoscrizioni. 

Nella tabella seguente viene eseguito il mapping di ogni possibile stato di assegnazione per l'iniziativa.

| Stato assegnazione | Descrizione | 
|------------------|-------------|
| **Successo** | Per tutte le macchine virtuali nell'ambito sono distribuiti gli agenti Log Analytics e Dependency.|
| **Avviso** | La sottoscrizione non è in un gruppo di gestione.|
| **Non avviato** | È stata aggiunta una nuova assegnazione. |
| **Blocco** | Non si dispone di privilegi sufficienti per il gruppo di gestione. <sup>1 : il</sup> nome del | 
| **Vuoto** | Non esistono macchine virtuali o non è assegnato alcun criterio. | 
| **Azione** | Assegnare un criterio o modificare un'assegnazione. | 

<sup>1</sup> Se non si ha accesso al gruppo di gestione, chiedere al proprietario di fornire l'accesso. In alternativa, visualizzare la conformità e gestire le assegnazioni tramite i gruppi di gestione figlio o le sottoscrizioni.

## <a name="review-and-remediate-the-compliance-results"></a>Esaminare i risultati di conformità e risolvere eventuali problemi

L'esempio seguente è per una macchina virtuale di Azure, ma si applica anche ai set di scalabilità delle macchine virtuali. Per informazioni su come esaminare i risultati della conformità, vedere Identificare i risultati della [non conformità.](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources) Nella pagina **Copertura criteri di Monitoraggio di Azure per le macchine virtuali** selezionare un gruppo di gestione o una sottoscrizione dalla tabella. Selezionare **Visualizza conformità** selezionando i conioni ai lipsia (...).   

![Conformità dei criteri per le macchine virtuali di Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

In base ai risultati dei criteri inclusi nell'iniziativa, le macchine virtuali vengono segnalate come non conformi negli scenari seguenti:

* L'agente Log Analytics o l'agente di dipendenza non è distribuito.  
    Questo scenario è tipico per un ambito con macchine virtuali esistenti. Per attenuarlo, distribuire gli agenti necessari [creando attività](../../governance/policy/how-to/remediate-resources.md) di correzione in criteri non conformi.  
    - Distribuire l'agente di dipendenza per le macchine virtuali LinuxDeploy Dependency agent for Linux VMs
    - Distribuire l'agente di dipendenza per le macchine virtuali WindowsDeploy Dependency agent for Windows VMs
    - Distribuire l'agente Log Analytics per macchine virtuali LinuxDeploy Log Analytics agent for Linux VMs
    - Distribuire l'agente Log Analytics per le macchine virtuali WindowsDeploy Log Analytics agent for Windows VMs

* L'immagine della macchina virtuale non è identificata nella definizione dei criteri.  
    I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato. Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per rendere conforme l'immagine.  
    - Distribuzione dell'agente di dipendenza di controllo: immagine della macchina virtuale (OS) non in elencoAudit Dependency agent deployment – VM image (OS) unlisted
    - Distribuzione dell'agente di Audit Log Analytics - Immagine della macchina virtuale non in elencoAudit Log Analytics agent deployment – VM image (OS) unlisted

* Le macchine virtuali non si connettono all'area di lavoro Log Analytics specificata.  
    È possibile che alcune macchine virtuali nell'ambito dell'iniziativa si connettano a un'area di lavoro Log Analytics diversa da quella specificata nell'assegnazione dei criteri. Questo criterio è uno strumento per identificare le macchine virtuali che segnalano a un'area di lavoro non conforme.  
    - Area di lavoro di Log Analytics di controllo per VM - Mancata corrispondenza del reportAudit Log Analytics workspace for VM – Report mismatch

## <a name="edit-an-initiative-assignment"></a>Modificare un'assegnazione di iniziativa

In qualsiasi momento dopo l'assegnazione di un'iniziativa a un gruppo di gestione o a una sottoscrizione, è possibile modificarla per modificare le proprietà seguenti:In any time after you assign an initiative to a management group or subscription, you can edit it to modify the following properties:

- Nome assegnazione
- Descrizione
- Assegnato da
- Area di lavoro Log Analytics
- Eccezioni

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali. 

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 

- Per identificare i colli di bottiglia e l'utilizzo complessivo con le prestazioni della macchina virtuale, vedere [Visualizzare le prestazioni della macchina virtuale](vminsights-performance.md)di Azure.To identify bottlenecks and overall utilization with your VM's performance, see View Azure VM performance . 
