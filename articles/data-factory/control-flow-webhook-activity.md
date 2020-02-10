---
title: Attività webhook in Azure Data Factory
description: L'attività webhook non continua l'esecuzione della pipeline fino a quando non convalida il set di dati collegato con determinati criteri specificati dall'utente.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 70c67a99274eaedc5592c7b90b1ef80a3a17acf8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109996"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Attività webhook in Azure Data Factory
È possibile usare un'attività webhook per controllare l'esecuzione delle pipeline tramite il codice personalizzato. Usando l'attività webhook, i clienti possono chiamare un endpoint e passare un URL di callback. L'esecuzione della pipeline attende che il callback venga richiamato prima di procedere all'attività successiva.

## <a name="syntax"></a>Sintassi

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>Proprietà del tipo



Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività Hook Web | String | Sì |
type | Deve essere impostato su **webhook**. | String | Sì |
metodo | Metodo API REST per l'endpoint di destinazione. | Stringa. Tipi supportati:' POST ' | Sì |
url | Endpoint e percorso di destinazione | Stringa (o espressione con l'elemento resultType della stringa). | Sì |
intestazioni | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: "headers": {"Accept-Language": "en-US", "Content-Type": "application/json"}. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. "headers": {"Content-Type": "application/json"} |
Corpo | Rappresenta il payload inviato all'endpoint. | JSON valido (o espressione con resultType JSON). Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Sì |
autenticazione | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No |
timeout | Tempo di attesa dell'attività per la &#39;richiamata&#39; del callBackUri. Per quanto tempo l'attività attenderà che venga richiamato ' callBackUri '. Il valore predefinito è 10mins ("00:10:00"). Il formato è TimeSpan, ovvero d. hh: mm: SS | String | No |
Segnala stato del callback | Consente all'utente di segnalare lo stato di errore dell'attività webhook che contrassegna l'attività come non riuscita | Boolean | No |

## <a name="authentication"></a>Autenticazione

Di seguito sono riportati i tipi di autenticazione supportati nell'attività webhook.

### <a name="none"></a>None

Se l'autenticazione non è necessaria, non includere la proprietà "authentication".

### <a name="basic"></a>Basic

Specificare il nome utente e la password da usare per l'autenticazione di base.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificato client

Specificare il contenuto con codifica Base64 di un file PFX e la password.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Identità gestita

Specificare l'URI di risorsa per cui verrà richiesto il token di accesso usando l'identità gestita per la data factory. Per chiamare l'API di gestione delle risorse di Azure, usare `https://management.azure.com/`. Per altre informazioni sul funzionamento delle identità gestite, consultare la [pagina di panoramica sulle identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Se la data factory è configurata con un repository git, è necessario archiviare le credenziali in Azure Key Vault per usare l'autenticazione di base o del certificato client. Azure Data Factory non archivia le password in git.

## <a name="additional-notes"></a>Note aggiuntive

Azure Data Factory passerà una proprietà aggiuntiva "callBackUri" nel corpo all'endpoint URL e si aspetterà che questo URI venga richiamato prima del valore di timeout specificato. Se l'URI non viene richiamato, l'attività avrà esito negativo con stato "timeout".

L'attività del webhook ha esito negativo quando la chiamata all'endpoint personalizzato ha esito negativo. È possibile aggiungere qualsiasi messaggio di errore nel corpo del callback e usarlo in un'attività successiva.

Il corpo passato all'URI di callback deve essere JSON valido. È necessario impostare l'intestazione Content-Type su `application/json`.

Quando si usa l'opzione "segnala stato su callback", è necessario aggiungere il frammento di codice seguente al corpo quando si effettua il callback:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Passaggi successivi

Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
