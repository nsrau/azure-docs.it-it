---
title: Parametri di input del runbook| Microsoft Docs
description: I parametri di input dei runbook ne aumentano la flessibilità perché consentono di passare dati a un runbook al momento dell'avvio. Questo articolo descrive vari scenari in cui i parametri di input vengono usati nei runbook.
services: automation
documentationcenter: ''
author: MGoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sngun

---
# <a name="runbook-input-parameters"></a>Parametri di input dei runbook
I parametri di input dei runbook ne aumentano la flessibilità perché consentono di passare dati a un runbook al momento dell'avvio. I parametri consentono di eseguire azioni runbook mirate per ambienti e scenari specifici. Questo articolo illustra vari scenari in cui i parametri di input vengono usati nei runbook.

## <a name="configure-input-parameters"></a>Configurare i parametri di input
È possibile configurare i parametri di input nei runbook grafici e in quelli di PowerShell e del flusso di lavoro PowerShell. Un runbook può avere più parametri con tipi di dati diversi oppure non avere parametri. I parametri di input possono essere obbligatori o facoltativi ed è possibile assegnare un valore predefinito per i parametri facoltativi. È possibile assegnare valori ai parametri di input per un runbook quando viene avviato tramite uno dei metodi disponibili. Questi metodi includono l'avvio di un runbook dal portale o da un servizio Web. È inoltre possibile avviare un runbook come figlio, definito inline in un altro runbook.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>Configurare i parametri di input nei runbook di PowerShell e nei runbook del flusso di lavoro PowerShell
I [runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md) e i runbook di PowerShell in Automazione di Azure supportano parametri di input definiti tramite gli attributi seguenti.  

| **Proprietà** | **Descrizione** |
|:--- |:--- |
| Type |Obbligatoria. Tipo di dati previsto per il valore del parametro. Qualsiasi tipo .NET è valido. |
| Nome |Obbligatoria. Nome del parametro. Questo valore deve essere univoco all'interno del runbook e può contenere solo lettere, numeri o caratteri di sottolineatura. Deve iniziare con una lettera. |
| Obbligatorio |Facoltativa. Specifica se è necessario specificare un valore per il parametro. Se la proprietà è impostata su **$true**, è necessario specificare un valore quando viene avviato il runbook. Se la proprietà è impostata su **$false**, il valore è facoltativo. |
| Default value |Facoltativa.  Specifica un valore che verrà usato per il parametro se non viene passato un valore all'avvio del runbook. È possibile impostare un valore predefinito per qualsiasi parametro, rendendolo automaticamente facoltativo indipendentemente dall'impostazione della proprietà Mandatory. |

Windows PowerShell supporta più attributi dei parametri di input di quelli elencati di seguito, ad esempio la convalida, gli alias e i set di parametri. Tuttavia, Automazione di Azure attualmente supporta solo i parametri di input sopra elencati.

Una definizione di parametri nei runbook del flusso di lavoro PowerShell ha il formato generale seguente, in cui più parametri sono separati da virgole.

   ```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     )
   ```

> [!NOTE]
> Quando si definiscono i parametri, se non si specifica l'attributo **Mandatory** , il parametro è considerato facoltativo per impostazione predefinita. Se si imposta un valore predefinito per un parametro nei runbook del flusso di lavoro PowerShell, il parametro verrà considerato come facoltativo da PowerShell, indipendentemente dal valore dell'attributo **Mandatory**.
> 
> 

Come esempio, di seguito vengono configurati i parametri di input per un runbook del flusso di lavoro PowerShell che restituisce informazioni dettagliate su macchine virtuali, che si tratti di una singola VM o di tutte le VM all'interno di un gruppo di risorse. Questo runbook ha due parametri, come illustrato nella schermata seguente: il nome della macchina virtuale e il nome del gruppo di risorse.

