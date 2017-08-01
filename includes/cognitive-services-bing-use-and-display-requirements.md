2017-07-07

Questi requisiti di uso e visualizzazione si applicano all'implementazione del contenuto e alle informazioni associate (ad esempio relazioni, metadati e altri segnali) disponibili tramite chiamate alle API Ricerca Web Bing, Ricerca immagini, Ricerca video, Ricerca notizie e API Ricerca entità, Controllo ortografico Bing e Suggerimenti automatici Bing. I dettagli di implementazione correlati a questi requisiti sono disponibili nella documentazione relativa alle funzionalità e ai risultati specifici.

## <a name="1-bing-spell-check-api-and-bing-autosuggest-api"></a>1. API CONTROLLO ORTOGRAFICO BING e API SUGGERIMENTI AUTOMATICI BING.

Non si deve:

• copiare, archiviare oppure memorizzare nella cache i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing, oppure 
  
• usare i dati ricevuti dalle API Controllo ortografico Bing o Suggerimenti automatici Bing nell'ambito di attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

## <a name="2-search-apis"></a>2. API DI RICERCA

I requisiti indicati nelle sezioni da 3 a 7 si applicano alle API Ricerca Web Bing, Ricerca immagini, Ricerca video, Ricerca notizie e API Ricerca entità Bing, ovvero alle "**API di ricerca**". Alle sezioni da 3 a 7 si applicano le definizioni seguenti:

• "riscontro" si riferisce a una categoria di risultati restituiti in una risposta. Una risposta dell'API Ricerca Web Bing può ad esempio includere riscontri delle categorie pagine Web, immagini, video e notizie;  
  
• "risposta" indica tutti i riscontri e i dati associati ricevuti in risposta a una singola chiamata a un'API di ricerca;  
  
• "risultato" si riferisce a un'informazione contenuta in un riscontro. Ad esempio, il set di dati collegato a un singolo articolo di notizie rappresenta un risultato in un riscontro relativo a notizie.

## <a name="3-bing-entity-search-api"></a>3. API RICERCA ENTITÀ BING

I dati restituiti dall'API Ricerca entità Bing devono essere:

• riservati agli utenti che si trovano negli Stati Uniti d'America;  

• visualizzati solo in risposta all'interesse di ricerca dell'utente finale per l'entità, ad esempio query indicata dall'utente;  
  
• accompagnati da un collegamento visibile all'URL bing.com fornito nella risposta, che consente all'utente di passare ai risultati della ricerca per la query corrispondente in bing.com;  
  
• usati osservando qualsiasi altra misura atta ad assicurare che l'uso dei dati ricevuti dall'API Ricerca entità Bing non violi norme vigenti o diritti di terze parti. Ad esempio,  
  
  - assicurare che le anteprime delle immagini siano in miniatura rispetto alla visualizzazione dell'utente, oppure  
    
  - se si usa una licenza Creative Commons, assicurare la corretta attribuzione di licenza e testo.

Non si deve:

• copiare, archiviare oppure memorizzare nella cache i dati ricevuti dall'API Ricerca Bing, oppure  
  
• usare i dati ricevuti dall'API Ricerca entità Bing nell'ambito di attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

## <a name="4-bing-web-search-image-search-news-search-and-video-search-apis"></a>4. API RICERCA WEB BING, RICERCA IMMAGINI, RICERCA NOTIZIE e RICERCA VIDEO:

**Esperienza di ricerca Internet.** Tutti i dati restituiti nelle risposte dalle API Ricerca Web, Ricerca immagini, Ricerca notizie e Ricerca video possono essere usati solo nelle esperienze di ricerca Internet. In un'esperienza di ricerca Internet, il contenuto visualizzato:

• è pertinente e risponde alla query diretta dell'utente finale o a un'altra indicazione dell'interesse e dell'intento di ricerca dell'utente, ad esempio a una query di ricerca indicata dall'utente;  
  
