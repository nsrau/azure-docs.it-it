---
title: Esecuzione dell'esportazione richiamando $export comando nell'API di Azure per FHIR
description: Questo articolo descrive come configurare e usare l'esportazione deselezionata
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308181"
---
# <a name="how-to-export-fhir-data"></a>Come esportare i dati di FHIR

Prima di usare $export, è necessario assicurarsi che l'API di Azure per FHIR sia configurata per usarla. Per la configurazione delle impostazioni di esportazione e la creazione dell'account di archiviazione di Azure, fare riferimento alla [pagina Configura dati di esportazione](configure-export-data.md).

## <a name="using-export-command"></a>Uso di $export comando

Dopo aver configurato l'API di Azure per FHIR per l'esportazione, è possibile usare il comando $export per esportare i dati dal servizio. I dati verranno archiviati nell'account di archiviazione specificato durante la configurazione dell'esportazione. Per informazioni su come richiamare $export comando in FHIR server, vedere la documentazione sulla [specifica $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Il comando $export nell'API di Azure per FHIR accetta un parametro _ \_ contenitore_ facoltativo che specifica il contenitore all'interno dell'account di archiviazione configurato in cui devono essere esportati i dati.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Scenari supportati

API di Azure per FHIR supporta $export a livello di sistema, paziente e gruppo. Per l'esportazione di gruppi, vengono esportate tutte le risorse correlate, ma non vengono esportate le caratteristiche del gruppo.

> [!Note] 
> $export esporterà le risorse duplicate se la risorsa si trova in un raggruppamento di più di una risorsa o si trova in più gruppi.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esportare le risorse di FHIR usando $export comando. È ora possibile esaminare le funzionalità supportate
 
>[!div class="nextstepaction"]
>[Funzionalità supportate](fhir-features-supported.md)
