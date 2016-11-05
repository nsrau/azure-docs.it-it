---
title: Gestire gli account di Servizi multimediali di Azure con PowerShell
description: Informazioni su come gestire gli account di Servizi multimediali di Azure con i cmdlet di PowerShell.
author: Juliako
manager: erikre
editor: ''
services: media-services
documentationcenter: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2016
ms.author: juliako

---
# Gestire gli account di Servizi multimediali di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)
> 
> [!NOTE]
> Per poter creare un account di Servizi multimediali di Azure, è necessario un account di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">versione di valutazione gratuita di Azure</a>.
> 
> 

## Overview
Questo articolo fornisce un elenco dei cmdlet di Azure PowerShell per Servizi multimediali di Azure (AMS) nel framework di Azure Resource Manager. I cmdlet esistono nello spazio dei nomi **Microsoft.Azure.Commands.Media**.

## Versioni
**ApiVersion**: "2015-10-01"

## New-AzureRmMediaService
Crea un servizio multimediale.

### Sintassi
Set di parametri: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Set di parametri: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | Nome |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |1 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

**-Location &lt;String&gt;**

Specifica la posizione della risorsa del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |2 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-StorageAccountId &lt;String&gt;**

Specifica un account di archiviazione primario associato al servizio multimediale.

* È supportato solo il nuovo account di archiviazione creato con l'API di Resource Manager.
* L'account di archiviazione deve esistere e ha la stessa posizione del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |3 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |StorageAccountIdParamSet |
| Caratteri jolly accettati? |false |

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Specifica gli account di archiviazione associati al servizio multimediale.

* È supportato solo il nuovo account di archiviazione creato con l'API di Resource Manager.
* L'account di archiviazione deve esistere e ha la stessa posizione del servizio multimediale.
* È possibile specificare come primario un solo account di archiviazione.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |3 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |StorageAccountsParamSet |
| Caratteri jolly accettati? |false |

**-Tags &lt;Hashtable&gt;**

Specifica una tabella hash dei tag associati al servizio multimediale.

* Esempio: @{"tag1"="value1";"tag2"=:value2"}

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione? |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Set-AzureRmMediaService
Aggiorna un servizio multimediale.

### Sintassi
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Accettare input da pipeline? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | Nome |
| --- | --- |
| Obbligatorio? |True |
| Posizione? |1 |
| Valore predefinito |None |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |False |

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Specifica gli account di archiviazione associati al servizio multimediale.

* È supportato solo il nuovo account di archiviazione creato con l'API di Resource Manager.
* L'account di archiviazione deve esistere e ha la stessa posizione del servizio multimediale.
* È possibile specificare come primario un solo account di archiviazione.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |false |
| Posizione? |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |StorageAccountsParamSet |
| Caratteri jolly accettati? |false |

**-Tags &lt;Hashtable&gt;**

Specifica una tabella hash dei tag associati a questo servizio multimediale.

* I tag associati al servizio multimediale vengono sostituiti con il valore specificato dal cliente.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |False |
| Posizione? |denominata |
| Valore predefinito |None |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Remove-AzureRmMediaService
Rimuove un servizio multimediale.

### Sintassi
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |2 |
| Valore predefinito |None |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |False |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Get-AzureRmMediaService
Ottiene tutti i servizi multimediali in un gruppo di risorse o un servizio multimediale con un nome specificato.

### Sintassi
Set di parametri: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

Set di parametri: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |ResourceGroupParameterSet, AccountNameParameterSet |

Caratteri jolly accettati? false

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |1 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |AccountNameParameterSet |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Get-AzureRmMediaServiceKeys
Ottiene le chiavi di un servizio multimediale.

### Sintassi
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |1 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Set-AzureRmMediaServiceKey
Rigenera una chiave primaria o secondaria di un servizio multimediale.

### Sintassi
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |1 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-KeyType &lt;KeyType&gt;**

Specifica il tipo di chiave del servizio multimediale.

* Primaria o secondaria

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |2 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |false |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Sync-AzureRmMediaServiceStorageKeys
Sincronizza le chiavi dell'account di archiviazione per un account di archiviazione associato al servizio multimediale.

### Sintassi
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-AccountName &lt;String&gt;**

Specifica il nome del servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |1 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**-StorageAccountId &lt;String&gt;**

Specifica l'account di archiviazione associato al servizio multimediale.

| Alias | ID |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |2 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## Passaggio successivo
Vedere i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->