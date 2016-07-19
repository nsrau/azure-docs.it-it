<properties
   pageTitle="Creare un modulo di integrazione di Automazione di Azure | Microsoft Azure"
   description="Esercitazione che illustra la creazione, i test e un uso di esempio dei moduli di integrazione in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="" />

<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="07/08/2016"
   ms.author="magoedte" />

# Moduli di integrazione di Automazione di Azure

PowerShell è la tecnologia alla base di Automazione di Azure. Poiché Automazione di Azure è basato su PowerShell, i moduli di PowerShell sono essenziali per l'estendibilità di Automazione di Azure. Questo articolo illustra le specifiche dell'uso dei moduli di PowerShell, indicati come "moduli di integrazione", in Automazione di Azure e le procedure consigliate per creare moduli di PowerShell personalizzati destinati a fungere da moduli di integrazione in Automazione di Azure.

## Che cos'è un modulo di PowerShell

Un modulo di PowerShell è un gruppo di cmdlet di PowerShell, come **Get-Date** o **Copy-Item**, che può essere usato dalla console di PowerShell, da script, da flussi di lavoro, da runbook e da risorse di PowerShell DSC come WindowsFeature o File, che possono essere usate da configurazioni di PowerShell DSC. Tutte le funzionalità di PowerShell vengono esposte tramite cmdlet e risorse DSC e ogni risorsa DSC o cmdlet è supportato da un modulo di PowerShell, molti dei quali sono inclusi in PowerShell. Ad esempio, il cmdlet **Get-Date**, il cmdlet **Copy-Item** e la risorsa Package DSC fanno parte rispettivamente dei moduli Microsoft.PowerShell.Utility, Microsoft.PowerShell.Management e PSDesiredStateConfiguration di PowerShell. Tutti questi moduli sono inclusi in PowerShell. Molti moduli di PowerShell, tuttavia, non sono inclusi in PowerShell e vengono invece distribuiti con prodotti proprietari o di terze parti come System Center 2012 Configuration Manager oppure dall'estesa community di PowerShell in posizioni come PowerShell Gallery. I moduli sono utili perché semplificano attività complesse tramite funzionalità incapsulate. Altre informazioni sui moduli di PowerShell sono disponibili in [MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx).

## Che cos'è un modulo di integrazione di Automazione di Azure

Un modulo di integrazione è simile a un modulo di PowerShell. È semplicemente un modulo di PowerShell che contiene facoltativamente un file aggiuntivo di metadati che specifica il tipo di connessione di Automazione di Azure che deve essere usato con i cmdlet del modulo nei runbook. Che sia presente o meno il file facoltativo, questi moduli di PowerShell possono essere importati in Automazione di Azure per rendere i cmdlet e le risorse DSC disponibili per l'uso rispettivamente in runbook e configurazioni DSC. In background, Automazione di Azure archivia questi moduli e durante l'esecuzione del processo del runbook e del processo di compilazione DSC li carica nelle sandbox di Automazione di Azure in cui i runbook vengono eseguiti e le configurazioni DSC compilate. Tutte le risorse DSC dei moduli vengono automaticamente inserite nel server pull di Automation DSC in modo che i computer che provano ad applicare le configurazioni DSC possano effettuarne il pull. In Automazione di Azure sono inclusi per impostazione predefinita alcuni moduli di Azure PowerShell che possono essere usati per iniziare subito ad automatizzare la gestione di Azure, ma è possibile importare facilmente moduli di PowerShell per qualsiasi sistema, servizio o strumento con cui si vuole eseguire l'integrazione.

>[AZURE.NOTE] Alcuni moduli sono disponibili come "moduli globali" nel servizio di automazione. Questi moduli globali sono disponibili per impostazione predefinita quando si crea un account di automazione e vengono talvolta aggiornati automaticamente nell'account di automazione dell'utente. Per evitare l'aggiornamento automatico è sempre possibile importare personalmente lo stesso modulo, che avrà la precedenza sulla versione del modulo globale messa a disposizione nel servizio.

Il formato di importazione del pacchetto di un modulo di integrazione è un file compresso con lo stesso nome del modulo ed estensione zip, che contiene il modulo di Windows PowerShell e tutti i file di supporto, incluso un file manifesto (con estensione psd1) se previsto dal modulo.

Se il modulo deve contenere un tipo di connessione di Automazione di Azure, deve contenere anche un file denominato *<ModuleName>*-Automation.json che specifica le proprietà del tipo di connessione. Questo file JSON, inserito nella cartella del modulo del file compresso con estensione zip, contiene i campi della "connessione" necessaria per connettersi al sistema o al servizio rappresentato dal modulo. In questo modo viene creato un tipo di connessione in Automazione di Azure. Usando questo file è possibile impostare i nomi, i tipi e l'eventuale crittografia e/o facoltatività dei campi per il tipo di connessione del modulo. Di seguito è riportato un modello nel formato di file json:

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Per chi ha eseguito la distribuzione di Service Management Automation e la creazione di pacchetti di moduli di integrazione per runbook di automazione, dovrebbe avere un aspetto familiare.


