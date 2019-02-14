---
title: Requisiti per l'uso e la visualizzazione delle API di ricerca Bing
titleSuffix: Azure Cognitive Services
description: Requisiti per visualizzare nelle proprie applicazioni i risultati della ricerca delle API di ricerca Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 25636cad514df18a289c9c81ea95bc53e6ec203c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861324"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Requisiti per l'uso e la visualizzazione delle API di ricerca Bing

Questi requisiti per l'uso e la visualizzazione si applicano a qualsiasi implementazione del contenuto e delle informazioni associate eseguita dalle API di ricerca Bing elencate di seguito, inclusi metadati, relazioni e altri segnali.

- Ricerca personalizzata Bing
- Ricerca entità Bing
- Ricerca immagini Bing
- Ricerca notizie Bing
- Ricerca video Bing
- Ricerca visiva Bing
- Ricerca Web Bing
- Controllo ortografico Bing
- Suggerimenti automatici Bing

## <a name="definitions"></a>Definizioni


|Termine  |DESCRIZIONE  |
|---------|---------|
|Risposta     | Una categoria di risultati restituiti in una risposta. Una risposta dell'API Ricerca Web Bing può ad esempio includere riscontri delle categorie pagine Web, immagini, video e notizie. |
|Risposta     | Tutti i riscontri e i dati associati ricevuti in risposta a una singola chiamata a un'API di ricerca. |
|Risultato    | Un'informazione contenuta in un riscontro. Ad esempio, il set di dati collegato a un singolo articolo di notizie rappresenta un risultato in un riscontro relativo a notizie. |
|API di ricerca    | L'insieme delle API di ricerca Bing, che include Ricerca personalizzata, Ricerca entità, Ricerca immagini, Ricerca notizie, Ricerca video, Ricerca visiva, Ricerca aziende locali e Ricerca Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Restrizioni delle API Controllo ortografico Bing e Suggerimenti automatici Bing

Non:

- Copiare, archiviare o memorizzare nella cache i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing.
- Usare i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing come parte di qualsiasi attività di apprendimento automatico o di un'attività algoritmica simile. Non usare questi dati per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

## <a name="bing-search-apis"></a>API di ricerca Bing

> [!NOTE]
> I requisiti in questa sezione si applicano solo alle API di ricerca. Sono quindi escluse le API Controllo ortografico Bing e Suggerimenti automatici Bing. 

### <a name="internet-search-experience-requirements"></a>Requisiti per le esperienze di ricerca Internet

Tutti i dati restituiti nelle risposte possono essere usati solo nelle esperienze di ricerca Internet. In un'esperienza di ricerca Internet, il contenuto visualizzato deve: 

- Essere pertinente e rispondere alla query diretta dell'utente finale o a un'altra indicazione dell'interesse e dell'intento di ricerca dell'utente, ad esempio a una query di ricerca indicata dall'utente. 

- Consentire agli utenti di trovare le origini dati della risposta e accedervi. Ad esempio, fornire collegamenti selezionabili da collegamenti ipertestuali nella risposta.

- Includere più risultati tra cui l'utente può scegliere. 

- Trovarsi in una posizione che consenta agli utenti di eseguire una ricerca.

- Includere un'indicazione visibile che il contenuto sia il risultato di una ricerca su Internet. Ad esempio, un'indicazione che il contenuto proviene "dal Web".

- Includere qualsiasi altra misura idonea ad assicurare che i dati ottenuti dalle API di ricerca Bing non violino norme vigenti o diritti di terze parti. Rivolgersi ai consulenti legali per determinare le misure appropriate.

L'unica eccezione ai requisiti delle esperienze di ricerca Internet riguarda l'individuazione degli URL, come descritto più avanti in questo articolo. 

### <a name="restrictions"></a>Restrizioni

Non:

