---
title: Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: Questo articolo descrive come eseguire l'onboarding e la configurazione di Monitoraggio di Azure per le macchine virtuali per ottenere informazioni sulle prestazioni di un'applicazione distribuita e sui problemi di integrità identificati.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2018
ms.author: magoedte
ms.openlocfilehash: 33d16e211667edc6c082ab8c101e69ee5875efb8
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390245"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Come eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali 
Questo articolo descrive come configurare Monitoraggio di Azure per le macchine virtuali per monitorare l'integrità del sistema operativo delle macchine virtuali di Azure e individuare e mappare le dipendenze delle applicazioni ospitate in tali macchine.  

Per abilitare Monitoraggio di Azure per le macchine virtuali è possibile usare uno dei metodi seguenti. Più avanti nell'articolo vengono fornite informazioni dettagliate sull'uso di ogni metodo.  

* Una singola macchina virtuale selezionando **Insights (anteprima)** direttamente dalla macchina virtuale.
* Più macchine virtuali di Azure usando Criteri di Azure per garantire che le macchine virtuali nuove ed esistenti valutate abbiano le dipendenze necessarie installate e siano configurate correttamente.  Le macchine virtuali non conformi vengono segnalate per permettere di scegliere il rimedio appropriato in base a ciò che non è conforme.  
* Più macchine virtuali di Azure o set di scalabilità di macchine virtuali in una sottoscrizione o un gruppo di risorse specificato, usando PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre di quanto descritto nelle sottosezioni seguenti.

### <a name="log-analytics"></a>Log Analytics 

Un'area di lavoro di Log Analytics, attualmente supportata nelle aree indicate di seguito:

  - Stati Uniti centro-occidentali  
  - Stati Uniti orientali  
  - Europa occidentale  
  - Asia sud-orientale<sup>1</sup>  

<sup>1</sup> Quest'area attualmente non supporta la funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali   

>[!NOTE]
>È possibile eseguire l'onboarding di Macchine virtuali di Azure da qualsiasi area e non solo dalle aree supportate per l'area di lavoro di Log Analytics.
>

Se non si ha un'area di lavoro, è possibile crearla tramite l'[interfaccia della riga di comando di Azure](../log-analytics/log-analytics-quick-create-workspace-cli.md), tramite [PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md), nel [portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md) o con [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md).  Se si abilita il monitoraggio per una singola macchina virtuale di Azure dal portale di Azure, è possibile creare un'area di lavoro durante questo processo.  

