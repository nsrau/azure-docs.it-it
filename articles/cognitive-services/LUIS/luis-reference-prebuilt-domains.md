---
title: Informazioni di riferimento sui domini predefiniti
titleSuffix: Azure
description: Informazioni di riferimento sui domini predefiniti che sono raccolte predefinite di finalità ed entità di Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: d17b6271468a9259314876e18ff7ead288c990bc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221012"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Informazioni di riferimento sui domini predefiniti per l'app LUIS
Queste informazioni di riferimento descrivono i [domini predefiniti](luis-how-to-use-prebuilt-domains.md) che sono raccolte predefinite delle finalità ed delle entità offerte da LUIS.

I [domini personalizzati](luis-how-to-start-new-app.md), al contrario, vengono avviati senza finalità e modelli. È possibile aggiungere le finalità e le entità dei domini predefiniti a un modello personalizzato.

## <a name="list-of-prebuilt-domains"></a>Elenco dei domini predefiniti
LUIS offre 20 domini predefiniti. 

| Dominio predefinito | Descrizione | Lingue supportate |
| ---------------- |-----------------------|:------:|
| Calendario | Il dominio Calendar (Calendario) offre finalità ed entità per l'aggiunta, l'eliminazione o la modifica di un appuntamento, la verifica della disponibilità dei partecipanti e la ricerca di informazioni su un evento del calendario.| en-US<br/> zh-CN |
| Camera | Il dominio Camera (Fotocamera) offre finalità ed entità per l'acquisizione di foto, la registrazione di video e la trasmissione di video in un'applicazione.| en-US |
| Comunicazione | Invio di messaggi e chiamate telefoniche.| en-US <br/> zh-CN |
| Entertainment  | Gestione delle query correlate a musica, film e TV.| en-US |
| Eventi | Prenotazione di biglietti per concerti, festival, competizioni sportive e spettacoli.| en-US |
| Fitness | Gestione delle richieste correlate alla registrazione delle attività di fitness.| en-US |
| Giochi | Gestisce le richieste relative a una partita in un gioco multiplayer.| en-US |
| HomeAutomation | Controllo dei dispositivi domestici intelligenti come luci e accessori.| en-US<br/> zh-CN |
| MovieTickets | Prenotazione di biglietti per film in una sala cinematografica.| en-US |
| Musica | Riproduzione di musica su un lettore.| en-US<br/> zh-CN |
| Note | Il dominio Note (Nota) offre finalità ed entità correlate alla creazione, alla modifica e alla ricerca di note.| en-US<br/> zh-CN |
| OnDevice | Il dominio OnDevice (Dispositivo) offre finalità ed entità correlate al controllo del dispositivo.| en-US<br/> zh-CN |
| Località  | Gestione delle query correlate a luoghi come aziende, istituzioni, ristoranti, spazi pubblici e indirizzi.| en-US<br/> zh-CN |
| Reminder | Gestione delle richieste correlate alla creazione, alla modifica e alla ricerca di promemoria.| en-US<br/> zh-CN |
| RestaurantReservation | Gestione delle richieste di prenotazione per i ristoranti.| en-US<br/> zh-CN |
| Taxi | Gestione delle prenotazioni di taxi.| en-US<br/> zh-CN |
| Translate | Traduzione del testo nella lingua di destinazione.| en-US<br/> zh-CN |
| TV | Controllo dei televisori.| en-US |
| Servizi pubblici  | Gestione delle richieste comuni a più domini, ad esempio le richieste di aiuto, di ripetizione o di riavvio.| en-US |
| Weather | Recupero dei report e delle previsioni meteo.| en-US<br/> zh-CN |
| Web | Navigazione di un sito Web.| en-US<br/> zh-CN |

Per informazioni dettagliate su ogni dominio, vedere le sezioni che seguono.

## <a name="calendar"></a>Calendario 

Il dominio Calendar (Calendario) offre finalità ed entità correlate alle voci di calendario. Le finalità del calendario includono l'aggiunta, l'eliminazione o la modifica di un appuntamento, la verifica della disponibilità e la ricerca di informazioni su una voce del calendario o un appuntamento.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Add | Aggiungere una voce singola al calendario.| Prendi un appuntamento con Lisa per le 14.00 di domenica <br/><br/>Voglio pianificare una riunione<br/><br/>Ho bisogno di organizzare una riunione|
| CheckAvailability | Cercare la disponibilità per un appuntamento o una riunione nel calendario dell'utente o di un altro utente.| Quando è possibile incontrare Jim? <br/><br/>Mostra quando Carol è disponibile domani<br/><br/>Chris è libero sabato?|
| Delete | Richiedere di eliminare una voce del calendario.| Annulla l'appuntamento con Carol. <br/><br/>Cancella la riunione delle 9.00<br/>|
| Modificare | Richiedere di modificare una riunione o una voce del calendario esistente.| Sposta la riunione delle 9.00 alle 10.00.<br/><br/>Voglio aggiornare la mia pianificazione.<br/><br/>Ripianificare la riunione con Ryan.|
| Find | Visualizzare il calendario settimanale personale.| Trova l'appuntamento di controllo dal dentista. <br/><br/>Mostra il mio calendario<br/>|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Località | Località dell'elemento del calendario, della riunione o dell'appuntamento. Indirizzi, città e regioni sono un ottimo esempio di località.| 209 Nashville Gym <br/><br/>897 Pancake house<br/><br/>Garage|
| Oggetto | Titolo di una riunione o di un appuntamento.| Appuntamento dal dentista <br/><br/>Pranzo con Julia<br/><br/>Appuntamento dal dottore|

