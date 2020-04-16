---
title: Il primo runbook grafico in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook grafico .
keywords: runbook, modello di runbook, automazione runbook, runbook di Azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: bcef0574e16e0b4d28755716c32670b00c65af14
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406096"
---
# <a name="my-first-graphical-runbook"></a>Il primo runbook grafico

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Questa esercitazione illustra la creazione di un [runbook grafico](automation-runbook-types.md#graphical-runbooks) in Automazione di Azure. Inizia con un semplice runbook che puoi testare e pubblicare, imparando a tenere traccia dello stato del processo del runbook. Modificare quindi il runbook per gestire effettivamente le risorse di Azure, in questo caso l'avvio di una macchina virtuale di Azure.Then modify the runbook to actually manage Azure resources, in this case starting an Azure virtual machine. Completare l'esercitazione per rendere il runbook più affidabile aggiungendo parametri del runbook e collegamenti condizionali.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questa macchina, non dovrebbe essere una macchina virtuale di produzione.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un runbook semplice che `Hello World`restituisce il testo .

1. Nel portale di Azure aprire l'account di automazione. 

    La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. La maggior parte di queste risorse sono i moduli inclusi automaticamente in un nuovo account di automazione. È inoltre necessario disporre della risorsa Credenziali associata alla sottoscrizione.
2. Selezionare **Runbooks** in **Process Automation** per aprire l'elenco dei runbook.
3. Creare un nuovo runbook selezionando **Create a runbook**.
4. Denominare il runbook **MyFirstRunbook-Graphical**.
5. In questo caso, si creerà un [runbook grafico](automation-graphical-authoring-intro.md). Selezionare **Grafico** per **Tipo Runbook**.<br> ![Nuovo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Fare clic su **Crea** per creare il runbook e aprire l'editor grafico.

## <a name="step-2---add-activities"></a>Passaggio 2 - Aggiungere attivitàStep 2 - Add activities

Il controllo Libreria sul lato sinistro dell'editor consente di selezionare le attività da aggiungere al runbook. Si aggiungerà un `Write-Output` cmdlet per l'output di testo dal runbook.

1. Nel controllo Libreria fare clic nel `write-output`campo di ricerca e digitare . I risultati della ricerca sono riportati nell'immagine seguente. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Scorrere fino alla fine dell'elenco. Fare clic con il pulsante destro del mouse su **Write-Output** e selezionare **Aggiungi all'area di disegno**. In alternativa, è possibile fare clic sui ellissi (...) accanto al nome del cmdlet e quindi selezionare **Aggiungi all'area di disegno**.
1. Fare clic sull'attività **Write-Output** nell'area di disegno. Si aprirà la pagina Controllo configurazione che consente di configurare l'attività.
1. Per impostazione predefinita, il campo **Etichetta** viene impostato sul nome del cmdlet, ma è possibile modificarlo in un nome più descrittivo. Cambiarlo `Write Hello World to output`in .
1. Fare clic su **Parametri** per specificare i valori per i parametri del cmdlet.

   Alcuni cmdlet dispongono di più set di parametri ed è necessario selezionare quello da utilizzare. In questo `Write-Output` caso, è impostato un solo parametro.

1. Selezionare `InputObject` il parametro. Questo è il parametro che consente di specificare il testo da inviare al flusso di output.
1. Il menu a discesa **Origine dati** fornisce origini che è possibile usare per popolare un valore di parametro. In questo menu selezionare Espressione di **PowerShell**. 

   È possibile usare l'output da origini di origine come un'altra attività, un asset di automazione o un'espressione di PowerShell.You can use output from such sources as another activity, an Automation asset, or a PowerShell expression. In questo caso, l'output è solo `Hello World`. È possibile usare un'espressione di PowerShell e specificare una stringa.<br>

1. Nel campo **Espressione** `Hello World` digitare e quindi fare clic **su OK** due volte per tornare all'area di disegno.
1. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, è consigliabile testarlo per assicurarsi che funzioni correttamente. Il test di un runbook esegue la versione Bozza e consente di visualizzarne l'output in modo interattivo.

1. Selezionare il **riquadro Test** per aprire il riquadro Test.Select Test pane to open the Test pane.
1. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
1. Si noti che viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del `Queued`processo inizia come , a indicare che il processo è in attesa che un ruolo di lavoro del runbook nel cloud diventi disponibile. Lo stato `Starting` cambia quando un lavoratore rivendica il processo. Infine, lo `Running` stato diventa quando il runbook inizia effettivamente a essere eseguito.

1. Al termine del processo del runbook, nel riquadro Test viene visualizzato l'output. In questo caso, `Hello World`viene visualizzato .

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Selezionare **Pubblica** per pubblicare il runbook, quindi **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il runbook nella pagina Runbooks e notare che il valore **Stato creazione** creazione è impostato su **Pubblicato**.
1. Scorrere verso destra per visualizzare la pagina **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico).

   Le opzioni nella parte superiore consentono di avviare il runbook ora, pianificare un'ora di inizio futura o creare un [webhook](automation-webhooks.md) in modo che il runbook possa essere avviato tramite una chiamata HTTP.

1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.
1. Viene aperto un riquadro Processo per il processo runbook creato. Verificare che nel campo **Stato processo** sia **visualizzato Completato**.
1. Fare clic su **Output** per aprire `Hello World` la pagina Output, in cui è possibile visualizzare.
1. Chiudere la pagina Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Si dovrebbe `Hello World` vedere solo nel flusso di output. 

    Si noti che il riquadro Flussi può mostrare altri flussi per un processo del runbook, ad esempio flussi Verbose ed Error, se il runbook scrive in essi.
1. Chiudere il riquadro Flussi e il riquadro Processo per tornare alla pagina MyFirstRunbook-Graphical.
1. Per visualizzare tutti i processi per il runbook, selezionare **Processi** in **Risorse**. Nella pagina Processi sono elencati tutti i processi creati dal runbook. Verrà visualizzato un solo processo nell'elenco, poiché il processo è stato eseguito una sola volta.
1. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato all'avvio del runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---create-variable-assets"></a>Passaggio 5: Creare asset di variabile

Il runbook è stato testato e pubblicato, ma finora non viene eseguita alcuna operazione utile per gestire le risorse di Azure.You've tested and published your runbook, but finora it doesn't do anything useful to manage Azure resources. Prima di configurare il runbook per l'autenticazione, è necessario creare una variabile per contenere l'ID sottoscrizione, impostare un'attività per l'autenticazione e quindi fare riferimento alla variabile. L'inclusione di un riferimento al contesto della sottoscrizione consente di lavorare facilmente con più sottoscrizioni.

1. Copiare l'ID sottoscrizione dall'opzione **Sottoscrizioni** nel riquadro di spostamento.
1. Nella pagina Account di automazione selezionare **Variabili** in **Risorse condivise**.
1. Selezionare **Aggiungi variabile**.
1. Nella pagina Nuova variabile apportare le impostazioni seguenti nei campi forniti.

    * **Nome** : `AzureSubscriptionId`immettere .
    * **Valore:** immettere l'ID sottoscrizione. 
    * **Tipo:** mantieni la stringa selezionata.
    * **Crittografia** -- utilizzare il valore predefinito.
1. Fare clic su **Crea** per creare la variabile.

## <a name="step-6---add-authentication"></a>Passaggio 6 - Aggiungere l'autenticazioneStep 6 - Add authentication

Ora che si dispone di una variabile per contenere l'ID sottoscrizione, è possibile configurare il runbook per l'autenticazione con le credenziali RunAs per la sottoscrizione. A tale scopo, aggiungere la connessione RunAs di Azure come un asset. È inoltre necessario aggiungere il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e il cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) all'area di disegno.

