---
title: Esportazione dei dati deselezionati (anteprima) per l'API di Azure per FHIR
description: Questo articolo descrive come configurare e usare l'esportazione deselezionata
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: bdbab0e032764d07119402686051d391376cb913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843869"
---
# <a name="exporting-de-identified-data-preview"></a>Esportazione dei dati deselezionati (anteprima)

> [!Note] 
> I risultati quando si utilizza l'esportazione deselezionata variano in base a fattori quali i dati inseriti e alle funzioni selezionate dal cliente. Microsoft non è in grado di valutare gli output di esportazione deselezionati o di determinare l'accettabilità per i casi d'uso e le esigenze di conformità del cliente. L'esportazione deselezionata non è garantita per soddisfare i requisiti legali, normativi o di conformità specifici.

Il $export comando può essere usato anche per esportare dati deselezionati dal server FHIR. Usa il motore anonimato dei di [FHIR Tools per anonimato dei](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e accetta i dettagli di configurazione di anonimato dei nei parametri di query. È possibile creare un file di configurazione anonimato dei personalizzato o usare il [file di configurazione di esempio](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) per il metodo Safe Harbor di HIPAA come punto di partenza. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Query parameter (Parametro di query)            | Esempio |Opzionalità| Description|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.js|Obbligatorio per l'esportazione deselezionata |Nome del file di configurazione. Vedere il formato del file di configurazione [qui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Questo file deve essere conservato all'interno di un contenitore denominato **anonimato dei** nello stesso account di archiviazione di Azure configurato come percorso di esportazione. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Facoltativo per esportazione deselezionata|Si tratta dell'ETAG del file di configurazione. È possibile ottenere il valore ETag usando Azure Storage Explorer dalla proprietà BLOB|

> [!IMPORTANT]
> L'esportazione non elaborata e l'esportazione deselezionata vengono scritte nello stesso account di archiviazione di Azure specificato come parte della configurazione di esportazione. Si consiglia di usare contenitori diversi corrispondenti a una configurazione deselezionata diversa e gestire l'accesso utente a livello di contenitore.