## <a name="camera"></a>Camera 
Il dominio Camera (Fotocamera) offre finalità ed entità correlate all'uso di una fotocamera. Le finalità includono lo scatto di una foto, selfie, screenshot o video e la trasmissione di video in un'applicazione.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| CapturePhoto| Acquisire una foto.| Scatta una foto<br/><br/>acquisire|
| CaptureScreenshot | Acquisire uno screenshot.| Fai uno screenshot.<br/><br/>acquisisci la schermata.|
| CaptureSelfie | Acquisire un selfie.| Fai un selfie <br/><br/>fai un autoscatto |
| CaptureVideo | Avviare la registrazione di un video.| Avvia registrazione <br/><br/>Inizia registrazione|
| StartBroadcasting| Avviare la trasmissione di un video.| Avvia trasmissione su Facebook|
| StopBroadcasting| Arrestare la trasmissione di un video.| Ferma trasmissione|
| StopVideoRecording| Arrestare la registrazione di un video.| È abbastanza<br/><br/>ferma registrazione|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AppName | Nome di un'applicazione in cui trasmettere un video.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Comunicazione 
Il dominio Communication (Comunicazione) offre le finalità e le entità correlate a messaggi di posta elettronica, messaggi e chiamate telefoniche.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AddContact| Aggiungere un nuovo contatto all'elenco dei contatti dell'utente.|Aggiungi nuovo contatto <br/><br/>Salva il numero e assegna il nome Carol|
| AddMore| Aggiungere altri dati a un messaggio di posta elettronica o a un messaggio di testo, come fase della composizione di un messaggio di posta elettronica o di testo.|Aggiungi altro al messaggio di testo <br/><br/>Aggiungi altro al corpo del messaggio di posta elettronica|
| Risposta| Rispondere a una chiamata in arrivo.|Rispondi alla chiamata <br/><br/>Rispondi|
| AssignContactNickname| Assegnare un nome alternativo a un contatto.|Cambia Isaac con papà <br/>Cambia nome alternativo di Jim<br/>Aggiungi nome alternativo a Patti Owens|
| CallVoiceMail| Connettersi alla posta vocale dell'utente.|Connettimi alla casella della posta vocale <br/>Messaggi vocali<br/>Chiama posta vocale|
| CheckIMStatus| Controllare lo stato di un contatto in Skype.|Lo stato online di Jim è impostato su Assente? <br/>Carol è disponibile per una chat?|
| Confirm| Confermare un'azione.|Yes<br/>OK<br/>Va bene<br/>Confermo di voler inviare questo messaggio di posta elettronica.<br/>|
| Dial| Effettuare una chiamata telefonica.|Chiama Jim<br/>Componi 311<br/>|
| FindContact| Trovare le informazioni sul contatto in base al nome.|Trova il numero di Carol<br/>Mostra il numero di Carol<br/>|
| FindSpeedDial| Trovare il numero rapido impostato per un numero telefonico e viceversa.|A quale numero corrisponde il numero 5?<br/>È impostato il numero rapido?<br/>Qual è il numero rapido per 941-5555-333?|
| GetForwardingsStatus| Ottenere lo stato corrente dell'inoltro di chiamata.|L'inoltro di chiamata è attivato?<br/>Dimmi se lo stato della chiamata è attivo o inattivo<br/>|
| Goback| Tornare al passaggio precedente.|Torna a Twitter<br/>Torna indietro di un passaggio<br/>Tornare alla schermata precedente|
| Ignora| Ignorare una chiamata in arrivo.|Non rispondere<br/>Ignora la chiamata|
| IgnoreWithMessage| Ignorare una chiamata in arrivo e rispondere invece con un messaggio di testo.|Non rispondere alla chiamata ma inviare un messaggio di testo.<br/>Ignora e invia un messaggio di testo.|
| PressKey| Premere un pulsante o un numero sul tastierino numerico.|Componi asterisco.<br/>Premi 1 2 3.|
| ReadAloud| Leggere un messaggio o un messaggio di posta elettronica all'utente.|Leggi testo.<br/>Cosa ha detto nel messaggio?|
| TurnForwardingOff| Effettuare una chiamata telefonica.|<br/><br/>|
| Redial| Ricomporre o chiamare di nuovo un numero.|Richiama.<br/>Richiama ultimo numero.|
| Rifiuto| Rifiutare una chiamata in arrivo.|Rifiuta chiamata<br/>Non posso rispondere<br/>Non sono disponibile ora, richiamo più tardi.|
| SendEmail| Inviare un messaggio di posta elettronica. Questa finalità si applica alla posta elettronica e non ai messaggi di testo.|Invia un messaggio di posta elettronica a Mike Waters: Mike, la cena della settimana scorsa è stata splendida.<br/>Invia un messaggio di posta elettronica a Bob<br/>|
| SendMessage| Inviare un messaggio di testo o un messaggio istantaneo.|Invia un messaggio di testo a Chris e Carol|
| SetSpeedDial| Impostare un numero rapido per il numero di telefono di un contatto.|Imposta numero rapido 1 per Carol.<br/>Imposta numero rapido per mamma.|
| ShowNext| Visualizzare l'elemento successivo, ad esempio in un elenco di messaggi di testo o messaggi di posta elettronica.|Visualizza successivo.<br/>Passa alla pagina successiva.|
| ShowPrevious| Visualizzare l'elemento precedente, ad esempio in un elenco di messaggi di testo o messaggi di posta elettronica.|Mostra precedente.<br/>Precedente<br/>Passa a precedente.|
| StartOver| Riavviare il sistema o avviare una nuova sessione.|Rinizia<br/>Nuova sessione<br/>restart|
| TurnForwardingOff| Disattivare l'inoltro di chiamata.|Interrompi l'inoltro delle chiamate<br/>Disattiva l'inoltro delle chiamate|
| TurnForwardingOn| Disattivare l'altoparlante.|Inoltra le chiamate al numero 3333<br/>Attiva l'inoltro delle chiamate al numero 3333|
| TurnSpeakerOff| Disattivare l'altoparlante.|Escludi altoparlante.<br/>Disattiva viva voce.<br/>|
| TurnSpeakerOn| Attivare l'altoparlante.|Modalità viva voce.<br/>Attiva viva voce.<br/>|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AudioDeviceType | Tipo di dispositivo audio (altoparlante, cuffie, microfono, ecc.).| Relatore<br/>Viva voce<br/>Bluetooth|
| Categoria | Categoria di un messaggio o di un messaggio di posta elettronica.| Importante<br/>Priorità alta|
| ContactAttribute | Un attributo del contatto cercato dall'utente.| Compleanni<br/>Indirizzo<br/>Numero di telefono|
| ContactName | Nome di un contatto o del destinatario del messaggio.| Carol<br/>Jim<br/>Chris|
| EmailSubject | Testo usato come oggetto del messaggio di posta elettronica.| Re: storia interessante|
| Grafico a linee | Linea che l'utente vuole usare per effettuare una chiamata o da cui inviare un messaggio di testo o un messaggio di posta elettronica.| Linea ufficio<br/>Cella inglese<br/>Skype|
| Message | Messaggio da inviare come messaggio di posta elettronica o messaggio di testo.| È stato bello incontrarti oggi. A presto!|
| MessageType | Nome di un contatto o del destinatario del messaggio.| Text<br/>Email|
| OrderReference | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare. Ad esempio, "ultimo" o "recente" in "Qual è l'ultimo messaggio inviato?"| Last (Ultimo)<br/>Recente|
| SenderName | Nome del mittente.| Patti Owens|

