<properties
   pageTitle="Automazione di Azure e gestione del cloud ibrido: automazione della distribuzione di una VM in Amazon Web Services"
   description="Automazione di Azure e gestione del cloud ibrido: automazione della distribuzione di una VM in Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Automazione di Azure e gestione del cloud ibrido: automazione della distribuzione di una VM in Amazon Web Services

Nel corso degli eventi del [Microsoft Cloud Roadshow](https://microsoftcloudroadshow.com/cities) in cui è stato presentato Automazione di Azure, è stato chiesto spesso se questo servizio sia concepito solo per Azure. Anticipando la risposta contenuta nella demo che verrà illustrata in questo articolo, **Automazione di Azure non è concepito solo per Azure, ma è destinato alle risorse locali *E* alla gestione degli ambienti cloud ibridi**. Ad esempio, Automazione di Azure è compatibile anche con Amazon Web Services (AWS).

## Obiettivi

Questo articolo illustra come usare Automazione di Azure per effettuare il provisioning di una macchina virtuale in AWS attribuendole un nome specifico. In AWS questa operazione è detta "applicazione di tag" alla macchina virtuale.

## Presupposti

Ai fini di questo articolo, si suppone che sia già stato configurato un account di automazione di Azure e che sia disponibile una sottoscrizione di AWS.

Per altre informazioni su come configurare un account di automazione di Azure, vedere la [pagina della documentazione](automation-configuring.md).

## Preparazione: archiviazione delle credenziali di AWS

Perché Automazione di Azure possa "dialogare" con AWS, è necessario recuperare le credenziali di AWS e archiviarle come asset in Automazione di Azure.

Dopo aver eseguito l'accesso al portale di AWS, fare clic sul piccolo triangolo sotto il nome e selezionare **Security Credentials**.

![Credenziali di sicurezza di AWS](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "Credenziali di sicurezza di AWS")

Fare clic su **Access Keys**.

![Chiavi di accesso di AWS](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "Chiavi di accesso di AWS")

Copiare i valori di Access Key ID e Secret Access Key. Facoltativamente è possibile scaricare il file delle chiavi per archiviarlo in un luogo sicuro.

![Creazione di chiavi di accesso di AWS](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "Creazione di chiavi di accesso di AWS")

## Asset di AWS in Automazione di Azure

Nel passaggio precedente sono stati copiati e salvati i valori di **Access Key ID** e **Secret Access Key** di AWS, che ora verranno archiviati in Automazione di Azure.

Passare all'account di automazione e selezionare **Asset** - **Credenziali**, aggiungere una nuova credenziale e denominarla **AWScred**.

![immagine](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "immagine")

Fornire una descrizione facoltativa e assicurarsi di immettere il valore di **Access Key ID** nel campo **Nome utente** e il valore di **Secret Access Key** nel campo **Password**. Salvare le credenziali di AWS.

## Modulo di PowerShell di Amazon Web Services

Il runbook di provisioning della macchina virtuale usa il modulo di PowerShell di AWS per l'esecuzione delle operazioni. Il modulo di PowerShell di AWS è incluso in [PowerShell Gallery](http://www.powershellgallery.com/packages/AWSPowerShell/) e può essere aggiunto facilmente a un account di automazione con il pulsante **Deploy to Azure Automation**.

![Importazione del modulo di PowerShell di AWS in AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "Importazione del modulo di PowerShell di AWS in AA")

Facendo clic su **Deploy to Azure Automation** viene aperta la pagina di accesso di Azure e dopo aver fornito le credenziali verrà visualizzata la schermata seguente.

![Importazione guidata del modulo di AWS](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Importazione guidata del modulo di AWS")

Selezionare un account di automazione nuovo o esistente. Si noti che non è presente una casella a discesa per un account esistente. È quindi necessario evitare errori di digitazione e assicurarsi che l'area e il gruppo di risorse per l'account di automazione siano corretti. Esaminare gli altri passaggi per completare la configurazione e quindi fare clic su **Crea**.

Passando all'account di automazione selezionato e quindi a **Asset** - **Moduli**, ora è possibile visualizzare il modulo di AWS con 1427 attività.

>[AZURE.NOTE] L'importazione di un modulo di PowerShell in Automazione di Azure è costituita da due passaggi:
>
> 1.  Importazione del modulo
> 2.  Estrazione dei cmdlet
>
>Le attività saranno visibili unicamente al termine dell'importazione del modulo e dell'estrazione dei cmdlet. Queste operazioni possono richiedere alcuni minuti.

## Effettuare il provisioning di runbook della macchina virtuale di AWS

Ora che tutti i prerequisiti sono soddisfatti è possibile creare il runbook che effettuerà il provisioning di una macchina virtuale in AWS. Verrà anche illustrato come usare uno script di PowerShell nativo in Automazione di Azure anziché il flusso di lavoro PowerShell.

Altre informazioni sulle differenze tra PowerShell nativo e flusso di lavoro PowerShell sono disponibili [qui](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/). Informazioni sulla creazione di runbook grafici sono disponibili [qui](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/).

Lo script di PowerShell per il runbook può essere scaricato da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).

Per scaricare lo script è possibile aprire una sessione di PowerShell e digitare: **Save-Script -Name New-AwsVM -Path <percorso>**

Dopo aver salvato lo script di PowerShell è possibile aggiungerlo ad Automazione di Azure come runbook eseguendo queste operazioni:

In **Account di automazione** fare clic su **Runbook** e selezionare **Aggiungi Runbook**. Selezionare **Creazione rapida** (Crea un nuovo runbook), specificare un nome per il runbook e in **Tipo di runbook** selezionare **PowerShell**. Quindi fare clic su **Crea**.

![Creazione di un runbook di AWS](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "Creazione di un runbook di AWS")

Quando viene aperto l'editor di runbook, copiare e incollare lo script di PowerShell nell'area di creazione del runbook.

![immagine](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "immagine")

Tenere presente quanto segue quando si usa lo script di PowerShell scaricato:

-   Il runbook contiene alcuni valori predefiniti dei parametri, come indicato nella sezione #ToDo. Valutare tutti i valori predefiniti e apportare gli aggiornamenti necessari.
-   Assicurarsi che le credenziali di AWS siano archiviate in **Asset** come **AWScred**.
-   L'area da usare dipende dalla sottoscrizione di AWS. Nel portale di AWS in cui sono anche disponibili le credenziali di sicurezza, fare clic sulla freccia accanto al proprio account per verificare l'area.

![Area di AWS](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "Area di AWS")

-   Risalire all'[area](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) da specificare nel runbook. Ad esempio, l'area Stati Uniti occidentali (Oregon) si traduce in us-west-2.

![Aree di AWS](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "Aree di AWS")

-   Usare PowerShell ISE per ottenere un elenco di nomi di immagine, importare il modulo, eseguire l'autenticazione in AWS sostituendo **Get-AutomationPSCredential** nell'ambiente ISE con **$AwsCred = Get-Credential**. Verranno richieste le credenziali. Specificare i valori **Access Key ID** e **Secret Access Key** come nome utente e password. Vedere l'esempio seguente:


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


L'output sarà il seguente:

![Ottenere immagini di AWS](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "Ottenere immagini di AWS")

Copiare e incollare il nome dell'immagine scelta in una variabile di automazione a cui viene fatto riferimento nel runbook come **$InstanceType**. Nell'esempio viene usata una versione gratuita di AWS e per il runbook viene selezionato **t2.micro**.

Altre informazioni sui tipi di istanze di Amazon EC2 sono disponibili [qui](https://aws.amazon.com/ec2/instance-types/).

## Test del provisioning di runbook della macchina virtuale di AWS

Elenco di controllo preliminare:

-   Gli asset per l'autenticazione in AWS sono stati creati e denominati **AWScred**
-   Il modulo di PowerShell di AWS è stato importato in Automazione di Azure
-   È stato creato un nuovo runbook e i valori dei parametri sono stati verificati e aggiornati, se necessario
-   Le opzioni **Registra record dettagliati** e facoltativamente **Registra record di avanzamento** nelle impostazioni del runbook sono impostate su **Sì**

Per iniziare a usare il nuovo runbook, specificare il parametro **VMname**.

![Avvio del runbook New-AwsVM](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "Avvio del runbook New-AwsVM")

Dal momento che le opzioni **Registra record dettagliati** e **Registra record di avanzamento** sono abilitate, i **Flussi** di output vengono visualizzati in **Tutti i log**.

![Output del flusso](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Output del flusso")

Verificare in AWS:

![Macchina virtuale distribuita in una console di AWS](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "Macchina virtuale distribuita in una console di AWS")

La dimostrazione è terminata.

In questo articolo è stato fornito un esempio di utilizzo di Automazione di Azure per la gestione di cloud ibridi, ad esempio creando una macchina virtuale in Amazon Web Services e applicando un tag personalizzato per denominarla.

Buon lavoro.

Tiander.

<!---HONumber=AcomDC_0204_2016-->