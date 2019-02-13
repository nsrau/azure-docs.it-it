---
title: Panoramica dello streaming live con Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo offre una panoramica dello streaming live con Servizi multimediali di Azure v3.
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
ms.date: 02/01/2019
ms.author: juliako
ms.openlocfilehash: e90dd052f6a4af83d2dd794dd405a4700da75bde
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656336"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming live con Servizi multimediali di Azure v3

Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per eseguire lo streaming di eventi live con Servizi multimediali, è necessario quanto segue:  

- Una fotocamera usata per riprendere l'evento live.<br/>Per alcune idee per la configurazione, vedere [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT) (Configurazione semplice e portabile di attrezzature video per eventi).
- Un codificatore video live in grado di convertire i segnali provenienti da una fotocamera (o da un altro dispositivo, come un portatile) in un feed di contributi inviato a Servizi multimediali. Il feed di contributi può includere segnali relativi alla pubblicità, ad esempio i marcatori SCTE-35.<br/>Per un elenco dei codificatori consigliati di streaming live, vedere [Codificatori di streaming live consigliati](recommended-on-premises-live-encoders.md). Vedere anche questo blog: [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT) (Produzione di streaming live con OBS).
- I componenti in Servizi multimediali, che consentono di inserire, visualizzare in anteprima, includere in un pacchetto, registrare, crittografare e trasmettere l'evento live ai clienti o a una rete CDN per un'ulteriore distribuzione.

Con Servizi multimediali è possibile sfruttare la **Creazione dinamica dei pacchetti**, che consente di visualizzare in anteprima e trasmettere i flussi live nei [formati MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) dal feed di contributi inviato al servizio. Gli utenti possono riprodurre il flusso live con qualsiasi lettore compatibile per HLS, DASH o Smooth Streaming. È possibile usare [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) nelle applicazioni web o per dispositivi mobili per distribuire lo streaming in uno di questi protocolli.

Servizi multimediali consente di distribuire contenuti crittografati dinamicamente (**Crittografia dinamica**) con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali fornisce inoltre un servizio per la distribuzione di chiavi AES e licenze Digital Rights Management ai client autorizzati. Per altre informazioni su come crittografare i contenuti con Servizi multimediali, consultare la [Panoramica sulla protezione dei contenuti](content-protection-overview.md)

È anche possibile applicare filtri dinamici, che possono essere usati per controllare il numero di tracce, i formati, la velocità in bit e gli intervalli di tempo di presentazione che vengono inviati ai lettori. Per altre informazioni, vedere [Filtri e manifesti dinamici](filters-dynamic-manifest-overview.md).

Questo articolo offre una panoramica e linee guida per lo streaming live con Servizi multimediali.

## <a name="prerequisites"></a>Prerequisiti

Per comprendere il flusso di lavoro per lo streaming live in Servizi multimediali v3, è necessario conoscere i concetti seguenti: 

- [Endpoint di streaming](streaming-endpoint-concept.md)
- [Eventi live e output live](live-events-outputs-concept.md)

## <a name="live-streaming-workflow"></a>Flusso di lavoro dello streaming live

Ecco i passaggi del flusso di lavoro di uno streaming live:

1. Nell'account di Servizi multimediali assicurarsi che l'**endpoint di streaming** sia in esecuzione. 
2. Creare un [Evento live](live-events-outputs-concept.md). <br/>Quando si crea l'evento, è possibile impostarne l'avvio automatico. In alternativa, è possibile avviare l'evento quando si è pronti ad avviare lo streaming.<br/> Quando l'avvio automatico è impostato su true, l'evento live verrà avviato subito dopo la creazione. La fatturazione inizia non appena viene avviata l'esecuzione dell'evento live. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione. Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore a livello locale per usare l'URL per inviare il feed di contributo.<br/>Vedere i [codificatori live consigliati](recommended-on-premises-live-encoders.md).
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**.
6. Creare un **LiveOutput** e usare il nome dell'asset creato.<br/>L'**output live** archivierà il flusso nell'**asset**.
7. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.<br/>Se si prevede di crittografare il contenuto, rivedere la [Panoramica della protezione del contenuto](content-protection-overview.md).
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare (questi sono deterministici).
9. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Se si vuole interrompere la creazione dell'**evento live** visualizzabile, è necessario arrestare lo streaming dell'evento ed eliminare il **localizzatore di streaming**.

## <a name="other-important-articles"></a>Altri articoli importanti

- [Codificatori live consigliati](recommended-on-premises-live-encoders.md)
- [Utilizzo di un DVR cloud](live-event-cloud-dvr.md)
- [Confronto tra le funzionalità dei tipi di eventi live](live-event-types-comparison.md)
- [Stati e fatturazione](live-event-states-billing.md)
- [Latency](live-event-latency.md)

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
* [Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3](migrate-from-v2-to-v3.md)