## <a name="entertainment"></a>Entertainment  
Il dominio Entertainment (Svago) offre le finalità e le entità correlate alla ricerca di film, musica, giochi e programmi televisivi.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Ricerca| Cercare film, musica, app, giochi e programmi televisivi.|Cerca Halo nello store.<br/>Cerca Avatar.|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| ContentRating | Classificazione dei contenuti multimediali, ad esempio G oppure R per i film.|Video per bambini.<br/>Classificazione PG.|
| Genre | Genere di un film, gioco, app o brano musicale.|Commedia<br/>Drammatico<br/>Divertimento|
| Parole chiave| Parola chiave di ricerca generica che specifica un attributo non presente negli slot multimediali più specifici.|Colonne sonore<br/>Moon River<br/>Amelia Earhart|
| Linguaggio | Linguaggio usato nei supporti, ad esempio lingua parlata del brano o del filmato.|Francese<br/>Inglese<br/>Coreano|
| MediaFormat | Tipologia tecnica specifica aggiuntiva in cui è formattato il contenuto multimediale.|Film in HD<br/>Film in 3D<br/>Scaricabile|
| MediaSource | Store o marketplace per l'acquisizione del contenuto multimediale.|Netflix<br/>Prime|
| MediaSubTypes| Tipi multimediali di dimensioni ridotte rispetto a film e giochi.|Demo<br/>Dlc<br/>Trailer|
| Nationality| Paese di creazione del film, del programma o del brano musicale.|Francese<br/>Tedesco<br/>Coreano|
| Person| Attore, regista, produttore, musicista o artista associato a un film, un'app, un gioco o un programma televisivo.|Madonna<br/>Stanley Kubrick|
| Ruolo| Ruolo svolto nella creazione di un contenuto multimediale.|Cantante<br/>Regista<br/>Con|
| Title| Nome di un film, un'app, un gioco, un programma televisivo o un brano musicale.|Friends<br/>Minecraft|
| Type| Tipo o formato multimediale di un film, un'app, un gioco, un programma televisivo o un brano musicale.|Musica<br/>Film per la TV <br/>programmi|
| UserRating| Valutazione dell'utente espressa in stelle.|5 stelle<br/>3 stelle<br/>4 stelle|

## <a name="events"></a>Eventi 
Il dominio Events (Eventi) offre le finalità e le entità correlate alla prenotazione di biglietti per eventi come concerti, festival, competizioni sportive e spettacoli.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Book| Acquistare biglietti per un evento.|Acquistare un biglietto per un concerto sinfonico per il fine settimana.|


### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Indirizzo | Località o indirizzo dell'evento. |Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| NOME | Nome di un evento.|Shakespeare in the Park|
| PlaceName| Nome della posizione dell'evento.|Louvre<br/>Opera House<br/>Broadway|
| PlaceType | Tipo di posizione in cui si terrà l'evento.|Caffè<br/>Teatro<br/>Libreria|
| Type | Tipo di evento.|Concerto<br/>Gioco di sport|

## <a name="fitness"></a>Fitness 
Il dominio Fitness offre le finalità e le entità correlate alla registrazione delle attività di fitness. Le finalità includono il salvataggio di note, del tempo o della distanza rimanente o dei risultati dell'attività.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AddNote| Aggiunge ulteriori note all'attività registrata.|La difficoltà della corsa è stata di 6/10<br/>Il terreno della corsa è l'asfalto<br/>Uso una bicicletta a 3 velocità|
|GetRemaining| Ottiene il tempo o la distanza rimanente di un'attività.|Quanto manca per il giro successivo?<br/>Quante sono le miglia rimanenti? Quanto manca per il tempo parziale?|
| LogActivity| Salvare o registrare i risultati delle attività completate.|Salva ultima corsa<br/>Registra la camminata di sabato mattina<br/>archivia la nuotata precedente|
| LogWeight| Salvare o registrare il peso attuale dell'utente.|Salva peso attuale<br/>registra peso attuale<br/>archivia peso corporeo attuale|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| ActivityType | Tipo di attività da registrare. |Esegui<br/>Camminata<br/>Nuotata<br/>Ciclo |
| Food | Tipo di alimento da registrare in un'app per il fitness. |Banana<br/>Salmone<br/>Frullato proteico|
| MealType| Tipo di pasto da registrare in un'app per il benessere o il fitness.|Colazione<br/>Pasto principale<br/>Pranzo<br/>Cena|
| Misura| Tipo di misura per tempo, distanza o peso da usare in un app per il benessere o il fitness.|Chilometri<br/>Miglia<br/>Minuti<br/>Chilogrammi|
| Number | Quantità numerica da usare in un'app per il benessere o il fitness.|19<br/>three<br/>200<br/>one|
| StatType | Tipo di statistica sui dati aggregati da usare in un'app per il benessere o il fitness.|Somma<br/>Media<br/>Massima<br/>Minima|

