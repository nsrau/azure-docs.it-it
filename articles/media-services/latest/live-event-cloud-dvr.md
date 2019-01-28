---
title: LiveEvent di Servizi multimediali di Azure e DVR cloud | Microsoft Docs
description: Questo articolo descrive LiveOutput e come usare un DVR cloud.
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
ms.openlocfilehash: 3646c6167f901fe43080d39df42fdb127b1c7fc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828146"
---
# <a name="using-a-cloud-dvr"></a>Utilizzo di un DVR cloud

Un [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) consente di regolare le proprietà del Live Stream in uscita, come la parte da registrare (ad esempio, la capacità del DVR cloud) e la possibilità da parte degli utenti di aprire il Live Stream. La relazione tra un **LiveEvent** e il relativo **LiveOutput** è simile alla trasmissione televisiva tradizionale: un canale (**LiveEvent**) rappresenta un flusso costante di video e una registrazione (**LiveOutput**) è limitata a uno specifico segmento di tempo (ad esempio, le notizie della sera dalle 18:30 alle 19:00). È possibile registrare programmi TV tramite un DVR (Digital Video Recorder): la funzionalità equivalente di LiveEvents è gestita dalla proprietà ArchiveWindowLength. È un timespan ISO-8601 (ad esempio, PTHH:MM:SS), il quale specifica la capacità del DVR e può essere impostato da un minimo di 3 minuti fino a un massimo di 25 ore.

## <a name="liveoutput"></a>LiveOutput

Il valore **ArchiveWindowLength** determina quanto un visualizzatore può tornare indietro dalla posizione live corrente.  **ArchiveWindowLength** determina anche il tempo per cui i manifesti client possono crescere.

Ad esempio, se si sta riproducendo una partita di calcio e la proprietà **ArchiveWindowLength** è di soli 30 minuti. Un visualizzatore che inizia a guardare l’evento 45 minuti dopo l’inizio della partita può tornare indietro al massimo fino all’indicatore di 15 minuti. I **LiveOutput** della partita continueranno finché il **LiveEvent** non verrà arrestato, ma i contenuti che non rientrano nel parametro **ArchiveWindowLength** verranno rimossi sempre dall’archiviazione e non potranno essere recuperati. In questo esempio, il video tra l'inizio dell'evento e l'indicatore dei 15 minuti verrebbe eliminato dai DVR e dal contenitore nell'archivio blob dell’[Asset](https://docs.microsoft.com/rest/api/media/assets). L’archivio non può essere recuperato e viene rimosso dal contenitore nell’archivio blob di Azure.

Ciascun **LiveOutput** è associato a un **Asset** che utilizza per registrare il video nel contenitore di archiviazione blob di Azure associato. Per pubblicare il LiveOutput, è necessario creare un **StreamingLocator** per l’**Asset**. Dopo aver creato un [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), è possibile compilare un URL di streaming da fornire ai visualizzatori.

Un **LiveEvent** supporta fino a tre **LiveOutput** in esecuzione simultanea, quindi consente di creare fino a 3 registrazioni/archivi dallo stesso flusso live. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Ad esempio, immaginiamo di dover trasmettere un feed lineare live 24x7 e di creare "registrazioni" dei diversi programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta. In questo caso, per prima cosa si crea un LiveOutput primario, con un intervallo di archiviazione breve di 1 ora o meno: questo è il flusso live primario con il quale si sintonizzano i visualizzatori. È necessario creare uno **StreamingLocator** per questo **LiveOutput** e pubblicarlo nell’applicazione o nel sito Web come feed "Live". Mentre il **LiveEvent** è in esecuzione, è possibile creare a livello di codice un secondo **LiveOutput** simultaneo all’inizio di un programma (o 5 minuti prima per fornire alcuni handle per il trimming successivo). Questo secondo **LiveOutput** può essere eliminato 5 minuti dopo la fine del programma. Con questo secondo **Asset** è possibile creare un nuovo **StreamingLocator** per pubblicare il programma come asset su richiesta nel catalogo dell'applicazione. È possibile ripetere questo processo più volte per altri limiti del programma o elementi di rilievo da condividere come video su richiesta, mentre il feed "Live" dal primo **LiveOutput** continua a trasmettere il feed lineare. 

> [!NOTE]
> I **LiveOutput** iniziano al momento della creazione e terminano quando vengono eliminati. Quando si elimina il **LiveOutput**, non si elimina l'**Asset** sottostante e il contenuto dell'asset. 
>
> Se l'asset **LiveOutput** è stato pubblicato usando un **StreamingLocator**, il **LiveEvent** (fino alla lunghezza dell'intervallo DVR) continuerà a essere visualizzabile fino alla scadenza o all'eliminazione di **StreamingLocator**, a seconda del valore raggiunto per primo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)