• consente agli utenti di trovare e passare alle origini dei dati, ad esempio gli URL vengono implementati come collegamenti ipertestuali, quindi il contenuto o l'attribuzione è un collegamento selezionabile visualizzato con i dati;  
  
• include più risultati selezionabili dall'utente finale, ad esempio vengono visualizzati più risultati del riscontro relativo alle notizie oppure tutti i risultati se sono pochi;  
  
• è limitato a una quantità idonea a soddisfare lo scopo della ricerca, ad esempio le anteprime delle immagini sono in miniatura rispetto alla visualizzazione dell'utente;  
  
• indica in modo visibile all'utente finale che il contenuto è rappresentato da risultati della ricerca Internet, ad esempio visualizza un'istruzione che indica che il contenuto "proviene dal web"; e  
  
• include qualsiasi altra combinazione di misure idonee ad assicurare che l'uso dei dati ricevuti dalle API di ricerca non violi norme vigenti o diritti di terze parti. Contattare i consulenti legali per determinare le misure appropriate.

L'unica eccezione ai requisiti dell'esperienza di ricerca Internet è costituita dall'individuazione di URL come descritto nella sezione 7 "Individuazione di URL non destinati alla visualizzazione" di seguito.

**Generale.** Non si deve: 

• copiare, archiviare o memorizzare nella cache i dati delle risposte, ad eccezione della conservazione consentita descritta nella sezione "Continuità del servizio" di seguito;  
  
• modificare il contenuto dei risultati, ad eccezione di attività di riformattazione che non violino gli altri requisiti;  
  
• omettere l'attribuzione e gli URL associati al contenuto dei risultati;  
  
• modificare l'ordine (neanche tramite omissione) dei risultati visualizzati in un riscontro in presenza di un ordine o di una classificazione;  
  
• visualizzare altri contenuti all'interno di una parte della risposta in un modo che possa indurre l'utente finale a ritenere che gli altri contenuti facciano parte della risposta;  
  
• visualizzare annunci pubblicitari non forniti da Microsoft in qualsiasi pagina che visualizzi una parte della risposta;  
  
• usare i dati ricevuti dalle API di ricerca nell'ambito di attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi nuovi o esistenti offerti dalla propria azienda o da terzi.

**Annunci pubblicitari.** Gli annunci pubblicitari, forniti da Microsoft o altri provider, non devono essere visualizzati con risposte (i) ottenute dalle API di ricerca di immagini, notizie o video; oppure (ii) filtrate o limitate principalmente (o esclusivamente) ai risultati di immagini, notizie e/o video restituiti da altre API di ricerca.

**Personalizzazione.** Ogni risposta (o parte di essa) visualizzata può essere attribuita a Microsoft come descritto in https://go.microsoft.com/fwlink/?linkid=833278, salvo diversa indicazione di Microsoft per l'uso specifico.

## <a name="5-transferring-responses"></a>5. Trasferimento delle risposte.

Se si abilita un utente al trasferimento di una risposta da un'API di ricerca a un altro utente, ad esempio tramite un'app di messaggistica o post di social media, si applicano le condizioni seguenti:

• Le risposte trasferite:  
  
  - Devono essere costituite da contenuti invariati rispetto ai contenuti delle risposte visualizzate all'utente che esegue il trasferimento, pur essendo consentite modifiche alla formattazione;  
    
  - Non devono includere dati sotto forma di metadati;  
    
  - Devono visualizzare un testo che indichi che la risposta è stata ottenuta tramite un'esperienza di ricerca Internet offerta da Bing, ad esempio "Con tecnologia Bing", "Altre informazioni su questa immagine sono disponibili in Bing", oppure "Scopri di più su questa immagine in Bing". In alternativa è possibile usare il logo Bing;  
    
  - Visualizzare chiaramente la query completa usata per generare la risposta; e  
    
  - Includere un collegamento o un'attribuzione simile ben visibile per l'origine della risposta, direttamente o tramite bing.com o m.bing.com.  
    
• Non è possibile automatizzare il trasferimento delle risposte. Il trasferimento deve essere avviato da un'azione utente che evidenzi chiaramente l'intenzione di trasferire una risposta.  
  