## <a name="gaming"></a>Giochi 
Il dominio Gaming (Giochi) offre le finalità e le entità correlate alla gestione di una partita in un gioco multiplayer.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| InviteParty| Invitare un contatto a partecipare a una partita.|Invita questo giocatore alla partita<br/>Gioca alla partita<br/>Unisciti alla squadra|
|LeaveParty| Ottiene il tempo o la distanza rimanente di un'attività.|Sono fuori<br/>Vado a un'altra festa<br/>Me ne vado|
| StartParty| Avviare una partita in un gioco multiplayer.|Amico iniziamo una partita<br/>avvia una partita<br/>dobbiamo creare una squadra stasera|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Contatto| Nome di un contatto da usare in un gioco multiplayer.|Carol<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
Il dominio HomeAutomation (Domotica) offre le finalità e le entità correlate al controllo dei dispositivi domestici intelligenti, ad esempio luci e accessori.

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| TurnOff| Spegnere, chiudere o sbloccare un dispositivo.|Spegni le luci<br/>Interrompi macchina del caffè<br/>Chiudi la porta del garage|
|TurnOn| Accendere un dispositivo o impostare una modalità specifica del dispositivo.|accendere la macchina del caffè<br/>puoi accendere la macchina del caffè?<br/>Impostare il termostato a 72 gradi.|


### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Dispositivo | Tipo di dispositivo che può essere acceso o spento.|macchina del caffè<br/>termostato<br/>luci|
| Operazione | Stato del dispositivo da impostare.|lock<br/>apre<br/>in<br/>spento|
| Room | Posizione o locale in cui si trova il dispositivo.|soggiorno<br/>camera da letto<br/>cucina|

## <a name="movietickets"></a>MovieTickets 
Il dominio MovieTickets (Biglietti cinema) offre le finalità e le entità correlate alla prenotazione di biglietti per gli spettacoli cinematografici.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Prenota due biglietti per I tre moschettieri|
|Annulla i biglietti|
|A che ora è I tre moschettieri?|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Book | Acquistare biglietti del cinema.|Prenota due biglietti per I tre moschettieri<br/>Voglio acquistare un biglietto per il film di domani<br/>Voglio un biglietto per I tre moschettieri per mercoledì prossimo|
|GetShowTime| Ottenere gli orari di programmazione di un film.|A che ora è I tre moschettieri?|


### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Indirizzo | Indirizzo della sala cinematografica.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | Titolo di un film.|Vita di Pi<br/>Hunger Games<br/>Inception|
| PlaceName | Nome di una sala cinematografica o di un cinema.|Cinema Amir<br/>Alexandria Theatre<br/>New York Theater|
| PlaceType | Tipo di posizione in cui è in programmazione un film.|cinema<br/>theater<br/>Cinema IMAX|

## <a name="music"></a>Music 
Il dominio Music (Musica) offre le finalità e le entità correlate alla riproduzione di musica su un lettore musicale.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Riproduci Beethoven|
|Alza il volume del brano|
|Passa al prossimo brano|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| DecreaseVolume | Abbassare il volume del dispositivo.|abbassa il volume del brano<br/>abbassa il volume|
| IncreaseVolume | Alzare il volume del dispositivo.|alza il volume del brano<br/>alza il volume|
| Mute |Disattivare l'audio della musica in riproduzione.|Disattiva audio del brano<br/>Togli audio del brano<br/>Disattiva audio della musica |
| Sospendi | Sospendere la musica in riproduzione.|Sospendi<br/>Sospendi musica<br/>Sospendi brano|
| PlayMusic | Riprodurre musica su un dispositivo.|riproduci Kevin Durant<br/>riproduci Paradise dei Coldplay<br/>riproduci Hello di Adele|
| Repeat |Ripetere la musica in riproduzione.|Ripeti brano<br/>Riproduci di nuovo il brano<br/>Ripeti musica|
| Riprendi | Riprendere la musica in riproduzione.|Riprendi brano<br/>Riavvia la musica<br/>Annulla la sospensione|
| SkipBack | Tornare indietro di un brano.|Passa al prossimo brano<br/>Riproduci il prossimo brano|
| SkipForward |Andare avanti di un brano.|Riproduci brano precedente<br/>Tornare al brano precedente |
| Arresto | Interrompere un'azione relativa alla riproduzione di musica. |Interrompi riproduzione di questo album.|
| Unmute | Attivare l'audio di un dispositivo di riproduzione di musica.| Attiva audio.|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| ArtistName | Attore, regista, produttore, autore, musicista o artista associato al contenuto multimediale da riprodurre su un dispositivo.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | Genere della musica richiesta.|Country<br/>Classici di Broadway<br/>Riproduci musica classica a partire dal periodo barocco|

