<properties 
   pageTitle="Sicurezza per il dispositivo StorSimple | Microsoft Azure"
   description="Vengono descritte considerazioni, linee guida e convenzioni di sicurezza e viene spiegato come installare e utilizzare il dispositivo StorSimple in modo sicuro."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# Installazione sicura e funzionamento del dispositivo StorSimple

![Icona di avviso](./media/storsimple-safety/IC740879.png)![Icona di avviso di sicurezza lettura](./media/storsimple-safety/IC740885.png)**LETTURA INFORMAZIONI DI SICUREZZA E INTEGRITÀ**

Leggere tutte le informazioni di sicurezza e integrità in questo articolo che si applicano al dispositivo Microsoft Azure StorSimple. Mantenere tutte le guide di stampata fornite con il dispositivo StorSimple per riferimento futuro. Errore nel seguire le istruzioni e configurazione corretta, utilizzo, e attenzione per questo prodotto può aumentare il rischio di gravi ferite o morte o danni al dispositivo o ai dispositivi. Una [versione scaricabile di questa Guida](http://www.microsoft.com/download/details.aspx?id=44233) è inoltre disponibile.

## Convenzioni di sicurezza

Ecco le icone dove sono visualizzate le precauzioni di sicurezza da osservare durante l'installazione e l'esecuzione del dispositivo Microsoft Azure StorSimple.

| Icona | Description |
|:------|:-------------| 
|![Icona di pericolo](./media/storsimple-safety/IC740879.png)**PERICOLO!**|Indica una situazione di pericolo che, se non viene evitato, comporterà morte o gravi ferite. Questa parola deve essere limitata a situazioni più estreme.| 
|![Icona di avviso](./media/storsimple-safety/IC740879.png) **AVVISO!**|Indica una situazione di pericolo che, se non viene evitata, può comportare morte o gravi ferite.|
|![Icona di avviso](./media/storsimple-safety/IC740879.png) **ATTENZIONE:**|Indica una situazione di pericolo che, se non viene evitato, comporterà ferite lievi o limitate.|
|![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**|Indica le informazioni considerate importanti, ma non correlate al rischio.|
|![Icona di scossa elettrica](./media/storsimple-safety/IC740882.png)**pericolo di scosse elettriche** |Voltaggio|
|![Icona peso elevato](./media/storsimple-safety/IC740883.png)**Pesante**| |
|![Nessuna icona di parti riparabili dall'utente](./media/storsimple-safety/IC740879.png)**Nessuna parte riparabile dall'utente**|Non accedere a meno che non si sia stati adeguatamente formati.|
|![Icona di avviso di sicurezza lettura](./media/storsimple-safety/IC740885.png)**Leggere prima tutte le istruzioni**| |
|![Suggerimento di pericolo icona](./media/storsimple-safety/IC740886.png)**Suggerimento di pericolo**| |


## Precauzioni relative alla gestione

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Icona peso elevato](./media/storsimple-safety/IC740883.png) **AVVISO**


Per ridurre il rischio di ferirsi:

- Un'enclosure completamente configurata può pesare fino a 32 kg (70 libbre); non tentare di sollevarla da soli.
- Prima di spostare lo chassis, assicurarsi sempre che due utenti siano disponibili per gestire il peso. Tenere presente che una persona tentando di sollevare questo peso è in grado di sostenere lesioni.
- Non sollevare lo chassis dagli handle sui moduli di potenza e di raffreddamento (PCM) che si trovano nella parte posteriore dell'unità. Questi non sono progettati per sostenere il peso.

## Precauzioni relative alla connessione

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Icona di scossa elettrica](./media/storsimple-safety/IC740882.png) **AVVISO**

Per ridurre la probabilità di ferite, scosse elettriche o morte:

- Quando si riceve energia da sorgenti multiple AC, disconnettere tutta la potenza di approvvigionamento per l'isolamento completo.

- Scollegare definitivamente l'unità prima di spostare o se si ritiene che sia stata danneggiata in alcun modo.

- Fornire una connessione di terra elettrica sicura per i cavi di alimentazione. Verificare che la messa a terra del locale soddisfi i requisiti nazionali e locali prima della fornitura di energia.

- Assicurarsi che le connessioni di alimentazione siano sempre disconnesse prima di rimuovere un PCM dallo chassis.

- Dato che il cavo di alimentazione Alimentatore Ethernet è il principale dispositivo di disconnessione, accertarsi che si trovino prese vicine alle apparecchiature e che siano facilmente accessibili.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Icona di scossa elettrica](./media/storsimple-safety/IC740882.png) **AVVISO**

