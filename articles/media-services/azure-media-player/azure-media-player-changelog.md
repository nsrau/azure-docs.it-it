---
title: Log delle modifiche di Azure Media Player
description: Log delle modifiche di Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: 9d126f591da1bffebbfd29119c56927cfbb21dcd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370577"
---
# <a name="changelog"></a>Log delle modifiche

## <a name="236-official-update-september-21-2020"></a>2.3.6 (aggiornamento ufficiale del 21 settembre 2020)

### <a name="features-236"></a>Funzionalità 2.3.6

È stato aggiunto il supporto per solo audio per la tecnologia azureHtml5JS (DASH) Supporto per l'avvio ritardato della trascrizione live Supporto della modifica della lingua nella trascrizione live

### <a name="bug-fixes-236"></a>Correzioni di bug 2.3.6

Quando si usa "playsinline" nelle riproduzioni HLS nei dispositivi Apple, facendo clic sul pulsante "LIVE" il video viene riavviato L'immagine del poster AMP causa talvolta un'eccezione Il pulsante del volume non era presente durante la riproduzione di HLS FairPlay [Accessibilità] Descrizioni comando non definite per i pulsanti quando viene usata la tastiera [Accessibilità] Il rapporto di luminosità è inferiore a 1.3:1 per la barra di stato [Accessibilità] Lo stato attivo della tastiera a volte non torna al pulsante di qualità video [Accessibilità] I controlli non sono visibili nella schermata Video, impedendo così all'assistente vocale di trovarli

### <a name="changes-236"></a>Modifiche 2.3.6

Restituzione di errori significativi per KeyDelivery alle applicazioni chiamanti

## <a name="235-official-update-june-1-2020"></a>2.3.5 (aggiornamento ufficiale del 1° giugno 2020)

### <a name="bug-fixes-235"></a>Correzioni di bug 2.3.5

- [Accessibilità] Il listener di tasto ESC nel riquadro Opzioni è collegato al documento
- [Accessibilità] È possibile impedire la scomparsa dell'interfaccia utente del lettore se la barra di controllo o il menu Opzioni hanno lo stato attivo
- La barra di controllo mostra il tempo reale quando è abilitata l'impostazione dello schermo relativa al tempo reale

### <a name="changes-235"></a>Modifiche 2.3.5

- Aggiunta e documentazione di un messaggio di errore per il codice errore 0x00400005

## <a name="234-official-update-march-4-2020"></a>2.3.4 (aggiornamento ufficiale del 4 marzo 2020)

### <a name="bug-fixes-234"></a>Correzioni di bug 2.3.4

- Non è possibile impostare overrideLicenseAcquistionUrl di PlayReady
- Non è possibile riprodurre alcuni contenuti con discontinuità
- [Accessibilità] Il valore dell'attributo ID per gli avvisi dell'utilità per la lettura dello schermo deve essere univoco
- [Accessibilità] Durante l'esplorazione della finestra di dialogo delle impostazioni Sottotitoli codificati lo stato attivo non rimane nella finestra di dialogo

### <a name="changes-234"></a>Modifiche 2.3.4

- Registrazione del valore Content-Length dopo un download riuscito per contribuire all'analisi degli errori di decrittografia 2.3.3 (aggiornamento ufficiale 12 novembre 2019)

### <a name="features-234"></a>Funzionalità 2.3.4

- Aggiunta di supporto per la visualizzazione del tempo reale di un video come sovrimpressione e sulla barra di controllo

### <a name="bug-fixes-234"></a>Correzioni di bug 2.3.4

- Il cambio di traccia audio funziona ma restituisce come output un errore di tipo "L'oggetto non supporta la proprietà o metodo 'enabled'" in Internet Explorer 11 e Windows 7
- Il cambio di traccia audio ha esito negativo quando il buffer è completamente caricato
- Il cambio di traccia audio ha esito negativo quando un utente sospende il video e si sposta molto rapidamente tra tracce audio
- [Accessibilità] Non sono definite descrizioni comando per il controllo video nel lettore video
- Pulsanti per il volume mancanti in Html5 in base al momento in cui viene ricevuto "loadstart"
- [Accessibilità] Non è possibile impostare il testo alternativo per l'immagine del poster
- [Accessibilità] Lo stato attivo dell'applicazione viene perso dopo la selezione di "Fine" nella finestra di dialogo delle impostazioni dei sottotitoli
- [Accessibilità] Sono definiti attributi ARIA non corretti per "video" in "anteprima segmenti"

### <a name="changes-234"></a>Modifiche 2.3.4

- Rimozione delle etichette di sottotitolo/tracce vuote durante la riproduzione di HLS in iOS e macOS Safari
- Riduzione del numero di errori 412 per i sottotitoli IMSC1
- Restituzione come output di un avviso nella console per 10 risposte di sottotitoli IMSC1 vuote per contribuire al debug in tempo reale

## <a name="232-official-update-october-9-2019"></a>2.3.2 (aggiornamento ufficiale del 9 ottobre 2019)

### <a name="features-232"></a>Funzionalità 2.3.2

\- Aggiunta di supporto di PlayReady per la riproduzione DASH per il browser Microsoft Edge basato su Chromium

### <a name="bug-fixes-232"></a>Correzioni di bug 2.3.2

- La velocità di riproduzione corrente viene mostrata nel menu corrispondente solo se l'utente configura manualmente questa opzione
- [Accessibilità] Non è possibile comprimere il riquadro "Impostazioni" con il tasto ESC
- [Accessibilità] Il tasto di scelta rapida "M" per AMP non funziona quando l'Assistente vocale è attivato

### <a name="changes-232"></a>Modifiche 2.3.2

- Per i browser che non supportano il codec audio E-AC3, le tracce audio E-AC3 sono nascoste nel menu della traccia audio
- Per i browser che supportano il codec audio E-AC3 viene selezionata per impostazione predefinita una traccia audio E-AC3
- Per i browser che non supportano il cambio di codec audio, le tracce audio con codec diverso dalla traccia selezionata sono nascoste nel menu della traccia audio

## <a name="231-official-update-august-12-2019"></a>2.3.1 (aggiornamento ufficiale del 12 agosto 2019)

### <a name="features-231"></a>Funzionalità 2.3.1

- Segnalazione di un evento quando vengono ricevute caselle emsg nella riproduzione DASH - Aggiunta di supporto per mostrare le tracce audio ec-3 nel menu audio nei browser che supportano ec-3 e consentono lo scambio della traccia audio da aac a ec3 e viceversa solo nel browser Microsoft Edge basato su Chromimum

### <a name="bug-fixes-231"></a>Correzioni di bug 2.3.1

