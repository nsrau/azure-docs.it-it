<properties
	pageTitle="Registrazione dell'insieme di credenziali delle chiavi di Azure| Microsoft Azure"
	description="Usare questa esercitazione per un'introduzione alla registrazione dell'insieme di credenziali delle chiavi di Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/31/2016"
	ms.author="cabailey"/>

# Registrazione dell'insieme di credenziali delle chiavi di Azure #
L'insieme di credenziali delle chiavi di Azure è disponibile nella maggior parte delle aree. Per altre informazioni, vedere la [pagina Insieme di credenziali delle chiavi - Prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

## Introduzione  
Dopo aver creato una o più credenziali delle chiavi, può essere utile monitorare come, quando e da chi vengono usate. A questo scopo è possibile abilitare la registrazione per l'insieme di credenziali delle chiavi, che salva le informazioni in un account di archiviazione di Azure specificato. Per tale account di archiviazione viene creato automaticamente un contenitore denominato **insights-log-auditevent**, che può essere usato per raccogliere i log relativi a più insiemi di credenziali delle chiavi.

È possibile accedere alle informazioni di registrazione dopo un massimo di 10 minuti dall'operazione sull'insieme di credenziali delle chiavi, ma nella maggior parte dei casi si potrà farlo prima. La gestione dei log nell'account di archiviazione è compito dell'utente:

- Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
- Eliminare i log che non è più necessario mantenere nell'account di archiviazione.

Usare questa esercitazione per un'introduzione all'uso della registrazione dell'insieme di credenziali delle chiavi di Azure, per creare l'account di archiviazione, abilitare la registrazione e interpretare le informazioni di registrazione raccolte.


>[AZURE.NOTE]  Questa esercitazione non include istruzioni per la creazione di insiemi di credenziali delle chiavi, chiavi o segreti. Per altre informazioni, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md). In alternativa, per le istruzioni relative all'interfaccia della riga di comando multipiattaforma, vedere [questa esercitazione equivalente](key-vault-manage-with-cli.md).
>
>Attualmente non è possibile configurare l'insieme di credenziali delle chiavi di Azure nel portale di Azure. Usare invece queste istruzioni per Azure PowerShell.

I log raccolti possono essere visualizzati con Log Analytics da Operations Management Suite. Per altre informazioni, vedere [Azure Key Vault (Preview) solution in Log Analytics](../log-analytics/log-analytics-azure-key-vault.md) (Soluzione Insieme di credenziali delle chiavi di Azure (anteprima) in Log Analytics).

Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Insieme di credenziali delle chiavi esistente e già in uso.
- Azure PowerShell **versione minima 1.0.1**. Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Se Azure PowerShell è già stato installato ma non si conosce la versione, dalla console di Azure PowerShell digitare `(Get-Module azure -ListAvailable).Version`.
- Spazio di archiviazione sufficiente in Azure per i log dell'insieme di credenziali delle chiavi.


## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:

    Login-AzureRmAccount

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

    Get-AzureRmSubscription

Per specificare quindi la sottoscrizione associata all'insieme di credenziali delle chiavi da registrare, digitare:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Creare un nuovo account di archiviazione per i log ##

Anche se è possibile usare un account di archiviazione esistente per i log, si creerà un nuovo account di archiviazione dedicato ai log dell'insieme di credenziali delle chiavi. Per praticità, si archivieranno i dettagli in una variabile denominata **sa**, in modo che siano disponibili quando sarà necessario specificarli in seguito.

Per rendere la gestione ancora più facile, si userà anche lo stesso gruppo di risorse che contiene l'insieme di credenziali delle chiavi. Dall'[esercitazione introduttiva](key-vault-get-started.md) questo gruppo di risorse è denominato **ContosoResourceGroup** e si continuerà a usare l'area geografica East Asia. Sostituire questi valori con i propri, a seconda dei casi:

	$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Se si sceglie di usare un account di archiviazione esistente, questo dovrà usare la stessa sottoscrizione dell'insieme di credenziali delle chiavi e il modello di distribuzione Resource Manager, anziché il modello di distribuzione classica.

## <a id="identify"></a>Identificare l'insieme di credenziali delle chiavi per i log ##

Nell'esercitazione introduttiva, il nome dell'insieme di credenziali delle chiavi è **ContosoKeyVault**, quindi si continuerà a usarlo e si archivieranno i dettagli in una variabile denominata **kv**:

	$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Abilitare la registrazione ##

Per abilitare la registrazione dell'insieme di credenziali delle chiavi, si userà il cmdlet Set-AzureRmDiagnosticSetting e le variabili create per il nuovo account di archiviazione e l'insieme di credenziali delle chiavi. Il flag **-Enabled** verrà impostato su **$true** e la categoria su AuditEvent, la sola disponibile per la registrazione dell'insieme di credenziali delle chiavi:


	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

L'output includerà:

	StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
	ServiceBusRuleId   :
	StorageAccountName :
		Logs
		Enabled           : True
		Category          : AuditEvent
		RetentionPolicy
		Enabled : False
		Days    : 0


