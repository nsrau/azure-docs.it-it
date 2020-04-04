---
title: Parametri di input dei runbook
description: I parametri di input dei runbook ne aumentano la flessibilità perché consentono di passare dati a un runbook al momento dell'avvio. Questo articolo descrive vari scenari in cui i parametri di input vengono usati nei runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656024"
---
# <a name="runbook-input-parameters"></a>Parametri di input dei runbook

I parametri di input del runbook aumentano la flessibilità di un runbook consentendo il passo dei dati all'avvio. Questi parametri consentono di eseguire azioni del runbook come destinazione per scenari e ambienti specifici. Questo articolo descrive la configurazione e l'uso dei parametri di input nei runbook.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="configuring-input-parameters"></a>Configurazione dei parametri di input

È possibile configurare i parametri di input per i runbook di PowerShell, PowerShell Workflow, grafici e Python.You can configure input parameters for PowerShell, PowerShell Workflow, graphical, and Python runbooks. Un runbook può avere più parametri con tipi di dati diversi oppure non avere parametri. I parametri di input possono essere obbligatori o facoltativi ed è possibile utilizzare valori predefiniti per i parametri facoltativi.

I valori vengono assegnati ai parametri di input per un runbook all'avvio. È possibile avviare un runbook dal portale di Azure, da un servizio Web o da PowerShell.You can start a runbook from the Azure portal, a web service, or PowerShell. È inoltre possibile avviare un runbook come figlio, definito inline in un altro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurare i parametri di input in runbook di PowerShell

I runbook di PowerShell e del flusso di lavoro di PowerShell in Automazione di Azure supportano i parametri di input definiti tramite le proprietà seguenti. 

| **Proprietà** | **Descrizione** |
|:--- |:--- |
| Type |Obbligatorio. Tipo di dati previsto per il valore del parametro. Qualsiasi tipo .NET è valido. |
| Nome |Obbligatorio. Nome del parametro. Questo nome deve essere univoco all'interno del runbook, deve iniziare con una lettera e può contenere solo lettere, numeri o caratteri di sottolineatura. |
| Obbligatorio |Facoltativa. Valore booleano che specifica se il parametro richiede un valore. Se si imposta questa proprietà su True, è necessario specificare un valore all'avvio del runbook. Se si imposta questa opzione su False, un valore è facoltativo. Se non si specifica un `Mandatory` valore per la proprietà, PowerShell considera il parametro di input facoltativo per impostazione predefinita. |
| Valore predefinito |Facoltativa. Valore utilizzato per il parametro se non viene passato alcun valore di input all'avvio del runbook. Il runbook può impostare un valore predefinito per qualsiasi parametro. |

Windows PowerShell supporta più attributi dei parametri di input rispetto a quelli elencati in precedenza, ad esempio convalida, alias e set di parametri. Tuttavia, l'automazione di Azure supporta attualmente solo le proprietà dei parametri di input elencate.

Ad esempio, esaminiamo una definizione di parametro in un runbook del flusso di lavoro di PowerShell.As an example, let's look at a parameter definition in a PowerShell Workflow runbook. Questa definizione ha la seguente forma generale, in cui più parametri sono separati da virgole.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

A questo punto è possibile configurare i parametri di input per un runbook del flusso di lavoro di PowerShell che restituisce dettagli sulle macchine virtuali, una singola macchina virtuale o tutte le macchine virtuali all'interno di un gruppo di risorse. Questo runbook ha due parametri, come illustrato nella schermata`VMName`seguente: il nome della`resourceGroupName`macchina virtuale ( ) e il nome del gruppo di risorse ( ).