## <a name="note"></a>Note 
Il dominio Note (Nota) offre finalità ed entità correlate alla creazione, alla modifica e alla ricerca di note.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Aggiungi alla lista della spesa lattuga pomodoro pane caffè|
|Aggiungi segno di spunta alle banane nella lista della spesa|
|Rimuovi tutte le voci dalla lista delle vacanze|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AddToNote | Aggiungere informazioni a una nota.|Aggiungi alla lista della spesa lattuga pomodoro pane caffè<br/>Aggiungi all'elenco di attività<br/>aggiungi cupcake alla Wunderlist|
| CheckOffItem | Aggiungere un segno di spunta alle voci di una nota preesistente.|Aggiungi segno di spunta alle banane nella lista della spesa<br/>Contrassegna cheesecake nella lista dello shopping delle vacanze|
| Cancellazione | Cancellare tutte le voci da una nota preesistente.|Rimuovi tutte le voci dalla lista delle vacanze<br/>Cancella tutto dalla lista delle letture|
| Confirm | Confermare un'azione relativa a una nota.|OK per me<br/>Sì<br/>Confermo di tenere tutte le voci nelle liste|
| Create | Creare una nuova nota. | Crea un elenco<br/>Ricordami che Jason è in città la prima settimana di maggio|
| Delete | Eliminare un'intera nota. |Elimina la lista delle vacanze <br/>elimina la lista della spesa|
| DeleteNoteItem | Eliminare le voci da una nota preesistente.| Elimina le patatine dall'elenco della spesa<br/>Rimuovi le penne dall'elenco delle spese per la scuola|
| ReadAloud | Leggere un elenco ad alta voce.|Leggimi il primo<br/>Leggimi i dettagli|
| ShowNext | Visualizzare la voce successiva di un elenco di note.|Mostra la prossima<br/>Pagina successiva<br/>Avanti|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AppName | Nome dell'applicazione delle note.|Wunderlist<br/>OneNote|
| ContactName | Nome di un contatto in una nota.|Carol<br/>Jim<br/>Chris|
| DataSource | Posizione delle note.|OneDrive<br/>Google Documenti<br/>computer personale|
| DataType | Tipo di file o documento in genere associato a specifici programmi software.|Diapositive<br/>Foglio di calcolo<br/>Foglio di lavoro|
| Text | Testo di una nota o un promemoria.|stretching prima della camminata<br/>corsa lunga domani|
| Title | Titolo di una nota.|spesa<br/>persone da chiamare<br/>attività|

## <a name="ondevice"></a>OnDevice 
Il dominio OnDevice (Dispositivo) offre finalità ed entità correlate al controllo del dispositivo.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Chiudi lettore video|
|Annulla la riproduzione|
|Schermo più luminoso|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AreYouListening | Chiedere se il dispositivo è in ascolto.|è acceso?<br/>sei in ascolto?|
|CloseApplication|Chiudere l'applicazione del dispositivo.|chiudi lettore video|
|FileBug|Segnalare un bug nel dispositivo.|segnala bug<br/>Puoi segnare un bug?<br/>Segnala questo bug|
|GoBack|Chiedere di tornare indietro di un passaggio o di tornare al passaggio precedente.|Torna indietro<br/>Torna a schermata precedente<br/>Torna indietro interrompi ascolto|
|Guida| Richiedere aiuto.|Aiuto<br/>Ciao<br/>Cosa fare<br/>Serve aiuto| 
|LocateDevice|Individuare la posizione del dispositivo.|Puoi trovare il mio telefono<br/>Trova iPhone di Tom<br/>Trova il mio iPhone|
|LogIn|Accedere a un servizio usando il dispositivo.|Accedi<br/>Accesso a Facebook<br/>Accedi a LinkedIn|
|LogOut|Disconnettersi da un servizio usando un dispositivo.|Disconnetti telefono<br/>Accedi a Twitter<br/>Effettuare la disconnessione|
|MainMenu|Visualizzare il menu principale di un dispositivo.|Visualizza menu.|
|OpenApplication|Aprire un'applicazione nel dispositivo.|Apri sveglia<br/>Attiva fotocamera<br/>Apri calendario|
|OpenSetting|Aprire un'impostazione nel dispositivo.|Apri impostazioni di rete.|
|PairDevice|Associare il dispositivo.|Associa segnale Bluetooth al telefono<br/>Attiva Bluetooth e associa al laptop<br/>Associa segnale Bluetooth al laptop|
|PowerOff | Spegnere il dispositivo.|Spegni computer<br/>Shutdown<br/>Spegni telefono cellulare|
|QueryBattery|Ottenere informazioni sulla durata della batteria.|Mostra durata batteria.<br/>Qual è lo stato della batteria<br/>Quanta carica è rimasta?<br/>Mostra batteria|
|QueryWifi|Ottenere informazioni sul WiFi.|Ottieni informazioni sul WiFi.|
|Riavvio|Riavviare il dispositivo.|Riavvia.|
|RingDevice| Chiedere al dispositivo di suonare per trovarlo nel caso venga perso.|Fai suonare il telefono.| 
|SetBrightness|Impostare la luminosità del dispositivo.|Imposta luminosità media<br/>Imposta luminosità alta<br/>Imposta luminosità bassa|
|SetupDevice|Avviare l'impostazione del dispositivo.|Voglio impostare il sistema operativo<br/>Imposta<br/>Imposta automaticamente|
|ShowAppBar|Visualizzare una barra delle applicazioni.|Mostra barra applicazioni<br/>Barra applicazioni<br/>Mostra barra delle applicazioni|
|ShowContextMenu|Visualizzare un menu di scelta rapida.|Mostra menu di scelta rapida<br/>Menu di scelta rapida<br/>Visualizza menu di scelta rapida|
|Sleep|Sospendere il dispositivo.|Vai in sospensione<br/>Sospendi<br/>Sospendi computer|
|SwitchApplication|Passare all'applicazione da usare nel dispositivo.|Passa a lettore multimediale.|
|TurnDownBrightness|Abbassare la luminosità del dispositivo.|Abbassa luminosità schermo.|
|TurnOffSetting|Disattivare un'impostazione del dispositivo.|Disattiva Bluetooth<br/>Disattiva dati<br/>Disconnetti Bluetooth|
|TurnOnSetting|Attivare un'impostazione del dispositivo.|Attivato <br/> Off|
|TurnUpBrightness|Aumentare la luminosità del dispositivo.|Schermo più luminoso|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AppName | Nome di un'applicazione nel dispositivo.|SoundCloud<br/>YouTube|
| BrightnessLevel | Impostare il livello di luminosità nel dispositivo.|Cento per cento<br/>Cinquanta<br/>40%|
| ContactName | Nome di un contatto nel dispositivo.|Paul<br/>Marlen Max|
| DeviceType | Tipo di dispositivo. |Telefono<br/>Kindle<br/>Laptop|
| MediaType | Tipo di contenuto multimediale gestito dal dispositivo.|Musica<br/>Film<br/>Programmi TV|
| SettingType | Tipo di impostazione o pannello delle impostazioni che l'utente vuole modificare.|WiFi<br/>Rete wireless<br/>Combinazione colori<br/>Centro notifiche|