- Copiare, archiviare o memorizzare nella cache i dati delle risposte, ad eccezione della conservazione nei limiti consentiti dalla [continuità del servizio](#continuity-of-service). 

- Usare i dati ricevuti dalle API di ricerca come parte di qualsiasi attività di apprendimento automatico o attività algoritmica simile. Non usare questi dati per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

- Modificare il contenuto dei risultati, ad eccezione di attività di riformattazione che non violino gli altri requisiti, se non richiesto dalla legge o concordato con Microsoft. 

- Omettere le informazioni di attribuzione e gli URL associati al contenuto dei risultati.

- Modificare l'ordine (neanche tramite omissione) dei risultati visualizzati in un riscontro, in caso di ordinamento o classificazione dei dati, se non richiesto dalla legge o concordato con Microsoft. 

    > [!NOTE]
    > Questo requisito non si applica alla modifica dell'ordine implementata tramite il portale per l'API Ricerca personalizzata Bing.

- Visualizzare altri contenuti all'interno di qualsiasi parte di una risposta in un modo che possa indurre l'utente a ritenere che gli altri contenuti facciano parte della risposta. 

- Visualizzare annunci pubblicitari non forniti da Microsoft in qualsiasi pagina che visualizzi una parte della risposta. 

- Visualizzare annunci pubblicitari nelle pagine con risposte:
    - Restituite dalle API Ricerca immagini, Ricerca notizie, Ricerca video o Ricerca visiva Bing
    - Filtrate o limitate principalmente (o esclusivamente) in base a risultati con immagini, notizie e/o video o oggetti visivi.

### <a name="notices-and-branding"></a>Informative e marchio 
Eseguire queste operazioni:

- Includere in posizione prominente un collegamento ipertestuale funzionante all'[informativa sulla privacy Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), nelle vicinanze di qualsiasi punto nell'esperienza utente che offre all'utente la possibilità di immettere una query di ricerca. Assegnare l'etichetta **Informativa sulla privacy Microsoft** al collegamento ipertestuale.

- Visualizzare in posizione prominente il marchio Bing, coerentemente con le [linee guida per l'utilizzo del marchio Bing](https://go.microsoft.com/fwlink/?linkid=833278), nelle vicinanze di qualsiasi punto nell'esperienza utente che offre all'utente la possibilità di immettere una query di ricerca. Le informazioni di marchio devono indicare chiaramente all'utente che l'esperienza di ricerca Internet è offerta e gestita da Microsoft.

- È possibile attribuire ogni riposta (o parte di una risposta) visualizzata dalle API Bing Ricerca Web, Ricerca immagini, Ricerca notizie, Ricerca video e Ricerca visiva a Microsoft, se non diversamente specificato da Microsoft per iscritto per uso dell'utente. Questo aspetto è descritto nelle [linee guida per l'utilizzo del marchio Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Non:

- Attribuire risposte (o parti di risposte) visualizzate dall'API Ricerca personalizzata Bing a Microsoft, se non diversamente specificato da Microsoft per iscritto per un uso specifico dell'utente.

### <a name="transferring-responses"></a>Trasferimento delle risposte

Se si abilita un utente al trasferimento di una risposta da un'API di ricerca a un altro utente, ad esempio tramite un'app di messaggistica o post di social media, si applicano le condizioni seguenti: 

- Le risposte trasferite:
  - Devono essere costituite da contenuti invariati rispetto ai contenuti delle risposte visualizzate all'utente che esegue il trasferimento. Sono consentite modifiche di formattazione.
  - Non devono includere dati sotto forma di metadati.
  - Per le risposte dalle API Bing Ricerca Web, Ricerca immagini, Ricerca notizie, Ricerca video e Ricerca visiva, visualizzare un messaggio che indica che la risposta è stata ottenuta tramite un'esperienza di ricerca Internet con tecnologia Bing. Ad esempio, è possibile visualizzare un messaggio come "Con tecnologia Bing" o "Altre informazioni su questa immagine in Bing" oppure è possibile usare il logo di Bing.
  - Per le risposte dall'API Ricerca personalizzata Bing, visualizzare un messaggio che indica che la risposta è stata ottenuta tramite un'esperienza di ricerca Internet. Ad esempio, è possibile visualizzare un messaggio come "Altre informazioni su questo risultato di ricerca."
  - Visualizzare in posizione prominente la query completa usata per generare la risposta.
  - Includere un collegamento o un'attribuzione simile ben visibile per l'origine della risposta, direttamente o tramite il motore di ricerca, (bing.com, m.bing.com o il servizio di ricerca personalizzato, come applicabile).
- Non è possibile automatizzare il trasferimento delle risposte. Il trasferimento deve essere avviato da un'azione utente che evidenzi chiaramente l'intenzione di trasferire una risposta.
- Un utente può essere abilitato solo al trasferimento delle risposte visualizzate in risposta alla query dell'utente che esegue il trasferimento.

### <a name="continuity-of-service"></a>Continuità del servizio 

Non copiare, archiviare o memorizzare nella cache i dati delle risposte delle API di ricerca. Per consentire tuttavia la continuità dell'accesso al servizio e del rendering dei dati, è possibile conservare i risultati esclusivamente alle condizioni seguenti:

#### <a name="device"></a>Dispositivo

È possibile abilitare un utente alla conservazione dei risultati in un dispositivo per il periodo più breve tra i seguenti: (i) 24 ore dal momento della query o (ii) fino a quando un utente non invia un'altra query per ottenere risultati aggiornati, purché i risultati conservati vengano usati esclusivamente:

- Per consentire all'utente di accedere ai risultati restituiti in precedenza a tale utente finale nel dispositivo, ad esempio in caso di interruzione del servizio.
- Per archiviare i risultati restituiti per la query proattiva personalizzata in anticipazione delle esigenze dell'utente in base ai segnali dell'utente stesso, ad esempio in caso di interruzione prevista del servizio.

#### <a name="server"></a>Server

È possibile conservare i risultati specifici di un singolo utente in modo sicuro in un server sotto il proprio controllo e visualizzare i risultati conservati esclusivamente:

- Per consentire all'utente di accedere a un report cronologico dei risultati restituiti in precedenza a tale utente nella soluzione. I risultati potrebbero non essere (i) conservati per più di 21 giorni dal momento della query iniziale dell'utente finale e (ii) visualizzati in risposta a query nuove o ripetute di un utente.
- Per archiviare i risultati restituiti per la query proattiva personalizzata in anticipazione delle esigenze dell'utente in base ai segnali dell'utente stesso. È possibile archiviare questi risultati per il periodo più breve tra i seguenti (i) 24 ore dal momento della query o (ii) fino a quando un utente non invia un'altra query per ottenere risultati aggiornati.

Ogni volta che vengono conservati, i risultati per un utente specifico non possono essere mescolati con i risultati di un altro utente. Vale a dire, i risultati di ogni utente devono essere conservati e recapitati separatamente.

### <a name="general"></a>Generale 

Per tutte le presentazioni dei risultati conservati:

- Includere un'indicazione chiara e visibile della data/ora di invio della query.
- Presentare all'utente un pulsante o uno strumento simile per ripetere la query e ottenere risultati aggiornati. 
- Mantenere il marchio Bing nella presentazione dei risultati.
- Eliminare (e aggiornare con una nuova query, se necessario) i risultati archiviati entro intervalli di tempo specificati.

### <a name="non-display-url-discovery"></a>Individuazione di URL non destinati alla visualizzazione 

È possibile usare le risposte di ricerca in un'esperienza di ricerca non Internet al solo scopo di individuare gli URL delle fonti di informazioni che rispondono a una query dell'utente o del cliente. È possibile copiare questi URL in un report o in una risposta simile forniti:

- Solo a tale utente o un cliente, in risposta alla query.
- Solo se includono contenuto aggiuntivo di valore significativo, rilevante per la query.

Le sezioni precedenti dei requisiti per l'uso e la visualizzazione delle API di ricerca non si applicano a questo uso non destinato alla visualizzazione, con le eccezioni seguenti: 

- Non memorizzare nella cache, copiare o archiviare dati o contenuti derivanti dalla risposta della ricerca, fatta eccezione per la copia limitata di URL indicata in precedenza.
- Assicurarsi che l'uso dei dati, inclusi gli URL, ricevuti dalle API di ricerca non violi norme vigenti o diritti di terze parti.
- Non usare i dati (inclusi gli URL) ricevuti dalle API di ricerca nell'ambito di indici di ricerca o attività di apprendimento automatico o attività algoritmiche simili. Non usare questi dati per il training, la valutazione o il miglioramento di servizi offerti dalla propria azienda o da terzi.

## <a name="gdpr-compliance"></a>Conformità al Regolamento generale sulla protezione dei dati (GDPR)  

Con riferimento a tutti i dati personali soggetti al Regolamento generale sulla protezione dei dati (GDPR) ed elaborati in relazione alle chiamate alle API di ricerca, l'API Controllo ortografico Bing o l'API Suggerimenti automatici Bing, l'utente prende atto di essere oltre a Microsoft titolare del trattamento dei dati indipendente in base al regolamento GDPR. L'utente è responsabile in modo indipendente della conformità al regolamento GDPR.  

