---
title: Azure Media Player Known Issues
description: La versione corrente presenta i seguenti problemi noti.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ff8dc58b9122e5173a9a6065e2efdbc5697be0d7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727216"
---
# <a name="known-issues"></a>Problemi noti #

La versione corrente presenta i seguenti problemi noti:

## <a name="azure-media-player"></a>Azure Media Player ##

- I codificatori configurati in modo errato possono causare problemi con la riproduzione
- I flussi con timestamp superiori a 2/53 potrebbero avere problemi di riproduzione.
  - Mitigazione: utilizzare video da 90 kHz e scale temporali audio da 44,1 kHz
- Nessungioco di riproduzione automatica sui dispositivi mobili senza interazione dell'utente. È bloccata dalla piattaforma.
- La ricerca di discontinuità vicine può causare errori di riproduzione.
- Il download di presentazioni di grandi dimensioni può causare il blocco dell'interfaccia utente.
- Non è possibile riprodurre automaticamente la stessa sorgente al termine della presentazione.
  - Per riprodurre un'origine al termine, è necessario impostare nuovamente l'origine.
- I manifesti vuoti possono causare problemi con il lettore.
  - Questo problema può verificarsi quando si avvia un flusso live e non si trovano blocchi sufficienti nel manifesto.
- Posizione di riproduzione potrebbe al di fuori della barra di ricerca dell'interfaccia utente.
- L'ordine degli eventi non è coerente in tutti i tecnici.
- La proprietà memorizzata nel buffer non è coerente tra i tecnici.
- nativeControlsForTouch deve essere false (impostazione predefinita) per evitare "Object doesn't support property or method 'setControls'"
- I manifesti devono ora essere URL assoluti
- Problemi estetici minori possono verificarsi nell'interfaccia utente quando si utilizza la modalità a contrasto elevato del dispositivo
- Gli URL che contengono "%" o "" nella stringa completamente decodificata possono avere problemi di impostazione dell'origine

## <a name="ad-insertion"></a>Inserimento di annunci ##

