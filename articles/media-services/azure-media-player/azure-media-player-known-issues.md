---
title: Problemi noti di Azure Media Player
description: La versione corrente presenta i problemi noti seguenti.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: 4d0d56dc4d6e7fa7765439e7387d67bb80a6ec9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83124636"
---
# <a name="known-issues"></a>Problemi noti #

La versione corrente presenta i seguenti problemi noti:

## <a name="azure-media-player"></a>Azure Media Player ##

- I codificatori configurati in modo errato possono causare problemi di riproduzione
- I flussi con timestamp maggiori di 2 ^ 53 possono presentare problemi di riproduzione.
  - Mitigazione: usare il video 90-kHz e le scadenze audio 44,1-kHz
- Nessun AutoPlay nei dispositivi mobili senza interazione dell'utente. È bloccata dalla piattaforma.
- La ricerca di quasi discontinuità può causare un errore di riproduzione.
- Il download di presentazioni di grandi dimensioni può causare il blocco dell'interfaccia utente.
- Non è possibile riprodurre automaticamente la stessa origine al termine della presentazione.
  - Per riprodurre un'origine dopo che è terminata, è necessario impostare di nuovo l'origine.
- I manifesti vuoti possono causare problemi con il lettore.
  - Questo problema può verificarsi quando si avvia un flusso live e non sono presenti blocchi sufficienti nel manifesto.
- Posizione di riproduzione probabilmente al di fuori dell'interfaccia utente SeekBar.
- L'ordine degli eventi non è coerente in tutti i Tech.
- La proprietà memorizzata nel buffer non è coerente in tutti i Tech.
- nativeControlsForTouch deve essere false (impostazione predefinita) per evitare che l'oggetto non supporti la proprietà o il metodo ' secontrols ' "
- I poster devono ora essere URL assoluti
- Quando si usa la modalità a contrasto elevato del dispositivo, è possibile che si verifichino problemi estetici minori nell'interfaccia utente
- Gli URL contenenti "%" o "+" nella stringa completamente decodificata possono avere problemi nell'impostazione dell'origine

## <a name="ad-insertion"></a>Inserimento ad ##

- Per gli annunci è possibile che vengano inseriti problemi (su richiesta o in tempo reale) quando nel browser è installato un blocco ad
- I dispositivi mobili possono avere problemi di riproduzione degli annunci.
- Gli annunci MP4 midroll non sono attualmente supportati da Azure Media Player.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Nella finestra DVR del contenuto attivo, quando il contenuto termina la sequenza temporale continuerà a crescere fino alla ricerca dell'area o fino alla fine della presentazione.
- Le presentazioni in tempo reale in Firefox con MSE abilitato hanno problemi

