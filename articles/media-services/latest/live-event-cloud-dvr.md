---
title: Evento live di Servizi multimediali di Azure e DVR cloud | Microsoft Docs
description: Questo articolo spiega cos'è un output live e come usare un DVR cloud.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322336"
---
# <a name="using-a-cloud-dvr"></a>Utilizzo di un DVR cloud

Un [output live](https://docs.microsoft.com/rest/api/media/liveoutputs) consente di regolare le proprietà del Live Stream in uscita, come la parte da registrare (ad esempio, la capacità del DVR cloud) e la possibilità da parte degli utenti di aprire il Live Stream. La relazione tra un **evento live** e il relativo **output live** è simile alla trasmissione televisiva tradizionale: un canale (**evento live**) rappresenta un flusso costante di video e una registrazione (**output live**) è limitata a uno specifico segmento di tempo (ad esempio, le notizie della sera dalle 18:30 alle 19:00). È possibile registrare programmi TV tramite un DVR (Digital Video Recorder): la funzionalità equivalente di eventi live è gestita dalla proprietà ArchiveWindowLength. È un timespan ISO-8601 (ad esempio, PTHH:MM:SS), il quale specifica la capacità del DVR e può essere impostato da un minimo di 3 minuti fino a un massimo di 25 ore.

## <a name="live-output"></a>Output live

Il valore **ArchiveWindowLength** determina quanto un visualizzatore può tornare indietro dalla posizione live corrente.  **ArchiveWindowLength** determina anche il tempo per cui i manifesti client possono crescere.

Ad esempio, se si sta riproducendo una partita di calcio e la proprietà **ArchiveWindowLength** è di soli 30 minuti. Un visualizzatore che inizia a guardare l’evento 45 minuti dopo l’inizio della partita può tornare indietro al massimo fino all’indicatore di 15 minuti. Gli **output live** della partita continueranno finché l'**evento live** non verrà arrestato, ma i contenuti che non rientrano nel parametro **ArchiveWindowLength** verranno rimossi sempre dall’archiviazione e non potranno essere recuperati. In questo esempio, il video tra l'inizio dell'evento e l'indicatore dei 15 minuti verrebbe eliminato dai DVR e dal contenitore nell'archivio blob dell’[Asset](https://docs.microsoft.com/rest/api/media/assets). L’archivio non può essere recuperato e viene rimosso dal contenitore nell’archivio blob di Azure.

Ogni **output live** è associato a un **asset** che usa per registrare il video nel contenitore di archiviazione BLOB di Azure associato. Per pubblicare l'output live, è necessario creare un **localizzatore di streaming** per l'**asset**. Dopo aver creato un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile compilare un URL di streaming da fornire ai visualizzatori.

Un **evento live** supporta fino a tre **output live** in esecuzione simultanea e consente quindi di creare fino a 3 registrazioni/archivi dallo stesso flusso live. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Ad esempio, immaginiamo di dover trasmettere un feed lineare live 24x7 e di creare "registrazioni" dei diversi programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta. In questo caso, per prima cosa si crea un output live primario, con un intervallo di archiviazione breve di 1 ora o meno: questo è il flusso live primario con il quale si sintonizzano i visualizzatori. È necessario creare un **localizzatore di streaming** per questo **output live** e pubblicarlo nell'applicazione o nel sito Web come feed "Live". Mentre l'**evento live** è in esecuzione, è possibile creare a livello di codice un secondo **output live** simultaneo all'inizio di un programma (o 5 minuti prima per fornire alcuni handle per il trimming successivo). Questo secondo **output live** può essere eliminato 5 minuti dopo la fine del programma. Con questo secondo **asset** è possibile creare un nuovo **localizzatore di streaming** per pubblicare il programma come asset su richiesta nel catalogo dell'applicazione. È possibile ripetere questo processo più volte per altri limiti del programma o elementi di rilievo da condividere come video su richiesta, mentre il feed "Live" dal primo **output live** continua a trasmettere il feed lineare. 

> [!NOTE]
> Gli **output live** iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina l'**output live**, non si elimina l'**asset** sottostante e il contenuto dell'asset. 
>
> Se l'asset **output live** è stato pubblicato usando un **localizzatore di streaming**, l'**evento live** (fino alla lunghezza dell'intervallo DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione del **localizzatore di streaming**, a seconda del valore raggiunto per primo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

