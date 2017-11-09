---
title: Il primo runbook grafico in Automazione di Azure | Documentazione Microsoft
description: Esercitazione che illustra la creazione, i test e la pubblicazione di un semplice runbook grafico .
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: runbook, modello di runbook, automazione runbook, runbook di Azure
ms.assetid: dcb88f19-ed2b-4372-9724-6625cd287c8a
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/17/2017
ms.author: magoedte;bwren
ms.openlocfilehash: b8accf16c43b7a01233742161e68db6f831a2d30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="my-first-graphical-runbook"></a>Il primo runbook grafico

> [!div class="op_single_selector"]
> * [Grafico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Flusso di lavoro PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Questa esercitazione illustra la creazione di un [runbook grafico](automation-runbook-types.md#graphical-runbooks) in Automazione di Azure.  Si inizia con un runbook semplice che esegue test e pubblicazioni, quindi viene illustrato come tenere traccia dello stato del processo del runbook.  Si modifica quindi il runbook per gestire effettivamente le risorse di Azure, avviando in questo caso una macchina virtuale di Azure.  Si completerà quindi l'esercitazione rendendo il runbook più affidabile con l'aggiunta di parametri e collegamenti condizionali.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.

## <a name="step-1---create-runbook"></a>Passaggio 1: Creare un runbook
Si inizia creando un runbook semplice che restituisce il testo *Hello World*.

1. Nel portale di Azure aprire l'account di automazione.  
   La pagina dell'account di automazione offre una visualizzazione rapida delle risorse di questo account.  Dovrebbero essere già disponibili alcuni asset.  Per la maggior parte sono i moduli inclusi automaticamente in un nuovo account di automazione.  Dovrebbe essere disponibile anche l'asset credenziali citato nei [prerequisiti](#prerequisites).
2. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.<br> ![Controllo Runbook](media/automation-first-runbook-graphical/runbooks-resources-tile.png)
3. Creare un nuovo runbook facendo clic sul pulsante **Aggiungi Runbook** e quindi su **Crea un nuovo runbook**.
4. Denominare il runbook *MyFirstRunbook-Graphical*.
5. In questo caso verrà creato un [runbook grafico](automation-graphical-authoring-intro.md), quindi selezionare **Grafico** in **Tipo di runbook**.<br> ![Nuovo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Fare clic su **Crea** per creare il runbook e aprire l'editor grafico.

## <a name="step-2---add-activities-to-the-runbook"></a>Passaggio 2: Aggiungere attività al runbook
Il controllo Libreria sul lato sinistro dell'editor consente di selezionare le attività da aggiungere al runbook.  Si aggiungerà un cmdlet **Write-Output** per l'output del testo dal runbook.

1. Nel controllo Libreria fare clic nella casella di testo di ricerca e digitare **Write-Output**.  I risultati della ricerca verranno visualizzati sotto. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2. Scorrere fino alla fine dell'elenco.  È possibile fare clic con il pulsante destro del mouse su **Write-Output** e scegliere **Aggiungi ad area di disegno** o fare clic sui puntini di sospensione accanto al cmdlet e quindi selezionare **Aggiungi ad area di disegno**.
3. Fare clic sull'attività **Write-Output** nell'area di disegno.  Si aprirà il pannello Controllo configurazione che consente di configurare l'attività.
4. In **Etichetta** è impostato il nome predefinito del cmdlet, ma è possibile modificarlo specificando un nome più descrittivo. Cambiarlo in *Scrivere Hello World nell'output*.
5. Fare clic su **Parametri** per specificare i valori per i parametri del cmdlet.  
   Alcuni cmdlet hanno più set di parametri ed è necessario selezionarne uno da usare. In questo caso, **Write-Output** ha un solo set di parametri, quindi non è necessario selezionarne uno. <br> ![Proprietà Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6. Selezionare il parametro **InputObject** .  Si tratta del parametro in cui si specificherà il testo da inviare al flusso di output.
7. Nell'elenco a discesa **Origine dati** selezionare **Espressione PowerShell**.  L'elenco a discesa **Origine dati** include diverse origini che è possibile usare per popolare il valore di un parametro.  
   È possibile usare l'output da tali origini, ad esempio un'altra attività, un asset di automazione o un'espressione di PowerShell.  In questo caso, si vuole solo generare il testo *Hello World*. È possibile usare un'espressione di PowerShell e specificare una stringa.
8. Nella casella **Espressione** digitare *"Hello World"* e quindi fare clic su **OK** due volte per tornare all'area di disegno.<br> ![PowerShell Expression](media/automation-first-runbook-graphical/expression-hello-world.png)
9. Salvare il runbook facendo clic su **Salva**.<br> ![Salvataggio del runbook](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## <a name="step-3---test-the-runbook"></a>Passaggio 3: Testare il runbook
Prima di pubblicare il runbook per renderlo disponibile nell'ambiente di produzione, occorre testarlo per verificare che funzioni correttamente.  Quando si testa un runbook, è necessario eseguire la versione **Bozza** e visualizzarne l'output in modo interattivo.

1. Fare clic su **Riquadro di test** per aprire il pannello Test.<br> ![Riquadro Test](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2. Fare clic su **Avvia** per avviare il test.  Questa deve essere l'unica opzione abilitata.
3. Viene creato un [processo del runbook](automation-runbook-execution.md) e il relativo stato viene visualizzato nel riquadro.  
   Lo stato iniziale del processo è *In coda* per indicare che è in attesa della disponibilità di un ruolo di lavoro per runbook nel cloud.  Lo stato passa quindi ad *Avvio in corso* quando un ruolo di lavoro richiede il processo e quindi a *In esecuzione* quando l'esecuzione del runbook viene effettivamente avviata.  
4. Al termine del processo del runbook, viene visualizzato l'output. In questo caso, dovrebbe essere visualizzato *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5. Chiudere il pannello Test per tornare all'area di disegno.

## <a name="step-4---publish-and-start-the-runbook"></a>Passaggio 4: Pubblicare e avviare il runbook
Il runbook creato è ancora in modalità Bozza. È necessario pubblicarlo prima di poterlo eseguire in produzione.  Quando si pubblica un runbook, è possibile sovrascrivere la versione pubblicata esistente con la versione bozza.  In questo caso, non esiste ancora una versione pubblicata perché il runbook è appena stato creato.

1. Fare clic su **Pubblica** per pubblicare il runbook, quindi su **Sì** quando richiesto.<br> ![Pubblica](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2. Se si scorre verso sinistra per visualizzare il runbook nel pannello **Runbook**, come **Stato di creazione** viene visualizzato **Pubblicato**.
3. Scorrere verso destra per visualizzare il pannello **MyFirstRunbook**.  
   Le opzioni nella parte superiore consentono di avviare il runbook, pianificarlo per avviarlo in qualsiasi momento in futuro o creare un [webhook](automation-webhooks.md) per poterlo avviare con una chiamata HTTP.
4. Si vuole solo avviare il runbook, quindi fare clic su **Avvia** e poi su **Sì** quando richiesto.<br> ![Avvia runbook](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5. Viene aperto un pannello per il processo del runbook creato.  È possibile chiudere questo pannello, ma in questo caso lo si lascerà aperto per poter controllare lo stato del processo.
6. Lo stato del processo è visualizzato in **Riepilogo processi** e corrisponde agli stati osservati quando è stato testato il runbook.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/runbook-job-summary.png)
7. Quando lo stato del runbook risulta *Completato*fare clic su **Output**. Viene aperto il pannello **Output** dove si può vedere il testo *Hello World* nel riquadro.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/runbook-job-output.png)  
8. Chiudere il pannello Output.
9. Fare clic su **Tutti i log** per aprire il pannello Flussi relativo al processo del runbook.  Nel flusso di output dovrebbe essere visibile solo *Hello World* , ma potrebbero essere visualizzati altri flussi per un processo del runbook, ad esempio Verbose ed Error se il runbook scrive in questi flussi.<br> ![Riepilogo dei processi](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Chiudere il pannello Tutti i log e il pannello Processo per tornare al pannello MyFirstRunbook.
11. Fare clic su **Processi** per aprire il pannello Processi per questo runbook.  Sono elencati tutti i processi creati da questo runbook. Dovrebbe essere elencato un solo processo, perché il processo è stato eseguito una sola volta.<br> ![Processi](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. È possibile fare clic su questo processo per aprire lo stesso pannello visualizzato quando è stato avviato il runbook.  In questo modo è possibile tornare indietro nel tempo e visualizzare i dettagli di tutti i processi creati per un runbook particolare.

## <a name="step-5---create-variable-assets"></a>Passaggio 5: Creare asset di variabile
Il runbook è stato testato e pubblicato, ma finora non esegue alcuna attività utile. Si vuole fare in modo che gestisca le risorse di Azure.  Prima di configurare il runbook per l'autenticazione verrà creata una variabile che conterrà l'ID sottoscrizione e alla quale si farà riferimento dopo aver configurato l'attività per l'autenticazione più avanti nel passaggio 6.  L'inserimento di un riferimento al contesto della sottoscrizione consente di usare facilmente più sottoscrizioni contemporaneamente.  Prima di procedere, copiare l'ID sottoscrizione dall'opzione Sottoscrizioni del riquadro di spostamento.  

1. Nel pannello Account di automazione fare clic sul riquadro **Asset** per aprire il pannello **Asset**.
2. Nel pannello Asset fare clic sul riquadro **Variabili**.
3. Nel pannello Variabili fare clic su **Aggiungi variabile**.<br>![Variabile di Automazione](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Nella casella **Nome** del pannello Nuova variabile immettere **AzureSubscriptionId** e nella casella **Valore** immettere l'ID sottoscrizione.  Lasciare *Stringa* in **Tipo** e il valore predefinito in **Crittografia**.  
5. Fare clic su **Crea** per creare la variabile.  

## <a name="step-6---add-authentication-to-manage-azure-resources"></a>Passaggio 6: Aggiungere l'autenticazione per gestire le risorse di Azure
Dopo avere creato una variabile che contiene l'ID sottoscrizione, è possibile configurare il runbook per l'autenticazione con le credenziali Esegui come a cui si fa riferimento nei [prerequisiti](#prerequisites).  Questa operazione viene eseguita aggiungendo il cmdlet **Asset** e **Add-AzureRMAccount** della connessione RunAs di Azure all'area di disegno.  

1. Aprire l'editor grafico facendo clic su **Modifica** nel pannello MyFirstRunbook.<br> ![Modificare il runbook](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2. **Scrivere Hello World nell'output** non è più necessario, quindi fare clic con il pulsante destro del mouse sul testo e scegliere **Elimina**.
3. Nel controllo Libreria espandere **Connessioni** e aggiungere **AzureRunAsConnection** all'area di disegno selezionando **Aggiungi ad area di disegno**.
4. Nell'area di disegno selezionare **AzureRunAsConnection** e nel riquadro del controllo Configurazione digitare **Get Run As Connection** nella casella di testo **Etichetta**.  Si tratta della connessione
5. Nel controllo Libreria digitare **Add-AzureRmAccount** nella casella di testo di ricerca.
6. Aggiungere **Aggiungere-AzureRmAccount** all'area di disegno.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7. Passare il puntatore su **Get Run As Connection** finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su **Add-AzureRmAccount**.  La freccia creata è un *collegamento*.  Il runbook verrà avviato con **Get Run As Connection** (Ottieni connessione RunAs) e quindi eseguirà **Add-AzureRmAccount**.<br> ![Creare un collegamento tra le attività](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8. Nell'area di disegno selezionare **Add-AzureRmAccount** e nel riquadro del controllo Configurazione digitare **Login to Azure** nella casella di testo **Etichetta**.
9. Fare clic su **Parametri** per visualizzare il pannello Configurazione parametro attività.
10. **Add-AzureRmAccount** include più set di parametri, quindi è necessario selezionarne uno prima di poterne indicare i valori.  Fare clic su **Set di parametri** e quindi selezionare il set di parametri **ServicePrincipalCertificatewithSubscriptionId**.
11. Dopo aver selezionato il set di parametri, nel pannello Configurazione parametro attività vengono visualizzati i parametri.  Fare clic su **APPLICATIONID**.<br> ![Aggiungere parametri all'account Azure RM](media/automation-first-runbook-graphical/add-azurermaccount-params.png)
12. Nel pannello Valore parametro selezionare **Output attività** per **Origine dati** e quindi selezionare **Get Run As Connection** dall'elenco, nella casella di testo **Percorso campo** digitare **ApplicationId** e quindi fare clic su **OK**.  Viene specificato il nome della proprietà per il percorso del campo perché l'attività restituisce un oggetto con più proprietà.
13. Fare clic su **CERTIFICATETHUMBPRINT** e nel pannello Valore parametro selezionare **Output attività** per **Origine dati**.  Selezionare **Get Run As Connection** dall'elenco, digitare **CertificateThumbprint** nella casella di testo **Percorso campo** e quindi fare clic su **OK**.
14. Fare clic su **SERVICEPRINCIPAL** e nel pannello Valore parametro selezionare **ConstantValue** per **Origine dati**, fare clic sull'opzione **True** e quindi fare clic su **OK**.
15. Fare clic su **TENANTID** e nel pannello Valore parametro selezionare **Output attività** per **Origine dati**.  Selezionare **Get Run As Connection** dall'elenco, digitare **TenantId** nella casella di testo **Percorso campo** e quindi fare clic su **OK** due volte.  
16. Nel controllo Libreria digitare **Set-AzureRmContext** nella casella di testo di ricerca.
17. Aggiungere **Set-AzureRmContext** all'area di disegno.
18. Nell'area di disegno selezionare **Set-AzureRmContext** e nel riquadro del controllo Configurazione digitare **Specify Subscription Id** nella casella di testo **Etichetta**.
19. Fare clic su **Parametri** per visualizzare il pannello Configurazione parametro attività.
20. **Set-AzureRmContext** include più set di parametri, quindi è necessario selezionarne uno prima di poterne indicare i valori.  Fare clic su **Set di parametri** e quindi selezionare il set di parametri **SubscriptionId**.  
21. Dopo aver selezionato il set di parametri, nel pannello Configurazione parametro attività vengono visualizzati i parametri.  Fare clic su **SubscriptionID**
22. Nel pannello Valore parametro selezionare **Asset variabile** per **Origine dati**, selezionare **AzureSubscriptionId** dall'elenco e quindi fare clic su **OK** due volte.   
23. Passare il puntatore su **Login to Azure** finché non viene visualizzato un cerchio nella parte inferiore della forma. Fare clic sul cerchio e trascinare la freccia su **Specify Subscription Id**.

A questo punto l'aspetto del runbook sarà simile al seguente:  <br>![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passaggio 7: Aggiungere l'attività per avviare una macchina virtuale
Qui viene aggiunta un'attività **Start-AzureRmVM** per avviare una macchina virtuale.  È possibile selezionare una macchina virtuale qualsiasi nella sottoscrizione di Azure. Per ora il nome sarà hardcoded nel cmdlet.

1. Nel controllo Libreria digitare **Start-AzureRm** nella casella di testo di ricerca.
2. Aggiungere **Start-AzureRmVM** all'area di disegno e quindi trascinarla sotto **Specify Subscription Id**.
3. Passare il puntatore su **Specify Subscription Id** finché non viene visualizzato un cerchio nella parte inferiore della forma.  Fare clic sul cerchio e trascinare la freccia su **Start-AzureRmVM**.
4. Selezionare **Start-AzureRmVM**.  Fare clic su **Parametri** e quindi su **Set di parametri** per visualizzare i set per **Start-AzureRmVM**.  Selezionare il set di parametri **ResourceGroupNameParameterSetName** . Si noti che accanto a **ResourceGroupName** e **Name** sono presenti punti esclamativi.  Indicano che si tratta di parametri obbligatori.  Si noti anche che i valori previsti per entrambi sono stringhe.
5. Selezionare **Name**.  Selezionare **Espressione PowerShell** in **Origine dati** e digitare il nome della macchina virtuale, racchiuso tra virgolette doppie, che viene avviata con questo runbook.  Fare clic su **OK**.<br>![Valore del parametro Name di Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6. Selezionare **ResourceGroupName**. Usare **Espressione PowerShell** in **Origine dati** e digitare il nome del gruppo di risorse racchiuso tra virgolette doppie.  Fare clic su **OK**.<br> ![Parametri di Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
7. Fare clic sul riquadro Test in modo da testare il runbook.
8. Fare clic su **Avvia** per avviare il test.  Dopo aver completato l'attività, controllare che la macchina virtuale sia stata avviata.

A questo punto l'aspetto del runbook sarà simile al seguente:  <br>![Configurazione dell'autenticazione runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters-to-the-runbook"></a>Passaggio 8: Aggiungere altri parametri di input al runbook
Il runbook avvia attualmente la macchina virtuale nel gruppo di risorse specificato nel cmdlet **Start-AzureRmVM**.  Il runbook sarebbe più utile se si potesse specificarli entrambi quando viene avviato.  Per fornire questa funzionalità, si aggiungeranno ora dei parametri di input al runbook.

1. Aprire l'editor grafico facendo clic su **Modifica** nel pannello **MyFirstRunbook**.
2. Fare clic su **Input e output** e quindi su **Aggiungi input** per aprire il riquadro Parametro di input runbook.<br> ![Input e output del runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Specificare *VMName* per **Nome**.  Mantenere *stringa* come **Tipo**, ma cambiare **Obbligatorio** in *Sì*.  Fare clic su **OK**.
4. Creare un secondo parametro di input obbligatorio denominato *ResourceGroupName* e quindi fare clic su **OK**per chiudere il riquadro **Input e output**.<br> ![Parametri di input del runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Selezionare l'attività **Start-AzureRmVM** e quindi fare clic su **Parametri**.
6. Cambiare **Origine dati** per **Nome** in **Input runbook** e quindi selezionare **VMName**.<br>
7. Cambiare **Origine dati** per **ResourceGroupName** in **Input runbook** e quindi selezionare **ResourceGroupName**.<br> ![Parametri Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Salvare il runbook e aprire il riquadro Test.  Si noti che ora è possibile fornire i valori per le due variabili di input che verranno usate nel test.
9. Chiudere il riquadro Test.
10. Fare clic su **Pubblica** per pubblicare la nuova versione del runbook.
11. Arrestare la macchina virtuale avviata nel passaggio precedente.
12. Fare clic su **Avvia** per avviare il runbook.  Digitare **VMName** e **ResourceGroupName** per la macchina virtuale da avviare.<br> ![Start Runbook](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Quando il runbook viene completato, controllare che la macchina virtuale sia stata avviata.

## <a name="step-9---create-a-conditional-link"></a>Passaggio 9: Creare un collegamento condizionale
Ora si modificherà il runbook in modo che provi solo ad avviare la macchina virtuale, se non è già avviata.  Per eseguire questa operazione, aggiungere al runbook un cmdlet **Get-AzureRmVM** che otterrà lo stato della macchina virtuale a livello di istanza. Verrà quindi aggiunto un modulo Codice del flusso di lavoro PowerShell denominato **Get Status** con un frammento di codice PowerShell per determinare se lo stato della macchina virtuale è in esecuzione o arrestato.  Un collegamento condizionale dal modulo **Get Status** eseguirà **Start-AzureRmVM** solo se lo stato di esecuzione corrente è arrestato.  Infine si genererà un messaggio per informare se la VM è stata avviata correttamente o meno usando il cmdlet Write-Output di PowerShell.

1. Aprire **MyFirstRunbook** nell'editor grafico.
2. Rimuovere il collegamento tra **Specify Subscription Id** e **Start-AzureRmVM** facendo clic su di esso e quindi premendo *CANC*.
3. Nel controllo Libreria digitare **Get-AzureRm** nella casella di testo di ricerca.
4. Aggiungere **Get-AzureRmVM** all'area di disegno.
5. Selezionare **Get-AzureRmVM** e quindi **Set di parametri** per visualizzare i set per **Get-AzureRmVM**.  Selezionare il set di parametri **GetVirtualMachineInResourceGroupNameParamSet** .  Si noti che accanto a **ResourceGroupName** e **Name** sono presenti punti esclamativi.  Indicano che si tratta di parametri obbligatori.  Si noti anche che i valori previsti per entrambi sono stringhe.
6. In **Origine dati** per **Nome** selezionare **Input runbook** e quindi selezionare **VMName**.  Fare clic su **OK**.
7. In **Origine dati** per **ResourceGroupName** selezionare **Input runbook** e quindi selezionare **ResourceGroupName**.  Fare clic su **OK**.
8. In **Origine dati** per **Stato** selezionare **Valore costante** e quindi fare clic su **True**.  Fare clic su **OK**.  
9. Creare un collegamento da **Specify Subscription Id** a **Get-AzureRmVM**.
10. Nel controllo Libreria espandere **Controllo Runbook** e aggiungere **Codice** all'area di disegno.  
11. Creare un collegamento da **Get-AzureRmVM** a **Codice**.  
12. Fare clic su **Codice** e nel riquadro Configurazione impostare l'etichetta su **Get Status**.
13. Selezionare il parametro **Codice** per visualizzare il pannello **Editor di codice**.  
14. Nell'editor di codice incollare il frammento di codice seguente:
    
     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```
15. Creare un collegamento da **Get Status** a **Start-AzureRmVM**.<br> ![Runbook con il modulo Code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Selezionare il collegamento e nel riquadro Configurazione impostare **Applica condizione** su **Sì**.   Si noti che il collegamento si trasforma in una linea tratteggiata per indicare che l'attività di destinazione verrà eseguita solo se la condizione restituisce true.  
17. Per **Espressione condizione**digitare *$ActivityOutput['Get Status'] -eq "Stopped"*.  **Start-AzureRmVM** verrà ora eseguito solo se la macchina virtuale è arrestata.
18. Nel controllo Libreria, espandere **Cmdlets** e quindi **Microsoft.PowerShell.Utility**.
19. Aggiungere **Write-Output** all'area di disegno due volte.<br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. Nel primo controllo **Write-Output** fare clic su **Parametri** e cambiare il valore di **Etichetta** in *Notify VM Started*.
21. Per **InputObject** cambiare **Origine dati** in **Espressione PowerShell** e digitare l'espressione *"$VMName successfully started."*
22. Nel secondo controllo **Write-Output** fare clic su **Parametri** e cambiare il valore di **Etichetta** in *Notify VM Start Failed*
23. Per **InputObject** cambiare **Origine dati** in **Espressione PowerShell** e digitare l'espressione *"$VMName could not start."*
24. Creare un collegamento da **Start-AzureRmVM** a **Notify VM Started** e **Notify VM Start Failed**.
25. Selezionare il collegamento a **Notify VM Started** e impostare **Applica condizione** su **True**.
26. Per **Espressione condizione**digitare *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  Questo controllo Write-Output verrà ora eseguito solo se la macchina virtuale viene avviata correttamente.
27. Selezionare il collegamento a **Notify VM Start Failed** e impostare **Applica condizione** su **True**.
28. Per **Espressione condizione**digitare *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*.  Questo controllo Write-Output verrà ora eseguito solo se la macchina virtuale non viene avviata correttamente.
29. Salvare il runbook e aprire il riquadro Test.
30. Avviare il runbook con la macchina virtuale arrestata, che dovrebbe essere avviata.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md)
* Per iniziare a usare i runbook PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Per iniziare a usare i runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)

