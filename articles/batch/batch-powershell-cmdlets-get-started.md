<properties
   pageTitle="Introduzione a PowerShell per Azure Batch | Microsoft Azure"
   description="Breve introduzione ai cmdlet di Azure PowerShell che è possibile usare per gestire il servizio Azure Batch"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/13/2015"
   ms.author="danlep"/>

# Guida introduttiva ai cmdlet Batch di Azure PowerShell
Questo articolo contiene una rapida introduzione ai cmdlet di Azure PowerShell che è possibile usare per gestire gli account Batch e ottenere informazioni su processi, attività Batch e altri dettagli.

Per la sintassi dettagliata dei cmdlet, digitare `get-help <Cmdlet_name>` o vedere le [informazioni di riferimento per i cmdlet di Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

[AZURE.INCLUDE [powershell-preview-include](../../includes/powershell-preview-include.md)]

## Prerequisiti

* **Azure PowerShell**: i cmdlet Batch sono inclusi nel modulo Gestione risorse di Azure. Per i prerequisiti, le istruzioni di installazione e l'utilizzo di base, vedere [Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/mt125356.aspx).



* **Eseguire la registrazione con lo spazio dei nomi del provider Batch (operazione occasionale)**: prima di poter gestire gli account Batch, è necessario eseguire la registrazione con lo spazio dei nomi del provider Batch. Solo questa operazione deve essere eseguita una volta per ogni sottoscrizione.

    ```
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch
    ```

## Gestire gli account e le chiavi Batch


### Creare un account Batch

**New-AzureRmBatchAccount** crea un nuovo account Batch in un gruppo di risorse specificato. Se non si ha già di un gruppo di risorse, crearne uno eseguendo il cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) specificando una delle aree di Azure nel parametro **Località**, ad esempio "Stati Uniti centrali". Ad esempio:

```
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Creare quindi un nuovo account Batch nel gruppo di risorse, specificando un nome account per <*account\_name*> e una località in cui è disponibile il servizio Batch. La creazione dell'account può richiedere alcuni minuti. Ad esempio:

```
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Il nome dell'account Batch deve essere univoco in Azure e contenere tra 3 e 24 caratteri (sono ammessi solo numeri e lettere minuscole).

### Ottenere le chiavi di accesso all'account
**Get-AzureRmBatchAccountKeys** mostra le chiavi di accesso associate a un account Azure Batch. Ad esempio, eseguire le operazioni seguenti per ottenere le chiavi primarie e secondarie dell'account creato.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### Generare una nuova chiave di accesso
**New-AzureRmBatchAccountKey** genera una nuova chiave dell'account primaria o secondaria per un account Azure Batch. Ad esempio, per generare una nuova chiave primaria per l'account Batch, digitare:

```
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Per generare una nuova chiave secondaria, specificare "Secondary" per il parametro **KeyType**. È necessario rigenerare la chiave primaria e quella secondaria separatamente.

### Eliminare un account Batch
**Remove-AzureRmBatchAccount** elimina un account Batch. Ad esempio:

```
Remove-AzureRmBatchAccount -AccountName <account_name>
```

Quando richiesto, confermare che si desidera rimuovere l'account. La rimozione di un account può richiedere alcuni minuti.

## Query per processi, attività e altri dettagli

Usare cmdlet come **Get-AzureBatchJob**, **Get-AzureBatchTask** e **Get-AzureBatchPool** per eseguire query per le entità create con un account Batch.

Per usare questi cmdlet, è necessario innanzitutto creare un oggetto AzureBatchContext per memorizzare il nome e le chiavi dell'account:

```
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

È possibile trasferire questo contesto in cmdlet che interagiscono con il servizio Batch usando il parametro **BatchContext**.

> [AZURE.NOTE]Per impostazione predefinita, la chiave primaria dell'account viene usata per l'autenticazione, ma è possibile selezionare in modo esplicito la chiave da usare modificando la proprietà **KeyInUse** dell'oggetto BatchAccountContext: `$context.KeyInUse = "Secondary"`.


### Eseguire query per ottenere dati

Ad esempio, usare **Get-AzureBatchPools** per trovare i pool. Per impostazione predefinita, questo comando esegue una query per tutti i pool dell'account, presupponendo che l'oggetto BatchAccountContext sia già archiviato in *$context*:

```
Get-AzureBatchPool -BatchContext $context
```
### Usare un filtro OData

È possibile fornire un filtro OData tramite il parametro **Filter** per trovare solo gli oggetti a cui si è interessati. Ad esempio, è possibile trovare tutti i pool con nomi che iniziano con "myPool":

```
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Questo metodo non è flessibile come l'uso di "Where-Object" in una pipeline locale. Tuttavia, la query viene inviata al servizio Batch direttamente e quindi tutti i filtri vengono applicati sul lato server, consentendo un risparmio della larghezza di banda Internet.

### Usare il parametro Id

In alternativa a un filtro OData, è possibile usare il parametro **Id**. Per eseguire una query per un pool specifico con Id "myPool":

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
Il parametro **Id** supporta solo la ricerca di ID completi, senza caratteri jolly o filtri di tipo OData.

### Utilizzare la pipeline

I cmdlet Batch possono usare la pipeline di PowerShell per inviare dati tra i cmdlet. Questo equivale a specificare un parametro, ma rende più semplice la visualizzazione di più entità. Ad esempio, con la stringa seguente è possibile trovare tutte le attività dell'account:

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Usare il parametro MaxCount

Per impostazione predefinita, ogni cmdlet restituisce un massimo di 1000 oggetti. Se si raggiunge questo limite, perfezionare il filtro in modo da restituire meno oggetti o impostare esplicitamente un limite massimo tramite il parametro **MaxCount**. Ad esempio:

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

Per rimuovere il limite superiore, impostare <1>MaxCount</1> su 0 o un valore inferiore.

## Argomenti correlati
* [Download di Azure PowerShell](http://go.microsoft.com/?linkid=9811175)
* [Come installare e configurare Azure PowerShell](../powershell-install-configure.md)
* [Informazioni di riferimento sui cmdlet di Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Eseguire query sul servizio Azure Batch in modo efficiente](batch-efficient-list-queries.md)

<!---HONumber=Oct15_HO4-->