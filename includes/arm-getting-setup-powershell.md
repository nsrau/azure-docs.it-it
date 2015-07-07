## Configurazione di PowerShell per i modelli di Gestione risorse
 
Prima di poter usare Azure PowerShell con Gestione risorse, è necessario verificare di disporre delle versioni corrette di Windows PowerShell e Azure PowerShell.

### Verificare le versioni di PowerShell

Verificare che la versione di Windows PowerShell in uso sia 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare il comando seguente al prompt dei comandi di Windows PowerShell.

	$PSVersionTable

Verrà visualizzato il tipo di informazioni seguente:

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


Verificare che il valore di **PSVersion** sia 3.0 o 4.0. In caso contrario, vedere [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

È anche necessario disporre di Azure PowerShell 0.9.0 o versione successiva. Se Azure PowerShell non è stato installato e configurato, fare clic [qui](powershell-install-configure.md) per le istruzioni.

Per verificare la versione di Azure PowerShell installata, usare il comando seguente al prompt dei comandi di Azure PowerShell.

	Get-Module azure | format-table version

Verrà visualizzato il tipo di informazioni seguente:

	Version
	-------
	0.9.0

Se non si dispone della versione 0.9.0 o di una versione successiva, è necessario rimuovere Azure PowerShell tramite Programmi e funzionalità del Pannello di controllo e quindi installare la versione più recente. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

### Impostare l'account e la sottoscrizione di Azure

Se non si dispone già di una sottoscrizione di Azure, è possibile attivare i [benefici della sottoscrizione MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

Aprire un prompt dei comandi di Azure PowerShell e usare il comando seguente per accedere ad Azure.

	Add-AzureAccount

Se si dispone di più sottoscrizioni di Azure, è possibile usare il comando seguente per visualizzarne l'elenco.

	Get-AzureSubscription

Verrà visualizzato il tipo di informazioni seguente:

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Per impostare la sottoscrizione di Azure corrente, eseguire i comandi seguenti al prompt dei comandi di Azure PowerShell. Sostituire tutti gli elementi all'interno delle virgolette, inclusi i caratteri < and >, con il nome corretto.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Per altre informazioni su sottoscrizioni e account di Azure, vedere [Procedura: Connettersi alla sottoscrizione](powershell-install-configure.md#Connect).

### Passare al modulo Gestione risorse di Azure

Per poter usare il modulo Gestione risorse di Azure, è necessario passare dal set predefinito di comandi di Azure al set di comandi di Gestione risorse. Eseguire il comando seguente.

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Per tornare al set di comandi predefinito, usare il comando **Switch-AzureMode AzureServiceManagement**.

<!---HONumber=62-->