- Il menu della traccia audio risulta danneggiato dopo la rimozione di tracce ec-3
- La durata corrente può essere superiore alla durata del video
- Non è possibile impostare la velocità di riproduzione tramite initialSpeed
- Il lettore sembra spesso bloccato dopo una ricerca
- In un touchscreen in Microsoft Edge e Internet Explorer la selezione o il passaggio del puntatore sulla barra di ricerca dopo l'ingrandimento di una pagina non consente di ottenere in modo preciso il segmento corretto del video
- [Accessibilità] L'attributo aria-label per Play/Pausa non è descrittivo per il lettore video. È possibile eseguire il mapping dell'errore relativo al segmento attivo per flashSS all'errore relativo alla funzione amp corretta
- [Accessibilità] I ruoli ARIA usati per Play/Pausa devono essere conformi ai valori validi (.vjs-text-track-display)
- [Accessibilità] Alcuni ruoli ARIA devono essere contenuti in determinati elementi padre
- [Accessibilità] Non sono stati definite descrizioni comando per il pulsante Play/Pausa del lettore video e i sottotitoli IMSC1 possono scomparire dopo la ricerca nel buffer audio/video corrente

### <a name="changes-231"></a>Modifiche 2.3.1

- Quando si riceve un errore segmentDecryptError e il lettore si trova già sul bordo attivo, il lettore aggiorna ora il manifesto invece di provare il segmento successivo
- Aggiunta di altre opzioni di registrazione per la diagnosi
- Aggiornamento della documentazione per includere il supporto di FairPlay per iOS Safari
- Aggiunta di un esempio per "srclang" dell'opzione IMSC1
- Aggiunta degli override padding, textPadding, boxShadow per le tracce di testo.
- Aggiunta di un codice errore (0x0020025B) per differenziare tale errore di download di segmenti a causa di Internet assente invece di generare semplicemente 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (release ufficiale del 30 aprile 2019)

### <a name="features-230"></a>Funzionalità 2.3.0

- Aggiunta del supporto per i sottotitoli IMSC1 per DASH
- Aggiunta del supporto per asset solo video per DASH
- Aggiunta della variabile presentationTimeOffsetInSec dell'API

### <a name="bug-fixes-230"></a>Correzioni di bug 2.3.0

- Il profilo dell'euristica LowLatency di AMP interferisce con le opzioni "Disattiva audio" e "Riattiva audio" della riproduzione video in iOS perché alcune lingue usano traduzioni errate
- Il valore aria-valuenow del dispositivo di scorrimento dell'indicatore di stato è a volte errato
- Il valore del ruolo ARIA della visualizzazione della traccia di testo non è corretto

### <a name="changes-230"></a>Modifiche 2.3.0

- I log includono ora le dimensioni dei frammenti di file multimediali scaricati
- Rimozione del supporto per Internet Explorer 9 e Internet Explorer 10
- Aggiornamento dell'esempio CEA708 per mostrare i sottotitoli allineati a sinistra
- Inclusione di MediaError.message nei log per gli errori di riproduzione

## <a name="224-official-update-february-22-2019"></a>2.2.4 (aggiornamento ufficiale del 22 febbraio 2019) ##

### <a name="bug-fixes-224"></a>Correzioni di bug 2.2.4 ###

- [Correzione di bug][Azure Media Player][Accessibilità] Rimozione di una scheda fantasma raggiungibile quando viene visualizzata la schermata di errore
- [Correzione di bug][Azure Media Player] Correzione del tasto di scelta rapida 'M' per IE11 e Microsoft Edge
- [Correzione di bug][Azure Media Player] Correzione di un'eccezione per i sottotitoli CEA708
- [Correzione di bug][Azure Media Player] Correzione di un problema di blocco video per il browser Microsoft Edge

### <a name="changes-224"></a>Modifiche 2.2.4 ###

- [Modifica][Azure Media Player] Quando si verifica un errore di decrittografia di un frammento, il lettore riprova a eseguire il frammento corrente e altri per ripristinare la riproduzione
- [Modifica][Azure Media Player] Azure Media Player è stato reso più tollerante ai frammenti video o audio sovrapposti

## <a name="223-official-update-january-9-2019"></a>2.2.3 (aggiornamento ufficiale del 9 gennaio 2019) ##

### <a name="features-223"></a>Funzionalità 2.2.3 ###

- [Funzionalità][HLS] Aggiunta del menu di tracce audio per la riproduzione in HLS Safari

### <a name="bug-fixes-223"></a>Correzioni di bug 2.2.3 ###

- [Correzione di bug][Azure Media Player][Accessibilità] Durante le riproduzioni di trasmissioni in diretta, non è possibile usare il pulsante corrispondente tramite tastiera
- [Correzione di bug][Azure Media Player] Correzione degli errori 0x0400003 falsi positivi dovuti a un test MSE non riuscito
- [Correzione di bug][Azure Media Player] Correzione di un problema per cui il video potrebbe bloccarsi durante l'avvio di un evento di streaming live

### <a name="changes-223"></a>Modifiche 2.2.3 ###

- [Modifica][Azure Media Player] Aggiunta di altre informazioni nel log per migliorare la diagnostica
- [Modifica][Azure Media Player] Quando sono disponibili più bitrate alla stessa risoluzione dello schermo, è possibile selezionarli tutti

## <a name="222-official-update"></a>2.2.2 (aggiornamento ufficiale) ##

### <a name="bug-fixes-222"></a>Correzioni di bug 2.2.2 ###

- [Correzione di bug][Azure Media Player] Quando il lettore rileva un'interruzione di rete temporanea, interrompe immediatamente la riproduzione
- [Correzione di bug][Azure Media Player][Accessibilità] La finestra di dialogo di errore non è accessibile tramite tastiera
- [Correzione di bug][Azure Media Player] Durante la riproduzione di asset solo audio, viene visualizzata una rotellina infinita invece dell'errore di mancato supporto

### <a name="changes-222"></a>Modifiche 2.2.2 ###

- [Modifica][Azure Media Player] Aggiunta di stringhe localizzate per l'interfaccia utente di annunci

## <a name="221-official-update"></a>2.2.1 (aggiornamento ufficiale) ##

### <a name="features-221"></a>Funzionalità 2.2.1 ###

- [Funzionalità][CMAF] Aggiunta del supporto per HLS CMAF

### <a name="bug-fixes"></a>Correzioni di bug ###

- [Correzione di bug][Azure Media Player] I timer non cancellati nella logica di ripetizione dei tentativi generando errori di riproduzione
- [Correzione di bug][Azure Media Player][Firefox] L'evento di fine non viene attivato in Firefox e Chrome quando il programma in diretta viene arrestato
- [Correzione di bug][Azure Media Player] Controlli visualizzati dopo setsource, anche quando sono impostati su False nelle opzioni del lettore

### <a name="changes"></a>Modifiche ###

- [Modifica][Sottotitoli in tempo reale] Nome dell'API cambiato da 608 a 708 per i sottotitoli CEA. Per altre informazioni, vedere le [impostazioni dei sottotitoli CEA708](/javascript/api/azuremediaplayer/cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (versione ufficiale) ##

### <a name="features-220"></a>Funzionalità 2.2.0 ###

- [Funzionalità][Azurehtml5JS][Flash][LiveCaptions] Supporto dei sottotitoli CEA 708 in nelle tecnologie Azurehtml5JS e FlashSS per contenuto Clear e AES.