>[!NOTE]
>Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico `Connect-AzAccount`può usare solo se stesso.

1. Passare al runbook e selezionare **Modifica** nella pagina MyFirstRunbook-Graphical.
1. Non hai più `Write Hello World to output` bisogno della voce. Basta fare clic sui solipsi e selezionare **Elimina**.
1. Nel controllo Libreria espandere **ASSETS**, quindi **Connessioni**. Aggiungi `AzureRunAsConnection` all'area di disegno selezionando **Aggiungi all'area di disegno**.
1. Rinominare `AzureRunAsConnection` con `Get Run As Connection`.
1. Nel controllo Libreria `Connect-AzAccount` digitare il campo di ricerca.
1. Aggiungi `Connect-AzAccount` alla tela.
1. Posizionare `Get Run As Connection` il puntatore del mouse finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio `Connect-AzAccount` e trascinare la freccia per creare un collegamento. Il runbook `Get Run As Connection` inizia con `Connect-AzAccount`e quindi viene eseguito.<br> ![Creare un collegamento tra le attività](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Nell'area di `Connect-AzAccount`disegno selezionare . Nel riquadro Controllo configurazione digitare Accesso ad Azure nel campo Etichetta.In the Configuration control pane, type **Login to Azure** in the **Label** field.
1. Fare clic su **Parametri**e viene visualizzata la pagina Configurazione parametri attività.
1. Il `Connect-AzAccount` cmdlet dispone di più set di parametri ed è necessario selezionarne uno prima di specificare i valori dei parametri. Fare clic su **Set** di parametri e quindi selezionare **ServicePrincipalCertificateWithSubscriptionId**.
1. I parametri per questo set di parametri vengono visualizzati nella pagina Configurazione parametri attività. Fare clic su **APPLICATIONID**.<br> ![Aggiungere i parametri dell'account AzureAdd Azure account parameters](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Nella pagina Valore parametro apportare le impostazioni seguenti e quindi fare clic su **OK**.

   * **Origine dati:** selezionare **Output attività**.
   * Elenco origini dati: selezionare **Ottieni connessione di automazione**.
   * **Percorso campo** `ApplicationId`-- tipo . Si specifica il nome della proprietà per il percorso del campo perché l'attività restituisce un oggetto con più proprietà.

1. Fare clic su **CERTIFICATETHUMBPRINT**e nella pagina Valore parametro apportare le impostazioni seguenti e quindi fare clic su **OK**.

    * **Origine dati:** selezionare **Output attività**.
    * Elenco origini dati: selezionare **Ottieni connessione di automazione**.
    * **Percorso campo** `CertificateThumbprint`-- tipo .
1. Fare clic su **SERVICEPRINCIPAL**e nella pagina Valore parametro selezionare **ConstantValue** per il campo **Origine dati;** fare clic sull'opzione **True**; e quindi fare clic su **OK**.
1. Fare clic su **TENANTID**e apportare le impostazioni seguenti nella pagina Valore parametro . Al termine, fare clic **su OK** due volte.

    * **Origine dati:** selezionare **Output attività**. 
    * Elenco origini dati: selezionare **Ottieni connessione di automazione**.
    * **Percorso campo** `TenantId`-- tipo . 
1. Nel controllo Libreria `Set-AzContext` digitare il campo di ricerca.
1. Aggiungi `Set-AzContext` alla tela.
1. Selezionare `Set-AzContext` nell'area di disegno. Nel riquadro Controllo `Specify Subscription Id` configurazione immettere il campo **Etichetta.**
1. Fare clic su **Parametri** per visualizzare la pagina Configurazione parametro attività.
1. Il `Set-AzContext` cmdlet dispone di più set di parametri ed è necessario selezionarne uno prima di specificare i valori dei parametri. Fare clic su **Set** di parametri e quindi selezionare **SubscriptionId**.
1. I parametri per questo set di parametri vengono visualizzati nella pagina Configurazione parametri attività. Fare clic su **SubscriptionID**.
1. Nella pagina Valore parametro selezionare **Asset variabile** per il campo **Origine dati** e selezionare **AzureSubscriptionId** dall'elenco di origine. Al termine, fare clic **su OK** due volte.
1. Posizionare `Login to Azure` il puntatore del mouse finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio `Specify Subscription Id`e trascinare la freccia su . A questo punto, il runbook dovrebbe essere simile al seguente.

    ![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passaggio 7: Aggiungere l'attività per avviare una macchina virtuale

A questo punto `Start-AzVM` è necessario aggiungere un'attività per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento si sta hardcoded il nome nel cmdlet [Start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. Nel controllo Libreria `Start-Az` digitare il campo di ricerca.
2. Aggiungere `Start-AzVM` all'area di disegno, `Specify Subscription Id`quindi fare clic e trascinarla sotto .
1. Posizionare `Specify Subscription Id` il puntatore del mouse finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio `Start-AzVM`e trascinare la freccia su .
1. Selezionare `Start-AzVM`. Fare clic su **Parametri,** quindi su **Set di** parametri per visualizzare i set per l'attività.
1. Selezionare **ResourceGroupNameParameterSetName** per il set di parametri. I campi **ResourceGroupName** e **Name** presentano punti esclamativi accanto a essi per indicare che sono parametri obbligatori. Si noti che entrambi i campi prevedono valori stringa.
1. Selezionare **Nome**. Scegliere **Espressione di PowerShell** per il campo **Origine dati.** Per la macchina virtuale usata per avviare questo runbook, digitare il nome del computer racchiuso tra virgolette doppie. Fare clic su **OK**.
1. Selezionare **ResourceGroupName**. Usare il valore **Espressione di PowerShell** per il campo **Origine dati** e digitare il nome del gruppo di risorse racchiuso tra virgolette doppie. Fare clic su **OK**.
1. Fare clic sul **riquadro Test** per testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata. A questo punto, il runbook dovrebbe essere simile al seguente.

    ![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passaggio 8 - Aggiungere parametri di input aggiuntiviStep 8 - Add additional input parameters

Il runbook avvia attualmente la macchina virtuale nel `Start-AzVM` gruppo di risorse specificato per il cmdlet. Il runbook sarà più utile se si specifica sia il nome che il gruppo di risorse all'avvio del runbook. Aggiungiamo parametri di input al runbook per fornire tale funzionalità.

1. Aprire l'editor grafico facendo clic su **Modifica** nella pagina MyFirstRunbook-Graphical.
1. Selezionare **Input e output** e quindi **Aggiungi input** per aprire il riquadro Parametro di input del runbook.
1. Effettuare le seguenti impostazioni nei campi specificati e quindi fare clic su **OK**.
   * **Nome** -- `VMName`specificare .
   * **Digitare** -- mantenere l'impostazione della stringa.
   * **Obbligatorio** -- modificare il valore in **Sì**.
1. Creare un secondo parametro di input obbligatorio denominato `ResourceGroupName` e quindi fare clic su **OK** per chiudere il riquadro Input e output.<br> ![Parametri di input del runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selezionare `Start-AzVM` l'attività e quindi fare clic su **Parametri**.
1. Modificare il campo **Origine dati** per **Nome** in **Input Runbook**. Selezionare quindi **VMName**.
1. Modificare il campo **Origine dati** per **ResourceGroupName** in **Input Runbook** e quindi selezionare **ResourceGroupName**.<br> ![Parametri Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Salvare il runbook e aprire il riquadro Test. Ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
1. Chiudere il riquadro Test.
1. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
1. Arrestare la macchina virtuale avviata in precedenza.
1. Fare clic su **Avvia** per avviare il runbook. Digitare i `VMName` valori `ResourceGroupName` per e per la macchina virtuale che si intende avviare.
1. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="step-9---create-a-conditional-link"></a>Passaggio 9: Creare un collegamento condizionale

È ora possibile modificare il runbook in modo che tenti di avviare la macchina virtuale solo se non è già stata avviata. A tale scopo, aggiungere un cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) che recupera lo stato a livello di istanza della macchina virtuale. È quindi possibile aggiungere un modulo `Get Status` di codice del flusso di lavoro di PowerShell chiamato con un frammento di codice PowerShell per determinare se lo stato della macchina virtuale è in esecuzione o arrestato. Un collegamento condizionale `Get Status` dal `Start-AzVM` modulo viene eseguito solo se lo stato in esecuzione corrente viene arrestato. Al termine di questa procedura, il `Write-Output` runbook utilizza il cmdlet per inviare un messaggio per informare l'utente se la macchina virtuale è stata avviata correttamente.

1. Aprire **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico) nell'editor grafico.
1. Rimuovere il `Specify Subscription Id` collegamento `Start-AzVM` tra e facendo clic su di esso e quindi premendo **CANC**.
1. Nel controllo Libreria `Get-Az` digitare il campo di ricerca.
1. Aggiungi `Get-AzVM` alla tela.
1. Selezionare `Get-AzVM` e quindi fare clic su **Set** di parametri per visualizzare i set per il cmdlet. 
1. Selezionare il set di parametri **GetVirtualMachineInResourceGroupNameParamSet** . I campi **ResourceGroupName** e **Name** presentano punti esclamativi accanto a essi, che indicano che specificano i parametri obbligatori. Si noti che entrambi i campi prevedono valori stringa.
1. In **Origine dati** per **Nome**selezionare Input **Runbook**, quindi **NomeVM**. Fare clic su **OK**.
1. In **Origine dati** per **ResourceGroupName**selezionare Input **Runbook**, quindi **ResourceGroupName**. Fare clic su **OK**.
1. In **Origine dati** per **Stato**selezionare **Valore costante**, quindi **Vero**. Fare clic su **OK**.
1. Creare un `Specify Subscription Id` collegamento `Get-AzVM`da a .
1. Nel controllo Libreria espandere **Controllo Runbook** e aggiungere **Codice** all'area di disegno.  
1. Creare un `Get-AzVM` collegamento `Code`da a .  
1. Fare `Code` clic su e, nel riquadro Configurazione, modificare l'etichetta in **Ottieni stato**.
1. Selezionare `Code` e viene visualizzata la pagina Editor di codice.  
1. Incollare il frammento di codice seguente nella pagina dell'editor.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Creare un `Get Status` collegamento `Start-AzVM`da a .

    ![Runbook con il modulo Code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selezionare il collegamento e, nel riquadro Configurazione, impostare **Applica condizione** su **Sì**. Si noti che il collegamento diventa una linea tratteggiata, a indicare che l'attività di destinazione viene eseguita solo se la condizione viene risolta in true.  
1. Per **Espressione**condizione `$ActivityOutput['Get Status'] -eq "Stopped"`, digitare . `Start-AzVM`ora viene eseguito solo se la macchina virtuale viene arrestata.
1. Nel controllo Libreria, espandere **Cmdlets** e quindi **Microsoft.PowerShell.Utility**.
1. Aggiungi `Write-Output` alla tela due volte.
1. Per il `Write-Output` primo controllo, fare clic su **Parametri** e modificare il valore **etichetta** in **Notifica VM avviata**.
1. Per **InputObject**, modificare **Origine dati** in Espressione di **PowerShell**e digitare l'espressione `$VMName successfully started.`.
1. Nel secondo `Write-Output` controllo fare clic su **Parametri** e modificare il valore **dell'etichetta** in **Notifica avvio macchina virtuale non riuscito**.
1. Per **InputObject**, modificare **Origine dati** in Espressione di **PowerShell**e digitare l'espressione `$VMName could not start`.
1. Creare collegamenti `Start-AzVM` `Notify VM Started` da `Notify VM Start Failed`e .
1. Selezionare il `Notify VM Started` collegamento a e modificare **Applica condizione** su true.
1. Per **l'espressione** `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`Condizione digitare . Questo `Write-Output` controllo ora viene eseguito solo se la macchina virtuale viene avviata correttamente.
1. Selezionare il `Notify VM Start Failed` collegamento a e modificare **Applica condizione** su true.
1. Per il campo Espressione `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` **condizione** digitare . Questo `Write-Output` controllo ora viene eseguito solo se la macchina virtuale non viene avviata correttamente. Il runbook dovrebbe essere simile all'immagine seguente.

    ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Salvare il runbook e aprire il riquadro Test.
1. Avviare il runbook con la macchina virtuale arrestata e il computer deve essere avviato.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure.To](automation-graphical-authoring-intro.md)learn more about graphical authoring, see Graphical authoring in Azure Automation .
* Per iniziare a usare i runbook di PowerShell, vedere il mio primo runbook di [PowerShell.](automation-first-runbook-textual-powershell.md)
* Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il mio primo runbook del flusso di lavoro](automation-first-runbook-textual.md)di PowerShell .
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).