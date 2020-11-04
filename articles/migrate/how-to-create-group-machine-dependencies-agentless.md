---
title: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server Assessment
description: Configurare l'analisi delle dipendenze senza agente in Azure Migrate server assessment.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 8893b0f41bb7a06c02a6c2aa28002980144b582a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337694"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analizzare le dipendenze dei computer (senza agente)

Questo articolo descrive come configurare l'analisi delle dipendenze senza agente in Azure Migrate: server assessment. L' [analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i computer per la valutazione e la migrazione ad Azure.


> [!IMPORTANT]
> La visualizzazione delle dipendenze senza agenti è attualmente disponibile in anteprima per le macchine virtuali VMware individuate con lo strumento Azure Migrate: server assessment.
> Le funzionalità possono essere limitate o incomplete.
> Questa versione di anteprima è coperta dal supporto tecnico e può essere usata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limitazioni correnti

- Nella visualizzazione analisi delle dipendenze non è attualmente possibile aggiungere o rimuovere un server da un gruppo.
- Una mappa delle dipendenze per un gruppo di server non è attualmente disponibile.
- In un progetto Azure Migrate la raccolta dei dati sulle dipendenze può essere configurata simultaneamente per i server 1000. È possibile analizzare un numero maggiore di server eseguendo la sequenziazione in batch di 1000.

## <a name="before-you-start"></a>Prima di iniziare

- [Esaminare](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) i sistemi operativi supportati e le autorizzazioni necessarie.
- Assicurarsi di:
    - Avere un progetto Azure Migrate. In caso contrario, [crearne](how-to-add-tool-first-time.md) uno ora.
    - Verificare di aver [aggiunto](how-to-assess.md) lo strumento Azure migrate: server Assessment al progetto.
    - Configurare un' [appliance Azure migrate](migrate-appliance.md) per individuare i computer locali. [Configurare un'appliance](how-to-set-up-appliance-vmware.md) per le macchine virtuali VMware. L'appliance individua i computer locali e invia i metadati e i dati sulle prestazioni a Azure Migrate: server assessment.
- Verificare che gli strumenti VMware (successivi a 10,2) siano installati in ogni macchina virtuale che si vuole analizzare.


## <a name="create-a-user-account-for-discovery"></a>Creazione di un account utente per l'individuazione

Configurare un account utente in modo che server assessment possa accedere alla macchina virtuale per individuare le dipendenze. [Informazioni sui requisiti](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) dell'account per le macchine virtuali Windows e Linux.


## <a name="add-the-user-account-to-the-appliance"></a>Aggiungere l'account utente al dispositivo

Aggiungere l'account utente al dispositivo.