![Flusso di lavoro PowerShell di Automazione](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In questa definizione di parametro, i parametri di input sono parametri semplici di tipo stringa.

Si noti che i runbook di PowerShell e del `Object` `PSCredential` flusso di lavoro di PowerShell supportano tutti i tipi semplici e i tipi complessi, ad esempio o per i parametri di input. Se il runbook dispone di un parametro di input dell'oggetto, è necessario usare una tabella hash di PowerShell con coppie nome-valore per passare un valore. Ad esempio, si dispone del parametro seguente in un runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In questo caso, è possibile passare il seguente valore al parametro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando non si passa un valore a un parametro String facoltativo con un valore predefinito null, il valore del parametro è una stringa vuota anziché Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurare i parametri di input in runbook grafici

Per illustrare la configurazione dei parametri di input per un runbook grafico, è possibile creare un runbook che restituisce dettagli sulle macchine virtuali, una singola macchina virtuale o tutte le macchine virtuali all'interno di un gruppo di risorse. Per informazioni dettagliate, consultate [Il mio primo runbook grafico.](automation-first-runbook-graphical.md)

Un runbook grafico utilizza queste principali attività di runbook:A graphical runbook uses these major runbook activities:

* Configurazione dell'account RunAs di Azure per l'autenticazione con Azure.Configuration of the Azure RunAs account to authenticate with Azure. 
* Definizione di un cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) per ottenere le proprietà della macchina virtuale.
* Uso dell'attività [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) per restituire i nomi delle macchine virtuali. 

L'attività `Get-AzVM` definisce due input, il nome della macchina virtuale e il nome del gruppo di risorse. Poiché questi nomi possono essere diversi ogni volta che viene avviato il runbook, è necessario aggiungere parametri di input al runbook per accettare questi input. Fare riferimento a [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

Seguire questi passaggi per configurare i parametri di input.

