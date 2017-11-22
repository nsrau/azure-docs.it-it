---
title: Informazioni sui codificatori consigliati da Servizi multimediali di Azure | Microsoft Docs
description: Informazioni sui codificatori consigliati dai servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Codificatori locali consigliati
Quando si esegue lo streaming live con Servizi multimediali di Azure è possibile specificare in che modo si vuole che il canale riceva il flusso di input. Se si sceglie di usare un codificatore locale con un canale di codifica live, il codificatore deve eseguire il push di un flusso a bitrate singolo di alta qualità come output. Se si sceglie di usare un codificatore locale con un canale pass-through, il codificatore deve eseguire il push di un flusso a bitrate multipli come output con tutte le qualità di output desiderate. Per altre informazioni, vedere [Streaming live con codificatori locali](media-services-live-streaming-with-onprem-encoders.md).

Servizi multimediali di Azure consiglia di usare uno dei codificatori live seguenti che hanno RTMP come output:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4

Servizi multimediali di Azure consiglia di usare uno dei codificatori live seguenti che hanno Smooth Streaming a bitrate multipli come output:
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live

> [!NOTE]
> Un codificatore live può inviare un flusso a bitrate singolo a un canale pass-through, ma questa configurazione non è consigliata perché non è consentito per lo streaming a bitrate adattivo nel client.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Come diventare un partner del codificatore locale
Come partner del codificatore locale di Servizi multimediali di Azure, Servizi multimediali alza di livello il prodotto consigliando il codificatore ai clienti aziendali. Per diventare un partner del codificatore locale, è necessario verificare la compatibilità del codificatore locale con Servizi multimediali. A questo scopo, completare le verifiche seguenti:

Verifica del canale pass-through
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale **pass-through**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato
5. Eseguire il codificatore live per circa 10 minuti
6. Arrestare l'evento live
7. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live
8. Reimpostare lo stato del canale dopo la creazione di ogni esempio
9. Ripetere i passaggi da 3 a 8 per tutte le configurazioni supportate dal codificatore (con e senza segnalazione/didascalie/diverse velocità di codifica)

Verifica del canale di codifica live
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale di **codifica live**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate singolo.
4. Creare un evento live pubblicato
5. Eseguire il codificatore live per circa 10 minuti
6. Arrestare l'evento live
7. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live
8. Reimpostare lo stato del canale dopo la creazione di ogni esempio
9. Ripetere i passaggi da 3 a 8 per tutte le configurazioni supportate dal codificatore (con e senza segnalazione/didascalie/diverse velocità di codifica)

Verifica della durata
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale **pass-through**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato
5. Eseguire un codificatore live per almeno una settimana
6. Arrestare l'evento live
7. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live

Infine inviare le impostazioni registrate e i parametri di archiviazione live a Servizi multimediali all'indirizzo e-mail amsstreaming@microsoft.com. Al momento della ricezione, Servizi multimediali esegue dei test di verifica sui campioni dal codificatore live. Per qualsiasi domanda su questo processo, contattare Servizi multimediali.