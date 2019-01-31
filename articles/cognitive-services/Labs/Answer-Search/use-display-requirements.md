---
title: Requisiti per l'uso e la visualizzazione - Project Answer Search
titlesuffix: Azure Cognitive Services
description: Requisiti per l'uso e la visualizzazione per l'endpoint di Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 521deef5c09d80eb37a26de384704fcfe540b87e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225109"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Requisiti per l'uso e la visualizzazione di Project Answer Search

I requisiti per l'uso e la visualizzazione si applicano a qualsiasi implementazione del contenuto e alle informazioni associate, ad esempio relazioni, metadati e altri segnali, disponibili tramite chiamate alle API Ricerca informazioni Bing, Ricerca personalizzata Bing, Ricerca entità, Ricerca immagini, Ricerca notizie, Ricerca video, Ricerca visiva, Ricerca Web, Controllo ortografico e Suggerimenti automatici. I dettagli di implementazione correlati a questi requisiti sono disponibili nella documentazione relativa alle funzionalità e ai risultati specifici.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. API Controllo ortografico Bing e API Suggerimenti automatici Bing

Non:

- copiare, archiviare oppure memorizzare nella cache i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing
- usare i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing nell'ambito di attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

## <a name="2-definitions"></a>2. Definizioni

- "riscontro" si riferisce a una categoria di risultati restituiti in una risposta. Una risposta dell'API Ricerca Web Bing può ad esempio includere riscontri delle categorie pagine Web, immagini, video e notizie;
- "risposta" indica tutti i riscontri e i dati associati ricevuti in risposta a una singola chiamata a un'API di ricerca;
- "risultato" si riferisce a un'informazione contenuta in un riscontro. Ad esempio, il set di dati collegato a un singolo articolo di notizie rappresenta un risultato in un riscontro relativo a notizie.
- Per "API di ricerca" si intendono collettivamente le API Ricerca personalizzata Bing, Ricerca entità, Ricerca immagini, Ricerca notizie, Ricerca video, Ricerca visiva e Ricerca Web. 


## <a name="3-search-apis"></a>3. API di ricerca

Alle API di ricerca si applicano i requisiti descritti in questa sezione.

**A. Esperienza di ricerca Internet.** Tutti i dati restituiti nelle risposte possono essere usati solo nelle esperienze di ricerca Internet. In un'esperienza di ricerca Internet, il contenuto visualizzato: 
- È pertinente e risponde alla query diretta dell'utente finale o a un'altra indicazione dell'interesse e dell'intento di ricerca dell'utente, ad esempio a una query di ricerca indicata dall'utente. 
- Consente agli utenti di trovare e passare alle origini dei dati, ad esempio gli URL vengono implementati come collegamenti ipertestuali, quindi il contenuto o l'attribuzione è un collegamento selezionabile visualizzato con i dati; oppure, se si usa l'API Ricerca entità Bing, è collegato visibilmente all'URL bing.com fornito nella risposta, che consente all'utente di passare ai risultati della ricerca per la query corrispondente in bing.com.
- Include più risultati selezionabili dall'utente finale, ad esempio vengono visualizzati più risultati del riscontro relativo alle notizie oppure tutti i risultati se sono pochi. 
- È limitato a una quantità idonea a soddisfare lo scopo della ricerca, ad esempio le anteprime delle immagini sono in miniatura rispetto alla visualizzazione dell'utente. 
- Indica in modo visibile all'utente finale che il contenuto è rappresentato da risultati della ricerca Internet, ad esempio visualizza un'istruzione che indica che il contenuto "proviene dal Web".
- Include qualsiasi altra combinazione di misure idonee ad assicurare che l'uso dei dati ricevuti dalle API di ricerca non violi norme vigenti o diritti di terze parti, ad esempio se ci si basa su una licenza Creative Commons, si rispettano le condizioni di licenza applicabili. Rivolgersi ai consulenti legali per determinare le misure appropriate.
L'unica eccezione ai requisiti dell'esperienza di ricerca Internet è costituita dall'individuazione di URL come descritto nella sezione 3E "Individuazione di URL non destinati alla visualizzazione" di seguito. 

