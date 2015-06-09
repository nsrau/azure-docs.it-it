<properties
	pageTitle="Guida introduttiva ai cmdlet Batch di Azure PowerShell"
	description="Introduzione ai cmdlet di Azure PowerShell usati per gestire il servizio Batch di Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Guida introduttiva ai cmdlet Batch di Azure PowerShell
Questo articolo contiene una rapida introduzione ai cmdlet di Azure PowerShell che è possibile usare per gestire gli account Batch e ottenere informazioni su elementi di lavoro, processi e attività Batch.

Per la sintassi dettagliata dei cmdlet, digitare ```get-help <Cmdlet_name>```.


## Prerequisiti

* **Anteprima Batch**: iscriversi ad [Anteprima Batch](https://account.windowsazure.com/PreviewFeatures), se non lo si è già fatto, per usare il servizio.
* **Azure PowerShell**: vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per i prerequisiti e per istruzioni di download e installazione. I cmdlet Batch sono stati introdotti nella versione 0.8.10 e successive.

## Usare i cmdlet Batch

Usare le procedure standard per avviare Azure PowerShell e [connettersi alle sottoscrizioni di Azure](../powershell-install-configure.md#Connect). Inoltre:

* **Selezionare la sottoscrizione di Azure**: se si dispone di più di una sottoscrizione, selezionare la sottoscrizione a cui è stata aggiunta la funzionalità di anteprima Batch:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Passare alla modalità AzureResourceManage**: i cmdlet Batch sono inclusi nel modulo Gestione risorse di Azure. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md). Per usare questo modulo, eseguire il cmdlet [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx):

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Gestire gli account e le chiavi Batch

È possibile usare i cmdlet di Azure PowerShell per creare e gestire chiavi e account Batch.

### Creare un account Batch

**New-AzureBatchAccount** crea un nuovo account Batch in un gruppo di risorse specificato. Se non si dispone già di un gruppo di risorse, crearne uno eseguendo il cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), specificando una delle aree di Azure nel parametro **Location**. È possibile trovare le aree disponibili per le diverse risorse di Azure eseguendo [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx). Ad esempio:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Quindi, creare un nuovo account Batch nel gruppo di risorse, specificando un nome account per <*account_name*> e una posizione in cui è disponibile il servizio Batch. La creazione dell'account può richiedere alcuni minuti. Ad esempio:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Il nome dell'account Batch deve essere univoco in Azure e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).

### Ottenere le chiavi di accesso all'account
**Get AzureBatchAccountKeys** mostra le chiavi di accesso associate a un account Batch di Azure. Ad esempio, eseguire le operazioni seguenti per ottenere le chiavi primarie e secondarie dell'account creato.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Generare una nuova chiave di accesso
**New-AzureBatchAccountKey** genera una nuova chiave account primaria o secondaria per un account Batch di Azure. Ad esempio, per generare una nuova chiave primaria per l'account Batch, digitare:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Per generare una nuova chiave secondaria, specificare "Secondary" per il parametro **KeyType**. È necessario rigenerare la chiave primaria e quella secondaria separatamente.

### Eliminare un account Batch
**Remove-AzureBatchAccount** elimina un account Batch. Ad esempio:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Quando richiesto, confermare che si desidera rimuovere l'account. La rimozione di un account può richiedere del tempo.

## Query per elementi di lavoro, processi e attività

Usare cmdlet come **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** e **Get-AzureBatchPool** per eseguire query per le entità create con un account Batch.

Per usare questi cmdlet, è necessario innanzitutto creare un oggetto AzureBatchContext per memorizzare il nome e le chiavi dell'account:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

È possibile trasferire questo contesto in cmdlet che interagiscono con il servizio Batch usando il parametro **BatchContext**.

> [AZURE.NOTE]Per impostazione predefinita, la chiave primaria dell'account viene usata per l'autenticazione, ma è possibile selezionare in modo esplicito la chiave da usare modificando la proprietà **KeyInUse** dell'oggetto BatchAccountContext: ```$context.KeyInUse = "Secondary"```.


### Eseguire query per ottenere dati

Come esempio, usare **Get-AzureBatchWorkItem** per trovare gli elementi di lavoro. Per impostazione predefinita, questo comando esegue una query per tutti gli elementi di lavoro dell'account, presupponendo che l'oggetto BatchAccountContext sia già archiviato in *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

La stessa operazione può essere eseguita per altre entità, ad esempio i pool:

```
Get-AzureBatchPool -BatchContext $context
```
### Usare un filtro OData

È possibile fornire un filtro OData tramite il parametro **Filter** per trovare solo gli oggetti a cui si è interessati. Ad esempio, è possibile trovare tutti gli elementi di lavoro con nomi che iniziano con "myWork":

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

Questo metodo non è flessibile come l'uso di "Where-Object" in una pipeline locale. Tuttavia, la query viene inviata al servizio Batch direttamente e quindi tutti i filtri vengono applicati sul lato server, consentendo un risparmio della larghezza di banda Internet.

### Usare il parametro Name

In alternativa a un filtro OData, è possibile usare il parametro **Name**. Per eseguire una query per un elemento di lavoro specifico chiamato "myWorkItem":

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
Il parametro **Name** supporta solo la ricerca dei nomi completi, senza caratteri jolly o filtri di tipo OData.

### Usare la pipeline

I cmdlet Batch possono usare la pipeline di PowerShell per inviare dati tra i cmdlet. Questo equivale a specificare un parametro, ma rende più semplice la visualizzazione di più entità. Ad esempio, è possibile trovare tutte le attività dell'account:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### Usare il parametro MaxCount

Per impostazione predefinita, ogni cmdlet restituisce un massimo di 1000 oggetti. Se si raggiunge questo limite, è possibile perfezionare il filtro in modo da restituire meno oggetti o impostare esplicitamente un limite massimo tramite il parametro **MaxCount**. Ad esempio:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

Per rimuovere il limite superiore, impostare **MaxCount** su 0 o un valore inferiore.

## Argomenti correlati
* [Panoramica tecnica di Batch](batch-technical-overview.md)
* [Download di Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->