1. Selezionare il runbook grafico dalla pagina Runbooks e quindi fare clic su **Modifica**.
2. Nell'editor grafico, fare clic sul pulsante **Input e output,** quindi **su Aggiungi input** per aprire il riquadro Parametro di input Runbook.

   ![Runbook grafico di Automazione](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Il controllo Input e Output visualizza un elenco di parametri di input definiti per il runbook. Qui è possibile aggiungere un nuovo parametro di input o modificare la configurazione di un parametro di input esistente. Per aggiungere un nuovo parametro per il runbook, fare clic su Aggiungi input per aprire il pannello dei parametri di **input Runbook,** in cui è possibile configurare i parametri usando le proprietà definite in [Creazione grafica in Automazione di Azure.To](automation-graphical-authoring-intro.md)add a new parameter for the runbook, click Add **input** to open the Runbook input parameter blade, where you can configure parameters using the properties defined in Graphical authoring in Azure Automation.

    ![Aggiunta di nuovo input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Creare due parametri con le seguenti `Get-AzVM` proprietà che devono essere utilizzate dall'attività e quindi fare clic su **OK**.

   * Parametro 1:
        * **Nome** -- **VMNameNameName**
        * **Tipo** -- Stringa
        * **Obbligatorio** -- **No**

   * Parametro 2:
        * **Nome** -- **resourceGroupNameName resourceGroupName**
        * **Tipo** -- Stringa
        * **Obbligatorio** -- **No**
        * **Valore predefinito****Personalizzato**  -- 
        * Valore predefinito personalizzato : nome del gruppo di risorse che contiene le macchine virtualiCustom default value -- Name of the resource group that contains the VMs

5. Visualizzare i parametri nel controllo Input e Output. 
6. Fare di nuovo **clic su OK** e quindi su **Salva**.
7. Fare clic su **Pubblica** per pubblicare il runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurare i parametri di input in runbook di Python

A differenza di PowerShell, del flusso di lavoro di PowerShell e dei runbook grafici, i runbook Python non accettano parametri denominati. L'editor del runbook analizza tutti i parametri di input come una matrice di valori di argomento. È possibile accedere alla `sys` matrice importando il modulo nello `sys.argv` script Python e quindi utilizzando la matrice. È importante notare che il primo `sys.argv[0]`elemento della matrice, , è il nome dello script. Pertanto il primo `sys.argv[1]`parametro di input effettivo è .

Per un esempio su come usare i parametri di input in un runbook di Python, vedere [Il primo runbook Python in Automazione di Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Assegnazione di valori ai parametri di input nei runbookAssigning values to input parameters in runbooks

Questa sezione descrive diversi modi per passare valori ai parametri di input nei runbook. È possibile assegnare valori di parametro quando:

* [Avviare un runbook](#start-a-runbook-and-assign-parameters)
* [Testare un runbook](#test-a-runbook-and-assign-parameters)
* [Collegare una pianificazione per il runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Creare un webhook per il runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Avviare un runbook e assegnare parametri

Un runbook può essere avviato in molti modi: tramite il portale di Azure, con un webhook, con i cmdlet di PowerShell, con l'API REST o con l'SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Avviare un runbook pubblicato usando il portale di Azure e assegnare parametriStart a published runbook using the Azure portal and assign parameters

Quando si [avvia il runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) nel portale di Azure, viene aperto il pannello **Avvia runbook** ed è possibile immettere i valori per i parametri creati.

![Attività iniziali con il portale](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Nell'etichetta sotto la casella di input è possibile visualizzare le proprietà impostate per definire gli attributi dei parametri, ad esempio obbligatori o facoltativi, digitare, il valore predefinito. Il fumetto della Guida accanto al nome del parametro definisce anche le informazioni chiave necessarie per prendere decisioni sui valori di input dei parametri. 

> [!NOTE]
> I parametri stringa supportano valori vuoti di tipo String.String parameters support empty values of type String. Immettendo `[EmptyString]` nella casella del parametro di input, viene passata una stringa vuota al parametro. Inoltre, i parametri di stringa non supportano Null. Se non si passa alcun valore a un parametro di stringa, PowerShell lo interpreta come Null.If you don't pass any value to a string parameter, PowerShell interprets it as Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Avviare un runbook pubblicato utilizzando i cmdlet di PowerShell e assegnare parametriStart a published runbook using PowerShell cmdlets and assign parameters

* Cmdlet di **Azure Resource Manager:** È possibile avviare un runbook di automazione creato in un gruppo di risorse utilizzando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlet del modello di distribuzione classica di Azure:** è possibile avviare un runbook di automazione creato in un gruppo di risorse predefinito usando [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Quando si avvia un runbook utilizzando i cmdlet `MicrosoftApplicationManagementStartedBy`di PowerShell, `PowerShell`viene creato un parametro predefinito, , con il valore . È possibile visualizzare questo parametro nel riquadro Dettagli processo.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Avviare un runbook usando un SDK e assegnare parametriStart a runbook using an SDK and assign parameters

* **Metodo di Azure Resource Manager:Azure Resource Manager method:** È possibile avviare un runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
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

* **Metodo del modello di distribuzione classica di Azure:** è possibile avviare un runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
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

   Per avviare questo metodo, creare un `VMName` dizionario per archiviare i parametri del runbook e `resourceGroupName` i relativi valori. Avviare quindi il runbook. Di seguito è riportato un frammento di codice C# per chiamare il metodo definito in precedenza.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Avviare un runbook usando l'API REST e assegnare parametriStart a runbook using the REST API and assign parameters

È possibile creare e avviare un processo di runbook con l'API REST di Automazione di Azure usando il metodo con l'URI di richiesta seguente:You can create and start a runbook job with the Azure Automation REST API by using the `PUT` method with the following request URI:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Nell'URI della richiesta sostituire i parametri seguenti:

* `subscriptionId`: ID sottoscrizione di Azure.: Your Azure subscription ID.  
* `resourceGroupName`: nome del gruppo di risorse per l'account di automazione.
* `automationAccountName`: il nome dell'account di automazione ospitato all'interno del servizio cloud specificato.  
* `jobName`: GUID per il processo. I GUID in PowerShell possono `[GUID]::NewGuid().ToString()*`essere creati utilizzando .

Per passare parametri al processo del runbook, usare il corpo della richiesta. Accetta le informazioni seguenti, fornite in formato JSON:It takes the following information, provided in JSON format:

* Nome del runbook: obbligatorio. Nome del runbook per il processo da avviare.  
* Parametri del runbook: facoltativi. Dizionario dell'elenco di parametri in formato (nome, valore), dove name è di tipo String e value può essere qualsiasi valore JSON valido.

Se si vuole avviare il runbook **Get-AzureVMTextual** creato in precedenza con `VMName` e `resourceGroupName` come parametri, usare il formato JSON seguente per il corpo della richiesta.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Se il processo è stato creato, viene restituito un codice di stato HTTP 201. Per altre informazioni sulle intestazioni di risposta e sul corpo della risposta, vedere [Creare un processo del runbook usando l'API REST.](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Eseguire il test di un runbook e assegnare parametri

Quando si [testa la versione bozza del runbook](automation-testing-runbook.md) utilizzando l'opzione test, viene visualizzata la pagina Test. Utilizzare questa pagina per configurare i valori per i parametri creati.

![Esecuzione di test e assegnazione di parametri](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Collegare una pianificazione a un runbook e assegnare parametri

È possibile [collegare una pianificazione](automation-schedules.md) al runbook in modo che venga avviato in un momento specifico. Assegnare i parametri di input durante la creazione della pianificazione. Il runbook usa questi valori quando viene avviato dalla pianificazione. Non è possibile salvare la pianificazione finché non vengono forniti tutti i valori dei parametri obbligatori.

![Pianificazione e assegnazione di parametri](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Creare un webhook per un runbook e assegnare parametri

È possibile creare un [webhook](automation-webhooks.md) per il runbook e configurare i parametri di input del runbook. Non è possibile salvare il webhook finché non vengono forniti tutti i valori dei parametri obbligatori.

![Creazione di webhook e assegnazione di parametri](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando si esegue un runbook utilizzando un webhook, viene inviato il parametro `[WebhookData](automation-webhooks.md)` di input predefinito, insieme ai parametri di input definiti. 

![Parametro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Passaggio di un oggetto JSON a un runbookPassing a JSON object to a runbook

Può essere utile archiviare i dati che si desidera passare a un runbook in un file JSON. Ad esempio, è possibile creare un file JSON che contiene tutti i parametri che si desidera passare a un runbook. A tale scopo, è necessario convertire il codice JSON in una stringa e quindi convertire la stringa in un oggetto PowerShell prima di passarla al runbook.

In questa sezione viene utilizzato un esempio in cui uno script di PowerShell chiama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) per avviare un runbook di PowerShell, passando il contenuto del file JSON al runbook. Il runbook di PowerShell avvia una macchina virtuale di Azure recuperando i parametri per la macchina virtuale dall'oggetto JSON.

### <a name="create-the-json-file"></a>Creare il file JSON

Digitare il codice seguente in un file di testo e salvarlo come **test.json** in un punto qualsiasi del computer locale.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Creare il runbook

Creare un nuovo runbook di PowerShell denominato Test-Json in Automazione di Azure.Create a new PowerShell runbook named **Test-Json** in Azure Automation. Vedere [il mio primo runbook](automation-first-runbook-textual-powershell.md)di PowerShell .

Per accettare i dati JSON, il runbook deve accettare un oggetto come parametro di input. Il runbook può quindi usare le proprietà definite nel file JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Salvare e pubblicare il runbook nell'account di automazione.

### <a name="call-the-runbook-from-powershell"></a>Chiamare il runbook da PowerShell

Ora è possibile chiamare il runbook dal computer locale tramite Azure PowerShell. 

1. Accedere ad Azure come illustrato. Successivamente, viene richiesto di immettere le credenziali di Azure.In a is prompted to enter your Azure credentials.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico `Connect-AzAccount` può usare solo se stesso.

1. Ottenere il contenuto del file JSON salvato e convertirlo in una stringa. `JsonPath`indica il percorso in cui è stato salvato il file JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Convertire il contenuto `$json` della stringa in un oggetto di PowerShell.Convert the string contents of to a PowerShell object.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Creare una tabella hash `Start-AzAutomationRunbook`per i parametri per . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Si noti che si imposta il valore di `Parameters` sull'oggetto PowerShell che contiene i valori dal file JSON.
1. Avviare il runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per modificare un runbook testuale, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Per modificare un runbook grafico, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
