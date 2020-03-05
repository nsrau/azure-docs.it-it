---
title: Parametri di input dei runbook
description: I parametri di input dei runbook ne aumentano la flessibilità perché consentono di passare dati a un runbook al momento dell'avvio. Questo articolo descrive vari scenari in cui i parametri di input vengono usati nei runbook.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 274ee0fe98281e733994f2d5df38886409cbc913
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273643"
---
# <a name="runbook-input-parameters"></a>Parametri di input dei runbook

I parametri di input Runbook aumentano la flessibilità di un Runbook consentendo di passare i dati al momento dell'avvio. Questi parametri consentono alle azioni Runbook di essere destinate a scenari e ambienti specifici. Questo articolo descrive la configurazione e l'uso dei parametri di input nei manuali operativi.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Configurazione dei parametri di input

È possibile configurare i parametri di input per PowerShell, il flusso di lavoro PowerShell, l'interfaccia grafica e manuali operativi Python. Un runbook può avere più parametri con tipi di dati diversi oppure non avere parametri. I parametri di input possono essere obbligatori o facoltativi ed è possibile usare i valori predefiniti per i parametri facoltativi.

I valori vengono assegnati ai parametri di input per un runbook all'avvio. È possibile avviare un Runbook dalla portale di Azure, da un servizio Web o da PowerShell. È inoltre possibile avviare un runbook come figlio, definito inline in un altro runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Configurare i parametri di input in runbook di PowerShell

PowerShell e manuali operativi del flusso di lavoro PowerShell in automazione di Azure supportano i parametri di input definiti tramite le proprietà seguenti. 

| **Proprietà** | **Descrizione** |
|:--- |:--- |
| Type |Obbligatorio. Tipo di dati previsto per il valore del parametro. Qualsiasi tipo .NET è valido. |
| Nome |Obbligatorio. Nome del parametro. Questo nome deve essere univoco all'interno di Runbook, deve iniziare con una lettera e può contenere solo lettere, numeri o caratteri di sottolineatura. |
| Obbligatorio |Facoltativa. Valore booleano che specifica se il parametro richiede un valore. Se si imposta questa impostazione su **true**, è necessario fornire un valore all'avvio del Runbook. Se si imposta questa opzione su **false**, un valore è facoltativo. Se non si specifica un valore per la proprietà **obbligatoria** , PowerShell considera il parametro di input facoltativo per impostazione predefinita. |
| Valore predefinito |Facoltativa. Valore utilizzato per il parametro se non viene passato alcun valore di input all'avvio del Runbook. Runbook può impostare un valore predefinito per qualsiasi parametro. |

Windows PowerShell supporta più attributi dei parametri di input rispetto a quelli elencati in precedenza, ad esempio convalida, alias e set di parametri. Tuttavia, automazione di Azure attualmente supporta solo le proprietà del parametro di input elencate.

Ad esempio, esaminiamo una definizione di parametro in un Runbook del flusso di lavoro PowerShell. Questa definizione presenta il formato generale seguente, in cui più parametri sono separati da virgole.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

A questo punto è possibile configurare i parametri di input per un Runbook del flusso di lavoro PowerShell che restituisce informazioni dettagliate sulle macchine virtuali, una singola VM o tutte le VM all'interno di un gruppo di risorse. Questo runbook ha due parametri, come illustrato nella schermata seguente: il nome della macchina virtuale (*VMName*) e il nome del gruppo di risorse (*resourceGroupName*).

![Flusso di lavoro PowerShell di Automazione](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

In questa definizione di parametro i parametri di input sono semplici parametri di tipo stringa.

Si noti che i manuali operativi di PowerShell e del flusso di lavoro PowerShell supportano tutti i tipi semplici e i tipi complessi, ad esempio **Object** o **PSCredential** per i parametri di input. Se il Runbook ha un parametro di input dell'oggetto, è necessario usare una tabella hash di PowerShell con coppie nome-valore per passare un valore. Ad esempio, si dispone del parametro seguente in un Runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

In questo caso, è possibile passare il valore seguente al parametro.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Quando non si passa un valore a un parametro di stringa facoltativo con un valore predefinito null, il valore del parametro è una stringa vuota anziché **null**.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Configurare i parametri di input in runbook grafici

Per illustrare la configurazione dei parametri di input per un Runbook grafico, è possibile creare un Runbook che restituisca informazioni dettagliate sulle macchine virtuali, una singola VM o tutte le VM all'interno di un gruppo di risorse. Per informazioni dettagliate, vedere [il primo Runbook grafico](automation-first-runbook-graphical.md).

Un Runbook grafico usa queste attività Runbook principali:

* Configurazione dell'account RunAs di Azure per l'autenticazione con Azure. 
* Definizione di un cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) per ottenere le proprietà della macchina virtuale.
* Usare l'attività [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) per restituire i nomi delle macchine virtuali. 

L'attività **Get-AzVM** definisce due input, il nome della macchina virtuale e il nome del gruppo di risorse. Poiché questi nomi possono essere diversi ogni volta che il Runbook viene avviato, è necessario aggiungere parametri di input al Runbook per accettare questi input. Vedere [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).

