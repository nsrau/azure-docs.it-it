---
title: Il primo runbook grafico in Automazione di Azure
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook grafico .
keywords: runbook, modello di runbook, automazione runbook, runbook di Azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 8b836ebc0adc6f0616d28b16bfb743dfc4553d1a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367416"
---
# <a name="my-first-graphical-runbook"></a>Il primo runbook grafico

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Questa esercitazione illustra la creazione di un [runbook grafico](automation-runbook-types.md#graphical-runbooks) in Automazione di Azure. Iniziare con un Runbook semplice che è possibile testare e pubblicare, mentre si apprende come tenere traccia dello stato del processo del Runbook. Modificare quindi il Runbook per gestire effettivamente le risorse di Azure, in questo caso avviando una macchina virtuale di Azure. Completare l'esercitazione per rendere il Runbook più affidabile aggiungendo i parametri Runbook e i collegamenti condizionali.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché si arresta e si avvia questo computer, non dovrebbe essere una macchina virtuale di produzione.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un Runbook semplice che restituisce il testo `Hello World`.

1. Nel portale di Azure aprire l'account di automazione. 

    La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. La maggior parte di tali asset sono i moduli inclusi automaticamente in un nuovo account di automazione. È anche necessario che l'asset delle credenziali sia associato alla sottoscrizione.
2. Selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
3. Creare un nuovo Runbook selezionando **Crea una Runbook**.
4. Denominare il runbook **MyFirstRunbook-Graphical**.
5. In questo caso verrà creato un [Runbook grafico](automation-graphical-authoring-intro.md). Selezionare **grafico** per **tipo di Runbook**.<br> ![Nuovo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Fare clic su **Crea** per creare il runbook e aprire l'editor grafico.

## <a name="step-2---add-activities"></a>Passaggio 2: aggiungere attività

Il controllo Libreria sul lato sinistro dell'editor consente di selezionare le attività da aggiungere al runbook. Si aggiungerà un cmdlet `Write-Output` per l'output del testo dal Runbook.

1. Nel controllo libreria fare clic nel campo di ricerca e digitare `write-output`. I risultati della ricerca vengono mostrati nell'immagine seguente. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Scorrere fino alla fine dell'elenco. Fare clic con il pulsante destro del mouse su **Write-Output** e scegliere **Aggiungi a Canvas**. In alternativa, è possibile fare clic sui puntini di sospensione (...) accanto al nome del cmdlet e quindi selezionare **Aggiungi ad area di disegno**.
1. Fare clic sull'attività **Write-Output** nell'area di disegno. Si aprirà la pagina Controllo configurazione che consente di configurare l'attività.
1. Per impostazione predefinita, il campo **etichetta** è il nome del cmdlet, ma è possibile modificarlo in un modo più semplice. Modificarlo in `Write Hello World to output`.
1. Fare clic su **Parametri** per specificare i valori per i parametri del cmdlet.

   Alcuni cmdlet hanno più set di parametri ed è necessario selezionare quello da usare. In questo caso, `Write-Output` dispone di un solo set di parametri.

1. Selezionare il parametro `InputObject`. Si tratta del parametro usato per specificare il testo da inviare al flusso di output.
1. Il menu a discesa **origine dati** fornisce le origini che è possibile utilizzare per popolare un valore di parametro. In questo menu selezionare **espressione PowerShell**. 

   È possibile usare l'output di tali origini come un'altra attività, un asset di automazione o un'espressione di PowerShell. In questo caso, l'output è semplicemente `Hello World`. È possibile usare un'espressione di PowerShell e specificare una stringa.<br>

1. Nel campo **espressione** Digitare `Hello World` e quindi fare clic su **OK** due volte per tornare all'area di disegno.
1. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il Runbook per renderlo disponibile nell'ambiente di produzione, è necessario testarlo per verificare che funzioni correttamente. Il test di un Runbook esegue la versione bozza e consente di visualizzarne l'output in modo interattivo.

1. Selezionare **riquadro Test** per aprire il riquadro test.
1. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.
1. Si noti che viene creato un [processo del Runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato del processo inizia come `Queued`, a indicare che il processo è in attesa che un Runbook Worker nel cloud diventi disponibile. Lo stato diventa `Starting` quando un thread di lavoro dichiara il processo. Infine, lo stato diventa `Running` quando l'esecuzione del Runbook viene effettivamente avviata.

1. Al termine del processo del Runbook, nel riquadro test viene visualizzato l'output. In questo caso, viene visualizzato `Hello World`.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il Runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Selezionare **Pubblica** per pubblicare il runbook, quindi **Sì** quando richiesto.
1. Scorrere verso sinistra per visualizzare il Runbook nella pagina manuali operativi e notare che il valore **dello stato di creazione** è impostato su **pubblicato**.
1. Scorrere verso destra per visualizzare la pagina **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico).

   Le opzioni nella parte superiore consentono di avviare il Runbook ora, pianificare un'ora di avvio futura o creare un [webhook](automation-webhooks.md) in modo che Runbook possa essere avviato tramite una chiamata http.

1. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.
1. Viene aperto un riquadro del processo per il processo Runbook creato. Verificare che il campo **stato processo** indichi **completato**.
1. Fare clic su **output** per aprire la pagina output, in cui è possibile visualizzare `Hello World` visualizzato.
1. Chiudere la pagina Output.
1. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output dovrebbe essere visibile solo `Hello World`. 

    Si noti che nel riquadro flussi possono essere visualizzati altri flussi per un processo Runbook, ad esempio flussi dettagliati e di errore, se il Runbook scrive in essi.
1. Chiudere il riquadro dei flussi e il riquadro dei processi per tornare alla pagina **MyFirstRunbook-Graphical** .
1. Per visualizzare tutti i processi per il Runbook, selezionare **processi** in **risorse**. Nella pagina processi sono elencati tutti i processi creati dal Runbook. Verrà visualizzato solo un processo, perché il processo è stato eseguito una sola volta.
1. Fare clic sul nome del processo per aprire lo stesso riquadro del processo visualizzato quando è stato avviato il Runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il Runbook.

## <a name="step-5---create-variable-assets"></a>Passaggio 5: Creare asset di variabile

Il Runbook è stato testato e pubblicato, ma finora non esegue alcuna operazione utile per gestire le risorse di Azure. Prima di configurare Runbook per l'autenticazione, è necessario creare una variabile che contenga l'ID sottoscrizione, configurare un'attività per l'autenticazione e quindi fare riferimento alla variabile. L'inclusione di un riferimento al contesto della sottoscrizione consente di utilizzare facilmente più sottoscrizioni.

1. Copiare l'ID sottoscrizione dall'opzione **sottoscrizioni** nel riquadro di spostamento.
1. Nella pagina account di automazione selezionare **variabili** in **risorse condivise**.
1. Selezionare **Aggiungi variabile**.
1. Nella pagina nuova variabile, eseguire le impostazioni seguenti nei campi specificati.

    * **Nome** : immettere `AzureSubscriptionId`.
    * **Valore** : immettere l'ID sottoscrizione. 
    * **Tipo** : Mantieni la stringa selezionata.
    * **Crittografia** : usare il valore predefinito.
1. Fare clic su **Crea** per creare la variabile.

## <a name="step-6---add-authentication"></a>Passaggio 6: aggiungere l'autenticazione

Ora che è disponibile una variabile per contenere l'ID sottoscrizione, è possibile configurare il Runbook per l'autenticazione con le credenziali RunAs per la sottoscrizione. A tale scopo, aggiungere la connessione RunAs di Azure come asset. È anche necessario aggiungere il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e il cmdlet [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) all'area di disegno.

>[!NOTE]
>Per manuali operativi di PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Si noti che questi alias non sono disponibili per il manuali operativi grafico. Un Runbook grafico può usare solo `Connect-AzAccount`.

1. Passare a Runbook e selezionare **modifica** nella pagina **MyFirstRunbook-Graphical** .
1. La voce `Write Hello World to output` non è più necessaria. È sufficiente fare clic sui puntini di sospensione e selezionare **Elimina**.
1. Nel controllo Libreria espandere **Asset**, quindi **connessioni**. Aggiungere `AzureRunAsConnection` all'area di disegno selezionando **Aggiungi ad area di disegno**.
1. Nel controllo libreria digitare `Connect-AzAccount` nel campo di ricerca.
1. Aggiungere `Connect-AzAccount` all'area di disegno.
1. Passare il puntatore su **Get Run As Connection** finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia per `Connect-AzAccount` per creare un collegamento. Il Runbook inizia con `Get Run As Connection`, quindi esegue `Connect-AzAccount`.<br> ![Creare un collegamento tra le attività](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Nell'area di disegno selezionare `Connect-AzAccount`. Nel riquadro controllo configurazione digitare **login to Azure** nel campo **Label** .
1. Fare clic su **parametri**e viene visualizzata la pagina configurazione parametro attività.
1. Il cmdlet `Connect-AzAccount` dispone di più set di parametri ed è necessario selezionarne uno prima di fornire i valori dei parametri. Fare clic su **set di parametri** e quindi selezionare **ServicePrincipalCertificate**.
1. I parametri per questo set di parametri vengono visualizzati nella pagina configurazione parametro attività. Fare clic su **APPLICATIONID**.<br> ![aggiungere parametri dell'account Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Nella pagina valore parametro apportare le seguenti impostazioni e quindi fare clic su **OK**.

   * **Origine dati** : selezionare **output attività**.
   * Elenco origine dati: selezionare **Get Run As Connection**.
   * **Percorso campo** : digitare `ApplicationId`. Si specifica il nome della proprietà per il percorso del campo perché l'attività restituisce un oggetto con più proprietà.
1. Fare clic su **CERTIFICATETHUMBPRINT**e nella pagina valore parametro apportare le seguenti impostazioni e quindi fare clic su **OK**.

    * **Origine dati** : selezionare **output attività**.
    * Elenco origine dati: selezionare **Get Run As Connection**.
    * **Percorso campo** : digitare `CertificateThumbprint`.
1. Fare clic su **SERVICEPRINCIPAL**e nella pagina valore parametro selezionare **ConstantValue** per il campo **origine dati** . fare clic sull'opzione **true**. e quindi fare clic su **OK**.
1. Fare clic su **TENANTID**e apportare le impostazioni seguenti nella pagina valore parametro. Al termine, fare clic su **OK** due volte.

    * **Origine dati** : selezionare **output attività**. 
    * Elenco origine dati: selezionare **Get Run As Connection**.
    * **Percorso campo** : digitare `TenantId`. 
1. Nel controllo libreria digitare `Set-AzContext` nel campo di ricerca.
1. Aggiungere `Set-AzContext` all'area di disegno.
1. Selezionare `Set-AzContext` nell'area di disegno. Nel riquadro controllo configurazione immettere `Specify Subscription Id` nel campo **etichetta** .
1. Fare clic su **Parametri** per visualizzare la pagina Configurazione parametro attività.
1. Il cmdlet `Set-AzContext` dispone di più set di parametri ed è necessario selezionarne uno prima di fornire i valori dei parametri. Fare clic su **set di parametri** e quindi selezionare **SubscriptionId**.
1. I parametri per questo set di parametri vengono visualizzati nella pagina configurazione parametro attività. Fare clic su **SubscriptionId**.
1. Nella pagina valore parametro selezionare **Asset variabile** per il campo **origine dati** e selezionare **AzureSubscriptionId** nell'elenco origine. Al termine, fare clic su **OK** due volte.
1. Passare il puntatore del mouse su `Login to Azure` fino a quando non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su `Specify Subscription Id`. 

A questo punto l'aspetto del runbook sarà simile al seguente: <br>![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passaggio 7: Aggiungere l'attività per avviare una macchina virtuale

A questo punto è necessario aggiungere un'attività `Start-AzVM` per avviare una macchina virtuale. È possibile selezionare qualsiasi macchina virtuale nella sottoscrizione di Azure e per il momento si sta impostando come hardcoded il nome nel cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) .

1. Nel controllo libreria digitare `Start-Az` nel campo di ricerca.
2. Aggiungere `Start-AzVM` all'area di disegno e quindi fare clic e trascinarlo sotto `Specify Subscription Id`.
1. Passare il puntatore del mouse su `Specify Subscription Id` fino a quando non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su `Start-AzVM`.
1. Selezionare `Start-AzVM`. Fare clic su **parametri** e quindi su **set di parametri** per visualizzare i set per l'attività.
1. Selezionare **ResourceGroupNameParameterSetName** per il set di parametri. I campi **ResourceGroupName** e **Name** contengono punti esclamativi accanto a essi per indicare che sono parametri obbligatori. Si noti che in entrambi i campi sono previsti valori stringa.
1. Selezionare **Name**. Scegliere **espressione PowerShell** per il campo **origine dati** . Per la VM usata per avviare questo Runbook, digitare il nome del computer racchiuso tra virgolette doppie. Fare clic su **OK**.
1. Selezionare **ResourceGroupName**. Usare l' **espressione di PowerShell** value per il campo **origine dati** e digitare il nome del gruppo di risorse racchiuso tra virgolette doppie. Fare clic su **OK**.
1. Fare clic su **Riquadro di test** in modo da testare il runbook.
1. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata. 

A questo punto l'aspetto del runbook sarà simile al seguente: <br>![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passaggio 8: aggiungere parametri di input aggiuntivi

Il Runbook avvia attualmente la macchina virtuale nel gruppo di risorse specificato per il cmdlet `Start-AzVM`. Il Runbook sarà più utile se si specificano sia il nome che il gruppo di risorse all'avvio del Runbook. Aggiungere i parametri di input al Runbook per fornire tale funzionalità.

1. Aprire l'editor grafico facendo clic su **Modifica** nel riquadro **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico).
1. Selezionare **Input e output** e quindi **Aggiungi input** per aprire il riquadro Parametro di input del runbook.
1. Apportare le impostazioni seguenti nei campi specificati, quindi fare clic su **OK**.
   * **Nome** : specificare `VMName`.
   * **Digitare** : Mantieni l'impostazione della stringa.
   * **Obbligatorio** : modificare il valore in **Sì**.
1. Creare un secondo parametro di input obbligatorio denominato `ResourceGroupName`, quindi fare clic su **OK** per chiudere il riquadro input e output.<br> ![Parametri di input del runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selezionare l'attività `Start-AzVM` e quindi fare clic su **parametri**.
1. Modificare il campo **origine dati** per **nome** in **input Runbook**. Quindi selezionare **VMName**.
1. Modificare il campo **origine dati** per **ResourceGroupName** in **input Runbook** e quindi selezionare **ResourceGroupName**.<br> ![parametri Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Salvare il runbook e aprire il riquadro Test. Ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
1. Chiudere il riquadro Test.
1. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
1. Arrestare la macchina virtuale avviata in precedenza.
1. Fare clic su **Avvia** per avviare il runbook. Digitare i valori per `VMName` e `ResourceGroupName` per la macchina virtuale che si sta per avviare.
1. Al termine del Runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="step-9---create-a-conditional-link"></a>Passaggio 9: Creare un collegamento condizionale

È ora possibile modificare il Runbook in modo che tenti solo di avviare la macchina virtuale, se non è già stata avviata. A tale scopo, aggiungere un cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) che recuperi lo stato a livello di istanza della macchina virtuale. È quindi possibile aggiungere un modulo di codice del flusso di lavoro PowerShell denominato `Get Status` con un frammento di codice PowerShell per determinare se lo stato della macchina virtuale è in esecuzione o arrestato. Un collegamento condizionale dal modulo `Get Status` viene eseguito solo `Start-AzVM` se lo stato di esecuzione corrente è arrestato. Al termine di questa procedura, la runbook usa il cmdlet `Write-Output` per restituire un messaggio che informa se la macchina virtuale è stata avviata correttamente.

1. Aprire **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico) nell'editor grafico.
1. Rimuovere il collegamento tra `Specify Subscription Id` e `Start-AzVM` facendo clic su di esso e quindi premendo **Canc**.
1. Nel controllo libreria digitare `Get-Az` nel campo di ricerca.
1. Aggiungere `Get-AzVM` all'area di disegno.
1. Selezionare `Get-AzVM` e quindi fare clic su **set di parametri** per visualizzare i set per il cmdlet. 
1. Selezionare il set di parametri **GetVirtualMachineInResourceGroupNameParamSet** . I campi **ResourceGroupName** e **Name** contengono punti esclamativi, a indicare che specificano i parametri obbligatori. Si noti che in entrambi i campi sono previsti valori stringa.
1. In **origine dati** per **nome**Selezionare **input Runbook**, quindi **VMName**. Fare clic su **OK**.
1. In **origine dati** per **ResourceGroupName**Selezionare **input Runbook**, quindi **ResourceGroupName**. Fare clic su **OK**.
1. In **origine dati** per **stato**Selezionare **valore costante**, quindi **true**. Fare clic su **OK**.
1. Creare un collegamento da `Specify Subscription Id` a `Get-AzVM`.
1. Nel controllo Libreria espandere il **Controllo Runbook** e aggiungere il **codice** all'area di disegno.  
1. Creare un collegamento da `Get-AzVM` a `Code`.  
1. Fare clic su `Code` e, nel riquadro Configurazione, modificare l'etichetta per **ottenere lo stato**.
1. Selezionare `Code` e verrà visualizzata la pagina editor di codice.  
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

1. Creare un collegamento da `Get Status` a `Start-AzVM`.<br> ![Runbook con il modulo Code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selezionare il collegamento e, nel riquadro Configurazione, impostare **applica condizione** su **Sì**. Si noti che il collegamento diventa una linea tratteggiata, a indicare che l'attività di destinazione viene eseguita solo se la condizione viene risolta in true.  
1. Per **espressione condizione**Digitare `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` ora viene eseguito solo se la macchina virtuale è arrestata.
1. Nel controllo Libreria, espandere **Cmdlets** e quindi **Microsoft.PowerShell.Utility**.
1. Aggiungere `Write-Output` al canvas due volte.
1. Per il primo controllo `Write-Output`, fare clic su **parametri** e modificare il valore dell' **etichetta** in **notifica avvio macchina virtuale**.
1. Per **InputObject**, modificare l' **origine dati** in **espressione PowerShell**e digitare l'espressione `$VMName successfully started.`.
1. Nel secondo controllo `Write-Output` fare clic su **parametri** e modificare il valore dell' **etichetta** in **notifica avvio macchina virtuale non riuscito**.
1. Per **InputObject**, modificare l' **origine dati** in **espressione PowerShell**e digitare l'espressione `$VMName could not start.`.
1. Creare collegamenti da `Start-AzVM` a `Notify VM Started` e `Notify VM Start Failed`.
1. Selezionare il collegamento per `Notify VM Started` e modificare **applica condizione** su true.
1. Per l' **espressione della condizione**, digitare `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Questo controllo `Write-Output` ora viene eseguito solo se la macchina virtuale viene avviata correttamente.
1. Selezionare il collegamento per `Notify VM Start Failed` e modificare **applica condizione** su true.
1. Per il campo **espressione condizione** Digitare `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Questo controllo `Write-Output` ora viene eseguito solo se la macchina virtuale non è stata avviata correttamente. L'aspetto del runbook sarà simile all'immagine seguente: <br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Salvare il runbook e aprire il riquadro Test.
1. Avviare il Runbook con la macchina virtuale arrestata e avviare il computer.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per iniziare a usare manuali operativi del flusso di lavoro PowerShell, vedere [il primo Runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).