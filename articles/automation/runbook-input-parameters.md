---
title: Configurare i parametri di input dei runbook in Automazione di Azure
description: Questo articolo descrive come configurare i parametri di input dei runbook, che consentono ai dati di essere passati a un runbook quando viene avviato.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: c996c51583d81905e7853323166407e38ae79225
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830039"
---
# <a name="configure-runbook-input-parameters"></a>Configurare parametri di input dei runbook

I parametri di input dei runbook aumentano la flessibilità di questi ultimi consentendo il passaggio dei dati al momento dell'avvio. Tali parametri consentono di eseguire azioni di runbook mirate per ambienti e scenari specifici. Questo articolo descrive la configurazione e l'uso dei parametri di input nei runbook in uso.

È possibile configurare i parametri di input per runbook grafici, di PowerShell, del flusso di lavoro PowerShell e di Python. Un runbook può avere più parametri con tipi di dati diversi oppure non avere parametri. I parametri di input possono essere obbligatori o facoltativi ed è possibile usare un valore predefinito per i parametri facoltativi.

I valori dei parametri di input per un runbook vengono assegnati all'avvio del runbook stesso. È possibile avviare un runbook nel portale di Azure, in un servizio Web o in PowerShell. È inoltre possibile avviare un runbook come figlio, definito inline in un altro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurare i parametri di input in runbook di PowerShell

I runbook di PowerShell e del flusso di lavoro PowerShell in Automazione di Azure supportano parametri di input definiti tramite le proprietà seguenti. 

| **Proprietà** | **Descrizione** |
|:--- |:--- |
| Tipo |Obbligatorio. Tipo di dati previsto per il valore del parametro. Qualsiasi tipo .NET è valido. |
| Nome |Obbligatorio. Nome del parametro. Questo nome deve essere univoco all'interno del runbook, deve iniziare con una lettera e può contenere solo lettere, numeri o caratteri di sottolineatura. |
| Obbligatorio |Facoltativa. Valore booleano che specifica che il parametro richiede un valore. Se la proprietà è impostata su true, è necessario specificare un valore quando il runbook viene avviato. Se la proprietà è impostata su false, il valore è facoltativo. Se non si specifica un valore per la proprietà `Mandatory`, PowerShell considera il parametro di input facoltativo per impostazione predefinita. |
| Valore predefinito |Facoltativa. Valore utilizzato per il parametro se non viene passato alcun valore di input all'avvio del runbook. Il runbook può impostare un valore predefinito per qualsiasi parametro. |

Windows PowerShell supporta più attributi dei parametri di input rispetto a quelli elencati di seguito, ad esempio convalida, alias e set di parametri. Automazione di Azure, tuttavia, supporta attualmente solo le proprietà dei parametri di input elencate.

Esaminiamo ad esempio una definizione di parametro in un runbook del flusso di lavoro PowerShell. Tale definizione presenta il formato generale seguente, in cui più parametri sono separati da virgole.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Configuriamo ora i parametri di input per un runbook del flusso di lavoro PowerShell che restituisce informazioni dettagliate su macchine virtuali, sia di una singola macchina virtuale che di tutte le macchine virtuali all'interno di un gruppo di risorse. Tale runbook ha due parametri, come illustrato nella schermata seguente: il nome della macchina virtuale (`VMName`) e il nome del gruppo di risorse (`resourceGroupName`).

![Flusso di lavoro PowerShell di Automazione](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In questa definizione i parametri di input sono semplici parametri di tipo stringa.

Si noti che i runbook di PowerShell e del flusso di lavoro PowerShell supportano tutti i tipi semplici e complessi, ad esempio `Object` o `PSCredential` per i parametri di input. Se il runbook include un parametro di input di tipo object, per passare un valore è necessario usare una tabella hash di PowerShell con coppie nome-valore. Si supponga ad esempio di avere un runbook con questo parametro.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In questo caso, è possibile passare al parametro il valore seguente.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando a un parametro stringa facoltativo non si passa un valore predefinito Null, il valore del parametro è una stringa vuota anziché Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurare i parametri di input in runbook grafici

Per illustrare la configurazione dei parametri di input per un runbook grafico, è possibile creare un runbook che restituisca informazioni dettagliate sulle macchine virtuali, una singola o tutte, all'interno di un gruppo di risorse. Per informazioni dettagliate, vedere [Il primo runbook grafico](automation-first-runbook-graphical.md).

Un runbook grafico usa queste attività di runbook principali:

* Configurazione dell'account RunAs di Azure per l'autenticazione con Azure. 
* Definizione di un cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) per ottenere le proprietà della macchina virtuale.
* Usare l'attività [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) per restituire i nomi delle macchine virtuali. 