### <a name="bug-fixes-220"></a>Correzioni di bug 2.2.0 ###

- [Correzione di bug] Rilevamento della versione di Flash non funzionante in Chrome/Microsoft Edge

### <a name="changes-220"></a>Modifiche 2.2.0 ###

- [Modifica][Azure Media Player][Euristica] Nome del profilo di euristica cambiato da QuickStartive a LowLatency
- [Modifica][Flash] Modifica in Flash Player per il rilevamento della versione, per consentire la riproduzione di contenuto AES con il nuovo aggiornamento di Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (hotfix ufficiale) ##

### <a name="bug-fixes-219"></a>Correzioni di bug 2.1.9 ###

- [Correzione di bug][Live] Eccezione che si verifica con la transizione di streaming live in video on demand/archivi live

### <a name="changes-219"></a>Modifiche 2.1.9 ###

- [Modifica][Flash][AES] Modifica della logica della tecnologia Flash in modo da non usare sharedbytearray per la decrittografia AES, perché Adobe ne ha bloccato l'utilizzo a partire da Flash 30. Si noti che la riproduzione funzionerà solo dopo che Adobe avrà distribuito una nuova versione di Flash a causa di un bug presente nella versione 30. Per altri dettagli, vedere [Problemi noti](azure-media-player-known-issues.md)

## <a name="218-official-update"></a>2.1.8 (aggiornamento ufficiale) ##

### <a name="bug-fixes-218"></a>Correzioni di bug 2.1.8 ###

- [Correzione di bug] La rotellina a volte non viene visualizzata dopo la ricerca e prima della riproduzione
- [Correzione di bug] Il lettore non viene avviato con l'audio disattivato quando l'opzione corrispondente è abilitata
- [Correzione di bug] Il dispositivo di scorrimento del volume viene visualizzato quando i controlli sono impostati su False
- [Correzione di bug] La riproduzione viene a volte ripetuta quando l'utente passa al margine live
- [Correzione di bug][Firefox] Il lettore genera a volte un'eccezione JavaScript al caricamento
- [Correzione di bug][Accessibilità] Il pulsante di riproduzione/pausa/volume perde il contorno dello stato attivo quando viene selezionato tramite i controlli
- [Correzione di bug] Correzione della perdita di memoria quando il lettore viene eliminato
- [Correzione di bug] La chiamata a src() dopo l'uscita degli errori del lettore non reimposta l'origine
- [Correzione di bug][Live] Azure Media Player rimane in stato di caricamento costante quando l'utente fa clic sul pulsante Live al termine della trasmissione
- [Correzione di bug][Chrome] Il lettore si blocca e la riproduzione non riesce quando il browser è ridotto a icona in background.

### <a name="changes-218"></a>Modifiche 2.1.8 ###

- [Modifica] Aggiornamento dell'errore 0x0600001 da visualizzare quando contenuto AES viene riprodotto con Flash 30, che non è supportato al momento. Per altri dettagli, vedere [Problemi noti](azure-media-player-known-issues.md)
- [Modifica] Aggiunta di altri tentativi di ripetizione per gli scenari live quando le richieste di manifesto restituiscono 404 oppure manifesti vuoti.

## <a name="217-official-update"></a>2.1.7 (aggiornamento ufficiale) ##

### <a name="features-217"></a>Funzionalità 2.1.7 ###

- [Funzionalità][AzureHtml5JS] Aggiunta dell'opzione di configurazione per scaricare i dati non aggiornati nel buffer di origine multimediale

### <a name="bug-fixes-217"></a>Correzioni di bug 2.1.7 ###

- [Correzione di bug][Accessibilità][Utilità di lettura dello schermo] Rimozione dell'intestazione vuota inclusa dal lettore quando il titolo non è impostato
- [Correzione di bug][UWA] Azure Media Player genera un'eccezione durante la riproduzione in un app di Windows universale
- [Correzione di bug][OSX] setActiveTextTrack() non funziona in Safari in OSx
- [Correzione di bug][Live] Se si fa clic sul margine live dopo l'eliminazione e la reinizializzazione, il lettore genera un'eccezione
- [Correzione di bug][Interfaccia] Ora corrente troncata per determinati asset
- [Correzione di bug][DRM] Correzione inclusa per supportare la riproduzione in browser che supportano più CENC DRM

### <a name="changes-217"></a>Modifiche 2.1.7 ###

- [Modifica][Esempi][Accessibilità] Aggiunta del tag di lingua a tutti gli esempi

## <a name="216-official-update"></a>2.1.6 (aggiornamento ufficiale) ##

### <a name="bug-fixes-216"></a>Correzioni di bug 2.1.6 ###

- [Correzione di bug] Azure Media Player visualizza una durata non corretta per specifici asset
- [Correzione di bug][FairPlay-HLS] Errori di Fairplay non propagati all'interfaccia utente
- [Correzione di bug] Proprietà euristiche personalizzate ignorate in Azure Media Player 2.1.5.

### <a name="changes-216"></a>Modifiche 2.1.6 ###

- [Modifica][FairPlayDRM] Rimozione del timeout per le richieste di certificato e di licenza per FairPlay per mantenere la parità con le implementazioni PlayReady e Widevine
- [Modifica] Miglioramenti vari dell'euristica per contrastare il contenuto sfocato

### <a name="features-216"></a>Funzionalità 2.1.6 ###

- [Funzionalità] Aggiunta del supporto per il formato mpd-time-cmaf

## <a name="215-official-hotfix"></a>2.1.5 (hotfix ufficiale) ##

### <a name="bug-fixes-215"></a>Correzioni di bug 2.1.5 ###

- [Correzione di bug][Sottotitoli] Il rendering dello stile VTT non viene eseguito correttamente dal lettore
- [Correzione di bug][Accessibilità] Pulsante Live sprovvisto di aria-label

## <a name="214-official-update"></a>2.1.4 (aggiornamento ufficiale) ##

### <a name="bug-fixes-214"></a>Correzioni di bug 2.1.4 ###

- [Correzione di bug][Accessibilità][Stato attivo] Gli utenti non possono usare la tabulazione per spostare lo stato attivo su pulsanti personalizzati aggiunti a destra del pulsante per lo schermo interno sulla barra di controllo
- [Correzione di bug][IE11][Barra del volume] Lo spostamento tramite tabulazione sul popup del volume fa lampeggiare l'intero schermo video in IE11 in modalità schermo intero
- [Correzione di bug][Interfaccia][Allineamento] Spazio visualizzato tra la barra di controllo e il popup della barra del volume
- [Correzione di bug][Azure Media Player][Sottotitoli] Le tracce incorporate precedenti non vengono cancellate quando l'origine viene cambiata in un lettore esistente
- [Correzione di bug][Accessibilità][Assistente vocale] L'utilità per la lettura dello schermo legge in modo non corretto il controllo del volume
- [Correzione di bug][FlashSS] L'evento di riproduzione a volte non viene attivato dalla tecnologia Flash
- [Correzione di bug][Azure Media Player][Stato attivo] Quando il lettore ha lo stato attivo ed è in modalità schermo intero, l'operazione di riproduzione/pausa richiede due clic
- [Correzione di bug][Azure Media Player][Interfaccia] Durata non corretta visualizzata sull'indicatore di stato per un asset specifico
- [Correzione di bug][Annunci][Ad Butler] Il parser VAST non gestisce un file VAST che non ha un evento di stato
- [Correzione di bug][SDN][Azure Media Player 2.1.1] Correzione del problema per il supporto del plug-in Hive SDN
- [Correzione di bug][Accessibilità] L'assistente vocale legge "Midnight Mute Button" quando lo stato attivo si trova sul pulsante del volume