Conferma che la registrazione è abilitata per l'insieme di credenziali delle chiavi. Le informazioni vengono salvate nell'account di archiviazione.

Facoltativamente è possibile impostare criteri di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, specificare i criteri di conservazione impostando il flag **-RetentionEnabled** su **$true** e il parametro **-RetentionInDays** su **90** per fare in modo che i log che risalgono a più di 90 giorni prima vengano eliminati automaticamente.

	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Informazioni registrate:

- Vengono registrate tutte le richieste API REST autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide.
- Operazioni sull'insieme di credenziali delle chiavi stesso, ad esempio creazione, eliminazione e impostazione di criteri di accesso all'insieme di credenziali delle chiavi, nonché aggiornamento degli attributi dell'insieme di credenziali delle chiavi, quali i tag.
- Operazioni su chiavi e segreti nell'insieme di credenziali delle chiavi, ad esempio creazione, modifica o eliminazione di chiavi o segreti. Operazioni quali accesso, verifica, crittografia, decrittografia, esecuzione e annullamento del wrapping delle chiavi, recupero di segreti, nonché elenco di chiavi e segreti e delle relative versioni.
- Richieste non autenticate che generano una risposta 401. Ad esempio, richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.


## <a id="access"></a>Accedere ai log ##

I log dell'insieme di credenziali delle chiavi vengono archiviati nel contenitore **insights-log-auditevent** nell'account di archiviazione specificato. Per elencare tutti i BLOB in questo contenitore, digitare:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

L'output sarà simile al seguente:

**URI del contenitore: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Name**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json**


Come si vede dall'output, i BLOB seguono una convenzione di denominazione: **resourceId=<ID risorsa ARM>/y=<anno>/m=<mese>/d=<giorno del mese>/h=<ora>/m=<minuto>/nomefile.json**

I valori di data e ora sono nel formato UTC.

Poiché si può usare lo stesso account di archiviazione per raccogliere i log per più risorse, l'ID della risorsa completo nel nome del BLOB è molto utile per accedere solo ai BLOB necessari o per scaricarli. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Creare prima di tutto una cartella per scaricare i BLOB. Ad esempio:

	New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Ottenere quindi un elenco di tutti i BLOB:

	$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Inviare l'elenco tramite pipe con 'Get-AzureStorageBlobContent' per scaricare i BLOB nella cartella di destinazione:

	$blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Quando si esegue questo secondo comando, il delimitatore **/** nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio:

- Se sono disponibili più insiemi di credenziali delle chiavi e si vogliono scaricare i log per un solo insieme di credenziali delle chiavi denominato CONTOSOKEYVAULT3, usare:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Per scaricare tutti i log per il mese di gennaio 2016, usare `-Blob '*/year=2016/m=01/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

A questo punto si può iniziare a osservare il contenuto dei log. Prima di continuare, ci sono altri due parametri di Get-AzureRmDiagnosticSetting che può essere necessario conoscere:

- Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa insieme di credenziali delle chiavi, usare: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Per disabilitare la registrazione per la risorsa insieme di credenziali delle chiavi, usare: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretare i log dell'insieme di credenziali delle chiavi ##

I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Questo è un esempio di voce di log generata dall'esecuzione di `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

	{
    	"records":
    	[
        	{
        	    "time": "2016-01-05T01:32:01.2691226Z",
        	    "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            	"operationName": "VaultGet",
            	"operationVersion": "2015-06-01",
            	"category": "AuditEvent",
            	"resultType": "Success",
            	"resultSignature": "OK",
            	"resultDescription": "",
            	"durationMs": "78",
            	"callerIpAddress": "104.40.82.76",
            	"correlationId": "",
            	"identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
            	"properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
        	}
    	]
	}


La tabella seguente elenca i nomi dei campi e le descrizioni.


| Nome campo | Descrizione |
| ------------- |-------------|
| time | Data e ora (in formato UTC).|
| resourceId | ID della risorsa Gestione risorse di Azure. Per i log dell'insieme di credenziali delle chiavi questo è sempre l'ID della risorsa insieme di credenziali delle chiavi.|
| operationName | Nome dell'operazione, come illustrato nella tabella seguente.|
| operationVersion | Questa è la versione dell'API REST richiesta dal client.|
| category | Per i log dell'insieme di credenziali delle chiavi, AuditEvent è il solo valore disponibile.|
| resultType | Risultato della richiesta API REST.|
| resultSignature | Stato HTTP.|
| resultDescription | Descrizione aggiuntiva del risultato, se disponibile.|
| durationMs | Tempo impiegato per soddisfare la richiesta API REST, in millisecondi. Non include la latenza di rete, quindi il tempo misurato sul lato client potrebbe non corrispondere a questo valore.|
| callerIpAddress | Indirizzo IP del client che ha eseguito la richiesta.|
| correlationId | GUID facoltativo che il client può passare per correlare i log sul lato client con quelli sul lato servizio (insieme di credenziali delle chiavi).|
| identity | Identità del token presentato al momento dell'esecuzione della richiesta API REST. In genere si tratta di un "utente", una "entità servizio" o una combinazione "utente+appId" come nel caso di una richiesta generata da un cmdlet di Azure PowerShell.|
| properties | Questo campo conterrà informazioni diverse in base all'operazione (operationName). Nella maggior parte dei casi contiene informazioni sul client (la stringa useragent passata dal client), l'URI esatto della richiesta API REST e il codice di stato HTTP. Quando un oggetto viene restituito come risultato di una richiesta, ad esempio KeyCreate o VaultGet, conterrà anche l'URI della chiave (come "id"), l'URI dell'insieme di credenziali o l'URI del segreto.|