- Gli asset che sono solo audio non verranno riprodotti tramite la tecnologia AzureHtml5JS.
  - Per riprodurre asset senza audio, è possibile eseguire questa operazione inserendo un audio vuoto usando lo [strumento di esplorazione di servizi multimediali di Azure](https://aka.ms/amse)
  - Le istruzioni su come inserire audio invisibile all'utente sono disponibili [qui](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Lampeggiante ##

- Il contenuto AES non viene riprodotto nella versione Flash 30.0.0.134 a causa di un bug nella logica di memorizzazione nella cache di Adobe. Adobe ha risolto il problema e lo ha rilasciato in 30.0.0.154
- Errori tecnici e http (ad esempio, i timeout di rete 404), il giocatore riprenderà più tempo per il ripristino rispetto ad altri tecnici.
- Fai clic sull'area video con flash Tech non Riproduci/sospende il lettore.
- Se l'utente ha installato Flash ma non concede l'autorizzazione per caricarlo nel sito, può verificarsi una rotazione infinita. Questo perché il lettore ritiene che il plug-in sia installato e disponibile e ritiene che il plug-in esegua il contenuto. Il codice JavaScript è stato inviato, ma le impostazioni del browser hanno impedito l'esecuzione del plug-in fino a quando l'utente non accetta il prompt per consentire il plug-in. Questa situazione può verificarsi in tutti i browser.  

## <a name="silverlight"></a>Silverlight ##

- Funzionalità mancanti
- Errori tecnici e http (ad esempio, i timeout di rete 404), il giocatore riprenderà più tempo per il ripristino rispetto ad altri tecnici.
- Per la riproduzione di Safari e Firefox su Mac con Silverlight è necessario definire in modo esplicito `"http://` o `https://` per l'origine.
- Se per questa tecnologia manca un'API, in genere verrà restituito null.
- Se l'utente ha installato Flash ma non concede l'autorizzazione per caricarlo nel sito, può verificarsi una rotazione infinita. Questo perché il lettore ritiene che il plug-in sia installato e disponibile e ritiene che il plug-in esegua il contenuto. Il codice JavaScript è stato inviato, ma le impostazioni del browser hanno impedito l'esecuzione del plug-in fino a quando l'utente non accetta il prompt per consentire il plug-in. Questa situazione può verificarsi in tutti i browser.  

## <a name="native-html5"></a>HTML5 nativo ##

- HTML5 Tech sta inviando solo un evento canplaythrough per la prima origine del set.
- Questa tecnologia supporta solo le informazioni implementate dal browser.  Alcune funzionalità potrebbero mancare in questa tecnologia.  
- Se per questa tecnologia manca un'API, in genere verrà restituito null.
- Si sono verificati problemi con didascalie e sottotitoli in questa tecnologia. Potrebbero non essere disponibili o visualizzabili in questa tecnologia.
- Una larghezza di banda limitata in uno scenario HLS/HTML5 Tech comporta la riproduzione audio senza video.

### <a name="microsoft"></a>Microsoft ###

- La riproduzione di IE8 attualmente non funziona a causa dell'incompatibilità con ECMAScript 5
- In IE e in alcune versioni di Edge non è possibile immettere fullscreen con la tabulazione del pulsante e selezionarlo o usare il tasto di scelta rapida F/f.

## <a name="google"></a>Google ##

- Più profili di codifica nello stesso manifesto presentano alcuni problemi di riproduzione in Chrome e non è consigliabile.
- Chrome non è in grado di riprodurre l'it-AAC con AzureHtml5JS. Seguire i dettagli sullo strumento di [rilevamento dei bug](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partire da Chrome V58, il contenuto di Widevine deve essere caricato/riprodotto tramite il protocollo https://altrimenti la riproduzione avrà esito negativo.

## <a name="mozilla"></a>Mozilla ##

- Per lo switch audio stream è necessario che i flussi audio abbiano lo stesso codec privato per i dati quando si usa AzureHtml5JS. Questa operazione è necessaria per la piattaforma Firefox.

## <a name="apple"></a>Apple ##

- Safari su Mac Abilita spesso la modalità di risparmio energia per impostazione predefinita con l'impostazione "Interrompi plug-in per il risparmio di energia", che blocca i plug-in come Flash e Silverlight quando ritiene che non sia a favore dell'utente. Questo blocco non blocca le funzionalità esistenti del plug-in. Dato il valore predefinito di techOrder, è possibile che si verifichino problemi durante il tentativo di riproduzione
  - Mitigazione 1: se il lettore video è' front and Center ' (all'interno di un limite di 3000 x 3000 pixel a partire dall'angolo superiore sinistro del documento), dovrebbe continuare a riprodursi.
  - Mitigazione 2: modificare techOrder per Safari in modo che sia ["azureHtml5JS", "HTML5"]. Questa mitigazione implica l'impossibilità di ottenere tutte le funzionalità disponibili nella tecnologia Flash.
- Il contenuto di PlayReady tramite Silverlight potrebbe avere problemi di riproduzione in Safari.
- AES e il contenuto del token con restrizioni non vengono riprodotti con iOS e i dispositivi Android meno recenti.
  - Per ottenere questo scenario, è necessario aggiungere al servizio un proxy.
- L'interfaccia predefinita per il telefono iOS viene visualizzata tramite.
- la riproduzione di iPhone viene sempre eseguita nel lettore nativo a schermo intero.
  - Le funzionalità, incluse le didascalie, potrebbero non essere mantenute in questa riproduzione non inline.
- Al termine della presentazione Live, i dispositivi iOS non termineranno correttamente la presentazione.
- iOS non consente le funzionalità DVR.
- l'opzione flusso audio iOS può essere eseguita solo se l'interfaccia utente del lettore nativo iOS e richiede flussi audio con lo stesso codec dati privati
- Le versioni precedenti di Safari potrebbero causare problemi durante la riproduzione dei flussi live.

## <a name="older-android"></a>Android precedente ##

- AES e il contenuto del token con restrizioni non vengono riprodotti con iOS e i dispositivi Android meno recenti.
  - Per ottenere questo scenario, è necessario aggiungere al servizio un proxy.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)