**B. Restrizioni.** Non:

- copiare, archiviare o memorizzare nella cache i dati delle risposte, ad eccezione della conservazione consentita descritta nella sezione "Continuità del servizio" di seguito; 
- usare i dati ricevuti dalle API di ricerca nell'ambito di attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi;
- modificare il contenuto dei risultati, ad eccezione di attività di riformattazione che non violino gli altri requisiti, se non richiesto dalla legge o concordato con Microsoft; 
- omettere l'attribuzione e gli URL associati al contenuto dei risultati;
- modificare l'ordine (neanche tramite omissione) dei risultati visualizzati in un riscontro in presenza di un ordine o di una classificazione, se non richiesto dalla legge o concordato con Microsoft (per l'API Ricerca personalizzata Bing, questa regola non si applica alla modifica dell'ordine implementata tramite il portale customsearch.ai.);
- visualizzare altri contenuti all'interno di una parte della risposta in un modo che possa indurre l'utente finale a ritenere che gli altri contenuti facciano parte della risposta; 
- visualizzare annunci pubblicitari non forniti da Microsoft in qualsiasi pagina che visualizzi una parte della risposta; visualizzare annunci pubblicitari con risposte (i) dalle API Bing Ricerca immagini, Ricerca notizie o Ricerca video; o (ii) filtrate o limitate principalmente (o esclusivamente) ai risultati immagini, notizie e/o video.

**C. Informative e marchio.** 

- Includere in posizione prominente un collegamento ipertestuale funzionante all'informativa sulla privacy Microsoft, disponibile in https://go.microsoft.com/fwlink/?LinkId=521839, nelle vicinanze di qualsiasi punto nell'esperienza utente che offre all'utente la possibilità di immettere una query di ricerca. Assegnare l'etichetta "Informativa sulla privacy Microsoft" al collegamento ipertestuale.
- Visualizzare in posizione prominente il marchio Bing, coerentemente con le linee guida disponibili https://go.microsoft.com/fwlink/?linkid=833278, nelle vicinanze di qualsiasi punto nell'esperienza utente che offre all'utente la possibilità di immettere una query di ricerca.  Le informazioni di marchio devono segnalare chiaramente all'utente che l'esperienza di ricerca Internet è offerta e gestita da Microsoft.
- È possibile attribuire ogni riposta (o parte di una risposta) visualizzata dalle API Bing Ricerca Web, Ricerca immagini, Ricerca notizie e Ricerca video a Microsoft come descritto in https://go.microsoft.com/fwlink/?linkid=833278, se non diversamente specificato da Microsoft per iscritto per uso dell'utente. 
- Non attribuire risposte (o parti di risposte) visualizzate dall'API Ricerca personalizzata Bing a Microsoft, se non diversamente specificato da Microsoft per iscritto per uso dell'utente.


**D. Trasferimento delle risposte.** Se si abilita un utente al trasferimento di una risposta da un'API di ricerca a un altro utente, ad esempio tramite un'app di messaggistica o post di social media, si applicano le condizioni seguenti: 
- Le risposte trasferite:
  - Devono essere costituite da contenuti invariati rispetto ai contenuti delle risposte visualizzate all'utente che esegue il trasferimento, pur essendo consentite modifiche alla formattazione;
  - Non devono includere dati sotto forma di metadati;
  - Per le risposte dalle API Bing Ricerca Web, Ricerca immagini, Ricerca notizie e Ricerca video, è possibile visualizzare un messaggio che indichi che la risposta è stata ottenuta tramite un'esperienza di ricerca Internet con tecnologia Bing (ad esempio, "Con tecnologia Bing", "Altre informazioni su questa immagine in Bing" o usando il logo di Bing);
  - Per le risposte dall'API Ricerca personalizzata Bing, è possibile visualizzare un messaggio che indichi che la risposta è stata ottenuta tramite un'esperienza di ricerca Internet, ad esempio "Altre informazioni su questo risultato di ricerca";
  - Visualizzare chiaramente la query completa usata per generare la risposta; e
  - Includere un collegamento o un'attribuzione simile ben visibile per l'origine della risposta, direttamente o tramite il motore di ricerca, (bing.com, m.bing.com o il servizio di ricerca personalizzato, come applicabile).