### <a name="changes-214"></a>Modifiche 2.1.4 ###

- [Modifica][Accessibilità][Assistive Technology] I pulsanti hanno ora una proprietà aria-live per migliorare l'esperienza con la assistive technology
- [Modifica][Accessibilità][Pulsante del volume|Assistente vocale] Accessibilità migliorata del pulsante del volume tramite modifica della funzionalità di spostamento tramite tabulazione e del comportamento del dispositivo di scorrimento. Queste modifiche consentono agli utenti di tastiera di modificare più facilmente il volume del lettore
- [Modifica] Aumento del timeout di inattività del menu di scelta rapida da 3 a 5 secondi
- [Modifica][Accessibilità][Luminosità] Rapporto di contrasto della luminosità migliorato nei menu a discesa delle impostazioni dei sottotitoli

## <a name="213-official-update"></a>2.1.3 (aggiornamento ufficiale) ##

### <a name="bug-fixes-213"></a>Correzioni di bug 2.1.3 ###

- [Correzione di bug][Plug-in|Title Overlay] Il plug-in Title Overlay genera eccezioni JS con Azure Media Player v2.X e versioni successive
- [Correzione di bug] L'evento Source Set viene inviato alla console JavaScript anche quando la registrazione è disattivata
- [Correzione di bug][Interfaccia] I suggerimenti sul tempo del lettore vengono visualizzati al di fuori del contesto del lettore quando si passa il puntatore del mouse sulla barra di fine o di durata
- [Correzione di bug][Accessibilità][Utilità di lettura dello schermo] L'assistente vocale legge "Region Landmark" o "Video Player Region Landmark" quando lo stato attivo si trova sul lettore
- [Correzione di bug][Azure Media Player] Non è possibile disabilitare il contorno del lettore tramite CSS
- [Correzione di bug][Accessibilità] Non è possibile spostare lo stato attivo tramite tabulazione sull'intero lettore in modalità schermo intero
- [Correzione di bug][Interfaccia][Live] L'interfaccia non risponde al testo LIVE localizzato in giapponese
- [Correzione di bug][Interfaccia] La durata e il tempo corrente vengono tagliate quando il flusso è superiore a 60 minuti - [Correzione di bug][iPhone|Live] Il letture mostra il testo per il tempo corrente e la durata sulla barra di controllo
- [Correzione di bug][Azure Media Player] La chiamata ad API euristiche del lettore genera eccezioni JavaScript
- [Correzione di bug][Html5 nativo|iOS] La proprietà Videotag "playsinline" non si propaga al lettore
- [Correzione di bug][iOS|iframe] Il lettore non può passare in modalità schermo intero su iPhone se viene caricato in un iframe
- [Correzione di bug][Azure Media Player][Euristica] Azure Media Player funziona sempre con un profilo ibrido indipendentemente dalle opzioni del lettore
- [Correzione di bug][Azure Media Player|Win 8.1] Viene generata un'eccezione se il lettore è ospitato in un'app di Win8.1 con una visualizzazione Web

### <a name="changes-213"></a>Modifiche 2.1.3 ###

- [Modifica][Azure Media Player] Aggiunta di informazioni sull'endpoint CDN nell'evento FragmentDownloadComplete
- [Modifica][Azure Media Player][Live] Miglioramento e ottimizzazione della latenza di eventi streaming live

## <a name="212-official-hotfix"></a>2.1.2 (hotfix ufficiale) ##

### <a name="bug-fixes-212"></a>Correzioni di bug 2.1.2 ####

- [Correzione di bug][Accessibilità][Assistente vocale di Windows] L'assistente vocale legge "Progress midnight" nel contesto dell'indicatore di stato e quando il tempo corrente è 0:00
- [Correzione di bug][Interfaccia] Le dimensioni del logo sono hardcoded nel codice JavaScript
- [Accessibilità][Tasti di scelta rapida] I tasti di scelta rapida non sono abilitati quando si fa clic sul lettore.

### <a name="changes-212"></a>Modifiche 2.1.2 ####

- [Modifica][Registrazione] L'URL del manifesto viene registrato quando il lettore non riesce a caricare il manifesto

### <a name="features-212"></a>Funzionalità 2.1.2 ###

- [Modifica][Prestazioni][Ottimizzazione] Miglioramento dei tempi di caricamento e di avvio del lettore

## <a name="211-official-update"></a>2.1.1 (aggiornamento ufficiale) ##

### <a name="bug-fixes-211"></a>Correzioni di bug 2.1.1 ####

- [Correzione di bug][iOS] L'impostazione di riproduzione automatica su False genera una rotella infinita in Safari per iOS
- [Correzione di bug] La ricerca di un tempo maggiore della durata del contenuto genera una rotella infinita
- [Correzione di bug] I tasti di scelta rapida richiedono l'uso di più TAB per il corretto funzionamento del contesto del lettore
- [Correzione di bug] Il video si blocca per alcuni secondi dopo il ridimensionamento del lettore in determinati asset
- [Correzione di bug] Rotella infinita (dopo il completamento della ricerca) quando l'utente esegue rapidamente più ricerche
- [Correzione di bug] La barra di controllo non è nascosta durante l'inattività
- [Correzione di bug] Se si apre un'app Web che ospita Azure Media Player, è possibile che la pagina Web venga caricata due volte
- [Correzione di bug] Rotella infinita durante la riproduzione del contenuto di determinati asset tramite tecnologia Flash
- [Correzione di bug] Il menu di altre opzioni non viene visualizzato con i plug-in di terze parti
- [Correzione di bug][Interfaccia|Tube][Live] Quando il lettore si trova al margine live di un programma, vengono visualizzate due icone live
- [Correzione di bug][Interfaccia] Non è possibile disabilitare il logo
- [Correzione di bug][DD + contenuto] Viene visualizzata una rotella continua per gli asset contenenti tracce audio Dolby Digital
- [Correzione di bug] La versione più recente di Azure Media Player si blocca quando si passa a tracce di lingua audio durante lo streaming live
- [Correzione di bug] Correzione della scomparsa dello sfondo per la rotella
- [Correzione di bug] Correzione del bug per cui viene visualizzata una rotella infinita negli esempi statici di token Flash AES

### <a name="changes-211"></a>Modifiche 2.1.1 ####

- [Modifica] Aggiunga del codice di errore per il requisito HTTPS Widevine: a partire da Chrome v58, il contenuto di Widevine deve essere caricato/riprodotto tramite il protocollo `https://`, altrimenti la riproduzione non riesce.
- [Modifica] Aggiunta dell'aria-label per la rotella di caricamento, così l'assistive technology può specificare che è in corso il caricamento di video  

