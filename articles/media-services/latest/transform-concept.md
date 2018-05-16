---
title: Trasformazioni e processi in Servizi multimediali di Azure | Microsoft Docs
description: Quando si usa Servizi multimediali, è necessario creare un oggetto Transform per descrivere le regole o le specifiche per l'elaborazione dei video. Questo articolo offre una panoramica dell'oggetto Transform e del relativo utilizzo.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Trasformazioni e processi

## <a name="overview"></a>Panoramica 

La versione più recente delle API REST di Servizi multimediali di Azure (v3) introduce una nuova risorsa di flusso di lavoro per la codifica e/o l'analisi dei video, definita **Transform**. È possibile usare gli oggetti **Transform** per configurare attività comuni relative alla codifica o all'analisi di video. Ogni oggetto **Transform** descrive un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio. 

L'oggetto **Transform** è la serie di istruzioni, mentre l'oggetto **Job** è la richiesta effettiva inviata a Servizi multimediali di Azure per applicare l'oggetto **Transform** a determinati contenuti audio o video di input. L'oggetto **Job** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare la posizione del video tramite: URL HTTP(S), URL di firma di accesso condiviso o un percorso ai file in locale o in Archiviazione BLOB di Azure. È possibile avere fino a 100 oggetti Transform nell'account di Servizi multimediali di Azure e inviare gli oggetti Job a tali oggetti Transform. È possibile quindi sottoscrivere eventi, ad esempio le modifiche dello stato del processo, tramite Notifiche, che si integrano direttamente con il sistema di notifica di Griglia di eventi di Azure. 

Poiché questa API è determinata da Azure Resource Manager, è possibile usare modelli di Resource Manager per creare e distribuire gli oggetti Transform nell'account di Servizi multimediali. È possibile impostare il controllo degli accessi in base al ruolo a livello di risorsa in questa API, consentendo di bloccare l'accesso a specifiche risorse, ad esempio gli oggetti Transform.

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

> [!div class="nextstepaction"]
> [Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md)
