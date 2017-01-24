---
title: Gestire gli account di Servizi multimediali di Azure con PowerShell
description: Informazioni su come gestire gli account di Servizi multimediali di Azure con i cmdlet di PowerShell.
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>Gestire gli account di Servizi multimediali di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Per poter creare un account di Servizi multimediali di Azure, è necessario un account di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">versione di valutazione gratuita di Azure</a>.
> 
> 

## <a name="overview"></a>Panoramica
Questo articolo fornisce un elenco dei cmdlet di Azure PowerShell per Servizi multimediali di Azure (AMS) nel framework di Azure Resource Manager. I cmdlet esistono nello spazio dei nomi **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versioni
**ApiVersion**:   "2015-10-01"

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
Crea un servizio multimediale.

### <a name="syntax"></a>Sintassi
Set di parametri: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Set di parametri: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

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

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
Aggiorna un servizio multimediale.

### <a name="syntax"></a>Sintassi
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |False |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

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
| Obbligatorio? |false |
| Posizione? |denominata |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |false |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
Rimuove un servizio multimediale.

### <a name="syntax"></a>Sintassi
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Caratteri jolly accettati? |False |

**&lt;CommandParameters&gt;**

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
Ottiene tutti i servizi multimediali in un gruppo di risorse o un servizio multimediale con un nome specificato.

### <a name="syntax"></a>Sintassi
Set di parametri: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

Set di parametri: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri
**-ResourceGroupName &lt;String&gt;**

Specifica il nome del gruppo di risorse a cui appartiene questo servizio multimediale.

| Alias | nessuno |
| --- | --- |
| Obbligatorio? |true |
| Posizione? |0 |
| Valore predefinito |nessuno |
| Input pipeline accettato? |true(ByPropertyName) |
| Nome del set di parametri |ResourceGroupParameterSet, AccountNameParameterSet |

Caratteri jolly accettati?   false

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

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
Ottiene le chiavi di un servizio multimediale.

### <a name="syntax"></a>Sintassi
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
Rigenera una chiave primaria o secondaria di un servizio multimediale.

### <a name="syntax"></a>Sintassi
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
Sincronizza le chiavi dell'account di archiviazione per un account di archiviazione associato al servizio multimediale.

### <a name="syntax"></a>Sintassi
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri
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

### <a name="inputs"></a>Input
Il tipo di input è il tipo degli oggetti che è possibile inviare tramite pipe al cmdlet.

### <a name="outputs"></a>Output
Il tipo di output è il tipo degli oggetti generati dal cmdlet.

## <a name="next-step"></a>Passaggio successivo
Vedere i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO5-->