## <a name="210-official-release"></a>2.1.0 (versione ufficiale) ##

### <a name="features-210"></a>Funzionalità 2.1.0 ###

- [Funzionalità][AzureHtml5JS] Supporto di annunci VOD prima, durante e dopo gli avvolgimenti
- [Funzionalità][Beta][AzureHtml5JS] Supporto per annunci live prima, durante e dopo gli avvolgimenti
- [Funzionalità] Aggiunta di una nuova opzione dell'interfaccia, AMP-flush
- [Funzionalità] Aggiunta di aria-label migliorate per una maggiore integrazione con utilità per la lettura dello schermo/assistive technology
- [Funzionalità][Interfaccia] L'interfaccia ora visualizza chiaramente tutte le icone e tutti i pulsanti in modalità a contrasto elevato

### <a name="bug-fixes-210"></a>Correzioni di bug 2.1.0 ###

- [Correzione di bug] Numero di correzioni dell'accessibilità e dell'interfaccia utente
- [Correzione di bug] Azure Media Player non viene caricato correttamente in IE9

### <a name="changes-210"></a>Modifica 2.1.0 ###

- [Modifica] Ristrutturazione di elementi DOM nel lettore per supportare il funzionamento degli annunci
- [Modifica] Passaggio da CSS a SCSS per lo sviluppo dell'interfaccia
- [Modifica][Eesempi] Aggiunta di un esempio per gli annunci VOD
- [Modifica][Esempi] Aggiunta di un esempio per la velocità di riproduzione
- [Modifica][Esempi] Aggiunta di un esempio per l'interfaccia Flush

## <a name="200-beta-release"></a>2.0.0 (versione beta) ##

- [Modifica] Aggiornamento a VJS5
- [Funzionalità] Aggiunta di una nuova API fluida per la reattività del lettore
- [Funzionalità] Velocità di riproduzione
- [Modifica] Passaggio da CSS a SCSS per l'interfaccia

## <a name="183-official-hotfix-update"></a>1.8.3 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-183"></a>Correzioni di bug 1.8.3 ###

- [Correzione di bug][AzureHtml5JS] Alcuni asset con DTS negativo non vengono riprodotti in Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-182"></a>Correzioni di bug 1.8.2 ###

- [Correzione di bug][AzureHtml5JS] I bitrate audio più alti non vengono riprodotti tramite AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (aggiornamento ufficiale) ##

### <a name="bug-fixes-181"></a>Correzioni di bug 1.8.1 ###

- [Correzione di bug][iOS] Sottotitoli non visualizzati nel lettore nativo
- [Correzione di bug][Azure Media Player] Gli URL di streaming supportati dalla rete CDN aggiunti con token di autenticazione non vengono riprodotti
- [Correzione di bug][Fairplay] Il codice di errore FairPlay non include l'ID tecnologia (bit [31-28] del codice errore). Per altri dettagli, vedere i codici di errore
- [Correzione di bug][Safari][PlayReady] I contenuto PlayReady in Safari genera una rotella infinita

### <a name="changes-181"></a>Modifiche 1.8.1 ###

- [Modifica][Html5] Modifica dei log dettagliati della tecnologia Html5 nativa per contenere eventi di VideoTag

## <a name="180-official-update"></a>1.8.0 (aggiornamento ufficiale) ##

### <a name="features-180"></a>Funzionalità 1.8.0 ###

- [Funzionalità][DRM] Aggiunta del supporto per FairPlay (per altre informazioni, vedere [Contenuto protetto](azure-media-player-protected-content.md))

### <a name="bug-fixes-180"></a>Correzioni di bug 1.8.0 ###

- [Correzione di bug][Azure Media Player] La ricerca dell'utente non attiva un evento di attesa quando la velocità della rete è limitata
- [Correzione di bug][FlashSS] La selezione della qualità nella tecnologia Flash genera un'eccezione
- [Correzione di bug][Azure Media Player] La selezione dinamica della qualità non viene visualizzata nel menu di scelta rapida
- [Correzione di bug][Interfaccia] È difficile selezionare l'ultima voce dei menu di scelta rapida

### <a name="changes-180"></a>Modifiche 1.8.0 ###

- [Modifica] Aggiornamento del lettore ai requisiti correnti di Chrome EME
- [Modifica] Il valore predefinito di techOrder è cambiato per supportare la nuova tecnologia html5FairPlayHLS (per altre informazioni, vedere [Contenuti protetto](azure-media-player-protected-content.md))
- [Modifica][AzureHtml5JS] Abilitazione della riproduzione MPEG-Dash in Safari
- [Modifica][Esempi] Modifica degli esempi multi-DRM per supportare FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-174"></a>Correzioni di bug 1.7.4 ###

- [Correzione di bug][Chrome] Nel contesto del lettore viene visualizzato un contorno blu intorno all'handle di ricerca
- [Correzione di bug][IE9] Eccezione JavaScript generata quando il lettore viene caricato in IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-173"></a>Correzioni di bug 1.7.3 ###

- [Correzione di bug][AzureHtml5JS] Timeout del lettore nelle reti vincolate

### <a name="changes-173"></a>Modifiche 1.7.3 ###

- [Modifica] Abilitazione di Webcrypto in Microsoft Edge per la decrittografia di contenuto AES
- [Modifica] Ottimizzazione dell'euristica di Azure Media Player per tenere conto dei blocchi memorizzati nella cache
- [Modifica][AzureHtml5JS] Ottimizzazione dell'euristica tramite riduzione della latenza di stima della larghezza di banda

## <a name="172-official-hotfix-update"></a>1.7.2 (aggiornamento hotfix ufficiale) ##

### <a name="features-172"></a>Funzionalità 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Funzionalità][AzureHtml5JS|Firefox] Abilitazione della riproduzione Widevine con EME per Firefox 47 e versioni successive
- [Funzionalità] Aggiunta dell'evento per l'eliminazione del lettore
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Correzioni di bug 1.7.2 ###

- [Correzione di bug] Parametri di query codificati dell'URL di rete CDN Akamai non correttamente decodificati
- [Correzione di bug] Eccezione generata in manifestPlayableWindowLength()
- [Correzione di bug] L'utente non può fare sempre clic sul pulsante di riproduzione del video per riguardarlo dopo la fine
- [Correzione di bug] Ridimensionamento reattivo non conforme alle modifiche rapide delle dimensioni della finestra
- [Correzione di bug][Microsoft Edge|IE] Il ridimensionamento reattivo non viene applicato al caricamento di pagine con larghezza= x, altezza =automatica
- [Correzione di bug][Android|Chrome] Chrome chiede le autorizzazioni per la riproduzione di contenuto DRM non crittografato
- [Correzione di bug][Accessibilità][Microsoft Edge] I controlli della tastiera non selezionano correttamente le voci di menu di scelta rapida
- [Correzione di bug][Accessibilità] Bordo visualizzato mancante in modalità a contrasto elevato
- [Correzione di bug][FlashSS] Listener dell'evento mouseup non rimosso dopo che l'eliminazione del lettore genera un'eccezione
- [Correzione di bug][FlashSS] Problema con l'analisi dell'URL del manifesto con spazi codificati
- [Correzione di bug][iOS] Errore di tipo durante la valutazione di tech.featuresVolumeControl

