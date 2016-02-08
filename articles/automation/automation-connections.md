<properties 
   pageTitle="Asset di tipo connessione in Automazione di Azure | Microsoft Azure"
   description="Gli asset di connessione di Automazione di Azure contengono le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una connessione DSC. Questo articolo illustra nel dettaglio le connessioni e spiega come usarle nella creazione testuale e grafica."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/27/2016"
   ms.author="bwren" />

# Asset di connessione in Automazione di Azure

Un asset di connessione di Automazione contiene le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una configurazione DSC. ad esempio le informazioni necessarie per l'autenticazione, quali nome utente e password, oltre alle informazioni di connessione quali un URL o una porta. Il valore di una connessione consiste nel mantenere tutte le proprietà per la connessione a un'applicazione specifica in un singolo asset, invece di creare più variabili. L'utente può modificare i valori per una connessione in un'unica posizione e può passare il nome di una connessione a un Runbook o a una configurazione DSC in un singolo parametro. È possibile accedere alle proprietà per una connessione nel Runbook o nella configurazione DSC con l'attività **Get-AutomationConnection**.

Quando si crea una connessione, è necessario specificare un *tipo di connessione*. Il tipo di connessione è un modello che definisce un set di proprietà. La connessione definisce i valori per ogni proprietà definita nel rispettivo tipo di connessione. I tipi di connessione vengono aggiunti ad Automazione di Azure nei moduli di integrazione o vengono creati con l'[API di Automazione di Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx). Gli unici tipi di connessione disponibili quando si crea una connessione sono i tipi installati nell'account di automazione.

>[AZURE.NOTE] Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure tramite una chiave univoca generata per ogni account di automazione. La chiave viene crittografata da un certificato master e archiviata in Automazione di Azure. Prima dell'archiviazione di un asset sicuro, la chiave per l'account di automazione viene decrittografata usando il certificato master e viene quindi usata per crittografare l'asset.

## Cmdlet di Windows PowerShell

I cmdlet inclusi nella tabella seguente vengono usati per creare e gestire le connessioni di Automazione con Windows PowerShell. Sono inclusi nel [modulo di Azure PowerShell](../powershell-install-configure.md), disponibile per l'uso nei Runbook e nelle configurazioni DSC di Automazione.

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|Recupera una connessione. Include una tabella hash con i valori dei campi della connessione.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|Crea una nuova connessione.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|Rimuove una connessione esistente.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|Imposta il valore di un campo specifico per una connessione esistente.|

## Attività

Le attività incluse nella tabella seguente vengono usate per accedere alle connessioni in un Runbook o configurazione DSC.

|Attività|Descrizione|
|---|---|
|Get-AutomationConnection|Ottiene una connessione da usare. Restituisce una tabella hash con le proprietà della connessione.|

>[AZURE.NOTE] È consigliabile evitare di usare le variabili nel parametro –Name di **Get- AutomationConnection**, poiché ciò può complicare l'individuazione delle dipendenze tra i Runbook o configurazioni DSC e gli asset di connessione durante la fase di progettazione.

## Creazione di una nuova connessione

### Per creare una nuova connessione con il portale di Azure

1. Dall'account di automazione fare clic su **Asset** nella parte superiore della finestra.
1. Nella parte inferiore della finestra, fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi connessione**.
2. Dall'elenco a discesa **Tipo di connessione** selezionare il tipo di connessione da creare. La procedura guidata presenta le proprietà per il tipo di connessione specifico.
1. Completare la procedura guidata e selezionare la casella di controllo per salvare la nuova connessione.


### Per creare una nuova connessione con il portale di anteprima di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
1. Fare clic sulla parte **Connessioni** per aprire il pannello **Connessioni**.
1. Fare clic su **Aggiungi connessione** nella parte superiore del pannello.
2. Dall'elenco a discesa **Tipo** selezionare il tipo di connessione da creare. Il modulo presenta le proprietà per il tipo di connessione specifico.
1. Completare il modulo e fare clic su **Crea** per salvare la nuova connessione.



### Per creare una nuova connessione con Windows PowerShell

Creare una nuova connessione con Windows PowerShell usando il cmdlet [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx). Questo cmdlet ha un parametro denominato **ConnectionFieldValues** che prevede una [tabella hash](http://technet.microsoft.com/library/hh847780.aspx) che definisce i valori per ogni proprietà definita dal tipo di connessione.


I comandi di esempio seguenti creano una nuova connessione per [Twilio](http://www.twilio.com), un servizio di telefonia che permette di inviare e ricevere messaggi di testo. Un modulo di integrazione di esempio che include un tipo di connessione Twilio è disponibile nell'area [Script Center](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8). Questo tipo di connessione definisce le proprietà per il SID dell'account e il token di autorizzazione, necessari per la convalida dell'account quando ci si connette a Twilio. Per il funzionamento corretto del codice di esempio, è necessario [scaricare questo modulo](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) e installarlo nell'account di automazione.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Uso di una connessione in un Runbook o in una configurazione DSC

Il cmdlet **Get-AutomationConnection** permette di recuperare una connessione in un Runbook o in una configurazione DSC. Questa attività recupera i valori dei diversi campi nella connessione e li restituisce come [tabella hash](http://go.microsoft.com/fwlink/?LinkID=324844), che può essere quindi usata con i comandi appropriati nel Runbook o nella configurazione DSC.

### Esempio di Runbook testuale
I comandi di esempio seguenti illustrano come usare la connessione Twilio dell'esempio precedente per inviare un messaggio di testo da un Runbook. L'attività Send-TwilioSMS usata in questo esempio ha due set di parametri, ognuno dei quali usa un metodo diverso per l'autenticazione al servizio Twilio. Uno usa un oggetto di connessione e l'altro usa singoli parametri per il SID dell'account e il token di autorizzazione. Entrambi i metodi sono illustrati in questo esempio.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### Esempi di Runbook grafici

Per aggiungere un'attività **Get-AutomationConnection** a un Runbook grafico, fare clic con il pulsante destro del mouse sulla connessione nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi a area di disegno**.

![](media/automation-connections/connection-add-canvas.png)

La figura seguente mostra un esempio dell'uso di una connessione in un Runbook grafico. Si tratta dello stesso esempio usato in precedenza per l'invio di un messaggio di testo tramite Twilio da un Runbook testuale. Questo esempio usa il set di parametri **UseConnectionObject** per l'attività **Send-TwilioSMS** che usa un oggetto di connessione per l'autenticazione al servizio. Viene usato un [collegamento pipeline](automation-graphical-authoring-intro.md#links-and-workflow), poiché il parametro Connection prevede un singolo oggetto.

Per il valore nel parametro **To** viene usata un'espressione PowerShell invece di un valore Constant poiché questo parametro prevede un tipo di valore matrice di stringa, in modo che sia possibile inviare più numeri. Un'espressione PowerShell permette di fornire un singolo valore o una matrice.

![](media/automation-connections/get-connection-object.png)

La figura seguente mostra lo stesso esempio precedente ma usa il set di parametri **SpecifyConnectionFields** che prevede i parametri AccountSid e AuthToken specificati individualmente, invece dell'uso di un oggetto di connessione per l'autenticazione. In questo caso, vengono specificati i campi della connessione invece dell'oggetto stesso.

![](media/automation-connections/get-connection-properties.png)



## Articoli correlati

- [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=AcomDC_0128_2016-->