![Flusso di lavoro PowerShell di Automazione](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In questa definizione di parametro i parametri **$VMName** e **$resourceGroupName** sono semplici parametri di tipo stringa. I runbook di PowerShell e del flusso di lavoro PowerShell supportano tuttavia tutti i tipi semplici e complessi, ad esempio **object** o **PSCredential** per i parametri di input.

Se il runbook include un parametro di input di tipo object, per passare un valore è necessario usare una tabella hash di PowerShell con coppie (nome/valore). Ad esempio, se si ha questo parametro in un runbook:

     [Parameter (Mandatory = $true)]
     [object] $FullName

è possibile passare il valore seguente al parametro:

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurare i parametri di input in runbook grafici
Per [configurare un runbook grafico](automation-first-runbook-graphical.md) con parametri di input, verrà creato un runbook grafico che restituisce informazioni dettagliate su macchine virtuali, che si tratti di una singola macchina virtuale o di tutte le macchine virtuali all'interno di un gruppo di risorse. La configurazione di un runbook è costituita da due attività principali, come descritto di seguito.

[**Autenticare runbook con account RunAs di Azure**](automation-sec-configure-azure-runas-account.md) per l'autenticazione con Azure.

[**Get-AzureRmVm**](https://msdn.microsoft.com/library/mt603718.aspx) per leggere le proprietà di una macchina virtuale.

È possibile usare l'attività [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) per restituire i nomi delle macchine virtuali. L'attività **Get-AzureRmVm** accetta due parametri: il **nome della macchina virtuale** and the **nome del gruppo di risorse**. Dal momento che questi parametri potrebbero richiedere valori diversi ogni volta che si avvia il runbook, è possibile aggiungere parametri di input al runbook. Per aggiungere parametri di input, seguire questa procedura:

1. Selezionare il runbook grafico nel pannello **Runbook** e fare clic su [**Modifica**](automation-graphical-authoring-intro.md).
2. Nell'editor dei runbook fare clic su **Input e output** per aprire il pannello **Input e output**.
   
    ![Runbook grafico di Automazione](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. Il pannello **Input e output** mostra un elenco di parametri di input definiti per il runbook. In questo pannello è possibile aggiungere un nuovo parametro di input o modificare la configurazione di un parametro di input esistente. Per aggiungere un nuovo parametro per il runbook, fare clic su **Aggiungi input** per aprire il pannello **Parametro di input runbook**, in cui è possibile configurare i parametri seguenti:
   
   | **Proprietà** | **Descrizione** |
   |:--- |:--- |
   | Nome |Obbligatoria.  Nome del parametro. Questo valore deve essere univoco all'interno del runbook e può contenere solo lettere, numeri o caratteri di sottolineatura. Deve iniziare con una lettera. |
   | Descrizione |Facoltativa. Descrizione dello scopo del parametro di input. |
   | Type |Facoltativa. Tipo di dati previsto per il valore del parametro. I tipi di parametro supportati sono **String**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** e **Object**. Se non è selezionato un tipo di dati, l'impostazione predefinita è **String**. |
   | Obbligatorio |Facoltativa. Specifica se è necessario specificare un valore per il parametro. Se si sceglie **Sì**, è necessario specificare un valore quando viene avviato il runbook. Se si sceglie **No**, non è necessario specificare un valore all'avvio del runbook ed è possibile impostare un valore predefinito. |
   | Default value |Facoltativa. Specifica un valore che verrà usato per il parametro se non viene passato un valore all'avvio del runbook. È possibile impostare un valore predefinito per un parametro non obbligatorio. Per impostare un valore predefinito, scegliere **Personalizzato**. Questo valore viene usato a meno che non venga specificato un altro valore all'avvio del runbook. Scegliere **Nessuno** se non si vuole specificare alcun valore predefinito. |
   
    ![Aggiunta di nuovo input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Creare due parametri con le proprietà seguenti che verranno usate dall'attività **Get-AzureRmVm**:
   
   * **Parametro 1:**
     
     * Nome: VMName
     * Tipo: String
     * Obbligatorio: no
   * **Parametro2:**
     
     * Nome: resourceGroupName
     * Tipo: String
     * Obbligatorio: no
     * Valore predefinito: personalizzato
     * Valore predefinito personalizzato: \< nome del gruppo di risorse che contiene le macchine virtuali>.
5. Dopo aver aggiunto i parametri, fare clic su **OK**.  Ora è possibile visualizzarli nel pannello **Input e output**. Fare nuovamente clic su **OK**, quindi su **Salva** e **pubblicare** il runbook.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Assegnare valori ai parametri di input nei runbook
È possibile passare valori ai parametri di input nei runbook negli scenari illustrati di seguito.

### <a name="start-a-runbook-and-assign-parameters"></a>Avviare un runbook e assegnare parametri
Ci sono diversi modi per avviare un runbook: dal portale di Azure, con un webhook, con i cmdlet di PowerShell, con l'API REST o con l'SDK. Di seguito vengono illustrati vari metodi per avviare un runbook e assegnare parametri.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Avviare un runbook pubblicato usando il portale di Azure e assegnare parametri
Quando si [avvia il runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal), viene aperto il pannello **Avvia runbook** ed è possibile configurare i valori per i parametri appena creati.

![Attività iniziali con il portale](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Nell'etichetta sotto la casella di input è possibile visualizzare gli attributi impostati per il parametro. Gli attributi includono obbligatorio o facoltativo, tipo e valore predefinito. Nel fumetto della Guida accanto al nome del parametro sono disponibili tutte le informazioni chiave necessarie per scegliere i valori dei parametri di input, Queste informazioni indicano se un parametro è obbligatorio o facoltativo. Includono inoltre il tipo e il valore predefinito (se presente) e altre note utili.

![Fumetto della Guida](media/automation-runbook-input-parameters/automation-05-helpbaloon.png)

> [!NOTE]
> I parametri di tipo String supportano valori di stringa **vuoti** .  Immettendo **[EmptyString]** nella casella del parametro di input viene passata una stringa vuota al parametro. I parametri di tipo String non supportano il passaggio di valori **Null** . Se non viene passato alcun valore al parametro String, PowerShell lo interpreterà come Null.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>Avviare un runbook pubblicato usando i cmdlet di PowerShell e assegnare parametri
* **Cmdlet di Azure Resource Manager:** è possibile avviare un runbook di Automazione creato in un gruppo di risorse usando [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx).
  
  **Esempio:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Cmdlet di Gestione dei servizi di Azure:** è possibile avviare un runbook di automazione creato in un gruppo di risorse predefinito usando [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx).
  
  **Esempio:**
  
  ```
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> Quando si avvia un runbook con i cmdlet di PowerShell viene creato un parametro predefinito, **MicrosoftApplicationManagementStartedBy**, con il valore **PowerShell**. Questo parametro può essere visualizzato nel pannello **Dettagli processo** .  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>Avviare un runbook usando un SDK e assegnare parametri
* **Metodo di Azure Resource Manager:** è possibile avviare un runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  
  
  ```
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Metodo di gestione del servizio Azure:** è possibile avviare un runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).
  
  ```      
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  Per avviare questo metodo, creare un dizionario per archiviare i parametri del runbook, **VMName** e **resourceGroupName**, e i relativi valori. Avviare quindi il runbook. Di seguito è riportato un frammento di codice C# per chiamare il metodo definito in precedenza.
  
  ```
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>Avviare un runbook usando l'API REST e assegnare parametri
È possibile creare e avviare un processo del runbook con l'API REST di Automazione di Azure usando il metodo **PUT** con l'URI della richiesta seguente.

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`

Nell'URI della richiesta sostituire i parametri seguenti:

* **subscription-id:** ID della sottoscrizione di Azure.  
* **cloud-service-name:** nome del servizio cloud a cui deve essere inviata la richiesta.  
* **automation-account-name:** nome dell'account di Automazione ospitato nel servizio cloud specificato.  
* **job-id:** GUID del processo. Per creare GUID in PowerShell è possibile usare il comando **[GUID]::NewGuid().ToString()** .

Per passare parametri al processo del runbook, usare il corpo della richiesta. Vengono usate le due proprietà seguenti, specificate in formato JSON:

* **Nome del runbook:** obbligatorio. Nome del runbook per il processo da avviare.  
* **Parametri del runbook:** facoltativi. Dizionario dell'elenco dei parametri in formato (nome, valore) in cui il nome deve essere di tipo String e il valore può essere qualsiasi valore JSON valido.

Per avviare il runbook **Get-AzureVMTextual** creato in precedenza con **VMName** e **resourceGroupName** come parametri, usare il formato JSON seguente per il corpo della richiesta.

   ```
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

Se il processo è stato creato, viene restituito un codice di stato HTTP 201. Per altre informazioni su intestazioni di risposta e sul corpo della risposta, vedere l'articolo relativo alla [creazione di un processo del runbook tramite l'API REST.](https://msdn.microsoft.com/library/azure/mt163849.aspx)

### <a name="test-a-runbook-and-assign-parameters"></a>Eseguire il test di un runbook e assegnare parametri
Quando si esegue il [test della versione bozza del runbook](automation-testing-runbook.md) con l'opzione di test, viene visualizzato il pannello **Test** in cui è possibile configurare i valori per i parametri appena creati.

![Esecuzione di test e assegnazione di parametri](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Collegare una pianificazione a un runbook e assegnare parametri
È possibile [collegare una pianificazione](automation-schedules.md) al runbook in modo che venga avviato in un momento specifico. Assegnare i parametri di input durante la creazione della pianificazione. Il runbook usa questi valori quando viene avviato dalla pianificazione. Fino a quando non vengono forniti tutti i valori dei parametri obbligatori non è possibile salvare la pianificazione.

![Pianificazione e assegnazione di parametri](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Creare un webhook per un runbook e assegnare parametri
È possibile creare un [webhook](automation-webhooks.md) per il runbook e configurare i parametri di input del runbook. Fino a quando non vengono forniti tutti i valori dei parametri obbligatori non è possibile salvare il webhook.

![Creazione di webhook e assegnazione di parametri](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando si esegue un runbook con un webhook, insieme ai parametri di input definiti viene inviato il parametro di input predefinito **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** . È possibile fare clic per espandere il parametro **WebhookData** e visualizzarne i dettagli.

![Parametro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su input e output dei runbook, vedere l'articolo [Azure Automation: runbook input, output, and nested runbooks](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)(Automazione di Azure: input e output del runbook e runbook nidificati).
* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per modificare un runbook testuale, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Per modificare un runbook grafico, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

<!--HONumber=Oct16_HO2-->


