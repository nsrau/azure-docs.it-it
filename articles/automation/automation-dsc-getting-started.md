---
title: Introduzione a DSC di Automazione di Azure | Microsoft Docs
description: "Spiegazione ed esempi delle attività più comuni in Automation DSC (Desired State Configuration) per Azure"
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;gwallace
ms.openlocfilehash: 45afb09f09e754e37ae8dba02e1e16b3fde1e408
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Introduzione ad Automation DSC per Azure
Questo articolo illustra come eseguire le attività più comuni in Automation DSC (Desired State Configuration) per Azure, come la creazione, l'importazione e la compilazione di configurazioni, il caricamento di computer per la gestione e la visualizzazione di report. Per una panoramica delle caratteristiche di Automation DSC per Azure, vedere [Panoramica della piattaforma DSC di Automazione di Azure](automation-dsc-overview.md). Per la documentazione di DSC, vedere [Panoramica di Windows PowerShell DSC (Desired State Configuration)](https://msdn.microsoft.com/PowerShell/dsc/overview).

Questo articolo offre una guida dettagliata all'uso di Automation DSC per Azure. Se si preferisce un ambiente di esempio già configurato senza seguire le procedure descritte in questo articolo, è possibile usare il [modello di Resource Manager](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup). Tale modello configura un ambiente Automation DSC per Azure completo, che include una VM di Azure gestita da Automation DSC per Azure.

## <a name="prerequisites"></a>prerequisiti
Per completare gli esempi di questo articolo, è necessario quanto segue:

* Un account di automazione di Azure. Per istruzioni sulla creazione di un account RunAs di Automazione di Azure, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).
* Una VM di Azure Resource Manager (non classica) che esegue Windows Server 2008 R2 o versioni successive. Per istruzioni sulla creazione di una VM, vedere [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Creazione di una configurazione DSC
Verrà creata una [configurazione DSC](https://msdn.microsoft.com/powershell/dsc/configurations) semplice che assicura la presenza o l'assenza della funzionalità di Windows (IIS) **Web-Server**, a seconda di come vengono assegnati i nodi.

1. Avviare Windows PowerShell ISE (o qualsiasi editor di testo).
2. Digitare il testo seguente:
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
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
3. Salvare il file con il nome `TestConfig.ps1`.

Questa configurazione chiama in ogni blocco di nodi una [risorsa WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource)che assicura la presenza o l'assenza della funzionalità **Web-Server** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importazione di una configurazione in Automazione di Azure
Successivamente, la configurazione verrà importata nell'account di automazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** selezionare **Configurazioni DSC** in **Gestione della configurazione**.
4. Nella pagina **Configurazioni DSC** fare clic su **+ Aggiungi una configurazione**.
5. Nella pagina **Importa configurazione** selezionare il file `TestConfig.ps1` nel computer.
   
    ![Screenshot del pannello **Importa configurazione**](./media/automation-dsc-getting-started/AddConfig.png)
6. Fare clic su **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Visualizzazione di una configurazione in Automazione di Azure
Dopo aver importato una configurazione, è possibile visualizzarla nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** selezionare **Configurazioni DSC** in **Gestione della configurazione**.
4. Nella pagina **Configurazioni DSC** fare clic su **TestConfig**, ovvero il nome della configurazione importata nella procedura precedente.
5. Nella pagina **Configurazione TestConfig** fare clic su **Visualizza origine configurazione**.
   
    ![Screenshot del pannello Configurazione TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    Verrà visualizzato un pannello **Origine configurazione TestConfig** contenente il codice PowerShell per la configurazione.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilazione di una configurazione in Automazione di Azure
Per poter applicare uno stato desiderato a un nodo, è prima necessario compilare una configurazione DSC che definisce tale stato in una o più configurazioni di nodo (documenti MOF) e inserire tale configurazione DSC nel server di pull di Automation DSC. Per una descrizione più dettagliata della compilazione di configurazioni in Automation DSC per Azure, vedere [Compilazione di configurazioni in Automation DSC per Azure](automation-dsc-compile.md). Per altre informazioni sulla compilazione di configurazioni, vedere [Configurazioni DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Configurazioni DSC** in **Gestione della configurazione**.
4. Nella pagina **Configurazioni DSC** fare clic su **TestConfig**, ovvero il nome della configurazione importata in precedenza.
5. Nella pagina **Configurazione TestConfig** fare clic su **Compila** e quindi su **Sì**. Verrà avviato un processo di compilazione.
   
    ![Screenshot della pagina Configurazione TestConfig con pulsante Compila evidenziato](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando si compila una configurazione in Automazione di Azure, tutti i documenti MOF delle configurazioni di nodo creati vengono distribuiti automaticamente nel server di pull.
> 
> 

## <a name="viewing-a-compilation-job"></a>Visualizzazione di un processo di compilazione
Dopo aver avviato una compilazione, è possibile visualizzarla nel riquadro **Processi di compilazione** del pannello **Configurazione**. Nel riquadro **Processi di compilazione** vengono visualizzati i processi attualmente in esecuzione, completati e non riusciti. Aprendo il pannello di un processo di compilazione vengono visualizzate informazioni sul processo, inclusi gli eventuali errori o avvisi rilevati, i parametri di input usati nella configurazione e i log di compilazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Configurazioni DSC** in **Gestione della configurazione**.
4. Nella pagina **Configurazioni DSC** fare clic su **TestConfig**, ovvero il nome della configurazione importata in precedenza.
5. In **Processi di compilazione** selezionare il processo di compilazione che si vuole visualizzare. Verrà visualizzato una pagina **Processo di compilazione** con la data in cui è stato avviato il processo di compilazione come etichetta.
   
    ![Screenshot della pagina Processo di compilazione](./media/automation-dsc-getting-started/CompilationJob.png)
6. Fare clic su qualsiasi riquadro nella pagina **Processo di compilazione** per visualizzare altri dettagli sul processo.

## <a name="viewing-node-configurations"></a>Visualizzazione delle configurazioni di nodo
Con il completamento di un processo di compilazione vengono create una o più configurazioni di nodo. Una configurazione di nodo è un documento MOF che viene distribuito nel server di pull ed è disponibile per il pull e l'applicazione da parte di uno o più nodi. È possibile visualizzare le configurazioni dei nodi dell'account di Automazione nel pannello **Configurazioni del nodo DSC** . Il nome di una configurazione del nodo presenta il formato *ConfigurationName*.*NodeName*.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu Hub e quindi fare clic sul nome dell'account di Automazione.
3. Nel pannello **Account di automazione** fare clic su **Configurazioni del nodo DSC**.
   
    ![Screenshot del pannello Configurazioni del nodo DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Caricamento di una VM di Azure per la gestione con Automation DSC per Azure
È possibile usare Automation DSC per Azure per gestire VM di Azure (sia classiche che di Resource Manager), VM locali, computer Linux, VM di AWS e computer fisici locali. In questo articolo viene descritto il caricamento solo delle macchine virtuali di Azure Resource Manager. Per informazioni sul caricamento di altri tipi di computer, vedere [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Per caricare una VM di Azure Resource Manager per la gestione con Automation DSC per Azure
1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Nodi DSC** in **Gestione della configurazione**.
4. Nella pagina **Nodi DSC** fare clic su **Aggiungi macchina virtuale di Azure**.
   
    ![Screenshot della pagina Nodi DSC con il pulsante Aggiungi macchina virtuale di Azure evidenziato](./media/automation-dsc-getting-started/OnboardVM.png)
5. Nella pagina **Macchine virtuali** selezionare la macchina virtuale.  Nella pagina **Aggiungi macchine virtuali di Azure** fare clic su **Seleziona macchine virtuali da caricare**.
6. Fare clic su **Connetti**.
   
   > [!IMPORTANT]
   > Deve trattarsi di una VM di Azure Resource Manager che esegue Windows Server 2008 R2 o versioni successive.
   > 
   > 
1. Nella pagina **Registrazione** immettere il nome della configurazione del nodo che si vuole applicare alla macchina virtuale nella casella **Nome della configurazione del nodo**. Deve corrispondere esattamente al nome di una configurazione di nodo nell'account di automazione. Specificare un nome in questo passaggio è facoltativo. È possibile modificare la configurazione di nodo assegnata dopo il caricamento del nodo.
   Selezionare **Riavvia il nodo se necessario** e fare clic su **OK**.
   
    ![Screenshot del pannello Registrazione](./media/automation-dsc-getting-started/RegisterVM.png)
   
    La configurazione del nodo specificata verrà applicata alla macchina virtuale agli intervalli specificati in **Frequenza modalità di configurazione** e la macchina virtuale verificherà la disponibilità di aggiornamenti agli intervalli specificati in **Frequenza di aggiornamento**. Per altre informazioni sul modo in cui vengono usati questi valori, vedere [Configuring the Local Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)(Configurazione di Gestione configurazione locale).
9. Nel pannello **Aggiungi macchine virtuali di Azure** fare clic su **Crea**.

Azure avvierà il processo di caricamento della macchina virtuale. Al termine, la macchina virtuale verrà visualizzata nel pannello **Nodi DSC** dell'account di Automazione.

## <a name="viewing-the-list-of-dsc-nodes"></a>Visualizzazione dell'elenco dei nodi DSC
L'elenco di tutti i computer caricati per la gestione nell'account di Automazione può essere visualizzato nel pannello **Nodi DSC** .

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Nodi DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Visualizzazione di report per i nodi DSC
Ogni volta che Automation DSC per Azure esegue una verifica di coerenza su un nodo gestito, il nodo restituisce un report di stato al server di pull. È possibile visualizzare tali report nella pagina per il nodo.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Nodi DSC**.
4. Nell'elenco **Nodi DSC** selezionare il nodo che si vuole visualizzare.
5. Nella pagina **Nodo** fare clic sul report che si vuole visualizzare in **Report**.
   
    ![Screenshot del pannello Report](./media/automation-dsc-getting-started/NodeReport.png)

Nel pannello per un singolo report è possibile visualizzare per la verifica di coerenza corrispondente le informazioni di stato seguenti.

* Stato del report: il nodo può essere "Conforme" o "Non conforme", quando il nodo è in modalità **ApplyAndMonitor** e il computer non è nello stato previsto, oppure la configurazione può essere "Non riuscita".
* Ora di inizio della verifica di coerenza.
* Runtime totale della verifica di coerenza.
* Tipo di verifica di coerenza.
* Eventuali errori, con codice e messaggio di errore. 
* Risorse DSC usate nella configurazione e lo stato di ogni risorsa, ovvero se il nodo è nello stato previsto per la risorsa. È possibile fare clic su ogni risorsa per ottenere informazioni più dettagliate su di essa.
* Nome, indirizzo IP e modalità di configurazione del nodo.

È anche possibile fare clic su **Visualizza report non elaborato** per visualizzare i dati effettivi inviati dal nodo al server. Per altre informazioni sull'uso di tali dati, vedere [Using a DSC report server](https://msdn.microsoft.com/powershell/dsc/reportserver)(Uso di un server di report DSC).

Dopo il caricamento di un nodo, può trascorrere tempo prima che sia disponibile il primo report e potrebbe essere necessario attendere fino a 30 minuti.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Riassegnazione di un nodo a una diversa configurazione di nodo
È possibile assegnare un nodo in modo che usi una configurazione di nodo diversa rispetto a quella inizialmente assegnata.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Nodi DSC**.
4. Nella pagina **Nodi DSC** fare clic sul nome del nodo da riassegnare.
5. Nella pagina per tale nodo fare clic su **Assegna configurazione nodo**.
   
    ![Screenshot del pannello del nodo con pulsante Assegna configurazione nodo evidenziato](./media/automation-dsc-getting-started/AssignNode.png)
6. Nella pagina **Assegna configurazione nodo** selezionare la configurazione che si vuole assegnare al nodo e quindi fare clic su **OK**.
   
    ![Screenshot del pannello Assegna configurazione nodo](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Annullamento della registrazione di un nodo
Se non si vuole più che un nodo venga gestito da Automation DSC per Azure, è possibile annullarne la registrazione.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra fare clic su **Tutte le risorse** e quindi fare clic sul nome dell'account di Automazione.
3. Nella pagina **Account di automazione** fare clic su **Nodi DSC**.
4. Nella pagina **Nodi DSC** fare clic sul nome del nodo per il quale annullare la registrazione.
5. Nella pagina per tale nodo fare clic su **Annulla registrazione**.
   
    ![Screenshot del pannello del nodo con pulsante Annulla registrazione evidenziato](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Articoli correlati
* [Panoramica di Automation DSC per Azure](automation-dsc-overview.md)
* [Caricamento di computer per la gestione con Automation DSC per Azure](automation-dsc-onboarding.md)
* [Panoramica di Windows PowerShell DSC (Desired State Configuration)](https://msdn.microsoft.com/powershell/dsc/overview)
* [Cmdlet di Automation DSC per Azure](/powershell/module/azurerm.automation/#automation)
* [Prezzi di Automation DSC per Azure](https://azure.microsoft.com/pricing/details/automation/)