Per ridurre il rischio di surriscaldamento o fiamme dalle connessioni elettriche:

- Fornire una fonte di alimentazione adatta con protezione dal sovraccarico elettrico per soddisfare i requisiti dettagliati nelle specifiche tecniche.

- Non utilizzare cavi di alimentazione di moltiplicazione (lead "Y").

- Per conformità ai requisiti termici, emissione e sicurezza applicabili, le coperture non devono essere rimosse e tutti gli alloggiamenti devono essere popolati con i moduli plug-in o unità spazi vuoti.

- Assicurarsi che l'attrezzatura venga utilizzata nel modo specificato dal produttore. Se il dispositivo viene utilizzato in modo non specificato dal produttore, la protezione fornita dai dispositivi potrebbe non riuscire.

![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**

Per il corretto funzionamento dell'apparecchiatura e per evitare danni al prodotto:

- Le porte RJ45 nella parte posteriore del dispositivo sono solo una connessione Ethernet. Questa non deve essere connessa a una rete di telecomunicazioni.

- Assicurarsi di installare il dispositivo in un rack che può contenere una progettazione di raffreddamento fronte-retro.

- Tutti i moduli plug-in e i piatti vuoti fanno parte dello chassis del sistema. È necessario rimuoverli solo quando viene aggiunta immediatamente una sostituzione. Il sistema non deve essere eseguito senza tutti i moduli o spazi vuoti sul posto.

## Precauzioni sistema rack

Quando si monta il dispositivo in un armadio rack, è necessario considerare i seguenti requisiti di sicurezza.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Suggerimento di pericolo icona](./media/storsimple-safety/IC740886.png) **AVVISO**
 
Per ridurre la probabilità di pregiudizio da un suggerimento su:

- La progettazione di rack deve supportare il peso totale di enclosure installati e dovrebbe incorporare funzionalità adatte a impedire il rack deposito o push su durante l'installazione o l'utilizzo normale.

- Quando si carica un rack, riempire rack dalla parte inferiore e vuota dall'alto verso il basso.

- Non fare scivolare più di una enclosure alla volta per evitare il pericolo di rovesciamento del rack.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Icona di scossa elettrica](./media/storsimple-safety/IC740882.png) **AVVISO**

Per ridurre la probabilità di ferite, scosse elettriche o morte:

- Rack deve disporre di un sistema sicuro di distribuzione elettrica. È necessario fornire protezione per ulteriore corrente per lo chassis e non deve essere sottoposto a sovraccarico per il numero totale di enclosure installate. La classificazione del consumo di energia elettrica illustrata sulla targhetta di identificazione dovrebbe essere esaminata.

- Il sistema di distribuzione elettrica deve fornire un motivo affidabile per ciascun alloggiamento nel rack.

- La progettazione del sistema di distribuzione elettrica deve prendere in considerazione la perdita di massa totale di corrente di tutti gli alimentatori in tutti i contenitori. Si noti che ogni alimentatore in ciascun alloggiamento ha una perdita terra corrente massima di mA 1.0 a 60 Hz, 264 volt. Il rack potrebbe richiedere l'assegnazione di etichette con "elevata perdita corrente. La connessione a terra è essenziale prima della connessione di un alimentatore."

