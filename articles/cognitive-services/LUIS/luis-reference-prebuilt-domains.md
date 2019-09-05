---
title: Riferimento a dominio predefinito-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni di riferimento sui domini predefiniti che sono raccolte predefinite di finalità ed entità di Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: b840f1ce42c9d7e4af8854a2c6bd7fd26f5b88e9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307421"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Informazioni di riferimento sui domini predefiniti per l'app LUIS
Queste informazioni di riferimento descrivono i [domini predefiniti](luis-how-to-use-prebuilt-domains.md) che sono raccolte predefinite delle finalità ed delle entità offerte da LUIS.

I [domini personalizzati](luis-how-to-start-new-app.md), al contrario, vengono avviati senza finalità e modelli. È possibile aggiungere le finalità e le entità dei domini predefiniti a un modello personalizzato.

# <a name="supported-domains-across-cultures"></a>Domini supportati tra le diverse impostazioni cultura

La tabella seguente riepiloga i domini attualmente supportati. Il supporto per l'inglese è in genere più completo rispetto ad altri. 

| Tipo di entità       | IT-IT      | ZH-CN   | DE    | VF     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Calendario](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Comunicazione](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Indirizzo di posta elettronica](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Note](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Posizioni](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [ToDo](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Utilità](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Meteo](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

I domini predefiniti **non sono supportati** in:

* Francese canadese
* Hindi
* Messicano spagnolo

# <a name="description-for-luis-prebuilt-domains"></a>Descrizione per i domini predefiniti LUIS
## <a name="calendar"></a>**Calendario**
Il calendario riguarda riunioni e appuntamenti personali, non eventi pubblici (ad esempio, pianificazioni di Coppa del mondo, calendario degli eventi di Seattle) o calendario generico (ad esempio, il giorno attuale, che cosa inizia, quando è il giorno del lavoro).
### <a name="intents"></a>**Intent**
Nome finalità | DESCRIZIONE | Esempi
---------|----------|---------------
 AcceptEventEntry | Accetta (n) appuntamento/riunione/evento nel calendario. | Accettare un appuntamento. <br> Accetta l'evento <br> accetta la riunione odierna.
 Annulla | Annullare l'azione in corso da parte di assistente virtuale, ad esempio annullare il processo di creazione di una riunione. <br> ***Avviso**: Questa finalità include principalmente l'azione "Annulla" nello scenario del calendario. Se è necessaria un'espressione generale in "Annulla", utilizzare l'intento "Annulla" nel dominio **Utilities** . * | È sufficiente annullare l'evento. <br> No, devo semplicemente annullare l'appuntamento.
 ChangeCalendarEntry | Modificare o ripianificare la voce del calendario. | Ripianificare le 6.00 appuntamento domani alle 14.00 <br> Ripianificare l'appuntamento del medico per le 17.00 <br> Ripianificare il pranzo con Jenny Olson a venerdì. <br> Modificare l'ora dell'evento.
 CheckAvailability | Cercare la disponibilità per un appuntamento o una riunione nel calendario dell'utente o di un altro utente. | Quando è possibile incontrare Jim? <br> Mostra quando Carol è disponibile domani. <br> Chris è libero sabato?
 Confirm | Verificare se eseguire un'operazione o un'azione in base all'intento precedente. <br> ***Avviso**: Questa finalità include principalmente l'azione "conferma" per lo scenario del calendario. Se sono necessarie più espressioni generali su "conferma", usare l'intento "conferma" nel dominio **Utilities** . *| Si tratta di una corretta creazione della riunione <br> Sì, grazie, ci connettiamo alla riunione.
 ConnectToMeeting | Connettersi a una riunione. | Connettimi alla chiamata a 11:00 Conference con Andy. <br> Accettare la chiamata al budget per la riunione.
 ContactMeetingAttendees | Contattare i partecipanti alla riunione. | Indica la riunione che sto eseguendo fino a 3:00 riunioni. <br> Inviare una notifica ai colleghi per le 8 riunioni che devono iniziare alle 8:30.
 CreateCalendarEntry | Aggiungere una voce singola al calendario. | Creare una riunione sulla discussione dei problemi. <br> creare una riunione conabc@microsoft.com
 DeleteCalendarEntry | Richiedere di eliminare una voce del calendario.| Annulla l'appuntamento con Carol. <br> Elimina le 9.00 riunione. <br> Elimina l'evento.
  FindCalendarEntry | Aprire l'applicazione calendario o cercare la voce del calendario. | Trova l'appuntamento di controllo dal dentista. <br> Mostra il calendario. <br> Che cosa si trova nel calendario giovedì?
 FindCalendarWhen | Controllare l'ora in cui si verifica la pianificazione. | Quando posso incontrare Amber e Susan? <br> Quando si dispone di un brunch pianificato? 
 FindCalendarWhere | Controllare la posizione in cui si svolge la pianificazione. | Dove sono i miei appuntamenti domani? <br>Dove devo incontrare Stacy e Michael domani per Dinner?
  FindCalendarWho | Controllare i partecipanti che parteciperanno alla pianificazione di destinazione. | Desidero che i supervisori confermati la riunione di domani a 2. <br> Jim sarà al prossimo incontro degli infermieri?
 FindCalendarDetail | Controllare e visualizzare i dettagli della pianificazione. | Ho bisogno di fornire i dettagli della riunione che ho pianificato con il mio collega Paolo.
 FindDuration | Controllare la durata. | Quanto tempo è necessario per prelevare le drogherie? <br> Quanto tempo è necessario per il pranzo?
 FindMeetingRoom | Trovare le sale riunioni disponibili. | Che cosa soddisfa le stanze? <br> Una nuova sede della riunione, trovarne una.
 GoBack | Tornare all'ultimo passaggio o elemento.  <br> ***Avviso**: Per ulteriori espressioni generali di GoBack, fare riferimento al dominio **Utilities** . * | Precedente <br> Tornare all'ultimo messaggio di posta elettronica.
 Rifiuta | L'utente rifiuta il assistente virtuale proposto. <br> ***Avviso**: Per ulteriori informazioni su come rifiutare espressioni generali, fare riferimento al dominio **Utilities** . * | Non è necessario impostare l'evento. <br> Sono disponibili altre operazioni da eseguire in quel momento.
ShowNext | Controllare l'evento successivo. <br> ***Avviso**: Per ulteriori espressioni generali ShowNext, fare riferimento al dominio **Utilities** . * | Inviami il mio prossimo evento. <br> Che cosa è prossimo al calendario?
 ShowPrevious | Controllare l'evento precedente. <br> ***Avviso**: Per ulteriori espressioni generali ShowPrevious, fare riferimento al dominio **Utilities** . * | Qual è la pianificazione prima?
 TimeRemaining | Controllare l'ora rimanente fino all'evento successivo. | Visualizza la quantità di tempo che ho prima delle riunioni. <br> Visualizza l'intervallo di tempo che ho prima che inizi la riunione successiva.
 
### <a name="entities"></a>**Entità**
Nome entità | Tipo di entità | Descrizione | Esempi | Slot
-------|-----------------------|-------------|---------|--------
ContactName | personName | Nome di un contatto o un partecipante alla riunione. | Incontro con **Betsy**. <br>  Incontro con **Aubrey** il 3 luglio alle 19.00 | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | semplice | Nome del calendario di destinazione. | pranzo con MOM Martedi 12 **personale** <br> USA **Google** Calendar come calendario predefinito. <br> Aggiornare la classe yoga a lun Wed alle 15.00 elenco nel calendario **personale** . | Google <br> personale <br> business <br> principale
Duration | Datetime | Durata di una riunione, un appuntamento o un tempo rimanente. | Aggiungi alla riunione di calendario di lavoro con Gary per discutere i dettagli della borsa di lavoro domani alle 11.00 per **20 minuti**. <br> Aggiungi al calendario un evento alla metropolitana il venerdì sarò a mangiare con Thomas per **un'ora** alle 21.00 | un'ora <br> 2 giorni <br> 20 minuti 
EndDate | Datetime | Data di fine per una riunione o un appuntamento. | Concerto di aggiunta al calendario presso il Bass Hall Mary da 3 a **Mary 5th** | Mary 5  
EndTime | Datetime | Ora di fine per una riunione o un appuntamento. | è possibile impostarlo 2 30 su **tre** | tre 
Location | semplice | Località dell'elemento del calendario, della riunione o dell'appuntamento.  Indirizzi, città e regioni sono un ottimo esempio di località. | inserire una riunione in **Fremont** per inserire il tablet in Birmania <br> riunione di Pro Bono in **Edina** | 209 Nashville Gym <br> 897 Pancake house <br> Garage 
Sala riunioni | semplice | Spazio per una riunione o un appuntamento. | Aggiungi alla riunione del calendario di lavoro con Jake alle 14.00 nel suo **ufficio** questo venerdì | Ufficio <br> sala riunioni <br> Stanza 2
MoveEarlierTimeSpan | Datetime | Tempo richiesto dall'utente per spostare una riunione o un appuntamento in precedenza. | Spostare la data di pranzo avanti di **30 minuti**. | 30 minuti <br> due ore 
MoveLaterTimeSpan |  Datetime | Il tempo richiesto dall'utente per spostare una riunione o un appuntamento in un secondo momento. | eseguire il push della riunione con la casella orchidea per **4 ore**. | 4 ore <br> 15 minuti 
OrderReference | semplice | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare. | Qual è il mio prossimo appuntamento per domani? | successiva
OriginalEndDate | Datetime | Data di fine originale di una riunione o un appuntamento. | Cambia vacanza da **venerdì** a lunedì | Venerdì 
OriginalEndTime | Datetime | Ora di fine originale di una riunione o un appuntamento. | Fare in modo che il termine a **3** vada fino a 4 | 3
OriginalStartDate | Datetime | Data di inizio originale di una riunione o un appuntamento. | Modificare l'appuntamento di **domani**dalle 10.00 a mercoledì alle 9.00  | domani 
OriginalStartTime | Datetime | Ora di inizio originale di una riunione o un appuntamento. | Modificare l'appuntamento di domani dalle **10.00** a mercoledì alle 9.00 | 10:00
PositionReference | ordinal | Posizione assoluta in un elenco, che identifica un elemento da recuperare. | Il **secondo** | secondo <br> No. 3 <br> numero 5
RelationshipName | list | Nome della relazione di un contatto. | Aggiungi pranzo alle ore 1:00 con la mia **moglie** | moglie <br> marito <br> sorella 
SlotAttribute | semplice | L'attributo utente desidera eseguire una query o modificare. | modifica **percorso** evento <br> modificarla a **sette ore** | location <br> time 
StartDate | Datetime | Data di inizio di una riunione o un appuntamento. | Crea una riunione il **mercoledì** alle 16.00 | Mercoledì 
StartTime | Datetime | Ora di inizio di una riunione o un appuntamento. | Crea una riunione il mercoledì alle **16.00** | 16.00
Subject | semplice, modello. Qualsiasi | Oggetto, ad esempio il titolo di una riunione o un appuntamento. | Che ora è la riunione di **preparazione dell'entità** ? | Dentisti <br> Pranzo con Julia 
Messaggio | semplice, modello. Qualsiasi | Messaggio da inviare ai partecipanti. | I partecipanti all'avviso della riunione della cena **saranno in ritardo**. | Sarò in ritardo

## <a name="communication"></a>**Comunicazione**
Richieste di effettuare chiamate, inviare testi/IMs, trovare/aggiungere contatti e diverse altre richieste correlate alla comunicazione (in genere in uscita). Il _nome contatto solo_ le espressioni non appartengono al dominio di comunicazione.

### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
AddContact | Aggiungere un nuovo contatto all'elenco dei contatti dell'utente. | Aggiungi nuovo contatto.  <br>   Salvare questo numero e inserire il nome Jane.
AddFlag | Aggiungi flag a un messaggio di posta elettronica | Contrassegna questo messaggio di posta elettronica <br> Aggiungere un flag a questo messaggio di posta elettronica.
AddMore | Aggiungere altri dati a un messaggio di posta elettronica o a un messaggio di testo, come fase della composizione di un messaggio di posta elettronica o di testo. | Aggiungere altro al testo.  <br>   Aggiungere altro al corpo del messaggio di posta elettronica.
Risposta | Rispondere a una chiamata in arrivo. | Rispondere alla chiamata.  <br>   Selezionarlo.
AssignContactNickname | Assegnare un nome alternativo a un contatto. | Modificare Isaac in Dad.  <br>   Modificare il soprannome di Jim. <br>   Aggiungere il nome alternativo a Patti Owens.
CallBack | Restituisce una telefonata. | Richiama.
CallVoiceMail | Connettersi alla posta vocale dell'utente. | Connettimi alla casella segreteria telefonica. <br>Posta vocale. <br>   Chiamare il messaggio vocale.
Annulla | Annulla un'operazione. | Annulla. <br>   Termina.
CheckIMStatus | Verificare lo stato di un contatto in messaggistica immediata. | Lo stato online di Jim è impostato su Assente? 
CheckMessages | Verificare la presenza di nuovi messaggi o messaggi di posta elettronica. | Controllare la posta in arrivo <br>  Sono presenti nuovi messaggi di posta elettronica?
Confirm | Confermare un'azione. | Sì, inviarlo. <br> Giusto, confermo di voler inviare questo messaggio di posta elettronica.
EndCall | Terminare una telefonata. | Blocca la chiamata. <br> Fine.
FindContact | Trovare le informazioni sul contatto in base al nome. | Trova il numero di mummia. <br>   Mostra il numero di Carol.
FinishTask | Termina l'attività corrente. | Ho finito <br> Questo è tutto.
TurnForwardingOff | Disattivare l'inoltro di chiamata. | Arrestare l'invio delle chiamate. <br> Disattivare l'invio delle chiamate.
TurnForwardingOn | Attivare l'invio della chiamata. | Inoltra le chiamate al numero 3333 <br>  Attivare la chiamata di invio a 3333.
GetForwardingsStatus | Ottenere lo stato corrente dell'inoltro di chiamata. | L'inoltro di chiamata è attivato? <br>   Indica se lo stato della chiamata è on o off.
Getnotifications | Ottenere le notifiche. | Apri notifiche personali <br>   è possibile controllare le notifiche di Facebook per il telefono
Goback | Tornare al passaggio precedente. | Torna a Twitter <br>   Torna indietro di un passaggio <br>   Indietro
Ignora | Ignorare una chiamata in arrivo. | Non rispondere <br>   Ignora la chiamata
IgnoreWithMessage | Ignorare una chiamata in arrivo e rispondere invece con un messaggio di testo. | Non rispondere alla chiamata ma inviare un messaggio di testo. <br>   Ignora e invia un messaggio di testo.
Dial | Effettuare una chiamata telefonica. | Chiama Jim <br>   Digitare 311.
FindSpeedDial | Trovare il numero rapido impostato per un numero telefonico e viceversa. | A quale numero corrisponde il numero 5? <br>   È impostato il numero rapido? <br>   Qual è il numero rapido per 941-5555-333?
Inoltra | Inoltra un messaggio di posta elettronica | Inviare questo messaggio di posta elettronica a Greg.
ReadAloud | Leggere un messaggio o un messaggio di posta elettronica all'utente. | Leggi testo. <br>   Cosa ha detto nel messaggio?
PressKey | Premere un pulsante o un numero sul tastierino numerico. | Componi asterisco. <br>   Premi 1 2 3.
QueryLastText | Eseguire una query sull'ultimo testo o messaggio. | Chi ha scritto un SMS? <br>   Chi ha recentemente un messaggio?
Redial | Ricomporre o chiamare di nuovo un numero. | Richiama. <br>   Richiama ultimo numero.
Rifiuta | Rifiutare una chiamata in arrivo. | Rifiuta chiamata <br>   Non posso rispondere <br>   Non sono disponibile ora, richiamo più tardi.
Rispondi | Rispondere a un messaggio. | Rispondi a Lore Hound <br>   Rispondi digitando il mio modo
SearchMessages | Eseguire una ricerca nei messaggi in base a determinate condizioni. | È possibile cercare i messaggi di posta elettronica inviati da Jane?
SendEmail | Inviare un messaggio di posta elettronica. Questa finalità si applica alla posta elettronica e non ai messaggi di testo. | Invia un messaggio di posta elettronica a Mike Waters: Mike, la cena della settimana scorsa è stata splendida. <br>   Invia un messaggio di posta elettronica a Bob.
SendMessage | Inviare un messaggio di testo o un messaggio istantaneo. | Invia un messaggio di testo a Chris e Carol <br>   Messaggio Facebook Greg <br>   
SetSpeedDial | Impostare un numero rapido per il numero di telefono di un contatto. | Imposta numero rapido 1 per Carol. <br>   Imposta numero rapido per mamma.
ShowCurrentNotification | Mostra le notifiche correnti. | Sono presenti nuove notifiche? <br> Visualizza una notifica.
ShowNext | Visualizzare l'elemento successivo, ad esempio in un elenco di messaggi di testo o messaggi di posta elettronica. | Visualizza successivo. <br>   Passa alla pagina successiva.
ShowPrevious | Visualizzare l'elemento precedente, ad esempio in un elenco di messaggi di testo o messaggi di posta elettronica. | Mostra precedente. <br>   Precedente. <br>   Passa a precedente.
TurnSpeakerOff | Disattivare l'altoparlante. | Escludi altoparlante. <br>   Disattiva viva voce.
TurnSpeakerOn | Attivare l'altoparlante. | Modalità viva voce. <br>   Attiva viva voce.

### <a name="entities"></a>**Entità**
Nome entità | Tipo di entità | DESCRIZIONE | Esempi | Slot
------|-------|----------|--------------|---------------
Attachment | semplice | Allegato che l'utente desidera inviare tramite testo o messaggio di posta elettronica. | Invia tramite posta elettronica un **file** da OneNote. <br> Invia il mio **documento** di pulizie a Katie. | file <br> doc
AudioDeviceType | semplice | Tipo di dispositivo audio (altoparlante, auricolare, microfono e così via). | Risposta tramite **Hands-Free**. <br> Ricomporre il **telefono del altoparlante**. | Madrelingua <br> Hands-Free <br> Bluetooth
Category | semplice | La categoria di un messaggio o di un messaggio di posta elettronica, la categoria deve avere una definizione chiara nel sistema di posta elettronica, ad esempio "non letto", "flag". La descrizione con definizione chiara, ad esempio, "New" e "recenti" non sono categorie. | Contrassegna tutti i messaggi di posta elettronica come **letti**  <br> Nuovo indirizzo di posta elettronica con **priorità alta** per Paul | importante <br> priorità alta <br> lettura
ContactAttribute | semplice | Attributo del contatto a cui si rivolge la richiesta dell'utente.| A tutti i **compleanni** successivi il mese di conoscenza? | compleanni <br> Address <br> numero di telefono
ContactName | personName  | Nome di un contatto o del destinatario del messaggio. | Invia il messaggio a **Stevens** | Stevens
Data/ora | Datetime | DateTime di un messaggio di posta elettronica ricevuto. | Leggi la posta **odierna** <br> Chi ha inviato un messaggio di posta elettronica **oggi**? <br> Chi ha telefonato alle **19.00**? | oggi <br> domani
DestinationPhone | semplice | L'utente di destinazione desidera chiamare o inviare un testo a. | effettuare una chiamata a **House** <br> Invia un messaggio di testo a **Home** | internamente <br> home
EmailAddress | email | L'utente dell'indirizzo di posta elettronica desidera inviare o eseguire una query. | Invia messaggio di posta elettronica aMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | semplice, modello. Qualsiasi | Testo usato come oggetto del messaggio di posta elettronica. | Comporre un messaggio di posta elettronica a David con l'oggetto **Hey**  | Re: storia interessante
Chiave | semplice | L'utente chiave vuole premere. | Premere il tasto **barra spaziatrice** . <br> Premere **9** | cancelletto <br> stelle <br> 8
Riga | semplice | L'utente della riga vuole usare per inviare un messaggio di posta elettronica o un testo da. | Leggi l'ultimo messaggio di posta elettronica **hotmail** . <br> Chiamare Peter per **dispositivi mobili**. <br> Chiama Dad con la mia riga di **lavoro** .| Hotmail <br> Skype <br> Cella inglese
SenderName | personName | Nome del mittente. | Leggi il messaggio di posta elettronica da **David** <br> Messaggi di posta elettronica da Coppola | David <br> Conti
FromRelationshipName | semplice | Nome della relazione del mittente. | Leggere il messaggio da **Dad**. <br> È possibile leggere tutti i messaggi di testo da **MOM**? | Dad <br> Mamma 
Messaggio | semplice, modello. Qualsiasi |  Messaggio da inviare come messaggio di posta elettronica o messaggio di testo.  | Invia un messaggio di posta elettronica**che informa che sono occupato**. | Sono occupato
OrderReference | semplice | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare. | Qual è stato l' **ultimo** messaggio inviato? <br> Leggi la posta elettronica **più recente** di Nokia. <br> Leggere **nuovi** messaggi di testo. | last <br> latest <br> recente <br> più recente
PositionReference | semplice, ordinale | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare.| Qual è il **primo** messaggio inviato? <br> Il **terzo** .| Primo <br> terze
phoneNumber | phoneNumber | L'utente del numero di telefono vuole chiamare o inviare un SMS a. | Invia un testo a **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | semplice | Nome della relazione di un destinatario del contatto o del messaggio. | Invia un messaggio di posta elettronica a mia **moglie** | moglie
SearchTexts | Simple, pattern. Any | Testi utilizzati per filtrare messaggi di posta elettronica o messaggi | Cerca tutti i messaggi di posta elettronica che contengono "**Surface Pro**" | Superficie Pro
SpeedDial | semplice | Composizione della velocità. | Chiamare **3 4 5**. <br> Impostare Speed Dial **uno**. | 345 <br> 5

## <a name="email"></a>**Indirizzo di posta elettronica**
Il messaggio di posta elettronica è un sottodominio del dominio di *comunicazione* . Contiene principalmente le richieste di invio e ricezione di messaggi tramite posta elettronica.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
AddMore | Aggiungere altri dati a un messaggio di posta elettronica o a un messaggio di testo, come fase della composizione di un messaggio di posta elettronica o di testo. | Aggiungere altro al corpo del messaggio di posta elettronica.
Annulla | Annulla un'operazione. <br> ***Avviso**: Per ulteriori informazioni su come annullare le espressioni generali, fare riferimento al dominio **Utilities** . * | Annulla. Non inviarlo. <br> Termina.
CheckMessages | Verificare la presenza di nuovi messaggi/messaggi di posta elettronica senza richiesta condizionale. Se si verifica una condizione, le espressioni appartengono alla finalità *SearchMessage* . | Controllare la posta in arrivo. <br> Sono presenti nuovi messaggi di posta elettronica? 
Confirm | Confermare un'azione continua relativa all'elaborazione di messaggi di posta elettronica. <br> ***Avviso**: Per ulteriori informazioni, vedere l'articolo relativo al dominio **Utilities** . * | Sì, inviarlo. <br> Confermo di voler inviare questo messaggio di posta elettronica.
Eliminare | Eliminare un messaggio di posta elettronica o i messaggi filtrati in base ad alcune condizioni. | Inserire il messaggio di posta elettronica nel cestino <br> Svuota la posta in arrivo. <br> Rimuovere la posta elettronica di Mary.
ReadAloud | Leggere un messaggio o un messaggio di posta elettronica all'utente. <br> ***Avviso**: Per ulteriori espressioni generali ReadAloud, fare riferimento al dominio **Utilities** . *  | Leggere il messaggio di posta elettronica inviato da Mary.
Rispondi | Rispondere al messaggio di posta elettronica corrente. | Creare una risposta al messaggio di posta elettronica. <br> Rispondi digitando "Grazie molto, i migliori saluti, Giorgio".
SearchMessages | Cercare i messaggi in base ad alcune condizioni, tra cui il nome del mittente, l'ora e il soggetto. | Mostra i messaggi di Nisheen. <br> È possibile cercare i messaggi di posta elettronica con il titolo "ABC"?
SendEmail | Inviare un messaggio di posta elettronica. | Invia un messaggio di posta elettronica a Mike: Mike, la cena della settimana scorsa è stata splendida. <br> Invia un messaggio di posta elettronica a Bob.
ShowNext | Vedere gli elementi successivi in un elenco di messaggi di testo o messaggi di posta elettronica. <br> ***Avviso**: Per ulteriori espressioni generali ShowNext, fare riferimento al dominio **Utilities** . * | Visualizza successivo. <br> Passa alla pagina successiva.
ShowPrevious | Mostra gli elementi precedenti in un elenco di messaggi di testo o messaggi di posta elettronica. <br> ***Avviso**: Per ulteriori espressioni generali ShowPrevious, fare riferimento al dominio **Utilities** . * | Mostra precedente. <br> Precedente. <br> Passa a precedente.
Inoltra | Inviare un messaggio di posta elettronica. | Inviare questo messaggio di posta elettronica a Greg.
AddFlag | Aggiungere il flag a un messaggio di posta elettronica. | Contrassegna questo messaggio di posta elettronica <br> Aggiungere un flag a questo messaggio di posta elettronica.
QueryLastText | Eseguire una query sull'ultimo messaggio di posta elettronica. | Chi ha inviato un messaggio di posta elettronica? <br> Chi ha recentemente inviato un messaggio di posta elettronica?


### <a name="entities"></a>**Entità**
Nome entità | Tipo di entità | DESCRIZIONE | Esempi | Slot
------|-------|----------|--------------|---------------
Attachment | semplice | Allegato che l'utente desidera inviare tramite testo o messaggio di posta elettronica. | Invia tramite posta elettronica un **file** da OneNote. <br> Invia il mio **documento** di pulizie a Katie. | file <br> doc
ContactName | personName  | Nome di un contatto o del destinatario del messaggio. | Invia il messaggio a **Stevens** | Stevens
Date | Datetime | Data di ricezione di un messaggio di posta elettronica. | Leggi la posta **odierna** <br> Chi ha inviato un messaggio di posta elettronica **oggi**? | oggi
EmailAddress | email | L'utente dell'indirizzo di posta elettronica desidera inviare o eseguire una query. | Invia messaggio di posta elettronica aMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | semplice, modello. Qualsiasi | Testo usato come oggetto del messaggio di posta elettronica. | Comporre un messaggio di posta elettronica a David con l'oggetto **Hey**  | Re: storia interessante
SenderName | personName | Nome del mittente. | Leggi il messaggio di posta elettronica da **David** <br> Messaggi di posta elettronica da Coppola | David <br> Conti
FromRelationshipName | semplice | Nome della relazione del mittente. | Leggere il messaggio da **Dad**. <br> È possibile leggere tutti i messaggi di testo da **MOM**? | Dad <br> Mamma 
Messaggio | semplice, modello. Qualsiasi |  Messaggio da inviare come messaggio di posta elettronica o messaggio di testo.  | Invia un messaggio di posta elettronica**che informa che sono occupato**. | Sono occupato
Category | semplice | La categoria di un messaggio o di un messaggio di posta elettronica, la categoria deve avere una definizione chiara nel sistema di posta elettronica, ad esempio "non letto", "flag". La descrizione con definizione chiara, ad esempio, "New" e "recenti" non sono categorie. | Contrassegna tutti i messaggi di posta elettronica come **letti**  <br> Nuovo indirizzo di posta elettronica con **priorità alta** per Paul | importante <br> priorità alta <br> lettura
OrderReference | semplice | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare. | Qual è stato l' **ultimo** messaggio inviato? <br> Leggi la posta elettronica **più recente** di Nokia. <br> Leggere **nuovi** messaggi di testo. | last <br> latest <br> recente <br> più recente
PositionReference | semplice, ordinale | Posizione ordinale o relativa in un elenco che identifica un elemento da recuperare.| Qual è il **primo** messaggio inviato? <br> Il **terzo** .| Primo <br> terze
RelationshipName | semplice | Nome della relazione di un destinatario del contatto o del messaggio. | Invia un messaggio di posta elettronica a mia **moglie** | moglie
Time | Datetime | Time | Invia un messaggio **di posta elettronica**. | cena
SearchTexts | Simple, pattern. Any | Testi utilizzati per filtrare messaggi di posta elettronica o messaggi | Cerca tutti i messaggi di posta elettronica che contengono "**Surface Pro**" | Superficie Pro 
Riga | semplice | L'utente della riga vuole usare per inviare un messaggio di posta elettronica da. | Leggi l'ultimo messaggio di posta elettronica **hotmail** . <br> Invia un messaggio di posta elettronica dall' **account di lavoro**.| Hotmail <br> account aziendale 

## <a name="homeautomation"></a>**HomeAutomation**
Il dominio HomeAutomation fornisce Intent ed entità correlati al controllo dei dispositivi Smart Home. Sono supportati principalmente i comandi di controllo correlati a luci e condizionatori d'aria. Tuttavia, presenta alcune funzionalità di generalizzazione su altri dispositivi elettrici.
### <a name="supported-devices-and-properties"></a>**Proprietà e dispositivi supportati**
Dispositivo | Properties
-------|---------
Sensore di temperatura | Temperatura
Luce, lampada | On-off, luminosità, colore
TV, Radio | On-off, volume
Condizionatore aria (A/C), termostato | On-off, temperatura, potenza
Fan | Spegni, Power
Outlet, DVD Player, Ice Maker e così via. | On-off

### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
 TurnOff | L'utente desidera disattivare il dispositivo o le impostazioni.  | Spegnere le luci. <br> Disattivare un elemento. <br> Un elemento disattivato.
 TurnOn | Attivare un dispositivo o attivare e impostare il dispositivo su una particolare impostazione o modalità. | Accendere la luce fino al 50%. <br> Accendere il creatore del caffè <br> È possibile accendere il creatore del caffè?
 Sedevice | L'utente vuole impostare il dispositivo su un'impostazione o un tipo specifico.  | Impostare il condizionatore aria su 26 gradi. <br> Accendere le luci blu. <br> Chiamare questa luce come luce notturna.
 QueryState | L'utente chiede lo stato di un dispositivo o di un'impostazione.  | In che modo il termostato è impostato su? <br> L'A/C è acceso? <br> Qual è la temperatura della camera?
 TurnUp | Attivare le impostazioni o la luminosità dei dispositivi. | Illumina le luci del 75%. <br> Per il 10 percento.  <br> Renderlo più caldo in salotto.
 Basso | Disattivazione ma non disattivazione di un dispositivo, tramite attenuazione, temperatura o luminosità delle luci. | Abbassare la libreria del 44%. <br> Attenua le luci. <br> Fai raffreddare lo spazio.
### <a name="entities"></a>**Entità**
Nome entità | Tipo di entità | DESCRIZIONE | Esempi
-------|----------|--------------|---------------
DeviceName | List | Testo definito dall'utente per i dispositivi. | Blu<br> Christmas <br> Reception
DeviceType | List | Dispositivi supportati. | Luci <br> Condizionatore aria <br> Nightlight
Location | semplice | Località o stanza in cui si trova il dispositivo. | Cucina<br> Starbucks <br> Camera
NumericalChange | semplice | Importo in base al quale viene aumentata o diminuita un'impostazione. <br> <br> _Lo slot viene visualizzato solo con turn_up e turn_down._ | 3<br> 50%<br>
OrderReference | ordinal | Lo scopo di questo slot è acquisire la selezione degli elementi. Indica la posizione dell'elemento in un elenco. | Primo<br> Secondo
Quantificatore | List | Il quantificatore indica il numero di istanze di un'entità particolare a cui si fa riferimento. Ad esempio, "All", "ogni" e così via. | Tutti<br> Ogni<br> Tutto
Impostazione | Semplice | Impostazione a cui l'utente desidera impostare il dispositivo, che include scena, livello, intensità, colore, modalità, temperatura, stato del dispositivo. | Blu<br> 72 <br> Calore 
SettingType | List | Impostazione del dispositivo a cui l'utente è interessato. | Temperatura<br> 
Data/ora |  Datetime | Tempo e durata per il funzionamento di un dispositivo| 5 minuti <br> 15:00
Unità | List | Per contrassegnare parole come ' degree ',' percent ', "Fahrenheit", "Celsius", ogni termine di unità deve essere contrassegnato separatamente. | Gradi<br> Percent


## <a name="notes"></a>**Note**
Nota dominio semplifica la creazione di note e la scrittura di elementi per gli utenti.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
AddToNote | Consente di aggiungere informazioni a una nota aperta. | Aggiungi alla nota sulla pianificazione per contattare il mio capo sul progetto.
Cancella | Cancellare tutte le voci da una nota preesistente. | Rimuovere tutti gli elementi dalla nota di ferie. <br>Cancella tutto dalla nota di lettura.
Confirm | Confermare un'azione relativa a una nota. <br> ***Avviso**: Questa finalità include principalmente l'azione "conferma" per lo scenario di nota. Se sono necessarie più espressioni generali su "conferma", usare l'intento "conferma" nel dominio **Utilities** . * | Questa nota è accettabile. <br>Sto confermando la conservazione di tutti gli elementi negli elenchi.
Create | Creare una nuova nota. | Creare una nota. <br>Nota per ricordare che Jason è in città la prima settimana del maggio. 
Eliminare | Eliminare un'intera nota. | Elimina la nota di ferie. <br>Elimina la nota relativa alle drogherie.
ReadAloud | Leggere una nota. | Leggi la prima nota. <br>Leggi i dettagli.
Chiudi | Chiude la nota corrente. | Chiudere la nota. <br>Chiude la nota corrente.
Aperte | Apre una nota preesistente. | Aprire la nota di chiamata. <br>Aprire la nota "Planning".
RemoveSentence | Rimuovere una frase per una nota. | Rimuovere l'ultima frase. <br>Elimina i chip dalla nota generica. <br>Rimuovi le penne dalla nota della scuola acquisti
ChangeTitle | Modificare il titolo della nota. | Questa nota è denominata "Planning".

### <a name="entities"></a>**Entità**
Nome entità | Tipo di entità | Descrizione | Esempi 
------- | ------- | ------- | -------
Text | semplice, modello. Qualsiasi | Testo di una nota o un promemoria. | stretching prima della camminata <br> corsa lunga domani
Titolo | semplice, modello. Qualsiasi | Titolo di una nota. | spesa <br> persone da chiamare <br> attività
CreationDate | datetimeV2 | Questo slot si verifica quando l'utente chiede le note create in un determinato intervallo di data/ora. | 
Quantificatore | List | Quando un utente chiede di eseguire un'azione sugli elementi "tutti", "ogni" o "any" o tutto il testo in una nota. | tutti <br> qualsiasi <br> ogni
OrderReference | ordinal | L'utente desidera eseguire azioni con gli elementi ' First ',' Last ',' Next ' e così via. | first <br> last


## <a name="places"></a>**Posizioni**
I luoghi includono aziende, istituzioni, ristoranti, spazi pubblici e indirizzi. Il dominio supporta la ricerca e l'individuazione delle informazioni in un luogo pubblico, ad esempio località, ore operative e distanza. 
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
MakeCall | Effettuare una chiamata telefonica a un luogo. | Applebees su 1000/200 Rojas St e chiamare.
FindPlace | Cercare un luogo (ufficio, istituzione, ristorante, luogo pubblico, indirizzo). Ma non: <li> Solo nome aziendale: Starbucks <li> Solo nome percorso: Seattle/Norvegia <li> Cucina, cibo o prodotto: Pizza/guacamole/Italiano | Dov'è la biblioteca più vicina? <br> Starbucks a Seattle. <br> Dov'è la biblioteca più vicina? <br> 
GetAddress | Chiedere l'indirizzo di un luogo. | Mostra l'indirizzo di Guu in Robson via. <br> Qual è l'indirizzo dello Starbucks più vicino?
GetDistance | Richiedere la distanza dalla posizione corrente a una posizione specifica. | Quanto è lontano l'Holiday Inn?<br> Quanto è possibile fare per Bellevue Square da qui? <br> Qual è la distanza per Tahoe?
GetPhoneNumber | Chiedere il numero di telefono di un luogo. | Qual è il numero di telefono dello Starbucks più vicino?<br> Assegnare il numero per Home Depot. <br> Un numero di telefono per Disneyland.
GetPriceRange | Chiedere la gamma di consumo per persona in una posizione. | Prezzo medio di J. sushi a Seattle. <br> Cineplex è a metà prezzo il mercoledì? <br> Quanto costa una cena completa a base di aragosta al Sizzler?
GetStarRating | Richiedere la classificazione a stelle di un luogo. | Come viene valutato zucca?<br> Quante stelle ha French Laundry?<br> È bello l'acquario di Monterrey?
GetHours | Chiedere gli orari di apertura di un luogo. | A che ora chiude Safeway?<br> Quali sono gli orari di Home Depot?<br> È ancora aperto Starbucks?
GetReviews | Chiedere le recensioni di un luogo. | Mostra me revisioni per Cheesecake Factory. <br> Leggere le revisioni Cineplex. <br> Sono presenti recensioni recenti per Humperdinks?
GetMenu | Chiedere informazioni sul menu di un ristorante. | Zucca serve alimenti vegani? <br> Che cosa si trova nel menu di frizzante? <br> Mostra il menu di applebeo.
RatePlace | Valutare una posizione. | classificazione a 4 stelle per la pizza di maxi in Kimberly. <br> Offri 4 stelle a Bonefish su TripAdvisor. <br> Creare una recensione di 4 stelle per la Hacienda.
AddFavoritePlace | L'utente desidera aggiungere un percorso ai Preferiti o all'elenco MVP. | Salvare questo percorso nei Preferiti. <br> Aggiungi l'acquisto migliore ai Preferiti.

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | Descrizione | Esempi | Esempi di espressioni
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | semplice | Posizione o indirizzo di un luogo. | Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield Rd.** in **palo alto** <br> archivi di inizializzazione Bird a **Seattle** <br> Ottenere la distanza da qui a **300 112O Ave se**.
Amenities | semplice | Caratteristiche e vantaggi dell'obiettivo di una posizione pubblica. | fronte mare <br> parcheggio gratuito | Ristoranti di Kirkland **Waterfront** Seafood. <br> Ci sono **parcheggi gratuiti** nelle vicinanze?
Cuisine | semplice | Tipo di alimenti, cucina o nazionalità della cucina. | Cinese <br> Italiano <br> Sushi <br> Noodle <br> | Aiutami a trovare un ristorante **cinese** . <br> Quali sono le ore di apertura del ristorante **Sushi** ? <br> Dove si trova la **Steak** House più vicina?
Date | Datetime | DateTime o Duration per la data del percorso di destinazione. | domani <br> oggi <br> 18:00 | Quali tempi si chiudono in acquario **domani**? <br> il negozio di biciclette più vicino aperto dopo le **18.00**
Distanza | dimensione | Distanza da una posizione pubblica dalla posizione currenct dell'utente. | 15 miglia <br> 10 chilometri | un negozio di abbigliamento entro **15 chilometri** <br> Un ristorante per bambini A soli **10 chilometri** di distanza
MealType | List | Tipo di pasto come colazione o pranzo. | colazione <br> pasto principale | Search **Breakfast** Greenwood Seattle <br> Trova un posto in cui **pranzare**.
Vicino | List | Descrivere un luogo vicino senza posizione o indirizzo assoluto. | vicino <br> in quest'area <br> per me | Trova il ristorante indiano **più vicino** . <br> Dove si trova il Wetherspoon **locale** ? <br> Tutti **i ristoranti più**interessanti?
OpenStatus | List | Indica se un luogo è aperto o chiuso. | apri <br> closed | Qual è la durata della **chiusura** di yogurt Land? <br> Quali sono le ore di **apertura** per Costco?
PlaceName | semplice | Nome di una destinazione (ufficio, ristorante, luogo pubblico o istituzione). Il nome del posto potrebbe contenere un PlaceType se è comunemente usato. | Central Park <br> Safeway <br> Walmart| Ora di apertura di **Safeway** Pharmacy <br> **Walmart** è aperto?
PlaceType | semplice | Tipo di una destinazione (ufficio locale, ristorante, luogo pubblico o istituzione). | restaurant <br> opera <br> cinema | **cinema** a Cambridge <br> C'è un **ristorante** vicino a me?
PriceRange | semplice | Intervallo di prezzi dei prodotti o servizi del luogo. | economici <br> economicamente conveniente <br> costoso | Trova la riparazione **conveniente** dell'appliance <br> Che cos'è una pizzeria **a basso costo** aperta adesso?
Prodotto | semplice | Prodotto offerto da un luogo. | vestiti <br> televisori | Dove si trova il posto migliore per ottenere **cibo**? <br> Trova me East Kilbride alla ricerca di **televisori**.
Classificazione | semplice | Classificazione di un luogo. | 5 stelle <br> top <br> buona | Ci sono posti **interessanti** da mangiare domani <br> **migliore** Ristoranti di Amsterdam <br> Elenca i **primi** tre pizzerie.


## <a name="restaurantreservation"></a>**RestaurantReservation**
Il dominio di prenotazione del ristorante supporta gli Intent sulla gestione della prenotazione per i ristoranti.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
Reserve | Effettuare una prenotazione per un ristorante. | Prenota a zucca per due per la notte. <br> Prenota una tabella per il futuro. <br> Tabella per 3 in palo alto a 7. <br> Effettuare una prenotazione per 3 a Red Lobster.
DeleteReservation | Annulla o Elimina una prenotazione per un ristorante. | Annulla la prenotazione all'zucca di domani notte. <br> Dimentica la mia prenotazione per Red Lobster alle ore 7:00. Venerdì successivo. <br> Non è necessaria la prenotazione per zucca, annullarla.
ChangeReservation | Modificare l'ora, la posizione o il numero di persone per una prenotazione del ristorante esistente. | Modificare la prenotazione per le 21.00 <br> Modificare la prenotazione da zucca a Red Lobster. <br> 7 persone anziché 6 per la prenotazione in zucca.
Confirm | Confermare un'azione relativa alla prenotazione. <br> ***Avviso**: Questa finalità include principalmente l'azione "conferma" per lo scenario di prenotazione del ristorante. Se sono necessarie più espressioni generali su "conferma", usare l'intento "conferma" nel dominio **Utilities** . * | Sì, ho fatto le prenotazioni per stasera alle 8.00 del produttore di pasta. <br> Sì, basta prenotare. <br> Confermare la prenotazione alla barra sushi.
FindReservationDetail | Visualizza le informazioni dettagliate relative a una prenotazione esistente. | Trova prenotazione alla San Diego del Rinascimento <br> Mostra la prenotazione domani. <br> Visualizza i dettagli della prenotazione per zucca.
FindReservationWhere | Controllare il percorso assoluto della prenotazione. | Dove si trova il percorso di zucca nella prenotazione? <br> Mostra le impostazioni locali della prenotazione domani.
FindReservationWhen | Controllare l'ora esatta della prenotazione | Quando è la prenotazione di zucca per domani? <br> Il tempo per la prenotazione al Red Lobster.
Rifiuta | L'utente rifiuta il assistente virtuale proposto per la gestione di una prenotazione. <br> *Si **noti**che:P lease si riferiscono al dominio **Utilities** per altri rifiuti di espressioni generali. * | Non è necessario impostare l'evento. | No, non voglio modificare la prenotazione. <br> No, non è stato prenotato, ho commesso un errore.

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | Descrizione | Esempi
-------|------|---------|-------------------
Indirizzo | semplice | Posizione o indirizzo di un evento per una prenotazione. | Palo Alto<br>300 112th Ave SE<br>Seattle
Atmosphere | List | Descrizione dell'atmosfera di un ristorante. | romantico<br>informale<br>adatto ai gruppi<br>interessante
Cuisine | semplice | Tipo di alimenti, cucina o nazionalità della cucina. | Cinese<br>Italiano<br>Messicana<br>Sushi<br>Noodle<br>bistecca
MealType | List | Tipo di pasto associato a una prenotazione. | colazione<br>pasto principale<br>pranzo<br>cena
PlaceName | semplice | Nome di un ristorante | Zucca<br>Cheesecake Factory<br>aragosta rossa
Classificazione | semplice | Classificazione di un luogo o un ristorante. | 5 stelle<br>3 stelle<br>4 stelle
NumberPeople | semplice | Numero di persone per la prenotazione | 3<br>sei
Time | Datetime| Tempo per la prenotazione del ristorante | domani<br>cena<br>7:00<br>Vigilia di Natale


## <a name="todo"></a>**ToDo**
_Todo_ Domain fornisce i tipi di elenchi di attività che consentono agli utenti di aggiungere, contrassegnare ed eliminare i relativi elementi todo.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
AddToDo | L'utente desidera aggiungere elementi attività per qualsiasi tipo di elenco. |  Ricordami di acquistare latte. <br> Aggiungi un elemento denominato "acquista latte" all'elenco attività
Confirm | L'utente desidera confermare un'azione in corso. L'espressione contiene un segnale esplicito come "Yes" per confermare un'operazione. <br> <br> ***Avviso**: Questa finalità include principalmente l'azione "conferma" per lo scenario ToDo. Se sono necessarie più espressioni generali su "conferma", usare l'intento "conferma" nel dominio **Utilities** . * | Eliminare l'attività. <br> Sono sicuro di voler aggiungere questa attività. <br> Sì, voglio farlo.
Annulla | L'utente desidera annullare l'azione in corso.  <br> <br> ***Avviso**: Questa finalità include principalmente l'azione "conferma" per lo scenario di prenotazione del ristorante. Se sono necessarie più espressioni generali su "conferma", usare l'intento "conferma" nel dominio **Utilities** . * | No, dimenticarlo. <br> è sufficiente annullare l'attività. <br> No, non aggiungerlo.
DeleteToDo | Per eliminare un elemento nell'elenco attività, fare riferimento all'ordine o eliminare tutti gli elementi attività. | Rimuovere tutte le attività. <br> Consente di eliminare la seconda.
MarkToDo | Contrassegnare un'attività come completata o eseguita facendo riferimento al contenuto dell'ordine o dell'attività. | Contrassegnare l'attività "acquista latte" come completata. <br> Completare la lettura dell'attività. <br> Completa tutto.
ShowNextPage | Un elenco verrà suddiviso in diverse pagine per essere visualizzato. Mostra gli elementi elenco nella pagina successiva per l'utente. | È possibile visualizzare la pagina successiva nell'elenco degli acquisti? <br> Quali sono i passaggi successivi? <br> Argomenti successivi
ShowPreviousPage | Mostra gli elementi elenco nella pagina precedente dell'utente. | Mostra indietro. <br> È necessario controllare le attività precedenti.
ShowToDo | Mostra tutti gli elementi nell'elenco attività. | Mostra l'elenco degli acquisti. <br> Mostra l'elenco drogheria.

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | Descrizione | Esempi
-------|------|---------|-------------------
ContainsAll | semplice | Rappresenta tutti gli elementi o qualsiasi elemento nell'elenco attività | consente di rimuovere **tutte le** attività. <br> Completa **tutto**.
ordinal | ordinal | Riferimento ordinale o numerico a un elemento. | Contrassegnare la **terza** come completata. <br> Eliminare la **prima** attività.
ListType | semplice | Tipo di elenco attività.  | Aggiungere scarpe all'elenco degli **acquisti** .
FoodOfGrocery | List | Rilevare un elenco di elementi alimentari | Ricordami di acquistare **latte**. <br> Aggiungere **Beef** all'elenco drogheria.
TaskContent | Simple, pattern. Any | Rilevare il contenuto di un'attività. | Ricordami di **chiamare la mia madre** . <br> Aggiungi **festeggiare il compleanno di John** per l'elenco attività


## <a name="utilities"></a>**Utilità**
_Utilities_ Domain è un dominio generale tra tutti i modelli predefiniti di _Luis_ , che contiene finalità e espressioni comuni negli scenari di differenza.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
 Annulla | Annullare un'azione, una richiesta, un'attività o un servizio. | Annullarlo. <br> Non importa, dimenticarlo.
 Confirm | Confermare un'azione. | Certo <br> Sì, <br> Confermare.
 Rifiuta | L'utente rifiuta il assistente virtuale proposto. | No
 FinishTask | Completare un'attività iniziata dall'utente. | L'operazione è terminata. <br> Questo è tutto. <br> Fine.
 GoBack | Tornare indietro di un passaggio o tornare al passaggio precedente. | Tornare indietro di un passaggio. <br> Indietro.
 ? | Richiedere aiuto. | Per assistenza. <br> Aprire la guida.
 Repeat | Ripetere un'azione. | Ripetere l'operazione.
 ShowNext | Mostra o indica gli elementi successivi. | Visualizza successivo.
 ShowPrevious | Visualizzare l'elemento precedente di una serie. | Mostra quello precedente.
 StartOver | Riavviare l'app o avviare una nuova sessione. | Riavviare.
 Arresto | Informare il Virtual Assistant per interrompere la comunicazione.  | Interrompi questa operazione.
 SelectAny | L'utente chiede di selezionare gli elementi o i risultati visualizzati sullo schermo. | Qualsiasi. <br> Selezionarne uno.
 SelectNone | L'utente seleziona Nessuno degli elementi esistenti e richiede ulteriori opzioni. | Fornire altri suggerimenti. <br> Nessuno di essi.
  SelectItem | L'utente chiede di selezionare un elemento o i risultati visualizzati sullo schermo. | Selezionare la terza. <br> Selezionare l'angolo in alto a destra.
 Escalation | Chiedere a un servizio clienti di gestire i problemi. | È possibile rivolgersi a una persona?
 ReadAloud | Consente di leggere un elemento ad alta voce all'utente. | Leggere questa pagina. <br> Leggilo ad alta voce.

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | DESCRIZIONE | Esempi
------------|-------------|-------------|---------
ordinal | ordinal | Riferimento ordinale o numerico a un elemento. | **Secondo** oggetto. <br> Quello **successivo** .
number | number | Quantità di elementi desiderati dall'utente | **3** elementi successivi
DirectionalReference | semplice | Punto di riferimento per la posizione della schermata in cui si trova un elemento. | Quello giusto <br> upper

## <a name="weather"></a>**Meteo**
Il dominio meteorologico è incentrato sul controllo della condizione meteorologica e degli avvisi con località e ora o verifica del tempo in base alle condizioni meteorologiche.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
 CheckWeatherValue | Richiedere un fattore meteorologico o correlato al meteo per una località in previsione o informazioni precedenti. <br> I fattori correlati al meteo includono: <li> temperatura </li> <li> pioggia/nevicata/precipitazione </li> <li> secco/bagnato/umidità </li> <li> nebbia </li> <li> Indice UV </li> <li> pollici di pioggia/neve </li> | Qual è l'indice di qualità dell'aria a Pechino? <br> Qual è la quantità di pioggia prevista a Seattle nel marzo? <br> Registrare la temperatura massima delle Hawaii.
 CheckWeatherTime | Chiedere la data e l'ora dei fattori correlati alle previsioni meteorologici o cronologici per una località. | Quando si prevede di piovere? <br> Il momento giusto per passare al Canada <br> Quando è il mese più caldo in Minnesota?
 QueryWeather | Chiedere la condizione meteorologica o i fattori correlati al meteo per una posizione specifica per la quale è prevista una risposta "Sì, no o forse" oppure richiedere consigli sulle attività che dipendono dalla condizione meteo. | Domani pioverà? <br> Oggi è Sunny? <br> È possibile che sia troppo presto per andare in Alaska?
 ChangeTemperatureUnit | Modificare l'unità di tempo, tra cui Celsius, Kelvin e Fahrenheit. | Convertire in Celsius. <br> Posso ottenerlo in Kelvin?
 GetWeatherAdvisory | Ottenere avvisi meteorologici o avvisi da una località specifica. | Sono presenti avvisi meteorologici a Memphis? <br> Sono presenti avvisi meteorologici per la mia area?

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | Descrizione | Esempi
------------|-------------|-------------|---------
Location | geography | Percorso assoluto o implicito per una richiesta Meteo. | Palo Alto<br>Shanghai<br>Seattle<br>Delvina<br>
Data/ora   | Datetime | DateTime o Duration per l'esecuzione di una query sul tempo. | Novembre<br>Ogni ora<br>mattina<br>Questo fine settimana<br>10 giorni<br>
AdditionalWeatherCondition | list | Descrizione aggiuntiva parola per il meteo, ad esempio la velocità o la direzione del vento. | direction<br>Veloce<br>velocità
Storico | semplice | Descrizione delle parole della condizione meteorologica cronologica, inclusa la media dei case con bordi nel periodo di tempo precedente. | passato<br>cronologia/cronologia<br>stagionale<br>tempo migliore<br>mai registrato
PrecipitationUnit | dimensione | Precipitazione per neve o pioggia. | 5 pollici<br>6 cm
SuitableFor | semplice | Descrizione di un'attività umana in una condizione meteo, che è comune quando gli utenti eseguono query su consigli attività che dipendono dalla condizione meteo. | giacca<br>ombrello<br>swimming
TemperatureUnit |temperatura | temperatura | 18 gradi Celsius<br>7 gradi Kelvin
WeatherRange | semplice | Condizione specifica di temperatura, vento e altre condizioni meteorologiche tra un periodo di tempo | maximum<br>elevata<br>bassa<br>media alta<br>Massima
WeatherCondition | semplice | Descrizione della condizione meteo | sole<br>pioggia<br>piovere<br>temperatura<br>neve<br>hot
WindDirectionUnit | list | Parole di direzione del vento | nord<br>Sud<br>est<br>occidentale<br>nord-orientale


## <a name="web"></a>**Web**
Il dominio web fornisce le finalità e le entità per la ricerca di un sito Web.
### <a name="intents"></a>**Intent**
Nome finalità | Descrizione | Esempi
---------|----------|---------
 WebSearch | Richiesta di navigazione a un sito Web specificato o di ricerca in un motore di ricerca. | Area di ricerca in google.com. <br> Trova la canzone Happy Birthday sul Web <br> Passare a www.twitter.com.

### <a name="entities"></a>**Entità**
Entità LUIS | Tipo di entità | DESCRIZIONE | Esempi
------------|-------------|-------------|---------
SearchEngine | List | L'utente del motore di ricerca vuole usare. | Bing <br> Google
SearchText | semplice, modello. Qualsiasi | L'utente di testo desidera eseguire la ricerca. <br> _Contrassegna "Friends in Facebook" come SearchText se il sito Web dopo "in" non è un motore di ricerca. L'URL deve anche essere contrassegnata come SearchText._ | Film <br> Apprendimento avanzato <br> Tom Cruise
Collegamento | url | Collegamento al sito Web. | www.twitter.com

