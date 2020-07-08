---
title: Introduzione a State Configuration di Automazione di Azure
description: Questo articolo descrive come eseguire le attività più comuni in State Configuration di Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b39fbdf19703d8b4b2f8683577701dcb23900616
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836958"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Introduzione a State Configuration di Automazione di Azure

Questo articolo descrive come eseguire le attività più comuni in State Configuration di Automazione di Azure, come la creazione, l'importazione e la compilazione di configurazioni, l'abilitazione delle macchine virtuali da gestire e la visualizzazione di report. Per una panoramica di State Configuration, vedere [Panoramica di State Configuration](automation-dsc-overview.md). Per la documentazione di DSC (Desired State Configuration, Configurazione dello stato desiderato), vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview).

Se si preferisce un ambiente di esempio già configurato senza dover seguire i passaggi descritti in questo articolo, è possibile usare il [modello di nodo gestito di Automazione di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Questo modello configura un ambiente State Configuration (DSC) completo, inclusa una macchina virtuale di Azure gestita da State Configuration (DSC).

## <a name="prerequisites"></a>Prerequisiti

Per completare gli esempi di questo articolo, è necessario quanto segue:

- Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
- Una macchina virtuale di Azure Resource Manager (non classica) che esegue un [sistema operativo supportato](automation-dsc-overview.md#operating-system-requirements). Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

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
1. Salvare il file come **TestConfig.ps1**.

Questa configurazione chiama una risorsa in ogni blocco di nodo, la [risorsa WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Questa risorsa garantisce la presenza o l'assenza della funzionalità **Web-Server**.

## <a name="import-a-configuration-into-azure-automation"></a>Importare una configurazione in Automazione di Azure

Successivamente, la configurazione verrà importata nell'account di automazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni** e quindi su **Aggiungi**.
1. Nel riquadro Importa configurazione selezionare il file `TestConfig.ps1` nel computer.

   ![Screenshot del pannello **Importa configurazione**](./media/automation-dsc-getting-started/AddConfig.png)

1. Fare clic su **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Visualizzare una configurazione in Automazione di Azure

Dopo aver importato una configurazione, è possibile visualizzarla nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione selezionare **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni** e quindi su **TestConfig**. Si tratta del nome della configurazione importata nella procedura precedente.
1. Nel riquadro Configurazione TestConfig fare clic su **Visualizza origine configurazione**.

   ![Screenshot del pannello Configurazione TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Verrà visualizzata una pagina Origine configurazione TestConfig contenente il codice PowerShell per la configurazione.

## <a name="compile-a-configuration-in-azure-automation"></a>Compilare una configurazione in Automazione di Azure

Per poter applicare uno stato desiderato a un nodo, è prima necessario compilare una configurazione DSC che definisce tale stato in una o più configurazioni di nodo (documenti MOF) e inserire tale configurazione DSC nel server di pull di Automation DSC. Per una descrizione più dettagliata della compilazione di configurazioni in State Configuration (DSC), vedere [Compilare configurazioni in State Configuration di Automazione di Azure](automation-dsc-compile.md).
Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](/powershell/scripting/dsc/configurations/configurations).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni** e quindi su **TestConfig**. Si tratta del nome della configurazione importata in precedenza.
1. Nella pagina Configurazione TestConfig fare clic su **Compila** e quindi su **Sì**. Verrà avviato un processo di compilazione.

   ![Screenshot della pagina Configurazione TestConfig con pulsante Compila evidenziato](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando si compila una configurazione in Automazione di Azure, tutti i file MOF delle configurazioni di nodo creati vengono distribuiti automaticamente nel server di pull.

## <a name="view-a-compilation-job"></a>Visualizzare un processo di compilazione

Dopo avere avviato una compilazione, è possibile visualizzarla nel riquadro **Processi di compilazione** nella pagina **Configurazione**. Nel riquadro **Processi di compilazione** vengono visualizzati i processi attualmente in esecuzione, completati e non riusciti. Aprendo il riquadro di un processo di compilazione vengono visualizzate informazioni sul processo, inclusi gli eventuali errori o avvisi rilevati, i parametri di input usati nella configurazione e i log di compilazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni** e quindi su **TestConfig**. Si tratta del nome della configurazione importata in precedenza.
1. In **Processi di compilazione** selezionare il processo di compilazione da visualizzare. Viene visualizzato il riquadro Processo di compilazione, con la data di avvio del processo di compilazione come etichetta.

   ![Screenshot della pagina Processo di compilazione](./media/automation-dsc-getting-started/CompilationJob.png)

1. Fare clic su qualsiasi sezione nel riquadro Processo di compilazione per visualizzare altri dettagli sul processo.

## <a name="view-node-configurations"></a>Visualizzare le configurazioni dei nodi

Con il completamento di un processo di compilazione vengono create una o più configurazioni di nodo. Una configurazione di nodo è un documento MOF che viene distribuito nel server di pull ed è disponibile per il pull e l'applicazione da parte di uno o più nodi. È possibile visualizzare le configurazioni dei nodi dell'account di Automazione nella pagina State Configuration (DSC). Il formato del nome di una configurazione è `ConfigurationName.NodeName`.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Configurazioni compilate**.

   ![Screenshot della scheda Configurazioni compilate](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Abilitare una macchina virtuale di Azure Resource Manager per la gestione con State Configuration

È possibile usare State Configuration per gestire macchine virtuali di Azure (sia classiche sia Resource Manager), macchine virtuali locali, computer Linux, macchine virtuali AWS e computer fisici locali. In questo articolo viene descritto come abilitare solo le macchine virtuali di Azure Resource Manager. Per informazioni sull'abilitazione di altri tipi di computer, vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) selezionare la scheda **Nodi** e quindi fare clic su **Aggiungi**.

   ![Screenshot della pagina Nodi DSC con il pulsante Aggiungi macchina virtuale di Azure evidenziato](./media/automation-dsc-getting-started/OnboardVM.png)

1. Nel riquadro Macchine virtuali selezionare la macchina virtuale.
1. Nel riquadro dei dettagli della macchina virtuale fare clic su **+ Connetti**.

   > [!IMPORTANT]
   > La macchina virtuale deve essere una macchina virtuale di Azure Resource Manager che esegue un [sistema operativo supportato](automation-dsc-overview.md#operating-system-requirements).

2. Nella pagina Registrazione selezionare il nome della configurazione del nodo da applicare alla macchina virtuale nel campo **Nome della configurazione del nodo**. Specificare un nome in questo passaggio è facoltativo. È possibile modificare la configurazione del nodo assegnata dopo l'abilitazione del nodo.

3. Selezionare **Riavvia il nodo se necessario** e quindi fare clic su **OK**.

   ![Screenshot del pannello Registrazione](./media/automation-dsc-getting-started/RegisterVM.png)

   La configurazione del nodo specificata viene applicata alla macchina virtuale in base agli intervalli specificati dal valore impostato per **Frequenza modalità di configurazione**. La macchina virtuale controlla la disponibilità di aggiornamenti alla configurazione del nodo in base agli intervalli specificati dal valore **Frequenza di aggiornamento**. Per altre informazioni sul modo in cui vengono usati questi valori, vedere [Configuring the Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig)(Configurazione di Gestione configurazione locale).

Azure avvia il processo di abilitazione della macchina virtuale. Al termine, la macchina virtuale viene visualizzata nella scheda **Nodi** della pagina State Configuration (DSC) nell'account di Automazione.

## <a name="view-the-list-of-managed-nodes"></a>Visualizzare l'elenco dei nodi gestiti

È possibile visualizzare l'elenco di tutte le macchine virtuali abilitate per la gestione nell'account di Automazione nella scheda **Nodi** della pagina State Configuration (DSC).

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Nodi**.

## <a name="view-reports-for-managed-nodes"></a>Visualizzare report per i nodi gestiti

Ogni volta che State Configuration esegue una verifica di coerenza su un nodo gestito, il nodo invia un report di stato al server di pull. È possibile visualizzare tali report nella pagina per il nodo.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Nodi**. Qui è possibile visualizzare la panoramica di Configurazione stato e i dettagli di ogni nodo.

   ![Screenshot della pagina Nodi](./media/automation-dsc-getting-started/NodesTab.png)

1. Sempre dalla scheda **Nodi**, fare clic sul record del nodo per aprire i report. Fare clic sul report desiderato per visualizzare dettagli aggiuntivi.

   ![Screenshot del pannello Report](./media/automation-dsc-getting-started/NodeReport.png)

Nel pannello per un singolo report è possibile visualizzare per la verifica di coerenza corrispondente le informazioni di stato seguenti.

- Report sullo stato. I valori possibili sono:
    * Conforme - Il nodo è conforme al controllo.
   * Operazione non riuscita - La configurazione non ha superato il controllo.
   * Non conforme - Il nodo si trova in modalità `ApplyandMonitor` e lo stato della macchina virtuale non è quello desiderato.
- Ora di inizio della verifica di coerenza.
- Runtime totale della verifica di coerenza.
- Tipo di verifica di coerenza.
- Eventuali errori, con codice e messaggio di errore.
- Tutte le risorse DSC usate nella configurazione e lo stato di ogni risorsa (se il nodo si trova nello stato desiderato per la risorsa). È possibile fare clic su ogni risorsa per ottenere informazioni più dettagliate per tale risorsa.
- Nome, indirizzo IP e modalità di configurazione del nodo.

È anche possibile fare clic su **Visualizza report non elaborato** per visualizzare i dati effettivi inviati dal nodo al server.
Per altre informazioni sull'uso di tali dati, vedere [Using a DSC report server](/powershell/scripting/dsc/pull-server/reportserver)(Uso di un server di report DSC).

Dopo l'abilitazione di un nodo, può trascorrere tempo prima che sia disponibile il primo report e potrebbe essere necessario attendere fino a 30 minuti.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Riassegnare un nodo a una diversa configurazione di nodo

È possibile assegnare un nodo in modo che usi una configurazione di nodo diversa rispetto a quella inizialmente assegnata.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Nodi**.
1. Nella scheda **Nodi** fare clic sul nome del nodo da riassegnare.
1. Nella pagina per il nodo fare clic su **Assegna configurazione nodo**.

    ![Screenshot della pagina dei dettagli del nodo in cui è evidenziato il pulsante Assegna configurazione nodo](./media/automation-dsc-getting-started/AssignNode.png)

1. Nella pagina Assegna configurazione nodo selezionare la configurazione che si vuole assegnare al nodo e quindi fare clic su **OK**.

    ![Screenshot della pagina Assegna configurazione nodo](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Annullare la registrazione di un nodo

Se non si vuole più che un nodo venga gestito da State Configuration, è possibile annullarne la registrazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
1. Nella pagina Account di automazione fare clic su **State Configuration (DSC)** in **Gestione della configurazione**.
1. Nella pagina State Configuration (DSC) fare clic sulla scheda **Nodi**.
1. Nella scheda **Nodi** fare clic sul nome del nodo di cui si vuole annullare la registrazione.
1. Nel riquadro per tale nodo fare clic su **Annulla registrazione**.

    ![Screenshot della pagina dei dettagli del nodo in cui è evidenziato il pulsante Annulla registrazione](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [Panoramica di State Configuration di Automazione di Azure](automation-dsc-overview.md).
- Per abilitare la funzionalità per le macchine virtuali nell'ambiente, vedere [Abilitare State Configuration di Automazione di Azure](automation-dsc-onboarding.md).
- Per informazioni su PowerShell DSC, vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview).
- Per informazioni sui prezzi, vedere [Prezzi di State Configuration di Automazione di Azure](https://azure.microsoft.com/pricing/details/automation/).
- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