1. Aprire l'app gestione Appliance. 
2. Passare al pannello **specificare i dettagli di vCenter** .
3. In **individua applicazione e dipendenze dalle macchine virtuali** fare clic su **Aggiungi credenziali** .
3. Scegliere il **sistema operativo** , specificare un nome descrittivo per l'account e la password del **nome utente** / **Password**
6. Fare clic su **Salva**.
7. Fare clic su **Salva e avvia individuazione**.

    ![Aggiungi account utente VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Avvia individuazione dipendenza

Scegliere i computer in cui si desidera abilitare l'individuazione delle dipendenze. 

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Aggiungi server**.
4. Nella pagina **Aggiungi server** scegliere l'appliance che sta scoprendo i computer pertinenti.
5. Dall'elenco computer selezionare le macchine virtuali.
6. Fare clic su **Aggiungi server**.

    ![Avvia individuazione dipendenza](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

È possibile visualizzare le dipendenze circa sei ore dopo l'avvio dell'individuazione delle dipendenze. Se si vuole abilitare più computer, è possibile usare [PowerShell](#start-or-stop-dependency-discovery-using-powershell) .

## <a name="visualize-dependencies"></a>Visualizza dipendenze

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Cercare il computer che si vuole visualizzare.
3. Nella colonna **dipendenze** fare clic su **Visualizza dipendenze** .
4. Modificare il periodo di tempo per cui si desidera visualizzare la mappa utilizzando l'elenco a discesa **durata tempo** .
5. Espandere il gruppo **client** per elencare i computer con una dipendenza nel computer selezionato.
6. Espandere il gruppo **porta** per elencare i computer che hanno una dipendenza dal computer selezionato.
7. Per passare alla visualizzazione mappa di uno dei computer dipendenti, fare clic sul nome del computer > **caricare la mappa del server**

    ![Espandere il gruppo di porte del server e caricare la mappa del server](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Espandi gruppo di client ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Espandere il computer selezionato per visualizzare i dettagli a livello di processo per ogni dipendenza.

    ![Espandere il server per visualizzare i processi](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Le informazioni sul processo per una dipendenza non sono sempre disponibili. Se non è disponibile, la dipendenza viene rappresentata con il processo contrassegnato come "processo sconosciuto".

## <a name="export-dependency-data"></a>Esporta dati sulle dipendenze

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Esporta dipendenze dell'applicazione**.
4. Nella pagina **Esporta dipendenze applicazione** scegliere l'appliance che sta individuando i computer pertinenti.
5. Selezionare l'ora di inizio e l'ora di fine. Si noti che è possibile scaricare i dati solo per gli ultimi 30 giorni.
6. Fare clic su **Esporta dipendenza**.

I dati sulle dipendenze vengono esportati e scaricati in formato CSV. Il file scaricato contiene i dati sulle dipendenze in tutti i computer abilitati per l'analisi delle dipendenze. 

![Esporta dipendenze](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informazioni sulle dipendenze

Ogni riga nel file CSV esportato corrisponde a una dipendenza osservata nello slot di tempo specificato. 

Nella tabella seguente sono riepilogati i campi del file CSV esportato. Si noti che i campi nome server, applicazione e processo vengono popolati solo per i server in cui è abilitata l'analisi delle dipendenze senza agenti.

**Nome campo** | **Informazioni dettagliate**
--- | --- 
Timeslot | Timeslot durante il quale è stata osservata la dipendenza. <br/> I dati sulle dipendenze vengono acquisiti attualmente negli slot di 6 ore.
Nome del server di origine | Nome del computer di origine 
Applicazione di origine | Nome dell'applicazione nel computer di origine 
Processo di origine | Nome del processo nel computer di origine 
Nome del server di destinazione | Nome del computer di destinazione
IP di destinazione | Indirizzo IP del computer di destinazione
Applicazione di destinazione | Nome dell'applicazione nel computer di destinazione
Processo di destinazione | Nome del processo nel computer di destinazione 
Porta di destinazione | Numero di porta nel computer di destinazione


## <a name="stop-dependency-discovery"></a>Arresta individuazione dipendenza

Scegliere i computer in cui si desidera arrestare l'individuazione delle dipendenze. 

1. In **Azure migrate: server Assessment** fare clic su **server individuati**.
2. Fare clic sull'icona **analisi dipendenza** .
3. Fare clic su **Rimuovi server**.
3. Nella pagina **Rimuovi server** scegliere il **dispositivo** che sta individuando le macchine virtuali in cui si vuole arrestare l'individuazione delle dipendenze.
4. Dall'elenco computer selezionare le macchine virtuali.
5. Fare clic su **Rimuovi server**.

Se si vuole arrestare la dipendenza da più computer, è possibile usare [PowerShell](#start-or-stop-dependency-discovery-using-powershell) .


## <a name="start-or-stop-dependency-discovery-using-powershell"></a>Avviare o arrestare l'individuazione delle dipendenze tramite PowerShell

Scaricare il modulo PowerShell dal repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) su GitHub.


### <a name="log-in-to-azure"></a>Accedere ad Azure

1. Accedere alla sottoscrizione di Azure usando il cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Se si usa Azure per enti pubblici, usare il comando seguente.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importare il modulo di PowerShell AzMig_Dependencies scaricato

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Abilitare o disabilitare la raccolta dei dati sulle dipendenze

1. Ottenere l'elenco delle macchine virtuali VMware individuate nel progetto Azure Migrate usando i comandi seguenti. Nell'esempio seguente il nome del progetto è FabrikamDemoProject e il gruppo di risorse a cui appartiene è FabrikamDemoRG. L'elenco dei computer verrà salvato in FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Nel file è possibile visualizzare il nome visualizzato della macchina virtuale, lo stato corrente della raccolta delle dipendenze e l'ID ARM di tutte le VM individuate. 

2. Per abilitare o disabilitare le dipendenze, creare un file CSV di input. È necessario che il file includa una colonna con intestazione "ARM ID". Eventuali intestazioni aggiuntive nel file CSV verranno ignorate. È possibile creare il CSV usando il file generato nel passaggio precedente. Creare una copia del file mantenendo le macchine virtuali per cui si vogliono abilitare o disabilitare le dipendenze. 

    Nell'esempio seguente viene abilitata l'analisi delle dipendenze nell'elenco di macchine virtuali nel file di input FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Nell'esempio seguente, l'analisi delle dipendenze viene disabilitata nell'elenco di macchine virtuali nel file di input FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualizzare le connessioni di rete in Power BI

Azure Migrate offre un modello di Power BI che è possibile usare per visualizzare le connessioni di rete di molti server contemporaneamente e filtrare in base al processo e al server. Per visualizzare, caricare il Power BI con i dati sulle dipendenze in base alle istruzioni riportate di seguito.

1. Scaricare il modulo PowerShell e il modello di Power BI dal repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) in GitHub.

2. Accedere ad Azure usando le istruzioni seguenti: 
- Accedere alla sottoscrizione di Azure usando il cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```

- Se si usa Azure per enti pubblici, usare il comando seguente.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Selezionare la sottoscrizione in cui è stato creato il progetto di Azure Migrate 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importare il modulo di PowerShell AzMig_Dependencies scaricato

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Eseguire il comando seguente. Questo comando Scarica i dati sulle dipendenze in un volume CSV e li elabora per generare un elenco di dipendenze univoche che possono essere usate per la visualizzazione in Power BI. Nell'esempio seguente il nome del progetto è FabrikamDemoProject e il gruppo di risorse a cui appartiene è FabrikamDemoRG. Le dipendenze verranno scaricate per i computer individuati da FabrikamAppliance. Le dipendenze univoche verranno salvate in FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Aprire il modello di Power BI scaricato

6. Caricare i dati di dipendenza scaricati in Power BI.
    - Aprire il modello in Power BI.
    - Fare clic su **Ottieni dati** sulla barra degli strumenti. 
    - Scegliere **testo/CSV** dalle origini dati comuni.
    - Scegliere il file delle dipendenze scaricato.
    - Fare clic su **Carica**.
    - Si noterà che viene importata una tabella con il nome del file CSV. È possibile visualizzare la tabella nella barra dei campi a destra. Rinominare il AzMig_Dependencies
    - Fare clic su Aggiorna nella barra degli strumenti.

    Il grafico delle connessioni di rete e il nome del server di origine, il nome del server di destinazione, il nome del processo di origine, i sezionamenti dei nomi dei processi di destinazione devono essere attivati con i dati

7. Consente di visualizzare la mappa delle connessioni di rete che filtrano i server e i processi. Salvare il file.


## <a name="next-steps"></a>Passaggi successivi

[Raggruppare i computer](how-to-create-a-group.md) per la valutazione.