### <a name="changes-172"></a>Modifiche 1.7.2 ###

- [Modifica][DRM] Spostamento dei controlli DRM dopo l'impostazione dell'origine per eseguire i controlli solo quando il contenuto è crittografato
- [Modifica][AES] Rimozione del corpo non definito di tipo normale dalla richiesta di distribuzione della chiave
- [Modifica][Accessibilità] Assistente vocale di Windows ora legge "Media Player" quando il contesto si trova nel lettore invece che nelle proprietà

## <a name="171-official-hotfix-update"></a>1.7.1 (aggiornamento hotfix ufficiale) ##

### <a name="features-171"></a>Funzionalità 1.7.1 ###

- [Funzionalità] Aggiunta opzione per profilo euristico ibrido (questo profilo è impostato per impostazione predefinita)

### <a name="bug-fixes-171"></a>Correzioni di bug 1.7.1 ###

- [Correzione di bug] La progettazione reattiva non funziona in base allo standard HTML5 (larghezza=100%, altezza= automatica)
- [Correzione di bug] Il comportamento di valori percentuali per larghezza e altezza non è come previsto in v 1.7.0

## <a name="170-official-update"></a>1.7.0 (aggiornamento ufficiale) ##

### <a name="features-170"></a>Funzionalità 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Funzionalità][AzureHtml5JS][FlashSS] Aggiunta di currentMediaTime() per ottenere il tempo corrente in secondi del codificatore multimediale
- [Funzionalità][FlashSS] Implementazione del download delle API di telemetria con videoBufferData() e AudioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Funzionalità][FlashSS] Aggiunta dell'evento 'downloadbitratechanged'
- [Funzionalità] Miglioramento del tempo di caricamento rispetto alle versioni precedenti del lettore
- [Funzionalità] Registrazione degli errori nella console JavaScript

### <a name="bug-fixes-170"></a>Correzioni di bug 1.7.0 ###

- [Correzione di bug] URL del poster codificato con parametri di stringa di query non visualizzato nel lettore
- [Correzione di bug] Eccezione generata quando non sono caricate tecnologie e viene chiamata l'API amp.Player.poster()
- [Correzione di bug] Eccezione generata quando le funzioni provano ad accedere al lettore dopo l'eliminazione
- [Correzione di bug][Accessibilità] Contorno mancante sullo stato attivo nell'intestazione di ricerca dell'indicatore di stato
- [Correzione di bug][Accessibilità] I menu di scelta rapida presentano un'ombreggiatura in modalità a contrasto elevato
- [Correzione di bug][iOS] La riproduzione di sottotitoli WebVTT del lettore nativo non funzionano
- [Correzione di bug][AzureHtml5JS] Dovrebbe essere visualizzato l'errore 0x0100002 quando viene riprodotto un flusso HTTP in un sito HTTPS che invece genera la rotella infinita come conseguenza di contenuto misto
- [Correzione di bug][AzureHtml5JS] Il segmento finale mancante causa un errore ciclico di controllo dell'integrità che visualizza uno stato di buffering infinito
- [Correzione di bug][AzureHtml5JS] Nome della traccia audio non corretto quando useManifestForLabel= false e vengono usati codici di lingua di tre lettere
- [Correzione di bug][AzureHtml5JS|Chrome] Stato di buffer infinito percepito alla fine del contenuto causato dall'imprecisione della virgola mobile nella durata con JavaScript in Chrome
- [Correzione di bug][FlashSS] Errore intermittente non irreversibile momentaneamente visualizzato quando viene creato Flash Player
- [Correzione di bug][FlashSS] La riproduzione non riesce quando i flussi video e audio usano scale cronologiche diverse a causa dell'imprecisione dell'arrotondamento che non riesce con il messaggio "Fragment url (...) is failed to generate FLVTags"
- [Correzione di bug][FlashSS] Problema con l'analisi degli URL del manifesto con spazi codificati
- [Correzione di bug][FlashSS] Controllo mancante per determinare se la versione di Flash Player è maggiore o uguale a 11.4 che causa un errore di riproduzione invece del fallback alla tecnologia successiva in techOrder
- [Correzione di bug][FlashSS][AES] Problemi nell'accettazione di token AES contenenti trattini di sottolineatura
- [Correzione di bug][SilverlightSS|OSX] L'aggiunta del prefisso "//" in un manifesto invece del protocollo (HTTP o HTTPS) viene riconosciuta come file locale, generando la rotella infinita

### <a name="changes-170"></a>Modifiche 1.7.0 ###

- [Modifica][FlashSS] Unione degli script SWF "MSAdaptiveStreamingPlugin-osmf2.0.swf" e "StrobeMediaPlayback.2.0.swf" in un singolo script SWF denominato "StrobeMediaPlayback.2.0.swf"
- [Modifica][FlashSS] Aggiornamento della propagazione di codici errore per ottenere codici più precisi, ad esempio gli errori 404 generano ora 0x30200194 anziché l'errore generico 0x30200000

## <a name="163-official-hotfix-update"></a>1.6.3 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-163"></a>Correzioni di bug 1.6.3 ###

- [Correzione di bug] Eccezione del runtime JavaScript quando il gestore dell'evento hotkeys viene eseguito dopo l'eliminazione del lettore
- [Correzione di bug][Android][AzureHtml5JS] Nessuna riproduzione nei dispositivi mobili che usano la rete cellulare
- [Correzione di bug] Aggiornamento di Forge per l'esecuzione come ruolo di lavoro per liberare interfaccia utente

## <a name="162-official-hotfix-update"></a>1.6.2 (aggiornamento hotfix ufficiale) ##

### <a name="features-162"></a>Funzionalità 1.6.2 ###

- [Funzionalità] Aggiunta di altre lingue per la localizzazione (vedere la documentazione per altri dettagli)

### <a name="bug-fixes-162"></a>Correzioni di bug 1.6.2 ###

- [Correzione di bug][IE9-10] Se si fa clic sulle aree intorno al lettore la finestra del browser viene ridotta a icona a causa di un bug di IE9/IE10 che riduce a icona la finestra con window.blur()
- [Correzione di bug][FlashSS] Problemi nell'accettazione di token AES contenenti trattini di sottolineatura

## <a name="161-official-hotfix-update"></a>1.6.1 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-161"></a>Correzioni di bug 1.6.1 ###

- [Correzione di bug][FlashSS|Microsoft Edge, IE][SilverlightSS|IE] Non è possibile ottenere lo stato attivo su altri elementi dell'interfaccia utente per gli input o altro in IE/Microsoft Edge
- [Correzione di bug] Riproduzione AES non riuscita quando Forge non è definito
- [Correzione di bug][Android][AzureHtml5JS|Chrome] Rotella continua senza riproduzione del contenuto durante il ciclo di controllo dell'integrità
- [Correzione di bug][IE9] console.log() non supportato da IE 9 e genera un'eccezione

