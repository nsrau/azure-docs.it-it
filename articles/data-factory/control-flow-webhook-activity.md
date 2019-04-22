---
title: Attività di Webhook in Azure Data Factory | Microsoft Docs
description: L'attività di Webhook non potrà continua l'esecuzione della pipeline fino a quando non viene convalidato il set di dati collegato a determinati criteri, che l'utente specifica.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495909"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Attività di Webhook in Azure Data Factory
È possibile usare un'attività di hook web per controllare l'esecuzione delle pipeline tramite il codice personalizzato. Utilizzo dell'attività di webhook, i clienti possono chiamare un endpoint e passare un URL di callback. L'esecuzione della pipeline è in attesa del callback da richiamare prima di procedere all'attività successiva.

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



Proprietà | DESCRIZIONE | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
name | Nome dell'attività hook web | string | Sì |
type | Deve essere impostata su **WebHook**. | string | Sì |
statico | Metodo API REST per l'endpoint di destinazione. | Stringa. Tipi supportati: 'POST' | Sì |
URL | Endpoint e percorso di destinazione | Stringa (o espressione con l'elemento resultType della stringa). | Sì |
headers | Intestazioni che vengono inviate alla richiesta. Ad esempio, per impostare la lingua e il tipo in una richiesta: "headers": {"Accept-Language": "en-us", "Content-Type": "application/json"}. | Stringa (o un'espressione con l'elemento resultType della stringa) | Sì, l'intestazione Content-type è obbligatoria. "headers":{ "Content-Type":"application/json"} |
Corpo | Rappresenta il payload inviato all'endpoint. | Il corpo passato nuovamente per la funzione di richiamata URI deve essere un oggetto JSON valido. Vedere lo schema del payload della richiesta nella sezione [Schema del payload della richiesta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Sì |
authentication | Metodo di autenticazione usato per chiamare l'endpoint. Tipi supportati sono "Basic" o "ClientCertificate". Per altre informazioni, vedere la sezione [Autenticazione](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Se l'autenticazione non è necessaria, escludere questa proprietà. | Stringa (o un'espressione con l'elemento resultType della stringa) | No  |
timeout | Tempo di attesa dell'attività per il &#39;callBackUri&#39; da richiamare. Tempo di attesa dell'attività per callBackUri da richiamare. Valore predefinito è 10mins ("00: 10:00"). È di formato Timespan, ad esempio d.hh:mm:ss | string | No  |

## <a name="additional-notes"></a>Note aggiuntive

Azure Data Factory passerà una proprietà aggiuntiva "callBackUri" nel corpo per l'endpoint dell'url e si aspetta di ricevere questo uri deve essere richiamato prima del valore di timeout specificato. Se l'uri non viene richiamato, l'attività avrà esito negativo con stato 'Timeout'.

L'attività di hook web stesso ha esito negativo solo quando la chiamata all'endpoint personalizzato ha esito negativo. Qualsiasi messaggio di errore può essere aggiunti nel corpo del metodo di callback e utilizzato in un'attività successiva.

## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività ExecutePipeline](control-flow-execute-pipeline-activity.md)
- [Attività ForEach](control-flow-for-each-activity.md)
- [Attività Get Metadata](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