## Procedure consigliate per la creazione

Nonostante i moduli di integrazione siano essenzialmente moduli di PowerShell, esiste comunque un insieme di procedure per la loro creazione. Per garantirne la massima usabilità in Automazione di Azure, durante la creazione di un modulo di PowerShell è consigliabile prendere in considerazione diversi aspetti. Alcuni sono specifici di Automazione di Azure, mentre altri sono utili per garantire il corretto funzionamento dei moduli in Flusso di lavoro PowerShell indipendentemente dal fatto che si usi Automazione.

1. Includere un riepilogo, una descrizione e un URI della Guida per ogni cmdlet del modulo. In PowerShell è possibile definire alcune informazioni della Guida per i cmdlet in modo che gli utenti possano visualizzare informazioni sul relativo uso con il cmdlet **Get-Help**. Di seguito, ad esempio, è illustrato come definire un riepilogo e un URI della Guida per un modulo di PowerShell scritto in un file con estensione psm1.<br>

    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,

       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,

       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )

    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken

    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
    
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred

    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
<br> Specificando queste informazioni, non solo la Guida verrà visualizzata usando il cmdlet **Get-Help** nella console di PowerShell, ma le funzionalità della Guida verranno esposte anche in Automazione di Azure, ad esempio quando si inseriscono attività durante la creazione di runbook. Facendo clic su "Visualizza Guida dettagliata", l'URI della Guida verrà aperto in un'altra scheda del Web browser usato per accedere ad Automazione di Azure.<br>![Guida dei moduli di integrazione](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Se il modulo viene eseguito su un sistema remoto, deve contenere un file di metadati del modulo di integrazione che definisce le informazioni necessarie per connettersi al sistema remoto, ovvero il tipo di connessione. Ogni cmdlet del modulo, inoltre, deve poter accettare un oggetto connessione (ovvero un'istanza del tipo di connessione) come parametro. L'uso dei cmdlet del modulo in Automazione di Azure risulta più semplice se si consente che al cmdlet venga passato come parametro un oggetto con i campi del tipo di connessione. In questo modo, gli utenti non devono eseguire il mapping dei parametri dell'asset della connessione ai parametri del cmdlet corrispondenti ogni volta che chiamano un cmdlet. In base all'esempio di runbook precedente, viene usato un asset di connessione Twilio denominato CorpTwilio per accedere a Twilio e restituire tutti i numeri di telefono nell'account. Si noti il mapping dei campi della connessione ai parametri del cmdlet.<br>

    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
    
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
<br> Un approccio più semplice ed efficiente consiste nel passare direttamente l'oggetto connessione al cmdlet.

    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
<br> È possibile abilitare un comportamento di questo tipo per i cmdlet consentendo loro di accettare direttamente un oggetto connessione come parametro, anziché soltanto i campi connessione per i parametri. È in genere consigliabile che sia presente un set di parametri per ognuno, in modo che un utente che non usa Automazione di Azure possa chiamare i cmdlet senza costruire una tabella hash che funga da oggetto connessione. Il set di parametri **SpecifyConnectionFields** riportato di seguito viene usato per passare una alla volta le proprietà dei campi connessione. **UseConnectionObject** consente di passare direttamente la connessione. Come si può notare, il cmdlet Send-TwilioSMS del [modulo Twilio di PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) consente di usare entrambe le modalità:

    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,

         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,

         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection

       )
    }
    ```
<br>
3. Definire il tipo di output per tutti cmdlet del modulo. Definendo un tipo di output per un cmdlet, IntelliSense in fase di progettazione consente di determinare le proprietà di output del cmdlet da usare durante la creazione. È particolarmente utile durante la creazione grafica di runbook di automazione, in cui la conoscenza in fase di progettazione è essenziale per facilitare l'esperienza dell'utente con il modulo.<br> ![Tipo di output di runbook grafici](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Si ottiene così una funzionalità simile al "completamento automatico" dell'output di un cmdlet in PowerShell ISE, senza che sia necessario eseguirlo.<br> ![PowerShell e IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. I cmdlet del modulo non devono accettare tipi di oggetto complessi per i parametri. Flusso di lavoro PowerShell si differenzia da PowerShell perché archivia i tipi complessi in formato deserializzato. I tipi primitivi vengono mantenuti come primitive, mentre i tipi complessi vengono convertiti nelle rispettive versioni deserializzate, che sono essenzialmente contenitori delle proprietà. Se è stato usato il cmdlet **Get-Process** in un runbook o un flusso di lavoro di PowerShell, viene restituito un oggetto di tipo [Deserialized.System.Diagnostic.Process] anziché il tipo [System.Diagnostic.Process] previsto. Questo tipo ha le stesse proprietà del tipo non deserializzato, ma nessun metodo. Se si cerca di passare questo valore come parametro a un cmdlet, che si aspetta un valore [System.Diagnostic.Process] per il parametro, viene visualizzato l'errore seguente: *Impossibile elaborare la trasformazione degli argomenti nel parametro 'process'. Errore: "Impossibile convertire il valore "System.Diagnostics.Process (CcmExec)" di tipo "Deserialized.System.Diagnostics.Process" nel tipo "System.Diagnostics.Process".* Ciò è dovuto alla mancata corrispondenza di tipo tra il tipo [System.Diagnostic.Process] previsto e il tipo [Deserialized.System.Diagnostic.Process] fornito. Per evitare il problema, assicurarsi che i cmdlet del modulo non accettino tipi complessi per i parametri. Di seguito è illustrato il modo errato di procedere.

    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
<br> Ecco invece il modo corretto, che consiste nell'accettare una primitiva che possa essere usata internamente dal cmdlet per recuperare l'oggetto complesso e usarlo. Poiché i cmdlet vengono eseguiti nel contesto di PowerShell, non di Flusso di lavoro PowerShell, nel cmdlet $process diventa il tipo [System.Diagnostic.Process] corretto.

    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName

      $process.Description
    }
    ```