L'attività `Get-AzVM` definisce due input, ovvero il nome della macchina virtuale e quello del gruppo di risorse. Poiché questi nomi possono essere diversi ogni volta che il runbook viene avviato, è necessario aggiungere parametri di input al runbook per accettare tali input. Vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).

Per configurare i parametri di input, eseguire queste operazioni.

1. Selezionare il runbook grafico nella pagina Runbook e quindi fare clic su **Modifica**.
2. Nell'editor grafico fare clic sul pulsante **Input e output**, quindi fare clic su **Aggiungi input** per aprire il riquadro dei parametri di input del runbook.

   ![Runbook grafico di Automazione](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Il pannello Input e output mostra un elenco di parametri di input definiti per il runbook. Qui è possibile aggiungere un nuovo parametro di input o modificare la configurazione di un parametro di input esistente. Per aggiungere un nuovo parametro per il runbook, fare clic su **Aggiungi input** per aprire il riquadro **Parametro di input del runbook** in cui è possibile configurare i parametri usando le proprietà definite in [Creare runbook grafici in Automazione di Azure](automation-graphical-authoring-intro.md).

    ![Aggiunta di nuovo input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Creare due parametri con le proprietà seguenti che verranno usate dall'attività `Get-AzVM` e quindi fare clic su **OK**.

   * Parametro 1
        * **Nome** -- **VMName**
        * **Tipo**: string
        * **Obbligatoria** -- **No**

   * Parametro 2
        * **Nome** -- **resourceGroupName**
        * **Tipo**: string
        * **Obbligatoria** -- **No**
        * **Valore predefinito** -- **Custom**
        * Valore predefinito personalizzato: nome del gruppo di risorse che contengono le macchine virtuali

5. Visualizzare i parametri nel controllo Input e output. 
6. Fare clic di nuovo su **OK** e quindi fare clic **Salva**.
7. Fare clic su **Pubblica** per pubblicare il runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurare i parametri di input in runbook di Python

A differenza dei runbook grafici, di PowerShell e del flusso di lavoro di PowerShell, i runbook di Python non accettano parametri denominati. L'editor di runbook analizza tutti i parametri di input come una matrice di valori di argomento. Accedere alla matrice importando il modulo `sys` nello script Python e quindi usando la matrice `sys.argv`. È importante notare che il primo elemento della matrice, `sys.argv[0]`, è il nome dello script. Il primo parametro di input effettivo è pertanto `sys.argv[1]`.

Per un esempio su come usare i parametri di input in un runbook di Python, vedere [Il primo runbook Python in Automazione di Azure](automation-first-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Assegnare valori ai parametri di input nei runbook

Questa sezione descrive diversi modi per passare i valori ai parametri di input nei runbook. È possibile assegnare i valori dei parametri nei casi seguenti:

* [Avvio di un runbook](#start-a-runbook-and-assign-parameters)
* [Test di un runbook](#test-a-runbook-and-assign-parameters)
* [Collegamento di una pianificazione per il runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Creazione di un webhook per il runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Avviare un runbook e assegnare parametri

Sono disponibili diversi modi per avviare un runbook, ovvero nel portale di Azure, con un webhook, con i cmdlet di PowerShell, con l'API REST o con il componente SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Avviare un runbook pubblicato usando il portale di Azure e assegnare parametri

Quando si [avvia il runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) nel portale di Azure, viene aperto il pannello **Avvia Runbook** dove è possibile immettere i valori per i parametri creati.

![Attività iniziali con il portale](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Nell'etichetta sotto la casella di input è possibile visualizzare le proprietà impostate per definire gli attributi dei parametri, ad esempio obbligatorio o facoltativo, tipo, valore predefinito. Il fumetto della guida accanto al nome del parametro definisce anche le informazioni chiave necessarie per prendere decisioni relative ai valori di input dei parametri. 

> [!NOTE]
> I parametri di tipo stringa supportano valori vuoti dello stesso tipo. Se si immette `[EmptyString]` nella casella del parametro di input, viene passata una stringa vuota al parametro. I parametri di tipo stringa, inoltre, non supportano il valore Null. Se non viene passato alcun valore al parametro di tipo stringa, PowerShell lo interpreta come Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Avviare un runbook pubblicato usando i cmdlet di PowerShell e assegnare parametri

* **Cmdlet di Azure Resource Manager:** è possibile avviare un runbook di Automazione creato in un gruppo di risorse usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
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
> Quando si avvia un runbook usando i cmdlet di PowerShell, viene creato un parametro predefinito, `MicrosoftApplicationManagementStartedBy`, con valore `PowerShell`. Questo parametro può essere visualizzato nel riquadro Dettagli processo.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Avviare un runbook usando un SDK e assegnare parametri

* **Metodo di Azure Resource Manager:** è possibile avviare un runbook usando il componente SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

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

   Per avviare questo metodo, creare un dizionario per archiviare i parametri `VMName` e `resourceGroupName` del runbook e i relativi valori. Avviare quindi il runbook. Di seguito è riportato un frammento di codice C# per chiamare il metodo definito in precedenza.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Avviare un runbook usando l'API REST e assegnare parametri

È possibile creare e avviare un processo del runbook con l'API REST di Automazione di Azure usando il metodo `PUT` con l'URI della richiesta seguente: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Nell'URI della richiesta sostituire i parametri seguenti:

* `subscriptionId`: ID sottoscrizione di Azure.  
* `resourceGroupName`: Nome del gruppo di risorse per l'account di Automazione.
* `automationAccountName`: nome dell'account di Automazione ospitato nel servizio cloud specificato.  
* `jobName`: GUID del processo. I GUID in PowerShell possono essere creati usando `[GUID]::NewGuid().ToString()*`.

Per passare parametri al processo del runbook, usare il corpo della richiesta. Vengono accettate le informazioni seguenti in formato JSON.

* Nome del runbook: obbligatorio. Nome del runbook per il processo da avviare.  
* Parametri del runbook: facoltativi. Dizionario dell'elenco dei parametri in formato (nome, valore) in cui il nome deve essere di tipo stringa e il valore può essere qualsiasi valore JSON valido.

Se si intende avviare il runbook **Get-AzureVMTextual** creato in precedenza con `VMName` e `resourceGroupName` come parametri, usare il formato JSON seguente per il corpo della richiesta.

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

Se il processo è stato creato, viene restituito un codice di stato HTTP 201. Per altre informazioni sulle intestazioni di risposta e sul corpo della risposta, vedere [Creare un processo del runbook usando l'API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Eseguire il test di un runbook e assegnare parametri

Quando si [esegue il test della versione bozza del runbook in uso](automation-testing-runbook.md) tramite l'opzione di test, viene visualizzata la pagina Test. Usare questa pagina per configurare i valori per i parametri creati.

![Esecuzione di test e assegnazione di parametri](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Collegare una pianificazione a un runbook e assegnare parametri

È possibile [collegare una pianificazione](automation-schedules.md) al runbook in modo che venga avviato in un momento specifico. Assegnare i parametri di input durante la creazione della pianificazione. Il runbook usa questi valori quando viene avviato dalla pianificazione. Fino a quando non vengono specificati tutti i valori dei parametri obbligatori, non è possibile salvare la pianificazione.

![Pianificazione e assegnazione di parametri](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Creare un webhook per un runbook e assegnare parametri

È possibile creare un [webhook](automation-webhooks.md) per il runbook e configurare i parametri di input del runbook. Fino a quando non vengono specificati tutti i valori dei parametri obbligatori, non è possibile salvare il webhook.

![Creazione di webhook e assegnazione di parametri](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando si esegue un runbook con un webhook, insieme ai parametri di input definiti viene inviato il parametro di input predefinito `[WebhookData](automation-webhooks.md)`. 

![Parametro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Passare un oggetto JSON a un runbook

Può essere utile archiviare i dati che si desidera passare a un runbook in un file JSON. È possibile ad esempio creare un file JSON che contiene tutti i parametri da passare a un runbook. A tale scopo, è necessario convertire il codice JSON in una stringa e quindi convertire la stringa in un oggetto di PowerShell prima di passarla al runbook.

Questa sezione usa un esempio in cui uno script di PowerShell chiama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) per avviare un runbook di PowerShell, passando il contenuto del file JSON al runbook. Il runbook di PowerShell avvia una macchina virtuale di Azure recuperando i parametri per la macchina dall'oggetto JSON.

### <a name="create-the-json-file"></a>Creare il file JSON

Digitare il codice seguente in un file di testo e salvarlo come **test.json** in un punto qualsiasi del computer locale.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Creare il runbook

Creare un nuovo runbook di PowerShell denominato **Test-Json** in Automazione di Azure. Vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).

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

1. Accedere ad Azure nel modo illustrato. Viene richiesto di immettere le credenziali di Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Si noti che questi alias non sono disponibili per i runbook grafici. Un runbook grafico può usare solo `Connect-AzAccount`.

1. Ottenere il contenuto del file JSON salvato e convertirlo in una stringa. `JsonPath` indica il percorso in cui è stato salvato il file JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Convertire i contenuti stringa di `$json` in un oggetto di PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Creare una tabella hash per i parametri per `Start-AzAutomationRunbook`. 

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

* Per preparare un runbook testuale, vedere [Modificare runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Per preparare un runbook grafico, vedere [Creare runbook grafici in Automazione di Azure](automation-graphical-authoring-intro.md).
