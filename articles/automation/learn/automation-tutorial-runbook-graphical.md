---
title: Creare un runbook grafico in Automazione di Azure
description: Questo articolo illustra come creare, testare e pubblicare un semplice runbook grafico in Automazione di Azure.
keywords: runbook, modello di runbook, automazione runbook, runbook di Azure
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 81dc23c208ca9fb292c849bdf35d8b91311ed9ce
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987649"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Esercitazione: Creare un runbook grafico

Questa esercitazione illustra la creazione di un [runbook grafico](../automation-runbook-types.md#graphical-runbooks) in Automazione di Azure. È possibile creare e modificare runbook grafici e runbook del flusso di lavoro PowerShell grafico usando l'editor grafico nel portale di Azure. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un runbook grafico semplice
> * Testare e pubblicare il runbook
> * Eseguire il processo del runbook e monitorarne lo stato
> * Aggiornare il runbook per avviare una macchina virtuale di Azure con i parametri del runbook e i collegamenti condizionali

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free).
* [Account di Automazione](../index.yml) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure. Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. Poiché la macchina virtuale viene arrestata e avviata, non deve essere una macchina virtuale di produzione.
* Se necessario, [importare i moduli di Azure](../shared-resources/modules.md) o [aggiornare i moduli](../automation-update-azure-modules.md) in base ai cmdlet usati.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook

Iniziare creando un runbook semplice che restituisce il testo `Hello World`.

1. Nel portale di Azure aprire l'account di automazione.

    La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account. Dovrebbero essere già disponibili alcuni asset. Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di Automazione. È anche necessario disporre dell'asset delle credenziali associato alla sottoscrizione.

2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.

3. Per creare un nuovo runbook, selezionare **Crea un runbook**.

4. Denominare il runbook **MyFirstRunbook-Graphical**.