## <a name="160-official-update"></a>1.6.0 (aggiornamento ufficiale) ##

### <a name="features-160"></a>Funzionalità 1.6.0 ###

- [Funzionalità] 33% di riduzione delle dimensioni di azuremediaplayer.min.js
- [Funzionalità][AzureHtml5JS|Microsoft Edge][Non testato] Supporto per flussi audio DD+ in Microsoft Edge (nessun cambiamento del codec dopo la scelta iniziale). L'app deve selezionare il flusso audio corretto in questo momento.
- [Funzionalità] Controlli dei tasti di scelta rapida (vedere la documentazione per altri dettagli)
- [Funzionalità] Suggerimento sul tempo di avanzamento al passaggio del mouse per la ricerca accurata del tempo
- [Funzionalità] Rilevamento asincrono dei plug-in consentito se il metodo setupDone esiste

### <a name="bug-fixes-160"></a>Correzioni di bug 1.6.0 ###

- [Correzione di bug] Il log della memoria non viene scaricato con getMemoryLog(true)
- [Correzione di bug] La casella di selezione del bitrate viene reimpostata con lo spostamento del mouse causando la selezione di bitrage inferiori tramite controllo del mouse
- [Correzione di bug] Arresto anomalo di Mac Office in app durante l'esecuzione del controllo DRM
- [Correzione di bug] Le classi CSS vengono facilmente sovrascritte per errore
- [Correzione di bug][Chrome] L'identificazione dell'aggiornamento dal browser delle stringhe dell'agente utente è Microsoft Edge
- [Correzione di bug][AzureHtml5JS] Il pulsante dei sottotitoli non viene visualizzato sulla barra degli strumenti in Microsoft Edge (Win10) o Chrome (Mac)
- [Correzione di bug][Android][AzureHtml5JS|Chrome] Eccezione InvalidStateError con chiamata endOfStream() per video brevi
- [Correzione di bug][Firefox] Rimozione dell'avviso di DRM causato da Firefox durante la verifica delle funzionalità del browser
- [Correzione di bug][Html5] Sottotitoli non visualizzati con il contenuto mP4 progressivo
- [Correzione di bug][FlashSS] I messaggi con timestamp corrispondenti vengono registrati in ordine inverso
- [Correzione di bug][Accessibilità][Chrome|Firefox] I controlli TAB e selezione selezionano automaticamente la prima voce di menu
- [Correzione di bug][Accessibilità] TAB per controllare il pulsante del volume

### <a name="changes-160"></a>Modifiche 1.6.0 ###

- [Modifica] Uso del tempo di decrittografia AES con la selezione del livello di qualità
- [Modifica] Aggiornamento di URL Rewriter per l'uso di HLS v4 prima di HLS v3 per flussi multi-audio
- [Modifica] Impostazione di nativeControlsForTouch su False come predefinita (deve essere False per il corretto funzionamento)

## <a name="150-official-update"></a>1.5.0 (aggiornamento ufficiale) ##

### <a name="features-150"></a>Funzionalità 1.5.0 ###

- [Funzionalità] Miglioramenti per la sicurezza Web generale (prevenzione di inserimento, XSS e così via)
- [Funzionalità] Hook di integrazione del plug-in SDN per l'evento sourceset e options.sdn
- [Funzionalità] Gestione dell'affidabilità degli errori 5XX e 4XX durante la riproduzione

### <a name="bug-fixes-150"></a>Correzioni di bug 1.5.0 ###

- [Correzione di bug] Aggiornamento della minimizzazione CSS per usare codici del tipo di carattere di entità HTML per i pulsanti invece di Unicode
- [Correzione di bug][AzureHtml5JS] Il contenuto multi-DRM seleziona sempre il primo token dell'elemento da protactionInfo causando l'errore del secondo DRM
- [Correzione di bug][AzureHtml5JS] La ricerca non viene mai completata quando viene eseguita in un'area con segmenti mancanti.
- [Correzione di bug][AzureHtml5JS|Microsoft Edge] Abilitazione di EME con prefisso nell'aggiornamento di Microsoft Edge per la riproduzione PlayReady
- [Correzione di bug][AzureHtml5JS|Firefox] Aggiornamento del controllo EME per consentire il fallback di Firefox v42 e successive (con MSE) a Silverlight per il contenuto protetto
- [Correzione di bug][FlashSS] Aggiornamento di error.message da numero a stringa dettagliata

### <a name="changes-150"></a>Modifiche 1.5.0 ###

- [Modifica] I poster attualmente funzionano solo come URL assoluti.

## <a name="140-official-update"></a>1.4.0 (aggiornamento ufficiale) ##

### <a name="features-140"></a>Funzionalità 1.4.0 ###

- [Funzionalità] [AzureHtml5JS|Chrome] Supporto semplificato di DRM Widevine
- [Funzionalità] [AzureHtml5JS] Gestione dell'affidabilità degli errori 404/412 durante la riproduzione

### <a name="bug-fixes-140"></a>Correzioni di bug 1.4.0 ###

- [Correzione di bug] [FlashSS] Miglioramento della convalida dei parametri

## <a name="130-official-update"></a>1.3.0 (aggiornamento ufficiale) ##

### <a name="features-130"></a>Funzionalità 1.3.0 ###

- [Funzionalità] [AzureHtml5JS] [FlashSS] Cambio dell'audio dello stesso contenuto multi-audio del codec

### <a name="bug-fixes-130"></a>Correzioni di bug 1.3.0 ###

- [Correzione di bug] [AzureHtml5JS|Chrome] Rotella intermittente infinita
- [Correzione di bug] [AzureHtml5JS|IE][Windows Phone] Eccezione che genera problemi di riproduzione su Windows Phone
- [Correzione di bug] [FlashSS] La riproduzione automatica impostata su False non riesce per istanze aggiuntive
- [Correzione di bug] Problemi di ridimensionamento dei menu dell'interfaccia utente

## <a name="120-official-update"></a>1.2.0 (aggiornamento ufficiale) ##

### <a name="features-120"></a>Funzionalità 1.2.0 ###

- [Funzionalità] [AzureHtml5JS|Firefox] Supporto con MSE abilitato
- [Funzionalità] Non è più necessario che l'app fornisca i percorsi dei file binari della tecnologia di fallback (swf, xap). Il percorso è relativo allo script di Azure Media Player.

### <a name="bug-fixes-120"></a>Correzioni di bug 1.2.0 ###