- Non è possibile automatizzare il trasferimento delle risposte. Il trasferimento deve essere avviato da un'azione utente che evidenzi chiaramente l'intenzione di trasferire una risposta.
- Un utente può essere abilitato solo al trasferimento delle risposte visualizzate in risposta alla query dell'utente che esegue il trasferimento.

**E. Continuità del servizio.** Non copiare, archiviare o memorizzare nella cache i dati delle risposte delle API di ricerca. Per consentire tuttavia la continuità dell'accesso al servizio e del rendering dei dati, è possibile conservare i risultati esclusivamente alle condizioni seguenti:

**Dispositivo.** È possibile abilitare un utente finale alla conservazione dei dati per il periodo più breve tra i seguenti: (i) 24 ore dal momento della query o (ii) fino a quando un utente non invia un'altra query per ottenere risultati aggiornati, purché i risultati conservati vengano usati esclusivamente:

- per consentire all'utente finale di accedere ai risultati restituiti in precedenza per tale utente finale nel dispositivo, ad esempio in caso di interruzione del servizio; oppure
- per archiviare i risultati restituiti per la query proattiva personalizzata in anticipazione delle esigenze dell'utente finale in base ai segnali dell'utente finale stesso, ad esempio in caso di interruzione prevista del servizio.

**Server.** È possibile conservare i risultati specifici di un singolo utente finale in modo sicuro in un server nel quale si controllano e visualizzano i risultati conservati esclusivamente:

- per consentire all'utente finale di accedere a un report cronologico dei risultati restituiti in precedenza a tale utente nella soluzione, purché i risultati non vengano (i) conservati per più di 21 giorni dal momento della query iniziale dell'utente finale e (ii) visualizzati in risposta a una query nuova o ripetuta di un utente finale; oppure
- per archiviare i risultati restituiti per la query attiva personalizzata in previsione delle esigenze dell'utente finale in base ai segnali dell'utente finale stesso per il periodo più breve tra i seguenti: (i) 24 ore dal momento della query oppure (ii) finché un utente finale non invia un'altra query per ottenere risultati aggiornati.

Quando vengono conservati, i risultati per un utente specifico non possono essere combinati con i risultati per un altro utente. In altre parole, i risultati di ogni utente devono essere conservati e inviati separatamente.

**Generale.** Per tutte le presentazioni dei risultati conservati:

- includere un'indicazione chiara e visibile della data/ora di invio della query,
- presentare all'utente un pulsante o uno strumento simile per ripetere la query e ottenere risultati aggiornati, 
- mantenere la personalizzazione di Bing nella presentazione dei risultati, e
- eliminare (e aggiornare con una nuova query, se necessario) i risultati archiviati all'interno di intervalli di tempo specificati.

**F. Individuazione di URL non destinati alla visualizzazione.** È possibile usare le risposte di ricerca in un'esperienza di ricerca non Internet al solo scopo di individuare gli URL delle fonti di informazioni che rispondono a una query dell'utente o del cliente. È possibile copiare questi URL in un report o una risposta simile (i) disponibile solo a tale utente o cliente in risposta alla query e (ii) che include altro contenuto significativo e utile pertinente per la query. I requisiti indicati nelle sezioni da 3A a 3E di questi requisiti di uso e visualizzazione non sono applicabili a questo uso non destinato alla visualizzazione, ad eccezione di quanto segue: 

- Non è consentito memorizzare nella cache, copiare o archiviare dati o contenuti derivanti dalla risposta della ricerca, fatta eccezione per la copia limitata di URL indicata in precedenza.
- Assicurarsi che l'uso dei dati, inclusi gli URL, ricevuti dalle API di ricerca non violi norme vigenti o diritti di terze parti.
- Non è consentito usare i dati (inclusi gli URL) ricevuti dalle API di ricerca nell'ambito di indici di ricerca o attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi offerti dalla propria azienda o da terzi.

## <a name="next-steps"></a>Passaggi successivi
[Panoramica di Answer Search](overview.md)