I valori del campo **operationName** sono nel formato OggettoVerbo. Ad esempio:

- Tutte le operazioni sull'insieme di credenziali delle chiavi hanno il formato 'Vault`<action>`', ad esempio `VaultGet` e `VaultCreate`.

- Tutte le operazioni sulle chiavi hanno il formato 'Key`<action>`', ad esempio `KeySign` e `KeyList`.

- Tutte le operazioni sui segreti hanno il formato 'Secret`<action>`', ad esempio `SecretGet` e `SecretListVersions`.

La tabella seguente include un elenco di operationName con il comando API REST corrispondente.

| operationName | Comando API REST |
| ------------- |-------------|
| Authentication | Tramite l'endpoint di Azure Active Directory|
| VaultGet | [Ottenere informazioni su un insieme di credenziali delle chiavi](https://msdn.microsoft.com/it-IT/library/azure/mt620026.aspx)|
| VaultPut | [Creare o aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/it-IT/library/azure/mt620025.aspx)|
| VaultDelete | [Eliminare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/it-IT/library/azure/mt620022.aspx)|
| VaultPatch | [Aggiornare un insieme di credenziali delle chiavi](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList | [Elencare tutti gli insiemi di credenziali delle chiavi in un gruppo di risorse](https://msdn.microsoft.com/it-IT/library/azure/mt620027.aspx)|
| KeyCreate | [Creare una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn903634.aspx)|
| KeyGet | [Ottenere informazioni su una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878080.aspx)|
| KeyImport | [Importare una chiave in un insieme di credenziali](https://msdn.microsoft.com/it-IT/library/azure/dn903626.aspx)|
| KeyBackup | [Eseguire il backup di una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878058.aspx)|
| KeyDelete | [Eliminare una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn903611.aspx)|
| KeyRestore | [Ripristinare una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878106.aspx)|
| KeySign | [Firmare con una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878096.aspx)|
| KeyVerify | [Verificare con una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878082.aspx)|
| KeyWrap | [Eseguire il wrapping di una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878066.aspx)|
| KeyUnwrap | [Annullare il wrapping di una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878079.aspx)|
| KeyEncrypt | [Crittografare con una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878060.aspx)|
| KeyDecrypt | [Decrittografare con una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn878097.aspx)|
| KeyUpdate | [Aggiornare una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn903616.aspx)|
| KeyList | [Elencare le chiavi in un insieme di credenziali](https://msdn.microsoft.com/it-IT/library/azure/dn903629.aspx)|
| KeyListVersions | [Elencare le versioni di una chiave](https://msdn.microsoft.com/it-IT/library/azure/dn986822.aspx)|
| SecretSet | [Creare un segreto](https://msdn.microsoft.com/it-IT/library/azure/dn903618.aspx)|
| SecretGet | [Ottenere informazioni su un segreto](https://msdn.microsoft.com/it-IT/library/azure/dn903633.aspx)|
| SecretUpdate | [Aggiornare un segreto](https://msdn.microsoft.com/it-IT/library/azure/dn986818.aspx)|
| SecretDelete | [Eliminare un segreto](https://msdn.microsoft.com/it-IT/library/azure/dn903613.aspx)|
| SecretList | [Elencare i segreti in un insieme di credenziali](https://msdn.microsoft.com/it-IT/library/azure/dn903614.aspx)|
| SecretListVersions | [Elencare le versioni di un segreto](https://msdn.microsoft.com/it-IT/library/azure/dn986824.aspx)|




## <a id="next"></a>Passaggi successivi ##

Per un'esercitazione sull'uso dell'insieme di credenziali delle chiavi di Azure in un'applicazione Web, vedere [Usare l'insieme di credenziali delle chiavi di Azure da un'applicazione Web](key-vault-use-from-web-application.md).

Per i riferimenti alla programmazione, vedere [Guida per gli sviluppatori dell’insieme di credenziali chiave Azure](key-vault-developers-guide.md).

Per un elenco di cmdlet di Azure PowerShell 1.0 per l'insieme di credenziali delle chiavi di Azure, vedere [Cmdlet per l'insieme di credenziali delle chiavi di Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Per un'esercitazione sulla rotazione delle chiavi e il controllo dei log con l'insieme di credenziali delle chiavi di Azure, vedere [Come configurare l'insieme di credenziali delle chiavi con rotazione e controllo delle chiavi end-to-end](key-vault-key-rotation-log-monitoring.md).

<!---HONumber=AcomDC_0907_2016-->