- [Correzione di bug] [AzureHtml5JS|Chrome] Il lettore si sposta dietro il margine live quando è in background
- [Correzione di bug] [AzureHtml5JS|Microsoft Edge] La modalità schermo interno non funziona
- [Correzione di bug] [AzureHtml5JS] La registrazione non viene abilitata correttamente quando è impostata nelle opzioni
- [Correzione di bug] [Flash] Durante l'evento di attesa vengono visualizzati sia "buffering" che l'icona di buffering
- [Correzione di bug] La riproduzione può continuare se la richiesta iniziale di larghezza di banda non riesce
- [Correzione di bug] Non è possibile caricare il lettore quando viene inizializzato con opzioni non definite
- [Correzione di bug] Quando si prova a eliminare il lettore dopo che è già stato eliminato, si verifica un'eccezione vdata
- [Correzione di bug] Icone della barra di qualità mappate in modo errato

## <a name="111-official-hotfix-update"></a>1.1.1 (aggiornamento hotfix ufficiale) ##

### <a name="bug-fixes-111"></a>Correzioni di bug 1.1.1 ###

- [Correzione di bug] Problema dello schermo intero di IE meno recente
- [Correzione di bug] I plug-in non vengono più sovrascritti

## <a name="110-official-update"></a>1.1.0 (aggiornamento ufficiale) ##

### <a name="features-110"></a>Funzionalità 1.1.0 ###

- [Funzionalità] Aggiornamento delle stringhe di localizzazione dell'interfaccia utente

### <a name="bug-fixes-110"></a>Correzioni di bug 1.1.0 ###

- [Correzione di bug] Il pulsante di riproduzione grande non ha un contrasto sufficiente
- [Correzione di bug] Indicatore visivo dello stato attivo tramite tabulazione
- [Correzione di bug] Il menu di selezione del bitrate ora include informazioni corrette sulla risoluzione
- [Correzione di bug] Altre opzioni di menu ridimensionate dinamicamente
- [Correzione di bug] Vari problemi dell'interfaccia utente

## <a name="100-official-release"></a>1.0.0 (versione ufficiale) ##

### <a name="features-100"></a>Funzionalità 1.0.0 ###

- [Funzionalità] Test di accessibilità di base per controllo TAB, il controllo dello stato attivo, l'utilità di lettura dello schermo, l'interfaccia utente a contrasto elevato
- [Funzionalità] Aggiornamento dell'interfaccia utente
- [Funzionalità] Registrazione dello sviluppo
- [Funzionalità] API per l'impostazione dinamica di tracce con sottotitoli
- [Funzionalità] Funzionalità di localizzazione di base
- [Funzionalità] Consolidamento dei codici di errore tra tecnologie
- [Funzionalità] Nuovo codice di errore per i casi in cui i plug-in (ad esempio Flash o Silverlight) non sono installati
- [Funzionalità] [AzureHtml5JS] Implementazione di eventi di diagnostica di base

### <a name="bug-fixes-100"></a>Correzioni di bug 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Correzione di bug] [AzureHtml5JS] La riproduzione live si blocca con gli aggiornamenti MPD quando sono presenti piccole imprecisioni nel timestamp
- [Correzione di bug] [AzureHtml5JS] Attenuazione di diversi problemi di riproduzione live
- [Correzione di bug] [AzureHtml5JS] Scaricamento dei buffer quando l'euristica delle dimensioni della finestra è attiva e si passa a una risoluzione dello schermo più alta
- [Correzione di bug] [AzureHtml5JS] Chrome ora visualizza correttamente l'evento terminato. Collegato a un problema noto precedente di *Chrome non invia correttamente un evento terminato quando si usa AzureHtml5JS. Esiste un problema con il browser sottostante.*
- [Correzione di bug] [AzureHtml5JS] Safari disabilitato per questa tecnologia per risolvere il *problema di riproduzione con OSX Yosemite con la tecnologia AzureHtml5JS. Sono presenti problemi di implementazione di MSE. Mitigazione temporanea: forzatura di flashSS, silverlightSS come ordine delle tecnologie per questi agenti utente*
- [Correzione di bug] [FlashSS] loadstart generato dopo che si è verificato un errore

## <a name="020-beta"></a>0.2.0 (beta) ##

### <a name="features-020"></a>Funzionalità 0.2.0 ###

- [Funzionalità] Test completati per PlayReady e AES per eventi live e on demand. Vedere la matrice di compatibilità
- [Funzionalità] Gestione delle discontinuità
- [Funzionalità] Supporto per i timestamp maggiori di 2^53
- [Funzionalità] Il parametro di query URL persiste nella richiesta del manifesto
- [Funzionalità] [Non testata] Supporto per i profili euristici `QuickStart` e `HighQuality`
- [Funzionalità] [Non testata] Esposizione delle informazioni sui flussi video per bitrate, larghezza e altezza in AzureHtml5JS e FlashSS
- [Funzionalità] [Non testata] Selezione di bitrate in AzureHtml5JS e FlashSS (vedere la documentazione dell'API)

### <a name="bug-fixes-020"></a>Correzioni di bug 0.2.0 ###

- [Correzione di bug] Pulsante di riproduzione grande ora visualizzabile in WP8.1
- [Correzione di bug] Correzione di più problemi di riproduzione live
- [Correzione di bug] Pulsante di disattivazione audio ora funzionante nell'interfaccia utente
- [Correzione di bug] Aggiornamento dell'esperienza di caricamento dell'interfaccia utente per la modalità di riproduzione automatica
- [Correzione di bug] Problemi del caricatore AMD e definizione di conflitti di metodi
- [Correzione di bug] Problema di caricamento dell'app Cordova in WP 8.1
- [Correzione di bug] Query sul contenuto protetto per trovare ProtectionType supportato da piattaforma/tecnologia e selezionare la tecnologia appropriata per la riproduzione.  Corregge il problema noto precedente per cui il ' _contenuto PlayReady in Chrome (desktop)/Safari 8 (OSX Yosemite) attualmente non esegue il fallback a Silverlight Player_ '
- [Correzione di bug] Eccezione non rilevata in WinServer 2012 R2 perché Media Foundation non è installato nel computer per impostazione predefinita.  Il tentativo di usare le API dei tag video HTML, che non sono implementate, genera quindi un errore. La mitigazione corrente consiste nell'intercettare l'errore e restituire false invece di generare l'errore.
- [Correzione di bug] Si ottiene sempre il segmento di inizializzazione dopo la ricerca o un errore HTTP per evitare problemi durante la riproduzione
- [Correzione di bug] Disattivazione del monitoraggio dell'avanzamento simulato e degli aggiornamenti temporali quando si verifica un errore.
- [Correzione di bug] Rimozione del menu di scelta rapida
- [Correzione di bug] [AzureHtml5JS] Messaggio di errore non visualizzato quando il set di token non è valido per il contenuto PlayReady
- [Correzione di bug] [AzureHtml5JS] Il passaggio a schermo intero durante la riproduzione live non tiene conto dell'euristica delle dimensioni della finestra
- [Correzione di bug] [FlashSS] Rimozione dei messaggi visualizzati in Strobe Media Player in modo che vengano visualizzati solo quelli di Azure Media Player
- [Correzione di bug] [SilverlightSS] Quando si esegue una ricerca oltre la durata o minore di 0, non si ottiene un evento 'seeked'

## <a name="010-beta-release"></a>0.1.0 (versione beta) ##

Versione preliminare iniziale

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
