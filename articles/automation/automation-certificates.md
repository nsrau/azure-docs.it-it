<properties 
   pageTitle="Certificati"
   description="I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che i Runbook possano accedervi per eseguire l'autenticazione rispetto a risorse di Azure e di terze parti. Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Certificati

I certificati possono essere archiviati in modo sicuro in Automazione di Azure, in modo che Runbook possano accedervi tramite l'attività **Get-AutomationCertificate**. Ciò permette di creare Runbook che usano certificati per l'autenticazione o di aggiungerli a risorse di Azure o di terze parti che possono essere create o configurate dal Runbook.

>[AZURE.NOTE]Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure tramite una chiave univoca generata per ogni account di automazione. La chiave viene crittografata da un certificato master e viene archiviata in Automazione di Azure. Prima dell'archiviazione di un asset sicuro, la chiave per l'account di automazione viene decrittografata usando il certificato master e viene quindi usata per crittografare l'asset.

## Cmdlet di Windows PowerShell

I cmdlet della tabella seguente vengono usati per creare e gestire asset di certificati di automazione con Windows PowerShell. Sono inclusi nel [modulo Azure PowerShell](../powershell-install-configure.md), disponibile per l'uso nei Runbook di Automazione.

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|Recupera informazioni su un certificato. È possibile recuperare solo il certificato stesso dall'attività Get-AutomationCertificate.|
|[New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|Importa un nuovo certificato in Automazione di Azure.|
|[Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|Rimuove un certificato da Automazione di Azure.|
|[Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione pfx.|

## Attività del Runbook

Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un Runbook.

|Attività|Descrizione|
|:---|:---|
|Get-AutomationCertificate|Ottiene un certificato da usare in un Runbook.|

>[AZURE.NOTE]È consigliabile evitare di usare le variabili nel parametro –Name di GetAutomationCertificate, poiché ciò può complicare l'individuazione delle dipendenze tra i Runbook e gli asset di certificato durante la fase di progettazione.

## Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, sarà possibile usarlo solo per la firma entro il Runbook.

### Per creare un nuovo certificato con il portale di Azure

1. Dall'account di automazione fare clic su **Asset** nella parte superiore della finestra.
1. Nella parte inferiore della finestra, fare clic su **Aggiungi impostazione**.
1. Fare clic su **Aggiungi credenziali**.
2. Nell'elenco a discesa **Tipo di credenziali** selezionare **Certificato**.
3. Digitare un nome per il certificato nella casella **Nome** e fare clic sulla freccia a destra.
4. Cercare un file con estensione cer o pfx. Se si seleziona un file con estensione pfx, specificare una password e indicare se ne deve essere consentita l'esportazione.
1. Fare clic sul segno di spunta per caricare il file del certificato e salvare il nuovo asset di certificato.


### Per creare un nuovo certificato con il portale di anteprima di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
1. Fare clic sulla parte **Certificati** per aprire il pannello **Certificati**.
1. Fare clic su **Aggiungi certificato** nella parte superiore del pannello.
2. Digitare un nome per il certificato nella casella **Nome**.
2. Fare clic su **Selezionare un file** in **Carica un file di certificato** per cercare un file con estensione cer o pfx. Se si seleziona un file con estensione pfx, specificare una password e indicare se ne deve essere consentita l'esportazione.
1. Fare clic su **Crea** per salvare il nuovo asset di certificato.


### Per creare un nuovo certificato con Windows PowerShell

I comandi di esempio seguenti mostrano come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. Verrà importato un file con estensione pfx esistente.

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## Uso di un certificato in un Runbook

Per usare un certificato in un Runbook, è necessario usare l'attività **Get-AutomationCertificate**. Non è possibile usare il cmdlet [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx), poiché restituisce informazioni sull'asset di certificato, ma non il certificato stesso.

### Esempio di Runbook testuale

Il codice di esempio seguente mostra come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### Esempio di Runbook grafico

Per aggiungere un'attività **Get-AutomationCerticiate** a un Runbook grafico, fare clic con il pulsante destro del mouse sul certificato nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi a area di disegno**.

![](media/automation-certificates/certificate-add-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un Runbook grafico. Si tratta dello stesso esempio mostrato in precedenza per l'aggiunta di un certificato a un servizio cloud da un Runbook testuale.

Questo esempio usa il set di parametri **UseConnectionObject** per l'attività **Send-TwilioSMS** che usa un oggetto di connessione per l'autenticazione al servizio. È necessario usare un [collegamento pipeline](automation-graphical-authoring-intro.md#links-and-workflow), poiché un collegamento sequenza restituirebbe una raccolta contenente un singolo oggetto, non previsto dal parametro Connection.

![](media/automation-certificates/add-certificate.png)


## Vedere anche

- [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow)

<!---HONumber=58--> 