## <a name="places"></a>Places  
Il dominio Places (Luoghi) offre le finalità per la gestione di query correlate a luoghi come uffici, istituzioni, ristoranti, luoghi pubblici e indirizzi.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Salva questa posizione nei Preferiti|
|Quanto è lontano l'Holiday Inn?|
|A che ora chiude Safeway?|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Aggiungere una posizione ai Preferiti dell'utente.|Salva questa posizione nei Preferiti<br/>Aggiungi questo indirizzo ai Preferiti|
|CheckAccident|Chiedere se si è verificato un incidente in una strada specificata.|C'è un incidente sulla 880?<br/>Mostra informazioni sull'incidente|
|CheckAreaTraffic|Controllare il traffico di una zona o un'autostrada, non una strada specifica.|Traffico a Seattle<br/>Com'è il traffico a Seattle?|
|CheckIntoPlace|Aggiungere un luogo usando i social media.|Aggiungi Foursquare<br/>Aggiungi questa posizione|
|CheckRouteTraffic| Controllare il traffico di una strada specificata dall'utente.|Com'è il traffico verso Mashiko?<br/>Mostra traffico verso Kirkland<br/>Com'è il traffico verso Seattle?| 
|Confirm|Confermare un'azione relativa a un luogo.|Confermare la prenotazione al ristorante.|
|Esci|Azione per uscire da un'attività relativa a un luogo.|Esci<br/>Esci dando istruzioni|
|FindPlace|Cercare un luogo (ufficio, istituzione, ristorante, luogo pubblico, indirizzo).|Dov'è la biblioteca più vicina?<br/>Trova buon ristorante italiano in Mountain View|
|GetAddress| Chiedere l'indirizzo di un luogo.|Mostra indirizzo di Guu in Robson Street<br/>Qual è l'indirizzo dello Starbucks più vicino?| 
|GetDistance|Chiedere la distanza da un luogo specifico.|Quanto è lontano l'Holiday Inn?<br/>quanto è lontana Bellevue Square da qui<br/>quanto è lontana Tahoe|
|GetHours|Chiedere gli orari di apertura di un luogo.|A che ora chiude Safeway?<br/>Quali sono gli orari di Home Depot?<br/>È ancora aperto Starbucks?|
|GetMenu|Chiedere informazioni sul menu di un ristorante.|Zucca serve alimenti vegani?<br/>Cosa c'è nel menu di Sizzler<br/>Mostra menu di Applebee|
|GetPhoneNumber| Chiedere il numero di telefono di un luogo.|Qual è il numero di telefono dello Starbucks più vicino?<br/>Dammi il numero di Home Depot| 
|GetPriceRange| Chiedere informazioni sui prezzi di un luogo.|È economico Zucca?<br/>Cineplex è a metà prezzo il mercoledì?<br/>Quanto costa una cena completa a base di aragosta al Sizzler?|
|GetReviews|Chiedere le recensioni di un luogo.|Mostra recensioni di Cheesecase Factory<br/>Leggi recensioni di Cineplex in Yelp|
|GetRoute|Chiedere le indicazioni per un luogo.|Come andare a piedi a Bellevue Square<br/>Mostra percorso più breve per l'ottava e la 59esima strada da qui<br/>Dammi indicazioni per Mountain View CA|
|GetStarRating|Richiedere la classificazione a stelle di un luogo.|Qual è la classificazione di Zucca su Yelp?<br/>Quante stelle ha French Laundry?<br/>È bello l'acquario di Monterrey?|
|GetTransportationSchedule|Ottenere gli orari di un autobus per un luogo.|A che ora è il prossimo autobus per il centro?<br/>Mostra autobus in King County|
|GetTravelTime|Chiedere il tempo di percorrenza per una destinazione specificata.|Quanto ci si impiega ad arrivare a San Francisco da qui<br/>Quanto ci si impiega da San Francisco a Denver in auto|
|MakeCall|Effettuare una chiamata telefonica a un luogo.|Chiama mamma<br/>Chiama Anna con Skype<br/>Chiama Jim|
|MakeReservation|Richiedere una prenotazione per un ristorante o altro.|Prenota allo Zucca per due per stasera<br/>Prenota un tavolo per domani<br/>Tavolo per 3 al Palo Alto per le 8|
|MapQuestions|Richiedere informazioni sulle indicazioni oppure se una strada specificata porta a una destinazione.|La 13 passa per il centro?<br/>Posso prendere la 880 per Oakland?|
|Rating|Ottenere la descrizione della classificazione di un ristorante o un luogo.|Quante stelle ha Contoso Inn?|
|ReadAloud|Leggere ad alta voce un elenco di luoghi.|Leggimi il primo<br/>Leggimi i dettagli|
|SelectItem|Scegliere una voce da un elenco relativo a uno o più luoghi.|Scegli il secondo<br/>Seleziona il primo|
|ShowMap|Visualizzare una mappa di un'area.|Mostra una mappa per il secondo<br/>Mostra mappa<br/>Trova San Francisco sulla mappa|
|ShowNext|Visualizzare l'elemento successivo di una serie.|Mostra il prossimo<br/>passa alla pagina successiva|
|ShowPrevious|Visualizzare l'elemento precedente di una serie.|mostra precedente<br/>previous<br/>passa a precedente|
|StartOver|Riavviare l'app o avviare una nuova sessione.|Rinizia<br/>Nuova sessione<br/>
restart|
|TakesReservations|Chiedere se un luogo accetta prenotazioni.|La galleria d'arte accetta prenotazioni?<br/>È possibile prenotare a Olive Garden

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| AbsoluteLocation | Posizione o indirizzo di un luogo.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Amenities | Caratteristiche e attrattive di un luogo.|pranzo gratis per i bambini<br/>fronte mare<br/>parcheggio gratuito|
| Atmosphere | Atmosfera di un luogo.|adatto ai bambini<br/>ristorante informale<br/>sport|
| Cuisine | Cucina di un luogo. |Mediterranea<br/>Italiano<br/>Indiana|
| DestinationAddress| Posizione o indirizzo di destinazione.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| Nome di una destinazione (ufficio, ristorante, luogo pubblico o istituzione).|central park<br/>safeway<br/>walmart|
| DestinationPlaceType | Tipo di una destinazione (ufficio locale, ristorante, luogo pubblico o istituzione). |Ristorante<br/>Opera<br/>Cinema|
| Distance | Distanza per un luogo.|15 miglia<br/>5 miglia<br/>10 miglia|
| MealType | Tipo di pasto come colazione o pranzo. |colazione<br/>pasto principale<br/>pranzo<br/>cena|
| OpenStatus | Indica se un luogo è aperto o chiuso.|Apri<br/>closed<br/>apertura|
| PlaceName | Nome di un luogo.|Cheesecake Factory|
| PlaceType | Tipo di luogo.|Caffè<br/>Teatro<br/>Libreria|
| PreferredRoute | Strada preferita specificata dall'utente. | 101 <br/>202 <br/>Strada 401|
| Prodotto | Prodotto offerto da un luogo. | Abbigliamento<br/>Fotocamere ASR digitali<br/>Pesce fresco | 
| PublicTransportationRoute | Nome del trasporto pubblico cercato dall'utente. | Northeast Corridor Train<br/>Autobus 3X |
| Rating | Classificazione di un luogo. | 5 stelle<br/>3 stelle<br/>4 stelle|
| RouteAvoidanceCriteria | Criteri per evitare strade specifiche, ad esempio per evitare incidenti, lavori stradali o pedaggi | Pedaggio <br/>Lavori stradali<br/>Strada 11|
| ServiceProvided | Servizio offerto da un'attività o un luogo, ad esempio parrucchiere, spazzaneve, paesaggio. | acconciatura<br/>meccanico<br/>idraulico|
| TransportationCompany | Nome dell'azienda dei trasporti.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Mezzo di trasporto.|Autobus<br/>Eseguire il training<br/>Driving (Guida)|

