---
title: Trasformazioni e processi in Servizi multimediali di Azure | Microsoft Docs
description: Quando si usa Servizi multimediali, è necessario creare un oggetto Transform per descrivere le regole o le specifiche per l'elaborazione dei video. Questo articolo offre una panoramica dell'oggetto Transform e del relativo utilizzo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377309"
---
# <a name="transforms-and-jobs"></a>Trasformazioni e processi

## <a name="overview"></a>Panoramica 

La versione più recente delle API REST di Servizi multimediali di Azure (v3) introduce una nuova risorsa di flusso di lavoro per la codifica e/o l'analisi dei video, definita **Transform**. È possibile usare gli oggetti **Transform** per configurare attività comuni relative alla codifica o all'analisi di video. Ogni oggetto **Transform** descrive un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio. 

L'oggetto **Transform** è la serie di istruzioni, mentre l'oggetto **Job** è la richiesta effettiva inviata a Servizi multimediali di Azure per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare la posizione del video tramite: URL HTTP(S), URL di firma di accesso condiviso o un percorso ai file in locale o in Archiviazione BLOB di Azure. È possibile avere fino a 100 oggetti Transform nell'account di Servizi multimediali di Azure e inviare gli oggetti Job a tali oggetti Transform. È possibile quindi sottoscrivere eventi, ad esempio le modifiche dello stato del processo, tramite Notifiche, che si integrano direttamente con il sistema di notifica di Griglia di eventi di Azure. 

Poiché questa API è determinata da Azure Resource Manager, è possibile usare modelli di Resource Manager per creare e distribuire gli oggetti Transform nell'account di Servizi multimediali. È possibile impostare il controllo degli accessi in base al ruolo a livello di risorsa in questa API, consentendo di bloccare l'accesso a specifiche risorse, ad esempio gli oggetti Transform.

## <a name="transform-definition"></a>Definizione di Transform

Nella tabella seguente vengono illustrate le proprietà Transform e le relative definizioni.

|NOME|type|DESCRIZIONE|
|---|---|---|
|ID|stringa|ID di risorsa completo per la risorsa.|
|name|stringa|Nome della risorsa.|
|properties.created |stringa|Data e ora UTC in cui l'oggetto Transform è stato creato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.description |stringa|Descrizione dettagliata facoltativa dell'oggetto Transform.|
|properties.lastModified |stringa|Data e ora UTC in cui l'oggetto Transform è stato aggiornato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.outputs |TransformOutput[]|Matrice di una o più proprietà TransformOutputs che l'oggetto Transform deve generare.|
|type|stringa|Tipo di risorsa.|

Per la definizione completa, vedere [Transforms](https://docs.microsoft.com/rest/api/media/transforms) (Oggetti Transform).

## <a name="job-definition"></a>Definizione del processo

Nella tabella seguente vengono illustrate le proprietà Job e le relative definizioni.

|NOME|type|DESCRIZIONE|
|---|---|---|
|ID|stringa|ID di risorsa completo per la risorsa.|
|name|stringa|Nome della risorsa.|
|properties.created |stringa|Data e ora UTC in cui l'oggetto Transform è stato creato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.description |stringa|Descrizione dettagliata facoltativa dell'oggetto Job.|
|properties.lastModified |stringa|Data e ora UTC in cui l'oggetto Transform è stato aggiornato, nel formato"AAAA-MM-GGhh:mm:ssZ".|
|properties.outputs |JobOutput[]:JobOutputAsset[] |Output di Job.|
|properties.priority |Priorità |Priorità con cui deve essere elaborato l'oggetto Job. Gli oggetti Job con priorità più alta vengono elaborati prima di quelli con priorità inferiore. Se questa proprietà non è impostata, il valore predefinito è quello normale.
|properties.state |JobState |Stato corrente dell'oggetto Job.
|type|stringa|Tipo di risorsa.|

Per la definizione completa, vedere [Jobs](https://docs.microsoft.com/rest/api/media/jobs) (Oggetti Job).

## <a name="typical-workflow-and-example"></a>Esempio e flusso di lavoro tipici

1. Creare un oggetto Transform 
2. Inviare i processi a tale oggetto Transform 
3. Elencare gli oggetti Transform 
4. Eliminare un oggetto Transform, se non si intende usare questa "serie di istruzioni" in futuro. 

Si supponga di voler estrarre il primo frame di tutti i video come immagine di anteprima, i passaggi da eseguire sono: 

1. Definire la regola per l'elaborazione, ad esempio usare il primo frame del video come anteprima 
2. Quindi, per ogni video disponibile come input per il servizio, è possibile indicare al servizio: 
    1. Dove trovare il video, e 
    2. Dove scrivere l'output, ad esempio l'immagine di anteprima 

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md)