- Gli annunci potrebbero avere problemi durante l'inserimento (su richiesta o dal vivo) quando nel browser è installato un ad-blocker
- I dispositivi mobili potrebbero avere problemi di riproduzione degli annunci.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Nella finestra DVR del contenuto Live, quando il contenuto termina la sequenza temporale continuerà a crescere fino a cercare l'area o raggiungere la fine della presentazione.
- Le presentazioni live in Firefox con MSE abilitato hanno alcuni problemi
- Le risorse solo audio o video non verranno riprodotte tramite la tecnologia AzureHtml5JS.Assets that are audio or video only won not play back via the AzureHtml5JS tech.
  - Se si vuole riprodurre risorse senza audio o video, è possibile farlo inserendo audio o video vuoti usando [lo strumento Esplora servizi multimediali](https://aka.ms/amse) di Azure
    - Le istruzioni su come inserire l'audio silenzioso sono disponibili [qui](https://azure.microsoft.com/documentation/articles/media-services-advanced-encoding-with-mes/#silent_audio)

## <a name="flash"></a>Lampeggiante ##

- Il contenuto AES non viene riprodotto nella versione 30.0.0.134 di Flash a causa di un bug nella logica di memorizzazione nella cache di Adobe. Adobe ha risolto il problema e lo ha rilasciato in 30.0.0.154
- Guasti tecnici e http (come timeout di rete 404), il giocatore richiederà più tempo per recuperare rispetto ad altri tecnici.
- Clicca sull'area video con la tecnologia flashSS non riprodurre / mettere in pausa il lettore.
- Se l'utente ha installato Flash ma non dà il permesso di caricarlo sul sito, può verificarsi una rotazione infinita. Questo perché il giocatore pensa che il plugin sia installato e disponibile e pensa che il plugin stia eseguendo il contenuto. Codice JavaScript è stato inviato, ma le impostazioni del browser hanno bloccato il plugin di esecuzione fino a quando l'utente accetta la richiesta di consentire il plugin. Ciò può verificarsi in tutti i browser.  

## <a name="silverlight"></a>Silverlight ##

- Funzionalità mancanti
- Guasti tecnici e http (come timeout di rete 404), il giocatore richiederà più tempo per recuperare rispetto ad altri tecnici.
- Safari e Firefox su Riproduzione `"http://` Mac `https://` con Silverlight richiede la definizione esplicita o per l'origine.
- Se un'API non è presente per questa tecnologia, in genere restituirà null.
- Se l'utente ha installato Flash ma non dà il permesso di caricarlo sul sito, può verificarsi una rotazione infinita. Questo perché il giocatore pensa che il plugin sia installato e disponibile e pensa che il plugin stia eseguendo il contenuto. Codice JavaScript è stato inviato, ma le impostazioni del browser hanno bloccato il plugin di esecuzione fino a quando l'utente accetta la richiesta di consentire il plugin. Ciò può verificarsi in tutti i browser.  

## <a name="native-html5"></a>HTML5 nativo ##

- La tecnologia Html5 invia solo l'evento canplaythrough per l'origine del primo set.
- Questa tecnologia supporta solo ciò che il browser ha implementato.  Alcune caratteristiche potrebbero mancare in questa tecnologia.  
- Se un'API non è presente per questa tecnologia, in genere restituirà null.
- Ci sono problemi con sottotitoli e sottotitoli su questa tecnologia. Essi possono o non possono essere disponibili o visualizzabili su questa tecnologia.
- Avendo larghezza di banda limitata nello scenario tecnico HLS/Html5 si traduce in riproduzione audio senza video.

### <a name="microsoft"></a>Microsoft ###

- La riproduzione di IE8 non funziona attualmente a causa di incompatibilità con ECMAScript 5
- In IE e in alcune versioni di Edge, non è possibile inserire la visualizzazione a schermo intero premendo TAB per il pulsante e selezionandolo o utilizzando il tasto di scelta rapida F/f.

## <a name="google"></a>Google ##

- Più profili di codifica nello stesso manifesto presentano alcuni problemi di riproduzione in Chrome e non sono consigliati.
- Chrome non può riprodurre HE-AAC con AzureHtml5JS. Seguire i dettagli sul [bug tracker](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- A partire da Chrome v58, il contenuto widevine deve essere caricato/riprodotto tramite il protocollo https:// altrimenti la riproduzione avrà esito negativo.

## <a name="mozilla"></a>Mozilla ##

- Audio stream switch requires audio streams to have the same codec private data when using AzureHtml5JS. La piattaforma Firefox richiede questo.

## <a name="apple"></a>Apple ##

- Safari su Mac spesso abilita la modalità Risparmio energia per impostazione predefinita con l'impostazione "Stop plug-in to save power", che blocca plugin come Flash e Silverlight quando credono che non sia a favore dell'utente. Questo blocco non blocca le funzionalità esistenti del plugin, ma solo. Dato l'ordine tecnico predefinito, ciò potrebbe causare problemi quando si tenta di riprodurre
  - Mitigazione 1: se il lettore video è 'anteriore e centrale' (all'interno di un limite di 3000 x 3000 pixel a partire dall'angolo superiore sinistro del documento), dovrebbe ancora essere riprodotto.
  - Mitigazione 2: modificare l'ordine tecnico per Safari in modo che sia ["azureHtml5JS", "html5"]. Questa mitigazione ha implicazione di non ottenere tutte le funzionalità che sono disponibili nella tecnologia FlashSS.
- Il contenuto PlayReady tramite Silverlight potrebbe avere problemi di riproduzione in Safari.
- AES e il contenuto di token con restrizioni non vengono riprodotti tramite iOS e dispositivi Android meno recenti.
  - Per ottenere questo scenario, è necessario aggiungere un proxy al servizio.
- L'interfaccia predefinita per iOS Phone viene visualizzata.
- La riproduzione dell'iPhone avviene sempre nella modalità a schermo intero del lettore nativo.
  - Le funzionalità, incluse le didascalie, potrebbero non persistere in questa riproduzione non in linea.
- Al termine della presentazione dal vivo, i dispositivi iOS non terminano correttamente la presentazione.
- iOS non consente funzionalità DVR.
- L'interruttore del flusso audio iOS può essere eseguito solo tramite l'interfaccia utente del lettore nativo di iOS e richiede che i flussi audio abbiano gli stessi dati privati del codec
- Le versioni precedenti di Safari potrebbero avere problemi di riproduzione in diretta streaming.

## <a name="older-android"></a>Android meno recente ##

- AES e il contenuto di token con restrizioni non vengono riprodotti tramite iOS e dispositivi Android meno recenti.
  - Per ottenere questo scenario, è necessario aggiungere un proxy al servizio.

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)