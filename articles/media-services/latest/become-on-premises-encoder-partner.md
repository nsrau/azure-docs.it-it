---
title: Diventare un partner del codificatore locale-servizi multimediali di Azure
description: Questo articolo illustra come verificare i codificatori di streaming live locali.
services: media-services
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 03/02/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f98d9942f8c30f06b0144503b056c1e8a393ae52
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298632"
---
# <a name="how-to-verify-your-on-premises-live-streaming-encoder"></a>Come verificare il codificatore Live streaming locale

Servizi multimediali di Azure offre ai partner di codificatore locale il vantaggio di promuovere il loro prodotto consigliandolo ai clienti aziendali. Per diventare un partner di codificatore locale, è necessario verificare la compatibilità del proprio codificatore locale con Servizi multimediali. A questo scopo, completare le verifiche seguenti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="pass-through-live-event-verification"></a>Verifica dell'evento live pass-through

1. Nell'account di Servizi multimediali assicurarsi che l'**endpoint di streaming** sia in esecuzione. 
2. Creare e avviare l'evento live **pass-through**. <br/> Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore locale per usare l'URL per l’invio del flusso live a bitrate multipli a Servizi multimediali.
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**.
6. Creare un **LiveOutput** e usare il nome dell'asset creato.
7. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare.
9. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Eseguire il codificatore live per circa 10 minuti.
12. Arrestare l'evento live. 
13. Usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer), per guardare la risorsa archiviata e accertarsi che la riproduzione non presenti anomalie visibili a tutti i livelli qualitativi. In alternativa, guardare e convalidare tramite l'anteprima URL durante la sessione live.
14. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live, le impostazioni e la versione usate dal codificatore live.
15. Reimpostare lo stato dell'evento live dopo la creazione di ogni esempio.
16. Ripetere i passaggi da 5 a 15 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni, sottotitoli o velocità di codifica diverse).

## <a name="live-encoding-live-event-verification"></a>Verifica dell'evento live di codifica live

1. Nell'account di Servizi multimediali assicurarsi che l'**endpoint di streaming** sia in esecuzione. 
2. Creare e avviare l'evento live di **codifica live**. <br/> Per altre informazioni, vedere [Stati e fatturazione dell'evento live](live-event-states-billing.md).
3. Ottenere gli URL di inserimento e configurare il codificatore per eseguire il push di un flusso live a bitrate singolo a Servizi multimediali.
4. Ottenere l'URL di anteprima e usarlo per verificare che l'input dal codificatore venga effettivamente ricevuto.
5. Creare un nuovo oggetto **Asset**.
6. Creare un **LiveOutput** e usare il nome dell'asset creato.
7. Creare un **localizzatore di streaming** con i tipi di **Criterio di streaming** predefiniti.
8. Elencare i percorsi nel **localizzatore di streaming** per ottenere gli URL da usare.
9. Ottenere il nome host per l'**endpoint di streaming** da cui si desidera trasmettere.
10. Combinare l'URL del passaggio 8 con il nome host del passaggio 9 per ottenere l'URL completo.
11. Eseguire il codificatore live per circa 10 minuti.
12. Arrestare l'evento live.
13. Usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer), per guardare la risorsa archiviata e accertarsi che la riproduzione non presenti anomalie visibili a tutti i livelli qualitativi. In alternativa, guardare e convalidare tramite l'anteprima URL durante la sessione live.
14. Registrare l'ID dell'asset, l'URL di streaming pubblicato per l'archivio live, le impostazioni e la versione usate dal codificatore live.
15. Reimpostare lo stato dell'evento live dopo la creazione di ogni esempio.
16. Ripetere i passaggi da 5 a 15 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni, sottotitoli o velocità di codifica diverse).

## <a name="longevity-verification"></a>Verifica della durata

Seguire gli stessi passaggi di [Verifica dell'evento live pass-through](#pass-through-live-event-verification), tranne il passaggio 11. <br/>Invece che per 10 minuti, eseguire il codificatore live per almeno una settimana. Usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer), per guardare di volta in volta lo streaming live (o la risorsa archiviata) e accertarsi che la riproduzione non presenti anomalie visibili.

## <a name="email-your-recorded-settings"></a>Inviare tramite posta elettronica le impostazioni registrate

Infine, inviare un messaggio di posta elettronica con le impostazioni registrate e i parametri di archiviazione live a Servizi multimediali di Azure all'indirizzo amshelp@microsoft.com per notificare il superamento di tutti i controlli di autoverifica. Includere anche le proprie informazioni di contatto per successive comunicazioni. È possibile contattare il team di Servizi multimediali di Azure per qualsiasi domanda su questo processo.

## <a name="see-also"></a>Vedere anche

[Codificatori locali testati](recommended-on-premises-live-encoders.md)

## <a name="next-steps"></a>Passaggi successivi

[Streaming live con Servizi multimediali v3](live-streaming-overview.md)