5. In questo caso, verrà creato un [runbook grafico](../automation-graphical-authoring-intro.md). Per **Tipo di runbook**, selezionare **Grafico**.

    ![Nuovo runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Fare clic su **Crea** per creare il runbook e aprire l'editor grafico.

## <a name="step-2---add-activities"></a>Passaggio 2: Aggiungere le attività

Il controllo Libreria sul lato sinistro dell'editor consente di selezionare le attività da aggiungere al runbook. Si aggiungerà un cmdlet `Write-Output` per l'output del testo dal runbook.

1. Nel controllo Libreria fare clic nella casella di ricerca e digitare `write-output`. I risultati della ricerca sono mostrati nell'immagine seguente.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Scorrere fino alla fine dell'elenco. Fare clic con il pulsante destro del mouse su **Write-Output** e scegliere **Aggiungi ad area di disegno**. In alternativa, è possibile fare clic sui puntini di sospensione (...) accanto al nome del cmdlet e quindi selezionare **Aggiungi ad area di disegno**.

3. Fare clic sull'attività **Write-Output** nell'area di disegno. Si aprirà la pagina Controllo configurazione che consente di configurare l'attività.

4. Nel campo **Etichetta** è impostato il nome predefinito del cmdlet, ma è possibile modificarlo specificando un nome più descrittivo. Sostituirlo con `Write Hello World to output`.

5. Fare clic su **Parametri** per specificare i valori per i parametri del cmdlet.

   Alcuni cmdlet hanno più set di parametri ed è necessario selezionarne uno da usare. In questo caso, `Write-Output` dispone di un solo set di parametri.

6. Selezionare il parametro `InputObject`. Si tratta del parametro che verrà usato per specificare il testo da inviare al flusso di output.

7. Il menu a discesa **Origine dati** contiene le origini che è possibile usare per popolare il valore di un parametro. In questo menu selezionare **Espressione PowerShell**.

   È possibile usare l'output da tali origini, ad esempio un'altra attività, un asset di Automazione o un'espressione PowerShell. In questo caso, l'output è semplicemente `Hello World`. È possibile usare un'espressione di PowerShell e specificare una stringa.

8. Nel campo **Espressione** digitare `Hello World` e quindi fare clic su **OK** due volte per tornare all'area di disegno.

9. Salvare il runbook facendo clic su **Salva**.

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook

Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente. Quando si testa un runbook, è necessario eseguire la versione Bozza e visualizzarne l'output in modo interattivo.

1. Fare clic su **Riquadro di test** per aprire il riquadro di test.

2. Fare clic su **Avvia** per avviare il test. Questa deve essere l'unica opzione abilitata.

3. Si noti che viene creato un [processo del runbook](../automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.

   Lo stato iniziale del processo è `Queued`, per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud. Lo stato passa a `Starting` quando un ruolo di lavoro richiede il processo. Infine, lo stato diventa `Running` quando viene avviata l'esecuzione effettiva del runbook.

4. Al termine del processo del runbook, viene visualizzato l'output nel riquadro di test. In questo caso, verrà visualizzato l'output `Hello World`.

    ![Output del runbook Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Chiudere il riquadro di test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook

Il runbook creato è ancora in modalità bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione. Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza. In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Selezionare **Pubblica** per pubblicare il runbook, quindi **Sì** quando richiesto.

2. Scorrere verso sinistra per visualizzare il runbook nella pagina Runbook e notare che il valore di **Stato creazione** è impostato su **Pubblicato**.

3. Scorrere verso destra per visualizzare la pagina **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico).

   Le opzioni nella parte superiore consentono di avviare il runbook adesso, pianificarlo per un'ora di inizo in futuro o creare un [webhook](../automation-webhooks.md) per poterlo avviare con una chiamata HTTP.

4. Selezionare **Avvia** e poi **Sì** quando richiesto per avviare il runbook.

5. Verrà aperto un riquadro per il processo del runbook creato. Verificare che il campo **Stato processo** indichi **Completato**.

6. Fare clic su **Output** per aprire la pagina Output, in cui è possibile visualizzare `Hello World`.

7. Chiudere la pagina Output.

8. Fare clic su **Tutti i log** per aprire il riquadro Flussi relativo al processo del runbook. Nel flusso di output sarà visibile solo `Hello World`.

    Si noti che nel riquadro Flussi potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Dettagliato ed Errore, se il runbook esegue la scrittura in questi flussi.

9. Chiudere il riquadro Flussi e il riquadro Processi per tornare alla pagina MyFirstRunbook-Graphical.

10. Per visualizzare tutti i processi per il runbook, selezionare **Processi** in **Risorse**. In questa pagina sono elencati tutti i processi creati dal runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.

11. Fare clic sul nome del processo per aprire lo stesso riquadro Processo visualizzato quando è stato avviato il runbook. Utilizzare questo riquadro per visualizzare i dettagli di qualsiasi processo creato per il runbook.

## <a name="step-5---create-variable-assets"></a>Passaggio 5: Creare asset di variabile

Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile per gestire le risorse di Azure. Prima di configurare il runbook per l'autenticazione, è necessario creare una variabile che conterrà l'ID sottoscrizione, configurare un'attività per l'autenticazione e infine fare riferimento alla variabile. L'inserimento di un riferimento al contesto della sottoscrizione consente di gestire facilmente più sottoscrizioni contemporaneamente.

1. Copiare l'ID sottoscrizione dall'opzione **Sottoscrizioni** del riquadro di spostamento.

2. Nella pagina Account di Automazione, selezionare **Variabili** in **Risorse condivise**.

3. Selezionare **Aggiungi variabile**.

4. Nella pagina Nuova variabile, definire le impostazioni seguenti nei campi specificati.

    * **Nome**: immettere `AzureSubscriptionId`.
    * **Valore**: immettere l'ID sottoscrizione.
    * **Tipo**: mantenere selezionata la voce stringa.
    * **Crittografia**: usare il valore predefinito.

5. Fare clic su **Crea** per creare la variabile.

## <a name="step-6---add-authentication"></a>Passaggio 6: Aggiungere l'autenticazione

Dopo avere creato una variabile che contiene l'ID sottoscrizione, è possibile configurare il runbook per l'autenticazione con le credenziali RunAs per la sottoscrizione. A tale scopo, aggiungere la connessione RunAs di Azure come asset. È anche necessario aggiungere il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) e il cmdlet [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) all'area di disegno.

>[!NOTE]
>Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico può usare solo `Connect-AzAccount`.

1. Passare al runbook e selezionare **Modifica** nella pagina MyFirstRunbook-Graphical.

2. La voce `Write Hello World to output` non è più necessaria. È sufficiente fare clic sui puntini di sospensione e selezionare **Elimina**.

3. Nel controllo Libreria espandere **ASSET**, quindi **Connessioni**. Aggiungere `AzureRunAsConnection` all'area di disegno selezionando **Aggiungi ad area di disegno**.

4. Rinominare `AzureRunAsConnection` con `Get Run As Connection`.

5. Nel controllo Libreria digitare `Connect-AzAccount` nel campo di ricerca.

6. Aggiungere `Connect-AzAccount` all'area di disegno.

7. Passare il puntatore del mouse su `Get Run As Connection` finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su `Connect-AzAccount` per creare un collegamento. Il runbook inizia con `Get Run As Connection` e quindi esegue `Connect-AzAccount`.

    ![Creare un collegamento tra le attività](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Nell'area di disegno selezionare `Connect-AzAccount`. Nel pannello di controllo Configurazione digitare **Accesso ad Azure** nel campo **Etichetta**.

9. Fare clic su **Parametri** per visualizzare la pagina Configurazione parametro attività.

10. Il cmdlet `Connect-AzAccount` include più set di parametri, quindi è necessario selezionarne uno prima di specificarne i valori. Fare clic su **Set di parametri** e quindi selezionare **ServicePrincipalCertificateWithSubscriptionId**.

11. I parametri per questo set di parametri verranno visualizzati nella pagina Configurazione parametro attività. Fare clic su **APPLICATIONID**.

    ![Aggiungere i parametri dell'account Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Nella pagina Valore parametro configurare le impostazioni seguenti e quindi fare clic su **OK**.

   * **Origine dati**: selezionare **Output attività**.
   * Elenco origini dati: selezionare **Get Automation Connection** (Ottieni connessione di Automazione).
   * **Percorso campo**: digitare `ApplicationId`. Viene specificato il nome della proprietà per il percorso del campo perché l'attività restituisce un oggetto con più proprietà.

13. Fare clic su **CERTIFICATETHUMBPRINT** e nella pagina Valore parametro configurare le impostazioni seguenti, quindi fare clic su **OK**.

    * **Origine dati**: selezionare **Output attività**.
    * Elenco origini dati: selezionare **Get Automation Connection** (Ottieni connessione di Automazione).
    * **Percorso campo**: digitare `CertificateThumbprint`.

14. Fare clic su **SERVICEPRINCIPAL**, quindi nella pagina Valore parametro selezionare **ConstantValue** per il campo **Origine dati**. Fare clic sull'opzione **True** e quindi su **OK**.

15. Fare clic su **TENANTID** e nella pagina Valore parametro configurare le impostazioni seguenti. Al termine, fare clic su **OK** due volte.

    * **Origine dati**: selezionare **Output attività**.
    * Elenco origini dati: selezionare **Get Automation Connection** (Ottieni connessione di Automazione).
    * **Percorso campo**: digitare `TenantId`.

16. Nel controllo Libreria digitare `Set-AzContext` nel campo di ricerca.

17. Aggiungere `Set-AzContext` all'area di disegno.

18. Nell'area di disegno selezionare `Set-AzContext`. Nel pannello di controllo Configurazione immettere `Specify Subscription Id` nel campo **Etichetta**.

19. Fare clic su **Parametri** per visualizzare la pagina Configurazione parametro attività.

20. Il cmdlet `Set-AzContext` include più set di parametri, quindi è necessario selezionarne uno prima di specificarne i valori. Fare clic su **Set di parametri** e quindi selezionare **SubscriptionId**.

21. I parametri per questo set di parametri verranno visualizzati nella pagina Configurazione parametro attività. Fare clic su **SubscriptionID**.

22. Nella pagina Valore parametro selezionare **Asset variabile** per il campo **Origine dati** e selezionare **AzureSubscriptionId** dall'elenco di origini. Al termine, fare clic su **OK** due volte.

23. Passare il puntatore del mouse su `Login to Azure` finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su `Specify Subscription Id`. A questo punto l'aspetto del runbook sarà simile al seguente.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Screenshot del runbook dopo il trascinamento della freccia sull'opzione per specificare l'ID sottoscrizione.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passaggio 7: Aggiungere l'attività per avviare una macchina virtuale

A questo punto è necessario aggiungere un'attività `Start-AzVM` per avviare una macchina virtuale. È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm).

1. Nel controllo Libreria digitare `Start-Az` nel campo di ricerca.

2. Aggiungere `Start-AzVM` all'area di disegno, fare clic su di esso e trascinarlo sotto `Specify Subscription Id`.

3. Passare il puntatore del mouse su `Specify Subscription Id` finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su `Start-AzVM`.

4. Selezionare `Start-AzVM`. Fare clic su **Parametri** e quindi su **Set di parametri** per visualizzare i set per l'attività.

5. Selezionare il set di parametri **ResourceGroupNameParameterSetName**. Accanto ai campi **ResourceGroupName** e **Nome** sono visualizzati punti esclamativi per indicare che sono parametri obbligatori. Si noti che per entrambi i campi sono previsti valori stringa.

6. Selezionare **Name**. Scegliere **Espressione PowerShell** per il campo **Origine dati**. Per la macchina virtuale usata per avviare questo runbook, digitare il nome della macchina virtuale racchiuso tra virgolette doppie. Fare clic su **OK**.

7. Selezionare **ResourceGroupName**. Usare il valore **Espressione PowerShell** per il campo **Origine dati** e digitare il nome del gruppo di risorse racchiuso tra virgolette doppie. Fare clic su **OK**.

8. Fare clic su **Riquadro di test** in modo da testare il runbook.

9. Fare clic su **Avvia** per avviare il test. Al termine, assicurarsi che la macchina virtuale sia stata avviata. A questo punto l'aspetto del runbook sarà simile al seguente.

    ![Output di Start-AzVM del runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passaggio 8: Aggiungere altri parametri di input

Il runbook avvia la macchina virtuale nel gruppo di risorse specificato nel cmdlet `Start-AzVM`. Il runbook sarà più utile se si specificano sia il nome che il gruppo di risorse all'avvio del runbook. Per fornire questa funzionalità, occorre aggiungere parametri di input al runbook.

1. Aprire l'editor grafico facendo clic su **Modifica** nella pagina MyFirstRunbook-Graphical.

2. Selezionare **Input e output** e quindi **Aggiungi input** per aprire il riquadro Parametro di input del runbook.

3. Configurare le impostazioni seguenti nei campi specificati e quindi fare clic su **OK**.
   * **Nome**: specificare `VMName`.
   * **Tipo**: mantenere l'impostazione stringa.
   * **Obbligatorio**: impostare il valore su **Sì**.

4. Creare un secondo parametro di input obbligatorio denominato `ResourceGroupName` e quindi fare clic su **OK** per chiudere il riquadro Input e output.

    ![Parametri di input del runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selezionare l'attività `Start-AzVM` e quindi fare clic su **Parametri**.

6. Modificare il campo **Origine dati** per **Nome** impostandolo su **Input runbook**. Selezionare quindi **VMName**.

7. Modificare il campo **Origine dati** per **ResourceGroupName** impostandolo su **Input runbook** e quindi selezionare **ResourceGroupName**.

    ![Parametri Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Salvare il runbook e aprire il riquadro Test. Ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.

9. Chiudere il riquadro Test.

10. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.

11. Arrestare la macchina virtuale avviata in precedenza.

12. Fare clic su **Avvia** per avviare il runbook. Digitare i valori per `VMName` e `ResourceGroupName` per la macchina virtuale da avviare.

13. Al termine del runbook, verificare che la macchina virtuale sia stata avviata.

## <a name="step-9---create-a-conditional-link"></a>Passaggio 9: Creare un collegamento condizionale

A questo punto sarà possibile modificare il runbook in modo che provi solo ad avviare la macchina virtuale, se non è già avviata. A tale scopo, aggiungere un cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) che recupera lo stato a livello dell'istanza della macchina virtuale. Verrà quindi aggiunto un modulo Codice del flusso di lavoro PowerShell denominato `Get Status` con un frammento di codice PowerShell per determinare se lo stato della macchina virtuale è in esecuzione o arrestato. Un collegamento condizionale dal modulo `Get Status` eseguirà `Start-AzVM` solo se lo stato di esecuzione corrente è arrestato. Al termine di questa procedura, il runbook usa il cmdlet `Write-Output` per restituire un messaggio che indica se la macchina virtuale è stata avviata correttamente.

1. Aprire **MyFirstRunbook-Graphical** (MyFirstRunbook-Grafico) nell'editor grafico.

2. Rimuovere il collegamento tra `Specify Subscription Id` e `Start-AzVM` facendo clic su di esso e quindi fare clic su **Elimina**.

3. Nel controllo Libreria digitare `Get-Az` nel campo di ricerca.

4. Aggiungere `Get-AzVM` all'area di disegno.

5. Selezionare `Get-AzVM` e quindi fare clic su **Set di parametri** per visualizzare i set per il cmdlet.

6. Selezionare il set di parametri **GetVirtualMachineInResourceGroupNameParamSet** . Accanto ai campi **ResourceGroupName** e **Nome** sono visualizzati punti esclamativi per indicare che sono parametri obbligatori. Si noti che per entrambi i campi sono previsti valori stringa.

7. In **Origine dati** per **Nome** selezionare **Input runbook** e quindi **VMName**. Fare clic su **OK**.

8. In **Origine dati** per **ResourceGroupName** selezionare **Input runbook** e quindi **ResourceGroupName**. Fare clic su **OK**.

9. In **Origine dati** per **Stato** selezionare **Valore costante** e quindi **True**. Fare clic su **OK**.

10. Creare un collegamento da `Specify Subscription Id` a `Get-AzVM`.

11. Nel controllo Libreria espandere **Controllo Runbook** e aggiungere **Codice** all'area di disegno.  

12. Creare un collegamento da `Get-AzVM` a `Code`.  

13. Fare clic su `Code` e nel pannello Configurazione modificare l'etichetta impostandola su **Get Status** (Recupera stato).

14. Selezionare `Code` per visualizzare la pagina Editor di codice.  

15. Incollare il frammento di codice seguente nella pagina dell'editor.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Creare un collegamento da `Get Status` a `Start-AzVM`.

    ![Runbook con il modulo Code](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selezionare il collegamento e nel pannello Configurazione impostare **Applica condizione** su **Sì**. Si noti che il collegamento diventa una linea tratteggiata per indicare che l'attività di destinazione verrà eseguita solo se la condizione restituisce true.  

18. Per **Espressione condizione** digitare `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` viene eseguito solo se la macchina virtuale è arrestata.

19. Nel controllo Libreria, espandere **Cmdlets** e quindi **Microsoft.PowerShell.Utility**.

20. Aggiungere `Write-Output` all'area di disegno due volte.

21. Per il primo controllo `Write-Output` fare clic su **Parametri** e modificare il valore di **Etichetta** impostandolo su **Notify VM Started**.

22. Per **InputObject** modificare il campo **Origine dati** impostandolo su **Espressione PowerShell** e digitare l'espressione `$VMName successfully started.`.

23. Nel secondo controllo `Write-Output` fare clic su **Parametri** e modificare il valore di **Etichetta** impostandolo su **Notify VM Start Failed**.

24. Per **InputObject** modificare il campo **Origine dati** impostandolo su **Espressione PowerShell** e digitare l'espressione `$VMName could not start`.

25. Creare collegamenti da `Start-AzVM` a `Notify VM Started` e `Notify VM Start Failed`.

26. Selezionare il collegamento a `Notify VM Started` e impostare **Applica condizione** su True.

27. Per **Espressione condizione** digitare `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Questo controllo `Write-Output` ora viene eseguito solo se la macchina virtuale viene avviata correttamente.

28. Selezionare il collegamento a `Notify VM Start Failed` e impostare **Applica condizione** su True.

29. Per il campo **Espressione condizione** digitare `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Questo controllo `Write-Output` ora viene eseguito solo se la macchina virtuale non è stata avviata correttamente. L'aspetto del runbook sarà simile all'immagine seguente.

    ![Runbook con Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Salvare il runbook e aprire il riquadro Test.

31. Avviare il runbook con la macchina virtuale arrestata, che dovrebbe essere avviata.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creare un runbook grafico in Automazione di Azure](../automation-graphical-authoring-intro.md).
* Per iniziare a usare i runbook PowerShell, vedere [Creare un runbook PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Creare un runbook del flusso di lavoro PowerShell](automation-tutorial-runbook-textual.md).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation).
