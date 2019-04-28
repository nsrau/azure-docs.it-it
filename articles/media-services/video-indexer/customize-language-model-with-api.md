---
title: Usare le API di Video Indexer per personalizzare un modello linguistico - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello linguistico con le API di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553743"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Personalizzare un modello linguistico con le API di Video Indexer

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute. 

Per una panoramica dettagliata e le procedure consigliate per i modelli linguistici personalizzati, vedere [Personalizzare un modello linguistico con Video Indexer](customize-language-model-overview.md).

È possibile usare le API di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È possibile anche usare il sito Web, come descritto in [Personalizzare il modello linguistico usando il sito Web di Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

Il comando seguente crea un nuovo modello linguistico personalizzato nell'account specificato. È possibile caricare i file per il modello linguistico in questa chiamata. In alternativa, è possibile creare il modello linguistico qui e caricare i file per il modello in un secondo momento aggiornando il modello linguistico.

> [!NOTE]
> È comunque necessario eseguire il training del modello con i relativi file abilitati per fare in modo che il modello possa apprendere il contenuto dei file. La sezione successiva contiene le indicazioni per eseguire il training di una lingua.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|string|Sì|Identificatore univoco globale per l'account|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|
|modelName|string|Sì|Il nome per il modello linguistico|
|Linguaggio|string|Sì|La lingua del modello linguistico. <br/>Al parametro **language** deve essere assegnata la lingua in formato BCP-47 di 'language tag-region', ad esempio 'en-US'. Sono supportate le lingue: inglese (en-US), tedesco (de-DE), spagnolo (es-SP), arabo (ar-EG), francese (fr-FR), hindi (hi-HI), italiano (it-IT), giapponese (ja-JP), portoghese (pt-BR), russo (ru-RU) e cinese (zh-CN).  |

### <a name="request-body"></a>Corpo della richiesta

Per caricare i file da aggiungere al modello linguistico, è necessario caricare i file nel corpo usando i dati del modulo, oltre a fornire i valori per i parametri obbligatori precedenti. A questo scopo è possibile procedere in due modi: 

1. La chiave sarà il nome del file e il valore sarà il file txt.
2. La chiave sarà il nome del file e il valore sarà un URL al file txt.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico appena creato, insieme ai metadati su ogni file di modello, seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Eseguire il training di un modello linguistico

Il comando seguente esegue il training di un modello linguistico personalizzato nell'account specificato con il contenuto nei file caricati e abilitati nel modello linguistico. 

> [!NOTE]
> È necessario prima creare il modello linguistico e caricare i relativi file. È possibile caricare i file quando si crea o si aggiorna il modello linguistico. 

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|L'ID del modello linguistico (generato al momento della creazione del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico di cui è stato appena eseguito il training, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

È quindi necessario usare il valore **id** del modello linguistico per il parametro **linguisticModelId** quando si [carica un video da indicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) e per il parametro **languageModelId** quando si [reindicizza un video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

Il comando seguente elimina un modello linguistico personalizzato dall'account specificato. Qualsiasi video che usava il modello linguistico eliminato manterrà lo stesso indice finché non si reindicizza il video. Se si reindicizza il video, è possibile assegnare un nuovo modello linguistico al video. In alternativa, verrà usato il modello predefinito per reindicizzare il video.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta DELETE.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|L'ID del modello linguistico (generato al momento della creazione del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

Quando l'eliminazione del modello linguistico viene completata, non viene restituito alcun contenuto.

## <a name="update-a-language-model"></a>Aggiornare un modello linguistico

Il comando seguente aggiorna un nuovo modello linguistico personalizzato nell'account specificato.

> [!NOTE]
> È necessario aver già creato il modello linguistico. È possibile usare questa chiamata per abilitare o disabilitare tutti i file nel modello, aggiornare il nome del modello linguistico e caricare i file da aggiungervi.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|L'ID del modello linguistico (generato al momento della creazione del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|
|modelName|string|No |Nuovo nome che è possibile assegnare al modello|
|enable|boolean|No |Scegliere se tutti i file in questo modello sono abilitati (true) oppure disabilitati (false)|

### <a name="request-body"></a>Corpo della richiesta

Per caricare i file da aggiungere al modello linguistico, è necessario caricare i file nel corpo usando i dati del modulo, oltre a fornire i valori per i parametri obbligatori precedenti. A questo scopo è possibile procedere in due modi: 

1. La chiave sarà il nome del file e il valore sarà il file txt.
2. La chiave sarà il nome del file e il valore sarà un URL al file txt.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico di cui è stato appena eseguito il training, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
È possibile usare l'**id** dei file restituito qui per scaricare il contenuto del file.

## <a name="update-a-file-from-a-language-model"></a>Aggiornare un file da un modello linguistico

Il comando seguente consente di aggiornare il nome e **abilitare** lo stato di un file in un modello linguistico personalizzato nell'account specificato.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountId|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|ID del modello linguistico che contiene il file (generato al momento della creazione del modello linguistico)|
|fileId|string|Sì|ID del file in fase di aggiornamento (generato quando il file viene caricato al momento della creazione o dell'aggiornamento del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|
|fileName|string|No |Nome a cui aggiornare il nome del file.|
|enable|boolean|No |Aggiornare se questo file è abilitato (true) oppure disabilitato (false) nel modello linguistico|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul file che è stato aggiornato seguendo il formato di output JSON di esempio riportato di seguito.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
È possibile usare l'**id** del file restituito qui per scaricare il contenuto del file.

## <a name="get-a-specific-language-model"></a>Ottenere un modello linguistico specifico

Il comando seguente restituisce informazioni sul modello linguistico specificato nell'account specificato, come la lingua e i file presenti nel modello linguistico. 

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>Parametri e corpo della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|L'ID del modello linguistico (generato al momento della creazione del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta fornisce i metadati sul modello linguistico specificato, insieme ai metadati su ogni file di modello seguendo il formato dell'output JSON di esempio di seguito.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

È possibile usare l'**id** del file restituito qui per scaricare il contenuto del file.

## <a name="get-all-the-language-models"></a>Ottenere tutti i modelli linguistici

Il comando seguente restituisce tutti i modelli linguistici personalizzati nell'account specificato in un elenco.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>Parametri della richiesta

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta restituisce un elenco di tutti i modelli linguistici nell'account e ognuno dei relativi metadati e file seguendo il formato dell'output JSON riportato di seguito.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Eliminare un file da un modello linguistico

Il comando seguente elimina il file specificato dal modello linguistico personalizzato dall'account specificato. 

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta DELETE.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|ID del modello linguistico che contiene il file (generato al momento della creazione del modello linguistico)|
|fileId|string|Sì|ID del file in fase di aggiornamento (generato quando il file viene caricato al momento della creazione o dell'aggiornamento del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

Non viene restituito contenuto quando il file viene eliminato con successo dal modello linguistico.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Ottenere i metadati su un file da un modello linguistico

Restituisce il contenuto e i metadati sul file specificato dal modello linguistico scelto nell'account.

### <a name="request-url"></a>URL richiesta

Si tratta di una richiesta GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|ID del modello linguistico che contiene il file (generato al momento della creazione del modello linguistico)|
|fileId|string|Sì|ID del file in fase di aggiornamento (generato quando il file viene caricato al momento della creazione o dell'aggiornamento del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta restituisce il contenuto e metadati del file in formato JSON, simile al seguente:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Il contenuto di questo file di esempio sono le parole "hello" e "world"in due righe distinte.

## <a name="download-a-file-from-a-language-model"></a>Scaricare un file da un modello linguistico

Il comando seguente scarica un file di testo che include il contenuto del file specificato dal modello linguistico specificato nell'account specificato. Questo file di testo dovrebbe corrispondere al contenuto del file di testo caricato in origine.

### <a name="request-url"></a>URL richiesta
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Di seguito è riportata la richiesta in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Vedere i parametri obbligatori e testare usando il portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>Parametri della richiesta 

|**Nome**|**Tipo**|**Obbligatorio**|**Descrizione**|
|---|---|---|---|
|location|string|Sì|L'area di Azure a cui deve essere instradata la chiamata. Per altre informazioni, vedere [Aree di Azure e Video Indexer](regions.md).|
|accountID|string|Sì|Identificatore univoco globale per l'account|
|modelId|string|Sì|ID del modello linguistico che contiene il file (generato al momento della creazione del modello linguistico)|
|fileId|string|Sì|ID del file in fase di aggiornamento (generato quando il file viene caricato al momento della creazione o dell'aggiornamento del modello linguistico)|
|accessToken|string|Sì|Token di accesso (deve essere di ambito [Token di accesso all'account](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) per l'autenticazione con la chiamata. I token di accesso scadono entro 1 ora.|

### <a name="request-body"></a>Corpo della richiesta 

Per questa chiamata non è necessario specificare altri dati nel corpo della richiesta.

### <a name="response"></a>Risposta

La risposta sarà il download di un file di testo con il contenuto del file in formato JSON. 

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello linguistico usando il sito Web](customize-language-model-with-website.md)