## <a name="reminder"></a>Reminder 
Il dominio Reminder (Promemoria) offre le finalità e le entità per la creazione, la modifica e la ricerca dei promemoria.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Sposta il mio colloquio alle 9.00 di domani|
|Ricordami di comprare il latte prima di rientrare a casa|
|Controlla se c'è un promemoria per il compleanno di Christine|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Modifica| Modificare un promemoria.|Sposta il mio colloquio alle 9.00 di domani<br/>Sposta promemoria assegnazione a domani|
| Create| Creare un nuovo promemoria.|Crea un promemoria<br/>Ricordami di comprare il latte<br/>Ricordami di chiamare Rebecca quando sono a casa|
| Delete | Eliminare un promemoria.|Elimina immagine promemoria<br/>Elimina questo promemoria|
| Find | Trovare un promemoria.|C'è un promemoria per l'anniversario?<br/>Controlla se c'è un promemoria per il compleanno di Christine|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Text | Testo di un promemoria.|ritiro in tintoria<br/>ritiro auto dal meccanico|

## <a name="restaurantreservation"></a>RestaurantReservation 
Il dominio RestaurantReservation (Prenotazione ristorante) offre le finalità e le entità correlate alla gestione delle prenotazioni dei ristoranti.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Prenota allo Zucca per due per stasera|
|Prenota un tavolo al BJ per domani|
|Tavolo per 3 al Palo Alto per le 7|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Reserve | Effettuare una prenotazione per un ristorante. |Prenota allo Zucca per due per stasera<br/>Prenota un tavolo per domani<br/>Tavolo per 3 al Palo Alto per le 7|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Indirizzo| Posizione o indirizzo di un evento per una prenotazione.|Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Amenities | Attributo che descrive le attrattive di un luogo.|vista oceano<br/>vietato fumare|
| AppName | Nome di un'applicazione per effettuare prenotazioni.|OpenTable<br/>Yelp<br/>TripAdvisor|
| Atmosphere | Descrizione dell'atmosfera di un ristorante o di un altro luogo.|romantico<br/>informale<br/>adatto ai gruppi|
| Cuisine | Tipo di alimenti, cucina o nazionalità della cucina. |Cinese<br/>Italiano<br/>Messicana|
| MealType | Tipo di pasto associato a una prenotazione.|colazione<br/>pasto principale<br/>pranzo<br/>cena|
| PlaceName | Nome di un ufficio locale, ristorante, luogo pubblico o istituzione.|IHOP<br/>Cheesecake Factory<br/>Louvre|
| PlaceType | Tipo di un ufficio locale, ristorante, luogo pubblico o istituzione.|restaurant<br/>opera<br/>cinema|
| Rating | Classificazione di un luogo o un ristorante.|5 stelle<br/>3 stelle<br/>4 stelle|