- Il Rack, se configurato con allegati, deve soddisfare i requisiti di sicurezza di UL 60950-1 e IEC 60950-1/EN 60950-1.

![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**

Per il raffreddamento corretto del sistema rack:

- Assicurarsi che la progettazione di rack prenda in considerazione la temperatura massima operativa di enclosure di 35 gradi Celsius (95 gradi Fahrenheit).

- Il sistema è gestito con l'installazione di bassa pressione, scarico posteriore (congestione creata da porte rack e ostacoli non devono superare 5 Pascal [0,5 mm acqua misuratore]).

## Precauzioni relative alla potenza modulo raffreddamento (PCM)

Il dispositivo è progettato per funzionare con due PCM. Ogni PCM dispone di un alimentatore e una ventola a due assi. Durante una condizione critica, il sistema consente un errore di un alimentatore continuando le normali operazioni. Due PCM (e pertanto alimentatori) devono sempre essere installati. Un singolo PCM non fornisce alimentazione ridondante. Pertanto, l'errore di PCM anche una può comportare tempi di inattività o la possibile perdita di dati.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![Icona di scossa elettrica](./media/storsimple-safety/IC740882.png) **AVVISO**

Per ridurre la probabilità di ferite, scosse elettriche o morte:

- Non rimuovere le coperture dal PCM. Non esiste il rischio di scosse elettriche all'interno. Per restituire il PCM e ottenere una sostituzione, [contattare il supporto tecnico Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**

Per il corretto funzionamento dell'apparecchiatura e per evitare danni al prodotto:

- È necessario sostituire il PCM non riuscito entro 24 ore. Dopo la rimozione di un PCM per la sostituzione, la sostituzione deve essere completata entro 10 minuti dopo la rimozione.

- Non rimuovere un PCM a meno che non possa essere installata immediatamente una sostituzione. L'enclosure non deve essere attivata senza tutti i moduli sul posto.

## Precauzioni di scarico elettrostatico (ESD)

![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**

Osservare le seguenti precauzioni relative a ESD .

- Assicurarsi di aver installato e archiviato un nastro appropriato antistatico o a strappo alla caviglia.

- Osservare tutte le precauzioni ESD convenzionali durante la gestione di moduli e i componenti.

- Evitare il contatto con i componenti backplane e connettori del modulo.

- I danni ESD non sono coperti da garanzia.

## Precauzioni relative alla disposizione della batteria

L'alimentatore utilizza una batteria speciale per proteggere il contenuto della memoria durante l'interruzione dell'alimentazione temporanea, a breve termine. La batteria è inserita nel PCM. Ricordare le seguenti informazioni riguardo la batteria.

![Icona di avviso](./media/storsimple-safety/IC740879.png) **AVVISO!**

Per ridurre il rischio di cortometraggi, incendio, esplosione, ferite o morte:

- Eliminare le batterie utilizzate in conformità alle normative nazionali/regionali.

- Non smontare, macinare, o scaldare al di sopra di 60 gradi Celsius (140 gradi Fahrenheit) di calore o incenerire. Sostituire la batteria PCM solo con la batteria fornita. L’utilizzo di un'altra batteria può costituire un rischio di incendio o esplosione.

- Utilizzare delimitatori finali protettivi per le batterie se queste vengono rimossi dal gruppo di continuità.

![Icona di notifica](./media/storsimple-safety/IC740881.png)**NOTIFICA:**

Al momento della spedizione o del trasporto delle batterie per via aerea, seguire il documento di istruzioni di batteria litio IATA disponibile all'indirizzo[http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Dopo aver esaminato questi avvisi sulla sicurezza, i passaggi successivi sono per decomprimere, togliere e cablare il vostro dispositivo.

## Passaggi successivi

- Per un dispositivo 8100, vedere [Installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).

- Per un dispositivo 8600, vedere [Installare il dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

<!---HONumber=AcomDC_0824_2016-->