Per configurare i parametri di input, attenersi alla seguente procedura.

1. Selezionare il Runbook grafico nella pagina **manuali operativi** e quindi fare clic su **modifica**.
2. Nell'editor grafico fare clic sul pulsante **input e output** , quindi **aggiungere input** per aprire il riquadro dei parametri di input Runbook.

   ![Runbook grafico di Automazione](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Il controllo input e output visualizza un elenco di parametri di input definiti per Runbook. Qui è possibile aggiungere un nuovo parametro di input o modificare la configurazione di un parametro di input esistente. Per aggiungere un nuovo parametro per il Runbook, fare clic su **Aggiungi input** per aprire il pannello **parametro di input Runbook** , in cui è possibile configurare i parametri usando le proprietà definite nella [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).

    ![Aggiunta di nuovo input](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Creare due parametri con le proprietà seguenti che verranno usate dall'attività **Get-AzVM** e quindi fare clic su **OK**.

   * Parametro 1:
        * **Nome** -- **VMName**
        * **Tipo** : stringa
        * **Obbligatorio** -- **No**

   * Parametro 2:
        * **Nome** -- **resourceGroupName**
        * **Tipo** : stringa
        * **Obbligatorio** -- **No**
        * **Valore predefinito** -- **personalizzato**
        * Valore predefinito personalizzato: nome del gruppo di risorse che contiene le macchine virtuali

5. Visualizzare i parametri nel controllo di input e output. 
6. Fare di nuovo clic su **OK** e quindi fare clic su **Salva**.
7. Fare clic su **pubblica** per pubblicare il Runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Configurare i parametri di input in runbook di Python

A differenza di PowerShell, del flusso di lavoro PowerShell e manuali operativi grafici, Python manuali operativi non accetta parametri denominati. L'editor Runbook analizza tutti i parametri di input come una matrice di valori di argomento. È possibile accedere alla matrice importando il modulo **sys** nello script Python e quindi usando la matrice **sys. argv** . È importante notare che il primo elemento della matrice, `sys.argv[0]`, è il nome dello script. Il primo parametro di input effettivo è quindi *sys. argv [1]* .

Per un esempio su come usare i parametri di input in un runbook di Python, vedere [Il primo runbook Python in Automazione di Azure](automation-first-runbook-textual-python2.md).

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Assegnazione di valori ai parametri di input in manuali operativi

In questa sezione vengono descritti diversi modi per passare i valori ai parametri di input in manuali operativi. È possibile assegnare i valori dei parametri quando:

* [Avviare un runbook](#start-a-runbook-and-assign-parameters)
* [Testare un runbook](#test-a-runbook-and-assign-parameters)
* [Collegare una pianificazione per il Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Creare un webhook per il Runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Avviare un runbook e assegnare parametri

È possibile avviare un Runbook in diversi modi: tramite la portale di Azure, con un webhook, con i cmdlet di PowerShell, con l'API REST o con l'SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Avviare un Runbook pubblicato usando il portale di Azure e assegnare parametri

Quando si [Avvia il Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) nel portale di Azure, viene aperto il pannello **Avvia Runbook** ed è possibile immettere i valori per i parametri creati.

![Attività iniziali con il portale](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Nell'etichetta sotto la casella di input è possibile visualizzare le proprietà impostate per definire gli attributi dei parametri, ad esempio, obbligatorio o facoltativo, tipo, valore predefinito. Il fumetto della guida accanto al nome del parametro definisce anche le informazioni chiave necessarie per prendere decisioni relative ai valori di input dei parametri. 

> [!NOTE]
> I parametri di stringa supportano valori vuoti di tipo String. Immettendo **[EmptyString]** nella casella del parametro di input viene passata una stringa vuota al parametro. Inoltre, i parametri di stringa non supportano null. Se non si passa alcun valore a un parametro di stringa, PowerShell lo interpreta come null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Avviare un Runbook pubblicato usando i cmdlet di PowerShell e assegnare parametri

* **Cmdlet di Azure Resource Manager:** È possibile avviare un Runbook di automazione creato in un gruppo di risorse usando [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
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
> Quando si avvia un Runbook usando i cmdlet di PowerShell, viene creato un parametro predefinito, *MicrosoftApplicationManagementStartedBy*, con il valore **PowerShell**. È possibile visualizzare questo parametro nel riquadro Dettagli processo.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Avviare un Runbook usando un SDK e assegnare parametri

* **Metodo Azure Resource Manager:** È possibile avviare un Runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).  

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

* **Metodo del modello di distribuzione classica di Azure:** è possibile avviare un runbook usando l'SDK di un linguaggio di programmazione. Di seguito è riportato un frammento di codice C# per l'avvio di un runbook nell'account di automazione. È possibile visualizzare tutto il codice nel [repository GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs).

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

   Per avviare questo metodo, creare un dizionario per archiviare i parametri Runbook *VMName* e *resourceGroupName* e i relativi valori. Avviare quindi il runbook. Di seguito è riportato un frammento di codice C# per chiamare il metodo definito in precedenza.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Avviare un Runbook usando l'API REST e assegnare parametri

È possibile creare e avviare un processo runbook con l'API REST di automazione di Azure usando il metodo **put** con l'URI della richiesta seguente: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Nell'URI della richiesta sostituire i parametri seguenti:

* *SubscriptionId*: ID sottoscrizione di Azure.  
* *resourceGroupName*: nome del gruppo di risorse per l'account di automazione.
* *automationAccountName*: nome dell'account di automazione ospitato all'interno del servizio cloud specificato.  
* *JobName*: il GUID per il processo. I GUID in PowerShell possono essere creati usando `[GUID]::NewGuid().ToString()*`.

Per passare parametri al processo Runbook, usare il corpo della richiesta. Accetta le informazioni seguenti, fornite in formato JSON:

* Nome Runbook: obbligatorio. Nome del runbook per il processo da avviare.  
* Parametri Runbook: facoltativo. Dizionario dell'elenco di parametri in formato (nome, valore), dove il nome è di tipo stringa e il valore può essere qualsiasi valore JSON valido.

Se si vuole avviare il Runbook **Get-AzureVMTextual** creato in precedenza con *VMName* e *resourceGroupName* come parametri, usare il formato JSON seguente per il corpo della richiesta.

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

Se il processo è stato creato, viene restituito un codice di stato HTTP 201. Per altre informazioni sulle intestazioni di risposta e sul corpo della risposta, vedere [creare un processo Runbook usando l'API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Eseguire il test di un runbook e assegnare parametri

Quando si esegue il [test della versione bozza del Runbook](automation-testing-runbook.md) con l'opzione di test, viene visualizzata la pagina **test** . Utilizzare questa pagina per configurare i valori per i parametri creati.

![Esecuzione di test e assegnazione di parametri](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Collegare una pianificazione a un runbook e assegnare parametri

È possibile [collegare una pianificazione](automation-schedules.md) al runbook in modo che venga avviato in un momento specifico. Assegnare i parametri di input durante la creazione della pianificazione. Il runbook usa questi valori quando viene avviato dalla pianificazione. Non è possibile salvare la pianificazione fino a quando non vengono forniti tutti i valori dei parametri obbligatori.

![Pianificazione e assegnazione di parametri](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Creare un webhook per un runbook e assegnare parametri

È possibile creare un [webhook](automation-webhooks.md) per il runbook e configurare i parametri di input del runbook. Non è possibile salvare il webhook fino a quando non vengono forniti tutti i valori dei parametri obbligatori.

![Creazione di webhook e assegnazione di parametri](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Quando si esegue un Runbook usando un webhook, viene inviato il parametro di input predefinito *[WebhookData](automation-webhooks.md#details-of-a-webhook)* , insieme ai parametri di input definiti dall'utente. 

![Parametro WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>Passaggio di un oggetto JSON a un Runbook

Può essere utile archiviare i dati che si desidera passare a un runbook in un file JSON. Ad esempio, è possibile creare un file JSON che contiene tutti i parametri che si desidera passare a un Runbook. A tale scopo, è necessario convertire il codice JSON in una stringa e quindi convertire la stringa in un oggetto di PowerShell prima di passarla a Runbook.

Questa sezione usa un esempio in cui uno script di PowerShell chiama [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) per avviare un Runbook di PowerShell, passando il contenuto del file JSON a Runbook. Il Runbook di PowerShell avvia una macchina virtuale di Azure recuperando i parametri per la VM dall'oggetto JSON.

### <a name="create-the-json-file"></a>Creare il file JSON

Digitare il codice seguente in un file di testo e salvarlo come `test.json` in un punto qualsiasi del computer locale.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Creare il runbook

Creare un nuovo Runbook di PowerShell denominato **test-JSON** in automazione di Azure. Vedere [il primo Runbook di PowerShell](automation-first-runbook-textual-powershell.md).

Per accettare i dati JSON, il runbook deve accettare un oggetto come parametro di input. Runbook può quindi usare le proprietà definite nel file JSON.

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

1. Accedere ad Azure come illustrato. Successivamente, verrà richiesto di immettere le credenziali di Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Per PowerShell manuali operativi, **Add-AzAccount** e **Add-AzureRMAccount** sono alias per **Connect-AzAccount**. Si noti che questi alias non sono disponibili per manuali operativi grafici. Un Runbook grafico può usare solo **Connect-AzAccount** .

1. Ottenere il contenuto del file JSON salvato e convertirlo in una stringa. `JsonPath` è il percorso in cui è stato salvato il file JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Converte il contenuto della stringa di `$json` in un oggetto di PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Creare una tabella hash per i parametri di **Start-AzAutomationRunbook**. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Si noti che il valore dei *parametri* viene impostato sull'oggetto PowerShell che contiene i valori del file JSON.
1. Avviare il Runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sulle diverse modalità di avvio dei runbook, vedere [Avvio di un Runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per modificare un runbook testuale, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Per modificare un runbook grafico, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
