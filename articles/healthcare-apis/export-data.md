---
title: Esecuzione dell'esportazione richiamando $export comando nell'API di Azure per FHIR
description: Questo articolo descrive come configurare e usare l'esportazione deselezionata
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482318"
---
# <a name="how-to-export-fhir-data"></a>Come esportare i dati di FHIR

Per la configurazione delle impostazioni di esportazione e la creazione dell'account di archiviazione di Azure, fare riferimento a [qui](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Esportazione di risorse FHIR con il comando $export

Dopo aver configurato l'API di Azure per FHIR per l'esportazione, è ora possibile usare il comando $export per esportare i dati dal servizio nell'account di archiviazione specificato durante la configurazione dell'esportazione. Per informazioni su come richiamare $export comando in FHIR server, vedere la documentazione su $export specifica all'indirizzo [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

Il comando $export nell'API di Azure per FHIR accetta un parametro _ \_ conatiner_ facoltativo che può essere usato per specificare il contenitore nell'account di archiviazione configurato a cui devono essere esportati i dati.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Si noti che attualmente l'API di Azure per FHIR supporta solo l'esportazione a livello di sistema, come definito nella specifica $export all'indirizzo [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Inoltre, solo _ \_ perché_ il parametro di query è attualmente supportato.

## <a name="exporting-de-identified-data-preview"></a>Esportazione dei dati deselezionati (anteprima)

Il $export comando può essere usato anche per esportare dati deselezionati dal server FHIR. Usa il motore anonimato dei di [FHIR Tools per anonimato dei](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e accetta i dettagli di configurazione di anonimato dei nei parametri di query. È possibile creare un file di configurazione anonimato dei personalizzato o usare il [file di configurazione di esempio](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) per il metodo Safe Harbor di HIPAA come punto di partenza. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Query parameter (Parametro di query)            | Esempio |Opzionalità| Descrizione|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js|Obbligatorio per l'esportazione deselezionata |Nome del file di configurazione. Vedere il formato del file di configurazione [qui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Questo file deve essere conservato all'interno di un contenitore denominato **anonimato dei** nello stesso account di archiviazione di Azure configurato come percorso di esportazione. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Facoltativo per esportazione deselezionata|Si tratta dell'ETAG del file di configurazione. È possibile ottenere il valore ETag usando Azure Storage Explorer dalla proprietà BLOB|

> [!IMPORTANT]
> Si noti che sia l'esportazione non elaborata che l'esportazione deselezionata vengono scritte nello stesso account di archiviazione di Azure specificato come parte della configurazione di esportazione. Si consiglia di usare contenitori diversi corrispondenti a una configurazione deselezionata diversa e gestire l'accesso utente a livello di contenitore.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esportare le risorse di FHIR usando $export comando, inclusi i dati deidentificati. Successivamente, è possibile configurare i dati di esportazione:
 
>[!div class="nextstepaction"]
>[configurare Esporta dati](configure-export-data.md)
