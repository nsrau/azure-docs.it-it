---
title: Introduzione alla configurazione dello stato di automazione di AzureGet started with Azure Automation State Configuration
description: Descrizione ed esempi delle attività più comuni in Configurazione stato (DSC) di Automazione di Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392162"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Introduzione alla configurazione dello stato di automazione di AzureGet started with Azure Automation State Configuration

Questo articolo descrive come eseguire le attività più comuni in Configurazione stato di Automazione di Azure, come la creazione, l'importazione e la compilazione di configurazioni, l'onboarding di computer da gestire e la visualizzazione di report. Per una panoramica di Configurazione stato di Automazione di Azure, vedere [Panoramica di Configurazione stato di Automazione di Azure](automation-dsc-overview.md). Per la documentazione di DSC (Desired State Configuration, Configurazione dello stato desiderato), vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview).

Questo articolo offre una guida dettagliata all'uso di Configurazione stato di Automazione di Azure. Se si preferisce un ambiente di esempio già configurato senza dover seguire i passaggi descritti in questo articolo, è possibile usare questo modello di Resource Manager: [modello di nodo gestito di Automazione di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Questo modello configura un ambiente Configurazione stato di Automazione di Azure completo, che include una macchina virtuale di Azure gestita tramite Configurazione stato di Automazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare gli esempi di questo articolo, è necessario quanto segue:

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
- Una macchina virtuale di Azure Resource Manager (non classica) che esegue un [sistema operativo supportato.](automation-dsc-overview.md#operating-system-requirements) Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Creazione di una configurazione DSC

Verrà creata una [configurazione DSC](/powershell/scripting/dsc/configurations/configurations) semplice che assicura la presenza o l'assenza della funzionalità di Windows (IIS) **Web-Server**, a seconda di come vengono assegnati i nodi.

1. Avviare [VSCode](https://code.visualstudio.com/docs) (o qualsiasi altro editor di testo).
1. Digitare il testo seguente:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Salvare il file con il nome **TestConfig.ps1**.

Questa configurazione chiama una risorsa in ogni blocco di nodi, la [risorsa WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Questa risorsa garantisce la presenza o l'assenza della funzionalità **Server Web.**

## <a name="importing-a-configuration-into-azure-automation"></a>Importazione di una configurazione in Automazione di Azure

Successivamente, la configurazione verrà importata nell'account di automazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni,** quindi su **Aggiungi**.
1. Nel riquadro Importa configurazione `TestConfig.ps1` individuare il file nel computer.

   ![Screenshot del pannello **Importa configurazione**](./media/automation-dsc-getting-started/AddConfig.png)

1. Fare clic su **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visualizzazione di una configurazione in Automazione di Azure

Dopo aver importato una configurazione, è possibile visualizzarla nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni,** quindi su **TestConfig**. Questo è il nome della configurazione importata nella procedura precedente.
1. Nel riquadro Configurazione TestConfig fare clic su **Visualizza origine configurazione**.

   ![Screenshot del pannello Configurazione TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Viene visualizzato il riquadro di origine TestConfig Configuration, in cui è visualizzato il codice di PowerShell per la configurazione.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilazione di una configurazione in Automazione di Azure

Per poter applicare uno stato desiderato a un nodo, è prima necessario compilare una configurazione DSC che definisce tale stato in una o più configurazioni di nodo (documenti MOF) e inserire tale configurazione DSC nel server di pull di Automation DSC. Per una descrizione più dettagliata della compilazione di configurazioni in Configurazione stato di Automazione di Azure, vedere [Compilazione di configurazioni in Configurazione stato di Automazione di Azure](automation-dsc-compile.md).
Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni,** quindi su **TestConfig**. Questo è il nome della configurazione importata in precedenza.
1. Nel riquadro Configurazione TestConfig fare clic su **Compila**e quindi su **Sì**. Verrà avviato un processo di compilazione.

   ![Screenshot della pagina Configurazione TestConfig con pulsante Compila evidenziato](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando si compila una configurazione in Automazione di Azure, vengono distribuiti automaticamente tutti i file MOF di configurazione del nodo creati nel server di pull.

## <a name="viewing-a-compilation-job"></a>Visualizzazione di un processo di compilazione

Dopo aver avviato una compilazione, è possibile visualizzarla nel riquadro Processi di **compilazione** nella pagina Configurazione.After you start a **compilation,** you can view it in the Compilation Jobs tile on the Configuration page. Nel riquadro **Processi di compilazione** vengono visualizzati i processi attualmente in esecuzione, completati e non riusciti. Quando si apre un riquadro dei processi di compilazione, vengono visualizzate informazioni su tale processo, inclusi eventuali errori o avvisi rilevati, parametri di input utilizzati nella configurazione e log di compilazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni,** quindi su **TestConfig**. Questo è il nome della configurazione importata in precedenza.
1. In **Processi di compilazione**selezionare il processo di compilazione da visualizzare. Viene aperto un riquadro Processo di compilazione, contrassegnato con la data di inizio del processo di compilazione.

   ![Screenshot della pagina Processo di compilazione](./media/automation-dsc-getting-started/CompilationJob.png)

1. Fare clic su qualsiasi riquadro nel riquadro Processo di compilazione per visualizzare ulteriori dettagli sul processo.

## <a name="viewing-node-configurations"></a>Visualizzazione delle configurazioni di nodo

Con il completamento di un processo di compilazione vengono create una o più configurazioni di nodo. Una configurazione di nodo è un documento MOF che viene distribuito nel server di pull ed è disponibile per il pull e l'applicazione da parte di uno o più nodi. È possibile visualizzare le configurazioni dei nodi nell'account di automazione nella pagina Configurazione stato (DSC). Una configurazione di nodo ha `ConfigurationName.NodeName`un nome con il modulo .

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni compilate**.

   ![Screenshot della scheda Configurazioni compilate](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Onboarding di una macchina virtuale di Azure per la gestione con Configurazione stato di Automazione di Azure

È possibile usare La configurazione dello stato di automazione di Azure per gestire le macchine virtuali di Azure (sia classica che Gestione risorse), le macchine virtuali locali, le macchine Linux, le macchine virtuali AWS e le macchine fisiche locali. In questo articolo viene descritto il caricamento solo delle macchine virtuali di Azure Resource Manager. Per informazioni sull'onboarding di altri tipi di computer, vedere [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Per caricare una macchina virtuale Azure Resource Manager per la gestione con Configurazione stato di Automazione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) selezionare la scheda **Nodi,** quindi fare clic su **Aggiungi**.

   ![Screenshot della pagina Nodi DSC con il pulsante Aggiungi macchina virtuale di Azure evidenziato](./media/automation-dsc-getting-started/OnboardVM.png)

1. Nel riquadro Macchine virtuali selezionare la macchina virtuale.
1. Nel riquadro dei dettagli della macchina virtuale fare clic su **Connetti**.

   > [!IMPORTANT]
   > La macchina virtuale deve essere una macchina virtuale di Azure Resource Manager che esegue un [sistema operativo supportato.](automation-dsc-overview.md#operating-system-requirements)

2. Nella pagina Registrazione selezionare il nome della configurazione del nodo da applicare alla macchina virtuale nel campo **Nome configurazione nodo.** Specificare un nome in questo passaggio è facoltativo. È possibile modificare la configurazione di nodo assegnata dopo il caricamento del nodo.

3. Selezionare **Riavvia il nodo se necessario** e quindi fare clic su **OK**.

   ![Screenshot del pannello Registrazione](./media/automation-dsc-getting-started/RegisterVM.png)

   La configurazione del nodo specificata viene applicata alla macchina virtuale a intervalli specificati dal valore fornito per **Frequenza modalità**di configurazione . La macchina virtuale verifica la disponibilità di aggiornamenti per la configurazione del nodo a intervalli specificati dal valore **Frequenza di aggiornamento.** Per altre informazioni sul modo in cui vengono usati questi valori, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

Azure avvierà il processo di caricamento della macchina virtuale. Al termine, la macchina virtuale viene visualizzata nella scheda **Nodi** della pagina Configurazione stato (DSC) nell'account di Automazione.

## <a name="viewing-the-list-of-managed-nodes"></a>Visualizzazione dell'elenco dei nodi gestiti

È possibile visualizzare l'elenco di tutti i computer caricati per la gestione nell'account di Automazione nella scheda **Nodi** della pagina Configurazione stato (DSC).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Nodi**.

## <a name="viewing-reports-for-managed-nodes"></a>Visualizzazione di report per i nodi gestiti

Ogni volta che Configurazione stato di Automazione di Azure esegue una verifica di coerenza su un nodo gestito, il nodo restituisce un report di stato al server di pull. È possibile visualizzare tali report nella pagina per il nodo.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Nodi.** Qui è possibile visualizzare la panoramica dello stato di configurazione e i dettagli per ogni nodo.

   ![Screenshot della pagina Nodi](./media/automation-dsc-getting-started/NodesTab.png)

1. Sempre dalla scheda **Nodi**, fare clic sul record del nodo per aprire i report. Fare clic sul report desiderato per visualizzare dettagli aggiuntivi.

   ![Screenshot del pannello Report](./media/automation-dsc-getting-started/NodeReport.png)

Nel pannello per un singolo report è possibile visualizzare per la verifica di coerenza corrispondente le informazioni di stato seguenti.

- Stato del report. I valori possibili sono:
    * Conforme: il nodo è conforme al controllo.
   * Non riuscito - la configurazione non ha superato il controllo.
   * Non conforme: il `ApplyandMonitor` nodo è in modalità e la macchina non è nello stato desiderato.
- Ora di inizio della verifica di coerenza.
- Runtime totale della verifica di coerenza.
- Tipo di verifica di coerenza.
- Eventuali errori, con codice e messaggio di errore.
- Tutte le risorse DSC utilizzate nella configurazione e lo stato di ogni risorsa (se il nodo si trova nello stato desiderato per tale risorsa). È possibile fare clic su ogni risorsa per ottenere informazioni più dettagliate per tale risorsa.
- Nome, indirizzo IP e modalità di configurazione del nodo.

È anche possibile fare clic su **Visualizza report non elaborato** per visualizzare i dati effettivi inviati dal nodo al server.
Per altre informazioni sull'uso di tali dati, vedere [Using a DSC report server](/powershell/scripting/dsc/pull-server/reportserver)(Uso di un server di report DSC).

Dopo il caricamento di un nodo, può trascorrere tempo prima che sia disponibile il primo report e potrebbe essere necessario attendere fino a 30 minuti.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Riassegnazione di un nodo a una diversa configurazione di nodo

È possibile assegnare un nodo in modo che usi una configurazione di nodo diversa rispetto a quella inizialmente assegnata.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Nodi**.
1. Nella scheda **Nodi** fare clic sul nome del nodo da riassegnare.
1. Nella pagina per il nodo fare clic su **Assegna configurazione nodo**.

    ![Screenshot della pagina dei dettagli del nodo in cui è evidenziato il pulsante Assegna configurazione nodo](./media/automation-dsc-getting-started/AssignNode.png)

1. Nella pagina Assegna configurazione nodo selezionare la configurazione che si vuole assegnare al nodo e quindi fare clic su **OK**.

    ![Screenshot della pagina Assegna configurazione nodo](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Annullamento della registrazione di un nodo

Se non si vuole più che un nodo venga gestito da Automation DSC per Azure, è possibile annullarne la registrazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Nodi**.
1. Nella scheda **Nodi** fare clic sul nome del nodo di cui si vuole annullare la registrazione.
1. Nel riquadro relativo a tale nodo fare clic su **Annulla registrazione**.

    ![Screenshot della pagina dei dettagli del nodo in cui è evidenziato il pulsante Annulla registrazione](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Articoli correlati

- [Panoramica di Configurazione stato di Automazione di Azure ](automation-dsc-overview.md)
- [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](automation-dsc-onboarding.md)
- [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview)
- [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/) (Prezzi di Configurazione stato di Automazione di Azure)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)