<br> Gli asset di connessione dei runbook sono tabelle hash, ovvero un tipo complesso. Sembra però che queste tabelle hash possano essere passate perfettamente ai cmdlet per il parametro –Connection senza eccezione del cast. Tecnicamente, il cast dal formato serializzato al formato deserializzato può essere eseguito correttamente per alcuni tipi di PowerShell, che possono quindi essere passati ai cmdlet per i parametri che accettano il tipo non deserializzato. La tabella hash è uno di questi tipi. Anche i tipi definiti dall'autore del modulo possono essere implementati in modo da essere deserializzati correttamente, ma sono necessari alcuni compromessi. Per il tipo devono essere disponibili un costruttore predefinito e un PSTypeConverter e tutte le proprietà del tipo devono essere pubbliche. I tipi già definiti che non sono di proprietà dell'autore del modulo, tuttavia, non possono essere in alcun modo "corretti" ed è per questo motivo che è consigliabile evitare completamente i tipi complessi per i parametri. Suggerimento per la creazione di runbook: se per qualche motivo i cmdlet devono accettare un parametro di tipo complesso o si usa un modulo altrui che richiede un parametro di tipo complesso, la soluzione alternativa nei runbook del flusso di lavoro di PowerShell e nei flussi di lavoro di PowerShell in PowerShell locale è eseguire il wrapping del cmdlet che genera il tipo complesso e del cmdlet che lo usa nella stessa attività InlineScript. Poiché InlineScript esegue il contenuto come PowerShell anziché come Flusso di lavoro PowerShell, il cmdlet che genera il tipo complesso produrrà il tipo corretto e non il tipo complesso deserializzato.
5. Assicurarsi che tutti i cmdlet del modulo siano senza stato. Flusso di lavoro PowerShell esegue tutti i cmdlet chiamati nel flusso di lavoro in una diversa sessione. Di conseguenza, i cmdlet che dipendono dallo stato della sessione creato/modificato da altri cmdlet dello stesso modulo non funzioneranno nei runbook Flusso di lavoro PowerShell. Di seguito è riportato un esempio della soluzione da non adottare.

    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
      
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
     
       $output
    }
    ```
<br>
6. Il modulo deve essere interamente contenuto in un pacchetto su cui è possibile eseguire Xcopy. Poiché i moduli di Automazione di Azure vengono distribuiti nelle sandbox di Automazione quando devono essere eseguiti i runbook, devono funzionare in modo indipendente dall'host in cui vengono eseguiti. Dovrebbe quindi essere possibile comprimere il pacchetto del modulo e spostarlo in qualsiasi altro host con la stessa versione di PowerShell o una più recente ottenendo il normale funzionamento dopo l'importazione nell'ambiente PowerShell di tale host. Perché ciò avvenga, il modulo non deve dipendere da file all'esterno della cartella del modulo (ovvero della cartella che viene compressa per l'importazione in Automazione di Azure) né da qualsiasi impostazione del Registro di sistema univoca di un host, come quelle definite dall'installazione di un prodotto. Se questa procedura consigliata non viene seguita, il modulo non potrà essere usato in Automazione di Azure.

## Passaggi successivi

- Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md)
- Per altre informazioni sulla creazione di moduli di PowerShell, vedere [Writing a Windows PowerShell Module](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx) (Scrittura di un modulo di Windows PowerShell).

<!---HONumber=AcomDC_0713_2016-->