Per abilitare la soluzione, è necessario essere membri del ruolo Collaboratore di Log Analytics. Per altre informazioni su come controllare l'accesso a un'area di lavoro di Log Analytics, vedere [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

L'abilitazione della soluzione per lo scenario su larga scala richiede prima di tutto la configurazione di quanto segue nell'area di lavoro di Log Analytics:

* Installare le soluzioni **ServiceMap** e **InfrastructureInsights**
* Configurare l'area di lavoro di Log Analytics per raccogliere i contatori delle prestazioni

Per configurare l'area di lavoro per questo scenario, vedere [Configurare l'area di lavoro di Log Analytics](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

La tabella seguente elenca le versioni dei sistemi operativi Windows e Linux supportati con Monitoraggio di Azure per le macchine virtuali.  L'elenco completo con le versioni principali e secondarie del kernel e del sistema operativo Linux supportate è disponibile più avanti in questa sezione.

|Versione del sistema operativo |Prestazioni |Mappe |Integrità |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> La funzionalità relativa alle prestazioni di Monitoraggio di Azure per le macchine virtuali è disponibile solo da Monitoraggio di Azure e non quando si accede direttamente dal riquadro a sinistra della macchina virtuale.  

>[!NOTE]
>Le informazioni seguenti si applicano al supporto del sistema operativo Linux:  
> - Sono supportate solo versioni predefinita e SMP del kernel Linux.  
> - Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per le distribuzioni Linux. Ad esempio, un sistema con la stringa di versione "2.6.16.21-0.8-xen" non è supportato.  
> - I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportate.  
> - Il kernel CentOSPlus non è supportato.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6,5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 con Unbreakable Enterprise Kernel
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6,5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 con Unbreakable Enterprise Kernel

| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione del sistema operativo | Versione del kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>Origini connesse dell'ambiente ibrido
La mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. Dependency Agent usa l'agente di Log Analytics per la connessione a Log Analytics e quindi in un sistema deve essere installato e configurato l'agente di Log Analytics con Dependency Agent. La tabella seguente descrive le origini connesse supportate dalla funzionalità di mappa in un ambiente ibrido.

| Origine connessa | Supportato | DESCRIZIONE |
|:--|:--|:--|
| Agenti di Windows | Yes | Oltre all'[agente di Log Analytics per Windows](../log-analytics/log-analytics-concept-hybrid.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Agenti Linux | Yes | Oltre all'[agente di Log Analytics per Linux](../log-analytics/log-analytics-concept-hybrid.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere [Sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No  | |  

In Windows, Microsoft Monitoring Agent (MMA) viene usato sia da System Center Operations Manager che da Log Analytics per raccogliere e inviare dati di monitoraggio. System Center Operations Manager e Log Analytics offrono versioni diverse dell'agente pronte all'uso. Ognuna di queste versioni può inviare segnalazioni a System Center Operations Manager, Log Analytics o entrambi.  

In Linux, l'agente di Log Analytics per Linux raccoglie e invia i dati di monitoraggio a Log Analytics.   

Se i computer Windows o Linux in uso non possono connettersi direttamente al servizio, è necessario configurare l'agente di Log Analytics per la connessione a Log Analytics tramite il gateway OMS. Per altre informazioni su come distribuire e configurare il gateway OMS, vedere [Connettere computer senza accesso a Internet usando il gateway OMS](../log-analytics/log-analytics-oms-gateway.md).  

È possibile scaricare Dependency Agent dalla posizione seguente.

| File | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo
Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per garantire e migliorare la qualità, la sicurezza e l'integrità del servizio. Per offrire capacità di risoluzione dei problemi accurate ed efficienti, i dati della funzionalità di mappa includono informazioni sulla configurazione del software, come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Contatori delle prestazioni abilitati
Monitoraggio di Azure per le macchine virtuali configura un'area di lavoro di Log Analytics per raccogliere i contatori delle prestazioni usati dalla soluzione.  La tabella seguente elenca gli oggetti e i contatori configurati dalla soluzione che vengono raccolti ogni 60 secondi.

### <a name="windows-performance-counters"></a>Contatori delle prestazioni di Windows

|Nome dell'oggetto |Nome contatore |  
|------------|-------------|  
|LogicalDisk |% Free Space |  
|LogicalDisk |Avg. Disk sec/Read |  
|LogicalDisk |Avg. Disk sec/Transfer |  
|LogicalDisk |Avg. Disk sec/Write |  
|LogicalDisk |Byte da/a disco/sec |  
|LogicalDisk |Byte letti da disco/sec  |  
|LogicalDisk |Letture disco/sec  |  
|LogicalDisk |Disk Transfers/sec |  
|LogicalDisk |Byte scritti su disco/sec |  
|LogicalDisk |Scritture disco/sec |  
|LogicalDisk |Free Megabytes |  
|Memoria |MByte disponibili |  
|Scheda di rete |Byte ricevuti/sec |  
|Scheda di rete |Byte inviati/sec |  
|Processore |% di tempo processore |  

### <a name="linux-performance-counters"></a>Contatori delle prestazioni di Linux

|Nome dell'oggetto |Nome contatore |  
|------------|-------------|  
|Logical Disk |% Used Space |  
|Logical Disk |Byte letti da disco/sec  |  
|Logical Disk |Letture disco/sec  |  
|Logical Disk |Disk Transfers/sec |  
|Logical Disk |Byte scritti su disco/sec |  
|Logical Disk |Scritture disco/sec |  
|Logical Disk |Free Megabytes |  
|Logical Disk |Logical Disk Bytes/sec |  
|Memoria |Available MBytes Memory |  
|Rete |Total Bytes Received |  
|Rete |Total Bytes Transmitted |  
|Processore |% di tempo processore |  

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-from-the-azure-portal"></a>Eseguire l'abilitazione dal portale di Azure
Per abilitare il monitoraggio della macchina virtuale di Azure nel portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Selezionare una macchina virtuale dall'elenco. 
3. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)**.
4. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro di Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro di Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, seguire le istruzioni in [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) in una delle aree supportate elencate in precedenza.   

Dopo aver abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare le metriche relative all'integrità per la macchina virtuale. 

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="on-boarding-at-scale"></a>Onboarding su larga scala
In questa sezione vengono fornite le istruzioni per eseguire la distribuzione su larga scala di Monitoraggio di Azure per le macchine virtuali tramite Criteri di Azure o Azure PowerShell.  Il primo passaggio necessario consiste nel configurare l'area di lavoro di Log Analytics.  

### <a name="setup-log-analytics-workspace"></a>Configurare l'area di lavoro di Log Analytics
Se non si ha un'area di lavoro di Log Analytics, esaminare i metodi disponibili suggeriti nella sezione [Prerequisiti](#log-analytics) per crearne una.  

#### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni
Se l'area di lavoro di Log Analytics a cui fa riferimento la soluzione non è configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. Questa operazione può essere eseguita manualmente come descritto [qui](../log-analytics/log-analytics-data-sources-performance-counters.md) oppure scaricando ed eseguendo uno script di PowerShell disponibile in [Azure Powershell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installare le soluzioni ServiceMap e InfrastructureInsights
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione per l'area di lavoro di Log Analytics.  

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Salvare il file come **installsolutionsforvminsights.json** in una cartella locale.
3. Modificare i valori di **WorkspaceName**, **ResourceGroupName** e **WorkspaceLocation**.  Il valore di **WorkspaceName** è l'ID risorsa completo dell'area di lavoro di Log Analytics, che include il nome dell'area di lavoro, mentre il valore di **WorkspaceLocation** è l'area in cui è definita l'area di lavoro.
4. È possibile distribuire il modello usando il comando di PowerShell seguente:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-using-azure-policy"></a>Eseguire l'abilitazione con Criteri di Azure
Per abilitare Monitoraggio di Azure per le macchine virtuali su larga scala in modo da garantire conformità coerente e abilitazione automatica per le nuove macchine virtuali di cui viene effettuato il provisioning, è consigliabile usare [Criteri di Azure](../azure-policy/azure-policy-introduction.md). Questi criteri:

* Distribuiscono l'agente di Log Analytics e Dependency Agent 
* Creano report con i risultati relativi alla conformità 
* Risolvono i problemi di macchine virtuali non conformi

Per l'abilitazione di Monitoraggio di Azure per le macchine virtuali tramite i criteri, il tenant richiede quanto segue: 

- Assegnare l'iniziativa a un ambito (gruppo di gestione, sottoscrizione o gruppo di risorse) 
- Esaminare i risultati di conformità e risolvere eventuali problemi  

Per altre informazioni sull'assegnazione di Criteri di Azure, vedere [Panoramica di Criteri di Azure](../governance/policy/overview.md#policy-assignment) ed esaminare la [panoramica dei gruppi di gestione](../governance/management-groups/index.md) prima di continuare.  

La tabella seguente elenca le definizioni dei criteri disponibili.  

|NOME |DESCRIZIONE |type |  
|-----|------------|-----|  
|[Anteprima]: Abilita Monitoraggio di Azure per le macchine virtuali |Abilita Monitoraggio di Azure per le macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro di Log Analytics come parametro. |Iniziativa |  
|[Anteprima]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale (sistema operativo) non è nell'elenco definito e l'agente non è installato. |Criterio |  
|[Anteprima]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco |Segnala le macchine virtuali come non conformi se l'immagine della macchina virtuale (sistema operativo) non è nell'elenco definito e l'agente non è installato. |Criterio |  
|[Anteprima]: Distribuisci Dependency Agent per le macchine virtuali Linux |Distribuisce Dependency Agent per le macchine virtuali Linux se l'immagine della macchina virtuale (sistema operativo) è nell'elenco definito e l'agente non è installato. |Criterio |  
|[Anteprima]: Distribuisci Dependency Agent per le macchine virtuali Windows |Distribuisce Dependency Agent per le macchine virtuali Windows se l'immagine della macchina virtuale (sistema operativo) è nell'elenco definito e l'agente non è installato. |Criterio |  
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Linux |Distribuisce l'agente di Log Analytics per le macchine virtuali Linux se l'immagine della macchina virtuale (sistema operativo) è nell'elenco definito e l'agente non è installato. |Criterio |  
|[Anteprima]: Distribuisci l'agente di Log Analytics per le macchine virtuali Windows |Distribuisce l'agente di Log Analytics per le macchine virtuali Windows se l'immagine della macchina virtuale (sistema operativo) è nell'elenco definito e l'agente non è installato. |Criterio |  

Criterio autonomo (non incluso nell'iniziativa) 

|NOME |DESCRIZIONE |type |  
|-----|------------|-----|  
|[Anteprima]: Controlla area di lavoro di Log Analytics per la macchina virtuale - Segnala mancata corrispondenza |Segnala le macchine virtuali come non conformi se non si connettono all'area di lavoro di Log Analytics specificata nell'assegnazione dei criteri o dell'iniziativa. |Criterio |

#### <a name="assign-azure-monitor-initiative"></a>Assegnare l'iniziativa di Monitoraggio di Azure
Con questa versione iniziale, è possibile creare un'assegnazione dei criteri solo dal portale di Azure. Per informazioni su come completare questi passaggi, vedere  [Creare un'assegnazione di criteri dal portale di Azure](../governance/policy/assign-policy-portal.md). 

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**. 
2. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.
3. Selezionare **Assegna iniziativa** nella parte superiore della pagina **Criteri - Assegnazioni**.
4. Nella pagina **Assegna iniziativa** selezionare il valore di **Ambito** facendo clic sui puntini di sospensione e selezionando un gruppo di gestione, una sottoscrizione e, facoltativamente, un gruppo di risorse. Un ambito limita l'assegnazione dei criteri a un raggruppamento di macchine virtuali per l'applicazione. Fare clic su **Seleziona** nella parte inferiore della pagina **Ambito** per salvare le modifiche.
5. L'opzione **Esclusioni** consente di omettere una o più risorse dall'ambito. Questa operazione è facoltativa. 
6. Selezionare i puntini di sospensione accanto a **Definizione dell'iniziativa** per aprire l'elenco delle definizioni disponibili e selezionare **[Anteprima] Abilita Monitoraggio di Azure per le macchine virtuali** nell'elenco, quindi fare clic su **Seleziona**.
7. Il valore di **Nome dell'assegnazione** viene popolato automaticamente con il nome dell'iniziativa selezionata, ma è possibile modificarlo. È anche possibile aggiungere una **descrizione** facoltativa. Il campo **Assegnato da** viene compilato automaticamente in base all'utente che ha eseguito l'accesso ed è facoltativo.
8. Nell'elenco a discesa **Area di lavoro di Log Analytics** selezionare un'area di lavoro disponibile nell'area supportata.

    >[!NOTE]
    >Se l'area di lavoro non rientra nell'ambito dell'assegnazione, è necessario concedere le autorizzazioni di **Collaboratore di Log Analytics** all'ID entità di sicurezza dell'assegnazione dei criteri. In caso contrario, potrebbe verificarsi un errore di distribuzione, come: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Esaminare le informazioni in [Configurare manualmente l'identità gestita](../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity) per concedere l'accesso.
    >

9. Osservare che l'opzione **Identità gestita** è selezionata. Questa opzione è selezionata quando l'iniziativa che viene assegnata include un criterio con l'effetto deployIfNotExists. Nell'elenco a discesa **Percorso identità gestita** selezionare l'area appropriata.  
10. Fare clic su **Assegna**.

#### <a name="review-and-remediate-the-compliance-results"></a>Esaminare i risultati di conformità e risolvere eventuali problemi 

Per informazioni su come esaminare i risultati di conformità, vedere [Identificare le risorse non conformi](../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Selezionare **Conformità** a sinistra nella pagina e individuare l'iniziativa **[Anteprima] Abilita Monitoraggio di Azure per le macchine virtuali** non conforme in base all'assegnazione creata.

![Conformità dei criteri per le macchine virtuali di Azure](./media/monitoring-vminsights-onboard/policy-view-compliance-01.png)

In base ai risultati dei criteri inclusi nell'iniziativa, le macchine virtuali vengono segnalate come non conformi negli scenari seguenti:  
  
1. L'agente di Log Analytics o Dependency Agent non è distribuito.  
   Questa situazione è tipica per un ambito con macchine virtuali esistenti. Per attenuare il problema, [creare attività di correzione](../governance/policy/how-to/remediate-resources.md) nei criteri non conformi per distribuire gli agenti necessari.    
 
    - [Anteprima]: Deploy Dependency Agent for Linux VMs   
    - [Anteprima]: Deploy Dependency Agent for Windows VMs  
    - [Anteprima]: Deploy Log Analytics Agent for Linux VMs  
    - [Anteprima]: Deploy Log Analytics Agent for Windows VMs  

2. L'immagine della macchina virtuale (sistema operativo) non è presente nell'elenco identificato nella definizione dei criteri.  
   I criteri di distribuzione includono solo le macchine virtuali distribuite da immagini di macchine virtuali di Azure note. Controllare nella documentazione se il sistema operativo della macchina virtuale è supportato o meno. Se non lo è, sarà necessario duplicare i criteri di distribuzione e aggiornarli o modificarli per rendere conforme l'immagine. 
  
    - [Anteprima]: Controlla la distribuzione di Dependency Agent - Immagine macchina virtuale (sistema operativo) non in elenco  
    - [Anteprima]: Controlla la distribuzione dell'agente di Log Analytics - Immagine macchina virtuale (sistema operativo) non in elenco

3. Le macchine virtuali non si connettono all'area di lavoro di Log Analytics specificata.  
È possibile che alcune macchine virtuali nell'ambito dell'iniziativa si connettano a un'area di lavoro di Log Analytics diversa da quella specificata nell'assegnazione dei criteri. Questo criterio è uno strumento per identificare le macchine virtuali collegate a un'area di lavoro non conforme.  
 
    - [Anteprima]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>Eseguire l'abilitazione con PowerShell
Per abilitare Monitoraggio di Azure per le macchine virtuali per più macchine virtuali o set di scalabilità di macchine virtuali, è possibile usare uno script di PowerShell fornito ([Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)) disponibile in Azure PowerShell Gallery per il completamento di questa attività.  Lo script eseguirà l'iterazione in ogni macchina virtuale e set di scalabilità di macchine virtuali nella sottoscrizione, nel gruppo di risorse incluso nell'ambito specificato da *ResourceGroup* o in una singola macchina virtuale o un singolo set di scalabilità di macchine virtuali specificato da *Name*.  Per ogni macchina virtuale o set di scalabilità di macchine virtuali, lo script verifica se l'estensione della macchina virtuale è già installata e, se non lo è, cerca di reinstallarla.  In caso contrario, procede all'installazione delle estensioni della macchina virtuale dell'agente di Log Analytics e di Dependency Agent.   

Questo script richiede il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

Per ottenere informazioni sullo script, è possibile eseguire `Get-Help` per visualizzare un elenco di dettagli sugli argomenti con esempi di utilizzo.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

L'esempio seguente illustra l'uso dei comandi di PowerShell nella cartella per abilitare Monitoraggio di Azure per le macchine virtuali e comprendere l'output previsto:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Eseguire l'abilitazione per l'ambiente ibrido
Questa sezione illustra come eseguire l'onboarding di macchine virtuali o computer fisici ospitati in un data center o in un altro ambiente cloud per il monitoraggio tramite Monitoraggio di Azure per le macchine virtuali.  

Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte. I dati nella mappa vengono sempre trasmessi dall'agente di Log Analytics al servizio Monitoraggio di Azure, direttamente o tramite il [gateway OMS](../log-analytics/log-analytics-oms-gateway.md), se i criteri di sicurezza IT non permettono la connessione dei computer in rete a Internet.

Esaminare i requisiti e i metodi di distribuzione per l'[agente di Log Analytics per Linux e Windows](../log-analytics/log-analytics-concept-hybrid.md).

Riepilogo dei passaggi:

1. Installare l'agente di Log Analytics per Windows o Linux
2. Installare Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali
3. Abilitare la raccolta dei contatori delle prestazioni
4. Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali

### <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows 
Dependency Agent può essere installato manualmente nei computer Windows eseguendo `InstallDependencyAgent-Windows.exe`. Se si esegue questo file eseguibile senza opzioni, si avvia una procedura guidata che consente di completare l'installazione in modo interattivo.  

>[!NOTE]
>Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

La tabella seguente illustra i parametri specifici supportati dal programma di installazione per l'agente dalla riga di comando.  

| Parametro | DESCRIZIONE |
|:--|:--|
| /? | Restituisce un elenco delle opzioni della riga di comando. |
| /S | Esegue un'installazione invisibile all'utente, senza interazione da parte dell'utente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `/?`, digitare `InstallDependencyAgent-Windows.exe /?`

I file di Dependency Agent per Windows vengono installati in `C:\Program Files\Microsoft Dependency Agent` per impostazione predefinita.  Se Dependency Agent non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. La directory dei log è `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux
Dependency Agent viene installato nei server Linux tramite `InstallDependencyAgent-Linux64.bin`, uno script shell con un file binario autoestraente. È possibile eseguire il file con `sh` oppure aggiungere autorizzazioni di esecuzione al file stesso.

>[!NOTE]
> Per installare o configurare l'agente è necessario l'accesso alla radice.
> 

| Parametro | DESCRIZIONE |
|:--|:--|
| -help | Ottenere un elenco delle opzioni della riga di comando. |
| -s | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| --check | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

Per eseguire, ad esempio, il programma di installazione con il parametro `-help`, digitare `InstallDependencyAgent-Linux64.bin -help`.

Installare Dependency Agent per Linux come utente ROOT eseguendo il comando `sh InstallDependencyAgent-Linux64.bin`
    
Se Dependency Agent non si avvia, controllare i registri per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory dei log è `/var/opt/microsoft/dependency-agent/log`.

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| File | Località |
|:--|:--|
| File core | /opt/microsoft/dependency-agent |
| File di log | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Abilitare i contatori delle prestazioni
Se l'area di lavoro di Log Analytics a cui fa riferimento la soluzione non è configurata per la raccolta dei contatori delle prestazioni richiesti dalla soluzione, sarà necessario abilitare i contatori. Questa operazione può essere eseguita manualmente come descritto [qui](../log-analytics/log-analytics-data-sources-performance-counters.md) oppure scaricando ed eseguendo uno script di PowerShell disponibile in [Azure Powershell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Eseguire l'onboarding di Monitoraggio di Azure per le macchine virtuali
Questo metodo include un modello JSON che specifica la configurazione per abilitare i componenti della soluzione per l'area di lavoro di Log Analytics.  

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È richiesta la versione 2.0.27 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Creare ed eseguire un modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Salvare il file come **installsolutionsforvminsights.json** in una cartella locale.
3. Modificare i valori di **WorkspaceName**, **ResourceGroupName** e **WorkspaceLocation**.  Il valore di **WorkspaceName** è l'ID risorsa completo dell'area di lavoro di Log Analytics, che include il nome dell'area di lavoro, mentre il valore di **WorkspaceLocation** è l'area in cui è definita l'area di lavoro.
4. È possibile distribuire il modello usando il comando di PowerShell seguente:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

    ```powershell
    provisioningState       : Succeeded
    ```
Dopo avere abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare lo stato di integrità e le metriche per il computer ibrido. 

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per la macchina virtuale, le informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.  Per informazioni su come usare la funzionalità di integrità, vedere [View Azure Monitor for VMs Health](monitoring-vminsights-health.md) (Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali) oppure, per visualizzare le dipendenze delle applicazioni individuate, vedere [View Azure Monitor for VMs Map](monitoring-vminsights-maps.md) (Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali).  