## <a name="taxi"></a>Taxi 
 
Il dominio Taxi offre le finalità e le entità per la creazione e la gestione delle prenotazioni di taxi.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Prenota un taxi per le 3 del pomeriggio|
|Quanto tempo deve attendere il taxi?|
|Annulla Uber|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Book | Chiamare un taxi. |Prenota taxi<br/>Trova taxi<br/>Prenota uber x|
| Annulla | Annullare un'azione relativa alla prenotazione di un taxi.|Annulla taxi<br/>Annulla Uber|
| Track | Tenere traccia del percorso di un taxi.|Quanto tempo deve attendere il taxi?<br/>Dov'è Uber?|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Indirizzo| Indirizzo associato alla prenotazione di un taxi. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| Posizione o indirizzo di destinazione. |Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | Nome di una destinazione (ufficio locale, ristorante, luogo pubblico o istituzione). |Central Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | Tipo di una destinazione (ufficio locale, ristorante, luogo pubblico o istituzione). |Ristorante<br/>Opera<br/>Cinema|
| PlaceName | Nome di un ufficio locale, ristorante, luogo pubblico o istituzione. |Central Park<br/>Safeway<br/>Walmart|
| PlaceType| Tipo di luogo in una richiesta di prenotazione di un taxi.|Ristorante<br/>Opera<br/>Cinema|
| TransportationCompany | Nome dell'azienda dei trasporti.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | Mezzo di trasporto.|Autobus<br/>Eseguire il training<br/>Driving (Guida)|

## <a name="translate"></a>Translate 
Il dominio Translate (Tradurre) offre le finalità e le entità correlate alla traduzione di testo in una lingua di destinazione.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Traduci in francese|
|Traduci ciao in tedesco|
|Traduci questa frase in inglese|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Translate| Tradurre un testo in un'altra lingua.|Traduci in francese<br/>Traduci ciao in tedesco|


### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| TargetLanguage | Lingua di destinazione di una traduzione.|Francese<br/>Tedesco<br/>Coreano|
| Text | Testo da tradurre.|Hello World<br/>Buongiorno<br/>Buonasera|

## <a name="tv"></a>TV 
 
Il dominio TV offre le finalità e le entità per il controllo dei televisori.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Cambia canale passando alla BBC|
|Mostra Guida TV|
|Guarda National Geographic|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| ChangeChannel| Cambiare un canale in un televisore.|Passa al canale CNN<br/>Cambia canale passando alla BBC<br/>Vai al canale 4|
| ShowGuide| Visualizzare la Guida TV.|Mostra Guida TV<br/>cosa c'è nel canale dei film adesso?<br/>mostra elenco programmi|
| WatchTV| Chiedere di vedere un canale TV.|Voglio vedere Disney Channel<br/>passa a TV<br/>Guarda National Geographic|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| ChannelName | Nome di un canale TV.|CNN<br/>BBC<br/>Canale Cinema|

## <a name="utilities"></a>Servizi pubblici  
Il dominio Utilities (Utilità) offre le finalità per operazioni comuni a molte attività, ad esempio saluti, annullamento, conferma, aiuto, ripetizione, navigazione, avvio e interruzione.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Torna a Twitter|
|Aiuto|
|Ripeti l'ultima domanda|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Annulla | Annullare un'azione.|Annulla messaggio<br/>Non inviare più il messaggio di posta elettronica|
| Confirm | Confermare un'azione.|Sì, confermo<br/>Bene, confermo<br/>OK, confermo|
| FinishTask | Completare un'attività iniziata dall'utente.|Ho finito<br/>Ho completato<br/>Fatto|
| GoBack | Tornare indietro di un passaggio o tornare al passaggio precedente.|Torna a Twitter<br/>Torna indietro di un passaggio<br/>Tornare alla schermata precedente|
| Guida | Richiedere aiuto.|Aiuto<br/>apri aiuto<br/>help|
| Repeat | Ripetere un'azione.|Ripeti l'ultima domanda<br/>ripeti l'ultimo brano|
| ShowNext | Visualizzare l'elemento successivo di una serie. |Mostra il prossimo<br/>passa alla pagina successiva|
| ShowPrevious | Visualizzare l'elemento precedente di una serie.|mostra precedente|
| StartOver | Riavviare l'app o avviare una nuova sessione.|Rinizia<br/>Nuova sessione<br/>restart|
| Arresto | Interrompere un'azione.| Smetti di dirlo<br/>Zitto<br/>Basta|

## <a name="weather"></a>Weather 
Il dominio Weather (Meteo) offre le finalità e le entità per recuperare i report e le previsioni meteo.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|meteo a Londra in settembre|
|Che tempo farà fra 10 giorni?|
|Qual è la temperatura media in India nel mese di settembre?|


### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| GetCondition | Ottenere informazioni storiche relative al meteo. |meteo a Londra in settembre<br/>Qual è la temperatura media in India a settembre?|
| GetForecast | Ottenere informazioni sul meteo e le previsioni per i giorni successivi. |Che tempo fa oggi?<br/>Quali sono le previsioni a 10 giorni?<br/>Come sarà il tempo nel fine settimana?|

### <a name="entities"></a>Entità
| Nome dell'entità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Località| Posizione assoluta per una richiesta meteo.|Seattle<br/>Parigi<br/>Palo Alto|

## <a name="web"></a>Web 
Il dominio Web offre una finalità per la navigazione in un sito Web.

### <a name="examples"></a>Esempi

|Esempi|
|--|
|Passa a facebook.com|
|Vai a www.twitter.com|
|Passa a www.bing.com|

### <a name="intents"></a>Finalità
| Nome della finalità | Descrizione | Esempi |
| ---------------- |-----------------------|----|
| Esplorare | Richiedere di passare a un sito Web specifico. |Passa a facebook.com<br/>Vai a www.twitter.com|

