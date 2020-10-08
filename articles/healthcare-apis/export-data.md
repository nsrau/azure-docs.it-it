---
title: Esecuzione dell'esportazione richiamando $export comando nell'API di Azure per FHIR
description: Questo articolo descrive come configurare e usare l'esportazione deselezionata
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 433d3391e7b2b99d72ea71f1b4d48c3e04a46afc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819983"
---
# <a name="how-to-export-fhir-data"></a>Come esportare i dati di FHIR

La funzionalità di esportazione bulk consente di esportare i dati dal server FHIR in base alla [specifica FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Prima di usare $export, è necessario assicurarsi che l'API di Azure per FHIR sia configurata per usarla. Per la configurazione delle impostazioni di esportazione e la creazione dell'account di archiviazione di Azure, fare riferimento alla [pagina Configura dati di esportazione](configure-export-data.md).

## <a name="using-export-command"></a>Uso di $export comando

Dopo aver configurato l'API di Azure per FHIR per l'esportazione, è possibile usare il comando $export per esportare i dati dal servizio. I dati verranno archiviati nell'account di archiviazione specificato durante la configurazione dell'esportazione. Per informazioni su come richiamare $export comando in FHIR server, vedere la documentazione sulla [specifica $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Il comando $export nell'API di Azure per FHIR accetta un parametro _ \_ contenitore_ facoltativo che specifica il contenitore all'interno dell'account di archiviazione configurato in cui devono essere esportati i dati. Se viene specificato un contenitore, i dati verranno esportati in tale contenitore in una nuova cartella con il nome del contenitore. Se non viene specificato alcun contenitore, i dati verranno esportati in un nuovo contenitore.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Scenari supportati

API di Azure per FHIR supporta $export a livello di sistema, paziente e gruppo. Per l'esportazione di gruppi, vengono esportate tutte le risorse correlate, ma non vengono esportate le caratteristiche del gruppo.

> [!Note] 
> $export esporterà le risorse duplicate se la risorsa si trova in un raggruppamento di più di una risorsa o si trova in più gruppi.

Inoltre, il controllo dello stato di esportazione tramite l'URL restituito dall'intestazione Location durante l'accodamento è supportato insieme all'annullamento del processo di esportazione effettivo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esportare le risorse di FHIR usando $export comando. È ora possibile esaminare le funzionalità supportate
 
>[!div class="nextstepaction"]
>[Funzionalità supportate](fhir-features-supported.md)
