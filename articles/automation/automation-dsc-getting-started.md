---
title: Introduzione alla configurazione dello stato di automazione di Azure
description: Spiegazione ed esempi delle attività più comuni nella configurazione dello stato di automazione di Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 45d56daee20d95230f77db249028883165acb951
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995772"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Introduzione alla configurazione dello stato di automazione di Azure

Questo articolo fornisce una guida dettagliata per eseguire le attività più comuni con la configurazione dello stato di automazione di Azure, ad esempio la creazione, l'importazione e la compilazione di configurazioni, l'abilitazione dei computer per la gestione e la visualizzazione di report. Per una panoramica della configurazione dello stato, vedere [Panoramica della configurazione dello stato](automation-dsc-overview.md). Per la documentazione di DSC (Desired State Configuration, Configurazione dello stato desiderato), vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview).

Se si vuole un ambiente di esempio già configurato senza seguire i passaggi descritti in questo articolo, è possibile usare il modello di [nodo gestito di automazione di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Questo modello configura un ambiente di configurazione dello stato completo (DSC), inclusa una macchina virtuale di Azure gestita da configurazione dello stato (DSC).

## <a name="prerequisites"></a>Prerequisiti

Per completare gli esempi di questo articolo, è necessario quanto segue:

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
- Una macchina virtuale Azure Resource Manager (non classica) che esegue un [sistema operativo supportato](automation-dsc-overview.md#operating-system-requirements). Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="create-a-dsc-configuration"></a>Creare una configurazione DSC

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
1. Salvare il file come **TestConfig. ps1**.

Questa configurazione chiama una risorsa in ogni blocco di nodo, ovvero la [risorsa WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Questa risorsa garantisce la presenza o l'assenza della funzionalità **server Web** .

## <a name="import-a-configuration-into-azure-automation"></a>Importare una configurazione in automazione di Azure

Successivamente, la configurazione verrà importata nell'account di automazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** , quindi su **Aggiungi**.
1. Nel riquadro Importa configurazione selezionare il `TestConfig.ps1` file nel computer.

   ![Screenshot del pannello **Importa configurazione**](./media/automation-dsc-getting-started/AddConfig.png)

1. Fare clic su **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Visualizzare una configurazione in automazione di Azure

Dopo aver importato una configurazione, è possibile visualizzarla nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina account di automazione selezionare **configurazione stato (DSC)** in **Gestione configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** , quindi fare clic su **TestConfig**. Si tratta del nome della configurazione importata nella procedura precedente.
1. Nel riquadro Configurazione di TestConfig fare clic su **Visualizza origine configurazione**.

   ![Screenshot del pannello Configurazione TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Viene visualizzato un riquadro origine configurazione TestConfig, che Visualizza il codice PowerShell per la configurazione.

## <a name="compile-a-configuration-in-azure-automation"></a>Compilare una configurazione in automazione di Azure

Per poter applicare uno stato desiderato a un nodo, è prima necessario compilare una configurazione DSC che definisce tale stato in una o più configurazioni di nodo (documenti MOF) e inserire tale configurazione DSC nel server di pull di Automation DSC. Per una descrizione più dettagliata della compilazione di configurazioni in state Configuration (DSC), vedere [compilazione di configurazioni in Azure Automation state Configuration](automation-dsc-compile.md).
Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** , quindi fare clic su **TestConfig**. Si tratta del nome della configurazione importata in precedenza.
1. Nel riquadro Configurazione di TestConfig fare clic su **Compila**e quindi su **Sì**. Verrà avviato un processo di compilazione.

   ![Screenshot della pagina Configurazione TestConfig con pulsante Compila evidenziato](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando si compila una configurazione in automazione di Azure, vengono distribuiti automaticamente i file MOF di configurazione dei nodi creati nel server di pull.

## <a name="view-a-compilation-job"></a>Visualizzare un processo di compilazione

Dopo avere avviato una compilazione, è possibile visualizzarla nel riquadro **processi di compilazione** della pagina **configurazione** . Nel riquadro **Processi di compilazione** vengono visualizzati i processi attualmente in esecuzione, completati e non riusciti. Quando si apre un riquadro del processo di compilazione, vengono visualizzate le informazioni sul processo, inclusi eventuali errori o avvisi rilevati, i parametri di input utilizzati nella configurazione e i log di compilazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **configurazioni** , quindi fare clic su **TestConfig**. Si tratta del nome della configurazione importata in precedenza.
1. In **processi di compilazione**selezionare il processo di compilazione da visualizzare. Viene aperto un riquadro del processo di compilazione con l'etichetta della data di avvio del processo di compilazione.

   ![Screenshot della pagina Processo di compilazione](./media/automation-dsc-getting-started/CompilationJob.png)

1. Fare clic su qualsiasi riquadro nel riquadro del processo di compilazione per visualizzare altri dettagli sul processo.

## <a name="view-node-configurations"></a>Visualizzazione delle configurazioni del nodo

Con il completamento di un processo di compilazione vengono create una o più configurazioni di nodo. Una configurazione di nodo è un documento MOF che viene distribuito nel server di pull ed è disponibile per il pull e l'applicazione da parte di uno o più nodi. È possibile visualizzare le configurazioni del nodo nell'account di automazione nella pagina configurazione stato (DSC). Una configurazione di nodo ha un nome con il `ConfigurationName.NodeName`formato.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Configurazioni compilate**.

   ![Screenshot della scheda Configurazioni compilate](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Abilitare una macchina virtuale Azure Resource Manager per la gestione con la configurazione dello stato

È possibile usare la configurazione dello stato per gestire le macchine virtuali di Azure (classiche e Gestione risorse), VM locali, computer Linux, VM AWS e computer fisici locali. Questo articolo illustra come abilitare solo Azure Resource Manager macchine virtuali. Per informazioni sull'abilitazione di altri tipi di computer, vedere [abilitare le macchine virtuali per la gestione mediante la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) selezionare la scheda **nodi** , quindi fare clic su **+ Aggiungi**.

   ![Screenshot della pagina Nodi DSC con il pulsante Aggiungi macchina virtuale di Azure evidenziato](./media/automation-dsc-getting-started/OnboardVM.png)

1. Nel riquadro macchine virtuali selezionare la macchina virtuale.
1. Nel riquadro dei dettagli della macchina virtuale fare clic su **+ Connetti**.

   > [!IMPORTANT]
   > La macchina virtuale deve essere una macchina virtuale Azure Resource Manager che esegue un [sistema operativo supportato](automation-dsc-overview.md#operating-system-requirements).

2. Nella pagina registrazione selezionare il nome della configurazione del nodo da applicare alla macchina virtuale nel campo **Nome configurazione nodo** . Specificare un nome in questo passaggio è facoltativo. Dopo l'abilitazione del nodo, è possibile modificare la configurazione del nodo assegnata.

3. Selezionare **Riavvia il nodo se necessario** e quindi fare clic su **OK**.

   ![Screenshot del pannello Registrazione](./media/automation-dsc-getting-started/RegisterVM.png)

   La configurazione del nodo specificata viene applicata alla macchina virtuale a intervalli specificati dal valore fornito per la **frequenza della modalità di configurazione**. La macchina virtuale controlla la presenza di aggiornamenti alla configurazione del nodo a intervalli specificati dal valore della **frequenza di aggiornamento** . Per altre informazioni sul modo in cui vengono usati questi valori, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

Azure avvia il processo di abilitazione della macchina virtuale. Al termine, la macchina virtuale viene visualizzata nella scheda **Nodi** della pagina Configurazione stato (DSC) nell'account di Automazione.

## <a name="view-the-list-of-managed-nodes"></a>Visualizzare l'elenco dei nodi gestiti

È possibile visualizzare l'elenco di tutti i computer che sono stati abilitati per la gestione nell'account di automazione nella scheda **nodi** della pagina configurazione stato (DSC).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina Configurazione stato (DSC) fare clic sulla scheda **Nodi**.

## <a name="view-reports-for-managed-nodes"></a>Visualizzare i report per i nodi gestiti

Ogni configurazione di stato dell'ora esegue una verifica di coerenza su un nodo gestito, il nodo invia un report di stato al server di pull. È possibile visualizzare tali report nella pagina per il nodo.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **Configurazione stato (DSC)** in **Gestione della configurazione**.
1. Nella pagina configurazione stato (DSC) fare clic sulla scheda **nodi** . Qui è possibile visualizzare la panoramica dello stato della configurazione e i dettagli per ogni nodo.

   ![Screenshot della pagina Nodi](./media/automation-dsc-getting-started/NodesTab.png)

1. Sempre dalla scheda **Nodi**, fare clic sul record del nodo per aprire i report. Fare clic sul report desiderato per visualizzare dettagli aggiuntivi.

   ![Screenshot del pannello Report](./media/automation-dsc-getting-started/NodeReport.png)

Nel pannello per un singolo report è possibile visualizzare per la verifica di coerenza corrispondente le informazioni di stato seguenti.

- Stato del report. I valori possibili sono:
    * Conforme: il nodo è conforme al controllo.
   * Operazione non riuscita: la configurazione non ha superato il controllo.
   * Non conforme: il nodo è in `ApplyandMonitor` modalità e lo stato del computer non è quello desiderato.
- Ora di inizio della verifica di coerenza.
- Runtime totale della verifica di coerenza.
- Tipo di verifica di coerenza.
- Eventuali errori, con codice e messaggio di errore.
- Tutte le risorse DSC usate nella configurazione e lo stato di ogni risorsa (se il nodo si trova nello stato desiderato per la risorsa). È possibile fare clic su ogni risorsa per ottenere informazioni più dettagliate per tale risorsa.
- Nome, indirizzo IP e modalità di configurazione del nodo.

È anche possibile fare clic su **Visualizza report non elaborato** per visualizzare i dati effettivi inviati dal nodo al server.
Per altre informazioni sull'uso di tali dati, vedere [Using a DSC report server](/powershell/scripting/dsc/pull-server/reportserver)(Uso di un server di report DSC).

L'abilitazione di un nodo prima della disponibilità del primo report può richiedere del tempo. Potrebbe essere necessario attendere fino a 30 minuti per il primo report dopo aver abilitato un nodo.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Riassegnare un nodo a una configurazione di nodo diversa

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

## <a name="unregister-a-node"></a>Annullare la registrazione di un nodo

Se non si vuole più che un nodo venga gestito dalla configurazione dello stato, è possibile annullarne la registrazione.

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