• Un utente può essere abilitato solo al trasferimento delle risposte ottenute dalle proprie query.

## <a name="6-continuity-of-service"></a>6. Continuità del servizio.

Non si devono copiare, archiviare o memorizzare nella cache i dati delle risposte delle API di ricerca. Per consentire tuttavia la continuità dell'accesso al servizio e del rendering dei dati, è possibile conservare i risultati esclusivamente alle condizioni seguenti:

**Dispositivo.** È possibile abilitare un utente finale alla conservazione dei dati per il periodo più breve tra i seguenti: (i) 24 ore dal momento della query o (ii) fino a quando un utente non invia un'altra query per ottenere risultati aggiornati, purché i risultati conservati vengano usati esclusivamente:

• per consentire all'utente finale di accedere ai risultati restituiti in precedenza per tale utente finale nel dispositivo, ad esempio in caso di interruzione del servizio; oppure  
  
• per archiviare i risultati restituiti per la query attiva personalizzata in previsione delle esigenze dell'utente finale in base ai segnali dell'utente finale stesso, ad esempio in caso di interruzione prevista del servizio.

**Server.** È possibile conservare i risultati specifici di un singolo utente finale in modo sicuro in un server nel quale si controllano e visualizzano i risultati conservati esclusivamente:

• per consentire all'utente finale di accedere a un report cronologico dei risultati restituiti in precedenza a tale utente nella soluzione, purché i risultati non vengano (i) conservati per più di 21 giorni dal momento della query iniziale dell'utente finale e (ii) visualizzati in risposta a una query nuova o ripetuta di un utente finale; oppure  
  
• per archiviare i risultati restituiti per la query attiva personalizzata in previsione delle esigenze dell'utente finale in base ai segnali dell'utente finale stesso per il periodo più breve tra i seguenti: (i) 24 ore dal momento della query oppure (ii) finché un utente finale non invia un'altra query per ottenere risultati aggiornati.

Quando vengono conservati, i risultati per un utente specifico non possono essere combinati con i risultati per un altro utente. In altre parole, i risultati di ogni utente devono essere conservati e inviati separatamente.

**Generale.** Per tutte le presentazioni dei risultati conservati è necessario:

• includere un'indicazione chiara e visibile della data/ora di invio della query,  
  
• presentare all'utente un pulsante o uno strumento simile per ripetere la query e ottenere risultati aggiornati,  
  
• mantenere la personalizzazione di Bing nella presentazione dei risultati, e  
  
• eliminare (e aggiornare con una nuova query, se necessario) i risultati archiviati all'interno di intervalli di tempo specificati.

## <a name="7-non-display-url-discovery"></a>7. Individuazione di URL non destinati alla visualizzazione.

È possibile usare le risposte di ricerca in un'esperienza di ricerca non Internet al solo scopo di individuare gli URL delle fonti di informazioni che rispondono a una query dell'utente o del cliente. È possibile copiare questi URL in un report o una risposta simile (i) disponibile solo a tale utente o cliente in risposta a una particolare query e (ii) che include altro contenuto significativo e utile pertinente per la query. I requisiti indicati nelle sezioni da 2 a 6 di questi requisiti di uso e visualizzazione non sono applicabili a questo uso non destinato alla visualizzazione, ad eccezione di quanto segue: 

• Non è consentito memorizzare nella cache, copiare o archiviare dati o contenuti derivanti dalla risposta della ricerca, fatta eccezione per la copia limitata di URL indicata in precedenza;  
  
• È necessario assicurare che l'uso dei dati, inclusi gli URL, ricevuti dalle API di ricerca non violi norme vigenti o diritti di terze parti; e  
  
• Non è consentito usare i dati (inclusi gli URL) ricevuti dalle API di ricerca nell'ambito di indici di ricerca o attività di Machine Learning o attività algoritmiche simili per il training, la valutazione o il miglioramento di servizi offerti dalla propria azienda o da terzi.

