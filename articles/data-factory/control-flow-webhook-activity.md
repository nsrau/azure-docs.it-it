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
ms.openlocfilehash: 3734472d9026d4e355c08b36d5ba58974288daac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678219"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Attività webhook in Azure Data Factory
È possibile usare un'attività Hook Web per controllare l'esecuzione delle pipeline tramite il codice personalizzato. Usando l'attività webhook, i clienti possono chiamare un endpoint e passare un URL di callback. L'esecuzione della pipeline attende che il callback venga richiamato prima di procedere all'attività successiva.

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



Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
name | Nome dell'attività Hook Web | String | Sì |
type | Deve essere impostato su **webhook**. | String | Sì |
statico | Metodo API REST per l'endpoint di destinazione. | Stringa. Tipi supportati:' POST ' | Sì |
URL | Endpoint e percorso di destinazione | Stringa (o espressione con l'elemento resultType della stringa). | Sì |
headers | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare il linguaggio e il tipo in una richiesta: "headers": {"Accept-Language": "en-US", "Content-Type": "application/json"}. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. "headers": {"Content-Type": "application/json"} |
body | Rappresenta il payload inviato all'endpoint. | Il corpo passato all'URI di richiamata deve essere un JSON valido. Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Sì |
authentication | Metodo di autenticazione usato per chiamare l'endpoint. I tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No |
timeout | Tempo di attesa dell'attività per la &#39;richiamata&#39; del callBackUri. Per quanto tempo l'attività attenderà che venga richiamato ' callBackUri '. Il valore predefinito è 10mins ("00:10:00"). Il formato è TimeSpan, ovvero d. hh: mm: SS | String | No |

## <a name="additional-notes"></a>Note aggiuntive

Azure Data Factory passerà una proprietà aggiuntiva "callBackUri" nel corpo all'endpoint URL e si aspetterà che questo URI venga richiamato prima del valore di timeout specificato. Se l'URI non viene richiamato, l'attività avrà esito negativo con stato "timeout".

L'attività Hook Web ha esito negativo solo quando la chiamata all'endpoint personalizzato ha esito negativo. È possibile aggiungere qualsiasi messaggio di errore nel corpo del callback e usarlo in un'attività successiva.

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
