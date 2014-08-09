<properties  linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Step 1: Overview" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 1: Overview" metaKeywords="Azure tutorial, email list service app, email service architecture, Azure tutorial overview, Azure multi-tier, Azure storage, Azure blobs, Azure tables, Azure queues" description="Learn about the five-part multi-tier Azure web application tutorial." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Multi-tier ASP.NET MVC Web Site Tutorial - Step 1: Overview" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Applicazione Web ASP.NET multilivello con tabelle, code e BLOB di Archiviazione di Azure - 1 di 5

Questa serie di esercitazioni mostra come creare un'applicazione Web ASP.NET MVC multilivello che utilizza tabelle, code e BLOB di Archiviazione di Azure e come distribuire l'applicazione a un servizio cloud di Azure. Nelle esercitazioni si presuppone che l'utente non abbia mai utilizzato Azure. Dopo aver completato la serie, l'utente sarà in grado di creare un'applicazione Web basata sui dati scalabile e resiliente e di distribuirla nel cloud.

Questo contenuto è disponibile come e-book gratuito nella [TechNet E-Book Gallery][1].

<h2><a name="whatyoulllearn"></a><span  class="short-header">Contenuto delle esercitazioni</span>Contenuto delle esercitazioni</h2>


In questa serie di esercitazioni si apprenderà come:

* Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
* Creare un progetto cloud di Visual Studio con un ruolo Web ASP.NET MVC
  e due ruoli di lavoro.
* Pubblicare il progetto cloud in un servizio cloud di Azure.
* Pubblicare, se si preferisce, il progetto MVC su un sito Web Azure
  utilizzando tuttavia i ruoli di lavoro in un servizio cloud.
* Utilizzare il servizio di archiviazione di accodamento di Azure per la
  comunicazione tra livelli o tra ruoli di lavoro.
* Utilizzare il servizio di archiviazione tabelle di Azure come archivio
  dati altamente scalabile per dati non relazionali strutturati.
* Utilizzare il servizio BLOB di Azure per archiviare file nel cloud.
* Visualizzare e modificare tabelle, code e BLOB di Azure utilizzando
  Visual Studio o Esplora archivi Azure.
* Utilizzare SendGrid per inviare messaggi di posta elettronica.
* Configurare la traccia e visualizzare i dati traccia.
* Scalare un'applicazione aumentando il numero di istanze del ruolo di
  lavoro.

<h2><a name="frontendoverview"></a><span  class="short-header">Panoramica del front-end</span>Panoramica del front-end</h2>


L'applicazione che verrà compilata è un servizio di elenchi di indirizzi di posta elettronica. Il front-end dell'applicazione multilivello include le pagine Web che gli amministratori del servizio utilizzano per gestire gli elenchi di indirizzi di posta elettronica.

![Pagina di indice della lista di
distribuzione](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png)

![Pagina di indice dei
sottoscrittori](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png)

Esiste inoltre un set di pagine utilizzato dagli amministratori per creare messaggi da inviare a un elenco di indirizzi di posta elettronica.

![Pagina di indice dei
messaggi](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png)

![Pagina di creazione del
messaggio](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png)

I clienti del servizio sono società che danno ai propri clienti la possibilità di iscriversi a una lista di distribuzione sul sito Web del cliente. Un amministratore, ad esempio, imposta un elenco per gli annunci del dipartimento di storia della Contoso University. Quando uno studente interessato agli annunci del dipartimento di storia fa clic su un collegamento nel sito Web della Contoso University, la Contoso University esegue una chiamata del servizio Web all'applicazione Azure Email Service. Il metodo di servizio genera l'invio di un messaggio di posta elettronica al cliente. Tale messaggio contiene un collegamento ipertestuale e, quando il destinatario fa clic sul collegamento, viene visualizzata una pagina di benvenuto nell'elenco di annunci del dipartimento di storia.

![Messaggio di posta elettronica di
conferma](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png)

![Pagina di benvenuto
nell'elenco](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png)

Ogni messaggio di posta elettronica inviato dal servizio (tranne la conferma della sottoscrizione) include un collegamento ipertestuale che può essere utilizzato per annullare la sottoscrizione. Se un destinatario fa clic sul collegamento, viene visualizzata una pagina Web in cui si chiede conferma dell'annullamento della sottoscrizione.

![Pagina di conferma dell'annullamento della
sottoscrizione](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png)

Se il destinatario fa clic sul pulsante **Confirm**, viene visualizzata una pagina che conferma che la persona è stata rimossa dall'elenco.

![Pagina di conferma dell'avvenuto annullamento della
sottoscrizione](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png)

<h2><a name="whyanemaillistapp"></a><span  class="short-header">Esercitazioni</span>Esercitazioni della serie</h2>


Di seguito è riportato un elenco delle esercitazioni con un riepilogo dei contenuti:

1.  **Introduzione all'applicazione Azure Email Service** (questa
    esercitazione). Panoramica dell'applicazione e della relativa
    architettura.
2.  [Configurazione e distribuzione dell'applicazione Azure Email
    Service](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).
    Come scaricare l'applicazione di esempio, configurarla, testarla
    localmente, distribuirla e testarla nel cloud.
3.  [Creazione del ruolo Web per l'applicazione Azure Email
    Service](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/).
    Come creare i componenti dell'applicazione MVC 4 dell'applicazione
    e testarli localmente.
4.  [Creazione del ruolo di lavoro A (utilità di pianificazione della
    posta elettronica) per l'applicazione Azure Email
    Service](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/).
    Come creare il componente back-end che crea gli elementi di lavoro
    della coda per l'invio di messaggi di posta elettronica e testarlo
    localmente.
5.  [Creazione del ruolo di lavoro B (mittente posta elettronica) per
    l'applicazione Azure Email
    Service](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
    Come creare il componente back-end che elabora gli elementi di
    lavoro della coda per l'invio di messaggi di posta elettronica e
    testarlo localmente.

Se si desidera scaricare l'applicazione e provarla, sono necessarie solo le prime due esercitazioni. Se si desidera visualizzare tutti i passaggi relativi alla compilazione da zero di un'applicazione come questa, dopo le prime due esercitazioni esaminare anche le ultime tre.

<h2><a name="whyanemaillistapp"></a><span  class="short-header">Perché questa app</span>Perché un'applicazione del servizio di elenchi di indirizzi di posta elettronica</h2>


Per questa applicazione di esempio è stato scelto un servizio di elenchi di indirizzi di posta elettronica perché è il tipo di applicazione che deve essere resiliente e scalabile, due funzionalità che la rendono particolarmente adatta per Azure.

### Resiliente

Se si verifica un errore del server durante l'invio di messaggi di posta elettronica a un elenco di grandi dimensioni, è necessario poter avviare facilmente e rapidamente un nuovo server e l'applicazione deve riprendere l'invio da dove era stato interrotto senza perdere o duplicare nessun messaggio. Un'istanza del ruolo di lavoro o Web dei servizi cloud di Azure (una macchina virtuale) viene automaticamente sostituita in caso di errore. Le code e le tabelle di Archiviazione di Azure consentono di implementare la comunicazione da server a server, che può proseguire anche in caso di errore senza perdere il lavoro.

### Scalabile

Un servizio di posta elettronica deve anche essere in grado di gestire picchi di carico di lavoro, poiché a volte si inviano messaggi di posta elettronica a piccoli elenchi e altre volte a elenchi molto grandi. In molti ambienti di hosting, è necessario acquistare e mantenere hardware sufficiente per gestire i picchi di carico di lavoro e pagare tutta la capacità per il 100% del tempo anche se la si utilizza solo per il 5% del tempo. Con Azure, si paga solo la capacità di elaborazione effettivamente necessaria solo per il tempo in cui è necessaria. Per una maggiore capacità, è sufficiente modificare un'impostazione di configurazione per aumentare il numero di server disponibili per l'elaborazione del carico di lavoro. Questa operazione può essere eseguita a livello di codice. È, ad esempio, possibile configurare l'applicazione in modo che, se gli elementi di lavoro in attesa nella coda superano un determinato numero, Azure avvii automaticamente ulteriori istanze del ruolo di lavoro che elabora tali elementi.

<h2><a name="backendoverview"></a><span  class="short-header">Panoramica del back-end</span>Panoramica del back-end</h2>


Il front-end archivia gli elenchi di indirizzi di posta elettronica e i messaggi da inviare a tali indirizzi nelle tabelle di Azure. Quando un amministratore pianifica l'invio di un messaggio, una riga di tabella contenente la data pianificata e altri dati, come ad esempio la riga dell'oggetto, viene aggiunta alla tabella `message`. Un ruolo di lavoro
(che verrà chiamato ruolo di lavoro A) analizza periodicamente la
tabella `message` per cercare i messaggi da inviare.

Quando il ruolo di lavoro A trova un messaggio da inviare, esegue le attività seguenti:

* Ottiene tutti gli indirizzi di posta elettronica nell'elenco di
  indirizzi e-mail di destinazione.
* Inserisce le informazioni necessarie per inviare ogni messaggio di
  posta elettronica nella tabella `message`.
* Crea un elemento di lavoro della coda per ogni messaggio di posta
  elettronica da inviare.

Un secondo ruolo di lavoro (ruolo di lavoro B) esegue il polling della coda per gli elementi di lavoro. Quando il ruolo di lavoro B trova un elemento di lavoro, elabora l'elemento inviando il messaggio di posta elettronica e quindi elimina l'elemento di lavoro dalla coda. Il seguente diagramma mostra queste relazioni.

![Elaborazione di messaggi di posta
elettronica](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png)

Nessun messaggio di posta elettronica viene perso se il ruolo di lavoro B ha esito negativo e deve essere riavviato, perché un elemento di lavoro della coda per un messaggio di posta elettronica non viene eliminato finché il messaggio non viene inviato. Il back-end implementa inoltre l'elaborazione della tabella che impedisce l'invio di più messaggi di posta elettronica nel caso in cui il ruolo di lavoro A abbia esito negativo e sia necessario riavviarlo. In tal caso, è possibile che vengano generati più elementi di lavoro della coda per un determinato indirizzo di posta elettronica di destinazione. Tuttavia per ogni indirizzo di posta elettronica di destinazione, una riga nella tabella
`message` registra se il messaggio è stato inviato. In base alla
tempistica di riavvio e di elaborazione dei messaggi di posta elettronica, il ruolo di lavoro A utilizza questa riga per evitare di creare un secondo elemento di lavoro della coda oppure il ruolo di lavoro B utilizza questa riga per evitare di inviare un secondo messaggio.

![Evitare messaggi di posta elettronica
duplicati](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png)

Il ruolo di lavoro B esegue inoltre il polling di una coda di sottoscrizioni per gli elementi di lavoro inseriti dal metodo di servizio delle API Web per le nuove sottoscrizioni. Quando ne trova una, invia il messaggio di posta elettronica di conferma.

![Elaborazione di messaggi della coda di
sottoscrizioni](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png)

<h2><a name="tables"></a><span  class="short-header">Tabelle</span>Tabelle di Azure</h2>


L'applicazione Azure Email Service archivia i dati nelle tabelle di Archiviazione di Azure. Le tabelle di Azure sono un archivio dati NoSQL, non un database relazionale come il [database SQL di Azure][2]. Per questo motivo sono una valida scelta quando efficienza e scalabilità sono più importanti della normalizzazione dei dati e dell'integrità relazionale. In questa applicazione, ad esempio, un ruolo di lavoro crea una riga ogni volta che viene creato un elemento di lavoro della coda e un altro recupera e aggiorna una riga ogni volta che viene inviato un messaggio di posta elettronica. Ciò potrebbe costituire un collo di bottiglia delle prestazioni se venisse utilizzato un database relazionale. Inoltre, le tabelle di Azure sono più economiche di SQL di Azure. Per ulteriori informazioni sulle tabelle di Azure, vedere le risorse elencate alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Nelle sezioni seguenti vengono descritti i contenuti delle tabelle di Azure utilizzate dall'applicazione Azure Email Service. Per un diagramma delle tabelle e delle rispettive relazioni, vedere il
[diagramma dati di Azure Email Service](#datadiagram) più avanti in
questa pagina.

### Tabella mailinglist

Nella tabella `mailinglist` vengono archiviate le informazioni sulle liste di distribuzione e sui sottoscrittori delle liste di distribuzione. La procedura consigliata per la convenzione di denominazione delle tabelle di Azure è di utilizzare tutte lettere minuscole. Gli amministratori utilizzano le pagine Web per creare e modificare le liste di distribuzione e i clienti e i sottoscrittori utilizzano un set di pagine Web e un metodo di servizio per la sottoscrizione e l'annullamento della sottoscrizione.

Nelle tabelle di NoSQL righe diverse possono avere schemi diversi e questa flessibilità è comunemente utilizzata per creare una sola tabella in cui archiviare dati che in un database relazionale richiederebbero più tabelle. Per archiviare i dati delle liste di distribuzione nel database SQL, ad esempio, è possibile utilizzare tre tabelle: una tabella `mailinglist` in cui vengono archiviate le informazioni sull'elenco, una tabella `subscriber` in cui vengono archiviate le informazioni sui sottoscrittori e una tabella `mailinglistsubscriber` che associa le liste di distribuzione ai sottoscrittori e viceversa. Nella tabella NoSQL di questa applicazione, tali funzioni sono tutte riunite in una sola tabella denominata `mailinglist`.

In una tabella di Azure, ogni riga dispone di una *chiave di partizione* e di una *chiave di riga* che identifica in modo univoco la riga. La chiave di partizione suddivide in modo logico la tabella in partizioni. All'interno di una partizione, la chiave di riga identifica in modo univoco una riga. Non esistono indici secondari. Pertanto, per assicurarsi che l'applicazione sia scalabile, è importante progettare le tabelle in modo da poter sempre specificare i valori della chiave di partizione e della chiave di riga nella clausola Where delle query.

La chiave di partizione della tabella `mailinglist` è il nome della lista di distribuzione.

La chiave di riga della tabella `mailinglist` può essere uno di due elementi: la costante "mailinglist" o l'indirizzo di posta elettronica del sottoscrittore. Le righe che dispongono della chiave di riga
"mailinglist" includono informazioni sulla lista di distribuzione. Le
righe che dispongono dell'indirizzo di posta elettronica come chiave di riga includono informazioni sui sottoscrittori dell'elenco.

In altre parole, le righe con la chiave di riga "mailinglist" sono equivalenti a una tabella `mailinglist` in un database relazionale. Le righe con chiave di riga = indirizzo di posta elettronica sono equivalenti a una tabella `subscriber` e a una tabella di associazione
`mailinglistsubscriber` in un database relazionale.

Facendo pertanto in modo che una tabella risponda a molteplici scopi, si ottengono prestazioni migliori. In un database relazionale sarebbe necessario leggere tre tabelle e ordinare e abbinare reciprocamente tre set di righe, ma queste operazioni richiedono tempo. In questa applicazione invece viene letta una sola tabella e le righe vengono automaticamente restituite nell'ordine della chiave di partizione e della chiave di riga.

Nella griglia seguente vengono indicate le proprietà delle righe contenenti le informazioni sulla lista di distribuzione (chiave di riga
= "MailingList").

<table  border="1">

<tr  bgcolor="lightgray">
<th>Proprietà</th>

<th>Tipo di dati</th>

<th>Descrizione</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName:  un identificatore univoco per la lista di distribuzione, ad esempio: contoso1. L'utilizzo tipico per la tabella è quello di recuperare tutte le informazioni per una lista di distribuzione specifica. L'utilizzo del nome dell'elenco è pertanto un modo efficiente per partizionare la tabella.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>La costante "mailinglist".</td>

</tr>


<tr>
<td>Descrizione</td>

<td>String</td>

<td>Descrizione della lista di distribuzione, ad esempio: "Contoso University History Department announcements".</td>

</tr>


<tr>
<td>FromEmailAddress</td>

<td>String</td>

<td>Indirizzo di posta elettronica "Da" nel messaggio inviato a questo elenco, ad esempio: donotreply@contoso.edu.</td>

</tr>


</table>

Nella griglia seguente vengono indicate le proprietà delle righe contenenti le informazioni sui sottoscrittori dell'elenco (chiave di riga = indirizzo di posta elettronica).

<table  border="1">

<tr  bgcolor="lightgray">
<th>Proprietà</th>

<th>Tipo di dati</th>

<th>Descrizione</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>ListName:  nome (identificatore univoco) della lista di distribuzione, ad esempio: contoso1.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>EmailAddress:  indirizzo di posta elettronica del sottoscrittore, ad esempio: student1@contoso.edu.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>Generata quando l'indirizzo di posta elettronica viene aggiunto a un elenco. Utilizzata nei collegamenti per la sottoscrizione e l'annullamento della sottoscrizione in modo che sia difficile sottoscrivere l'indirizzo di posta elettronica di qualcun altro o annullarne la sottoscrizione. <br  />
<br  />
 Alcune query per le pagine Web Subscribe e Unsubscribe specificano solo l'oggetto PartitionKey e questa proprietà. L'esecuzione di query di una partizione senza utilizzare l'oggetto RowKey limita la scalabilità dell'applicazione, perché le query richiederanno più tempo in quanto aumentano le dimensioni della lista di distribuzione. Un'opzione per migliorare la scalabilità è quella di aggiungere righe di ricerca con SubscriberGUID nella proprietà RowKey. Per ogni indirizzo di posta elettronica, ad esempio, una riga potrebbe avere "email:student1@domain.com" in RowKey e un'altra riga per lo stesso sottoscrittore potrebbe avere "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" in RowKey. Questo è semplice da implementare perché le transazioni batch atomiche nelle righe all'interno di una partizione sono facili da codificare. Microsoft spera di implementarlo nella prossima versione dell'applicazione di esempio. Per ulteriori informazioni, vedere <a  href="http://msdn.microsoft.com/it-it/library/windowsazure/hh508997.aspx">Mondo reale: progettazione di una strategia di partizionamento scalabile per l'archiviazione tabelle di Azure</a>

</td>

</tr>


<tr>
<td>Verified</td>

<td>Boolean</td>

<td>Quando viene inizialmente creata la riga per un nuovo sottoscrittore, il valore è false. Diventa true solo dopo che il nuovo sottoscrittore ha fatto clic sul collegamento ipertestuale Confirm nel messaggio di posta elettronica di benvenuto o dopo che un amministratore l'ha impostata su true. Se un messaggio viene inviato a un elenco mentre il valore di Verified per uno dei sottoscrittori è false, a tale sottoscrittore non viene inviato alcun messaggio di posta elettronica.</td>

</tr>


</table>

Nell'elenco seguente viene illustrato un esempio di come potrebbero essere i dati nella tabella.

<table  border="1">
<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Descrizione</th>

<td>Contoso University History Department announcements</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>student1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>6f32b03b-90ed-41A9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>student2@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>01234567-90ed-41A9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>false</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>mailinglist</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Descrizione</th>

<td>Fabrikam Engineering job postings</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@fabrikam.com</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>fabrikam1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>applicant1@domain.com</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41A9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Verified</th>

<td>true</td>

</tr>


</table>

### Tabella message

Nella tabella `message` vengono archiviate le informazioni sui messaggi programmati per essere inviati a una lista di distribuzione. Gli amministratori creano e modificano le righe in questa tabella mediante pagine Web e i ruoli di lavoro la utilizzano per passare informazioni su ogni messaggio di posta elettronica dal ruolo di lavoro A al ruolo di lavoro B.

La chiave di partizione per la tabella message è la data di invio pianificata per il messaggio di posta elettronica, nel formato aaaa-mm-gg. In questo modo si ottimizza la tabella per la query eseguita più spesso sulla tabella, che seleziona le righe con `ScheduledDate` corrispondente a oggi o a date precedenti. Si crea tuttavia un potenziale collo di bottiglia delle prestazioni, perché la velocità effettiva massima delle tabelle di Archiviazione di Azure è di 500 entità al secondo per una partizione. Per ogni messaggio di posta elettronica da inviare, l'applicazione scrive una riga della tabella
`message`, legge una riga ed elimina una riga. Pertanto il minor tempo
possibile per l'elaborazione di 1.000.000 di messaggi di posta elettronica pianificati per un solo giorno è di quasi due ore, indipendentemente da quanti ruoli di lavoro vengono aggiunti per gestire l'aumento dei carichi.

La chiave di riga della tabella `message` può essere uno di due elementi: la costante "message" più una chiave univoca per il messaggio, denominata `MessageRef`, o il valore di `MessageRef` più l'indirizzo di posta elettronica del sottoscrittore. Le righe contenenti la chiave di riga che inizia con "message" includono informazioni sul messaggio, ad esempio la lista di distribuzione a cui inviarlo e il momento in cui deve essere inviato. Le righe contenenti `MessageRef` e l'indirizzo di posta elettronica come chiave di riga dispongono di tutte le informazioni necessarie per inviare un messaggio di posta elettronica a tale indirizzo.

In termini di database relazionale, le righe con la chiave di riga che inizia con "message" sono equivalenti a una tabella `message`. Le righe con la chiave di riga = `MessageRef` più l'indirizzo di posta elettronica equivalgono a una visualizzazione query di tipo join contenente le informazioni `mailinglist`, `message` e `subscriber`.

Nella griglia seguente vengono indicate le proprietà per le righe della tabella `message` che dispongono di informazioni sul messaggio stesso.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Proprietà</th>

<th>Tipo di dati</th>

<th>Descrizione</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>Data pianificata per l'invio del messaggio, nel formato aaaa-mm-gg.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>Costante "message" concatenata al valore <code>MessageRef</code>
. <code>MessageRef</code>
 è un valore univoco creato ottenendo il valore <code>Ticks</code>
 da <code>DateTime.Now</code>
 quando viene creata la riga. <br  />
<br  />
Nota: Le applicazioni a istanze multiple e multithread con volume elevato devono essere preparate per gestire le eccezioni di RowKey duplicate quando si utilizza Ticks. L'univocità dei valori di Ticks non è garantita.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Data pianificata per l'invio del messaggio. È uguale a <code>PartitionKey</code>
, ma in formato data.</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Riga dell'oggetto del messaggio di posta elettronica.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Elenco a cui questo messaggio deve essere inviato.</td>

</tr>


<tr>
<td>Status</td>

<td>String</td>

<td><ul>
<li>"Pending": il ruolo di lavoro A non ha ancora iniziato a creare i messaggi della coda per pianificare i messaggi di posta elettronica.</li>

<li>"Queuing": il ruolo di lavoro A ha iniziato a creare i messaggi della coda per pianificare i messaggi di posta elettronica.</li>

<li>"Processing": il ruolo di lavoro A ha creato gli elementi di lavoro della coda per tutti i messaggi di posta elettronica dell'elenco, ma non sono ancora stati inviati tutti i messaggi.</li>

<li>"Completed": il ruolo di lavoro B ha finito di elaborare tutti gli elementi di lavoro della coda (tutti i messaggi di posta elettronica sono stati inviati). Le righe completate vengono archiviate nella tabella <code>messagearchive</code>
, come verrà descritto più avanti. Microsoft spera di impostare questa proprietà come <code>enum</code>
 nella prossima versione.</li>
</ul>
</td>

</tr>


</table>

Quando il ruolo di lavoro A crea un messaggio della coda per un messaggio di posta elettronica da inviare a un elenco, crea una riga messaggio nella tabella `message`. Quando il ruolo di lavoro B invia il messaggio di posta elettronica, sposta la riga messaggio nella tabella
`messagearchive` e imposta la proprietà `EmailSent` su `true`. Quando
tutte le righe per un messaggio di posta elettronica con stato Processing sono state archiviate, il ruolo di lavoro A imposta lo stato su Completed e sposta la riga `messaggio` nella tabella
`messagearchive`.

Nella griglia seguente vengono indicate le proprietà per le righe del messaggio di posta elettronica nella tabella `message`.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Proprietà</th>

<th>Tipo di dati</th>

<th>Descrizione</th>

</tr>


<tr>
<td>PartitionKey</td>

<td>String</td>

<td>Data pianificata per l'invio del messaggio, nel formato aaaa-mm-gg.</td>

</tr>


<tr>
<td>RowKey</td>

<td>String</td>

<td>Valore <code>MessageRef</code>
 e indirizzo di posta elettronica di destinazione dalla riga <code>subscriber</code>
 della tabella <code>mailinglist</code>
.
</td>

</tr>


<tr>
<td>MessageRef</td>

<td>Long</td>

<td>Uguale al componente <code>MessageRef</code>
 di <code>RowKey</code>
.</td>

</tr>


<tr>
<td>ScheduledDate</td>

<td>Date</td>

<td>Data pianificata dalla riga <code>message</code>
 della tabella <code>message</code>
. È uguale a <code>PartitionKey</code>
, ma in formato data.</td>

</tr>


<tr>
<td>SubjectLine</td>

<td>String</td>

<td>Riga dell'oggetto del messaggio di posta elettronica dalla riga <code>message</code>
 della tabella <code>message</code>
.</td>

</tr>


<tr>
<td>ListName</td>

<td>String</td>

<td>Nome della lista di distribuzione dalla tabella <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>From EmailAddress</td>

<td>String</td>

<td>Indirizzo di posta elettronica "from" dalla riga <code>mailinglist</code>
 della tabella <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailAddress</td>

<td>String</td>

<td>Indirizzo di posta elettronica dalla riga <code>subscriber</code>
 della tabella <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>SubscriberGUID</td>

<td>String</td>

<td>GUID del sottoscrittore dalla riga <code>subscriber</code>
 della tabella <code>mailinglist</code>
.</td>

</tr>


<tr>
<td>EmailSent</td>

<td>Boolean</td>

<td>False indica che il messaggio di posta elettronica non è stato ancora inviato. True indica che il messaggio è stato inviato.</td>

</tr>


</table>

In queste righe sono presenti dati ridondanti che in genere si eviterebbero in un database relazionale. Ma in questo caso gli svantaggi dei dati ridondanti sono compensati dal vantaggio di una maggiore scalabilità ed efficienza di elaborazione. Poiché tutti i dati necessari per un messaggio di posta elettronica sono presenti in una di queste righe, il ruolo di lavoro B deve solo leggere una riga per inviare un messaggio quando estrae dalla coda un elemento di lavoro.

Ci si potrebbe chiedere da dove proviene il corpo del messaggio di posta elettronica. Queste righe non dispongono di riferimenti BLOB per i file contenti il corpo del messaggio di posta elettronica, perché tale valore deriva dal valore `MessageRef`. Se, ad esempio, `MessageRef` è 634852858215726983, i BLOB vengono denominati 634852858215726983.htm e 634852858215726983.txt.

Nell'elenco seguente viene illustrato un esempio di come potrebbero essere i dati nella tabella.

<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>message634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Processing</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>634852858215726983student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student1@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>76543210-90ed-41A9-b8ac-c1310c67b66a</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>634852858215726983student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>634852858215726983</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-10-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New lecture series</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>contoso1</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">FromEmailAddress</th>

<td>donotreply@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailAddress</th>

<td>student2@contoso.edu</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubscriberGUID</th>

<td>12345678-90ed-41a9-b8ac-c1310c679876</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">EmailSent</th>

<td>true</td>

</tr>


</table>

* * *


<table  border="1">

<tr>
<th  width="200" align="right" bgcolor="lightgray">Chiave di partizione</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Chiave di riga</th>

<td>message124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">MessageRef</th>

<td>124852858215726999</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ScheduledDate</th>

<td>2012-11-15</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">SubjectLine</th>

<td>New job postings</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">ListName</th>

<td>fabrikam</td>

</tr>


<tr>
<th  align="right" bgcolor="lightgray">Status</th>

<td>Pending</td>

</tr>


</table>

### Tabella messagearchive

Una strategia per fare in modo che le query vengano eseguite in modo efficiente, soprattutto se è necessario effettuare una ricerca in campi diversi da `PartitionKey` e `RowKey`, è quella di limitare le dimensioni della tabella. La query nel ruolo di lavoro A, che verifica se sono stati inviati tutti i messaggi di posta elettronica per un messaggio, deve trovare nella tabella `message` le righe messaggio di posta elettronica con `EmailSent` = false. Poiché il valore `EmailSent` non si trova in PartitionKey o in RowKey, questa non sarebbe una query efficiente per un messaggio con un numero elevato di righe messaggio di posta elettronica. Pertanto, quando i messaggi di posta elettronica vengono inviati, l'applicazione sposta le relative righe nella tabella
`messagearchive`. Di conseguenza, per controllare se sono stati inviati
tutti i messaggi di posta elettronica per un messaggio, è sufficiente eseguire la query della tabella message in `PartitionKey` e in `RowKey` perché, se vengono trovate righe messaggio di posta elettronica per un messaggio, significa che sono presenti messaggi non inviati e il messaggio non può essere contrassegnato come `Complete`.

Lo schema delle righe nella tabella `messagearchive` è identico a quello della tabella `message`. A seconda di come si prevede di utilizzare questi dati di archivio, è possibile limitarne le dimensioni e la spesa riducendo il numero di proprietà memorizzate per ogni riga ed eliminando le righe precedenti a una data specifica.

<h2><a name="queues"></a><span  class="short-header">Code</span>Code di Azure</h2>


Le code di Azure facilitano la comunicazione tra i livelli di questa applicazione multilivello e tra i ruoli di lavoro nel livello di back-end. Le code vengono utilizzate per la comunicazione tra il ruolo di lavoro A e il ruolo di lavoro B per rendere scalabile l'applicazione. Il ruolo di lavoro A può creare una riga nella tabella Message per ogni messaggio di posta elettronica e il ruolo di lavoro B può eseguire l'analisi delle righe della tabella che rappresentano i messaggi di posta elettronica non inviati, ma non sarà possibile aggiungere altre istanze del ruolo di lavoro B per suddividere il lavoro. Il problema con l'utilizzo delle righe della tabella per coordinare il lavoro tra il ruolo di lavoro A e il ruolo di lavoro B è che non è possibile essere certi che una sola istanza del ruolo di lavoro selezioni una determinata riga della tabella per l'elaborazione. Le code offrono questa garanzia. Quando un'istanza del ruolo di lavoro estrae un elemento di lavoro da una coda, il servizio di accodamento assicura che nessun'altra istanza del ruolo di lavoro possa eseguire il pull dello stesso elemento di lavoro. Questa funzionalità di lease esclusiva code di Azure facilita la condivisione di un carico di lavoro tra più istanze di un ruolo di lavoro.

Azure fornisce inoltre il servizio di accodamento bus di servizio. Per ulteriori informazioni sulle code di Archiviazione di Azure e sulle code del bus di servizio, vedere le risorse elencate alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

L'applicazione Azure Email Service utilizza due code, denominate
`AzureMailQueue` e `AzureMailSubscribeQueue`.

### AzureMailQueue

La coda `AzureMailQueue` coordina l'invio di messaggi di posta elettronica a elenchi di indirizzi di posta elettronica. Il ruolo di lavoro A inserisce nella coda un elemento di lavoro per ogni messaggio di posta elettronica da inviare e il ruolo di lavoro B esegue il pull di un elemento di lavoro dalla coda e invia il messaggio di posta elettronica.

Un elemento di lavoro della coda contiene una stringa con valori delimitati da virgole costituita dalla data pianificata del messaggio
(chiave di partizione per la tabella `message`) e i valori `MessageRef`
e `EmailAddress` (chiave di riga per la tabella `message`), più un flag indicante se l'elemento viene creato dopo che il ruolo di lavoro ha avuto esito negativo ed è stato riavviato, ad esempio:

      2012-10-15,634852858215726983,student1@contoso.edu,0

Il ruolo di lavoro B utilizza questi valori per cercare la riga nella tabella `message` contenente tutte le informazioni necessarie per inviare il messaggio di posta elettronica. Se il flag di riavvio indica un riavvio, il ruolo di lavoro B, prima di inviare il messaggio di posta elettronica, verifica che non sia già stato inviato.

Quando si verificano picchi di traffico, il servizio cloud può essere riconfigurato in modo che vengano create più istanze del ruolo di lavoro B e che ciascuna di esse possa estrarre gli elementi di lavoro dalla coda in modo indipendente.

### AzureMailSubscribeQueue

La coda `AzureMailSubscribeQueue` coordina l'invio dei messaggi di posta elettronica di conferma della sottoscrizione. In risposta a una chiamata, il metodo di servizio inserisce un elemento di lavoro nella coda. Il ruolo di lavoro B esegue il pull dell'elemento di lavoro dalla coda e invia il messaggio di posta elettronica di conferma della sottoscrizione.

Un elemento di lavoro della coda contiene il GUID del sottoscrittore. Questo valore identifica in modo univoco un indirizzo di posta elettronica e l'elenco in cui sottoscriverlo. Queste sono le sole informazioni necessarie al ruolo di lavoro B per inviare un messaggio di posta elettronica di conferma. Come spiegato in precedenza, a questo scopo è necessario eseguire una query in un campo non presente in
`PartitionKey` o in `RowKey`, il che è inefficiente. Per rendere
l'applicazione più scalabile, la tabella `mailinglist` avrebbe dovuto essere ristrutturata in modo da includere il GUID del sottoscrittore in
`RowKey`.

<h2><a name="datadiagram"></a><span  class="short-header">Diagramma dati</span>Diagramma dati di Azure Email Service</h2>


Nel diagramma seguente vengono illustrate le tabelle e le code e le relazioni tra di esse.

![Diagramma dati per l'applicazione Azure Email
Service](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png)

<h2><a name="blobs"></a><span  class="short-header">BLOB</span>BLOB di Azure</h2>


I BLOB sono oggetti binari di grandi dimensioni. Il servizio BLOB di Azure offre un mezzo per il caricamento e l'archiviazione dei file nel cloud. Per ulteriori informazioni sui BLOB di Azure, vedere le risorse elencate alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

Gli amministratori di Azure Mail Service inseriscono il corpo di un messaggio di posta elettronica in formato HTML in un file con estensione
*htm* e come testo normale in un file con estensione *txt*. Quando
pianificano un messaggio di posta elettronica, caricano questi file nella pagina Web **Create Message** e il controller ASP.NET MVC per la pagina archivia il file caricato in un BLOB di Azure.

I BLOB vengono archiviati in contenitori BLOB, proprio come i file vengono archiviati nelle cartelle. L'applicazione Azure Mail Service utilizza un solo contenitore BLOB, denominato
**azuremailblobcontainer**. Il nome dei BLOB nel contenitore viene
ricavato concatenando il valore MessageRef con l'estensione del file, ad esempio: 634852858215726983.htm e 634852858215726983.txt.

Poiché i messaggi HTML e di testo normale sono essenzialmente stringhe,
è possibile progettare l'applicazione in modo da archiviare il corpo
del messaggio di posta elettronica nelle proprietà delle stringhe nella tabella `Message` invece che nei BLOB. Poiché tuttavia esiste un limite di 64 KB per le dimensioni di una proprietà in una riga della tabella, l'utilizzo di un BLOB evita tale limitazione per le dimensioni del corpo del messaggio di posta elettronica. 64 KB è la dimensione massima totale della proprietà. Se si consente la codifica del sovraccarico, la dimensione massima delle stringhe che è possibile archiviare in una proprietà sarà in realtà di quasi 48 KB.

<h2><a name="wawsvswacs"></a><span  class="short-header">Servizio cloud e sito Web</span>Servizio cloud di Azure e sito Web di Azure</h2>


Azure Email Service, quando viene scaricato, è configurato in modo che il front-end e il back-end vengano eseguiti entrambi in un solo servizio cloud di Azure.

![Panoramica dell'architettura
dell'applicazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png)

Un'architettura alternativa consiste nell'eseguire il front-end in un sito Web di Azure.

![Architettura alternativa
dell'applicazione](./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png)

Mantenere tutti i componenti in un servizio cloud semplifica la configurazione e la distribuzione. Se si crea l'applicazione con il front-end ASP.NET MVC in un sito Web di Azure, si disporrà di due distribuzioni, una per il sito Web di Azure e una per il servizio cloud di Azure. I ruoli Web del servizio cloud di Azure forniscono inoltre le seguenti funzionalità non disponibili nei siti Web di Azure:

* Supporto per certificati personalizzati e jolly.
* Pieno controllo sulla configurazione di IIS. Molte funzionalità di IIS
  non possono essere abilitate nei siti Web di Azure. Con ruoli Web di
  Azure, è possibile definire un comando di avvio che esegue il
  programma [AppCmd][3] per modificare le impostazioni di IIS che non
  possono essere configurate nel file *Web.config*. Per ulteriori
  informazioni, vedere [Configurare i componenti di IIS in Azure][4] e
  [Bloccare l'accesso a un ruolo Web a specifici indirizzi IP][5].
* Supporto per la scalabilità automatica dell'applicazione Web mediante
  il [blocco applicazione per la scalabilità
  automatica](/en-us/develop/net/how-to-guides/autoscaling/).
* Possibilità di eseguire script di avvio elevati per installare
  applicazioni, modificare impostazioni del Registro di sistema,
  installare contatori di prestazioni e così via.
* Isolamento della rete per l'utilizzo con [Azure Connect][6] e [Rete
  virtuale di Azure][7].
* Accesso desktop remoto per il debug e la diagnostica avanzata.
* Aggiornamenti in sequenza con lo [scambio IP virtuale][8]. Questa
  funzionalità scambia il contenuto delle distribuzioni di gestione
  temporanea e di produzione.

L'architettura alternativa potrebbe offrire alcuni vantaggi a livello di costi, perché un sito Web di Azure con una capacità analoga potrebbe essere meno costoso rispetto a un ruolo Web in esecuzione in un servizio cloud. Nelle successive esercitazioni della serie vengono illustrate in dettaglio le differenze tra le due architetture dal punto di vista dell'implementazione.

Per ulteriori informazioni sulla scelta tra siti Web di Azure e servizi cloud di Azure, vedere [Modelli di esecuzione di Azure][9].

<h2><a name="cost"></a><span  class="short-header">Costo</span>Costo</h2>


Questa sezione fornisce una breve panoramica dei costi per l'esecuzione dell'applicazione di esempio in Azure, considerate le tariffe in vigore quando l'esercitazione è stata pubblicata nel dicembre del 2012. Prima di prendere decisioni aziendali sulla base dei costi, assicurarsi di controllare le tariffe correnti nella pagine Web seguenti:

* [Calcolatore dei costi di Azure][10]
* [SendGrid Azure][11]

I costi dipendono dal numero di istanze del ruolo di lavoro e Web che si decide di gestire. Per essere qualificati per il [Contratto di servizio per il 99,95% del servizio cloud di Azure][12], è necessario distribuire due o più istanze di ogni ruolo. Uno dei motivi per cui è necessario eseguire almeno due istanze del ruolo è che le macchine virtuali che eseguono l'applicazione vengono riavviate circa due volte al mese per gli aggiornamenti del sistema operativo. Per ulteriori informazioni sugli aggiornamenti del sistema operativo, vedere [Riavvi delle istanze del ruolo dovuti ad aggiornamenti del sistema operativo][13].

Il lavoro eseguito dai due ruoli di lavoro in questo esempio non pone problemi di tempo e pertanto non è necessario un contratto di servizio per il 99,5%. L'esecuzione di una singola istanza di ogni ruolo di lavoro è realizzabile purché un'istanza sia in grado di gestire il carico di lavoro. I tempi sono importanti per l'istanza del ruolo Web perché gli utenti si aspettano che il sito Web non abbia tempi di inattività e pertanto un'applicazione di produzione deve avere almeno due istanze del ruolo Web.

Nella tabella seguente sono indicati i costi per l'architettura predefinita per l'applicazione di esempio Azure Email Service presumendo un carico di lavoro minimo. I costi indicati sono basati sull'utilizzo di una dimensione macchina virtuale molto piccola
(condivisa). La dimensione predefinita della macchina virtuale quando si
crea un progetto cloud di Visual Studio è piccola, che è circa sei volte più costosa della dimensione molto piccola.

<table  border="1">

<tr  bgcolor="lightgray">
<th>Componente o servizio</th>

<th>Tariffa</th>

<th>Costo mensile</th>

</tr>


<tr>
<td>Ruolo Web</td>

<td>2 istanze a $ 0,02/ora per istanze molto piccole</td>

<td>$ 29,00</td>

</tr>


<tr>
<td>Ruolo di lavoro A (pianifica i messaggi di posta elettronica da inviare)</td>

<td>1 istanza a $ 0,02/ora per un'istanza molto piccola</td>

<td>$ 14,50?</td>

</tr>


<tr>
<td>Ruolo di lavoro B (invia i messaggi di posta elettronica)</td>

<td>1 istanza a $ 0,02/ora per un'istanza molto piccola</td>

<td>$ 14,50</td>

</tr>


<tr>
<td>Transazioni di Archiviazione di Azure</td>

<td>1 milione di transazioni al mese a $ 0,10/milione. Ogni query corrisponde a una transazione, il ruolo di lavoro A cerca continuamente nelle tabelle i messaggi da inviare. L'applicazione è inoltre configurata in modo da scrivere i dati diagnostici in Archiviazione di Azure e ognuna di queste operazioni corrisponde a una transazione.</td>

<td>$ 0,10</td>

</tr>


<tr>
<td>Archiviazione con ridondanza locale di Azure</td>

<td>$ 2,33 per 25 GB (include la memoria per le tabelle dell'applicazione e dati diagnostici)</td>

<td>$ 2,33</td>

</tr>


<tr>
<td>Larghezza di banda</td>

<td>L'uscita da 5 GB è gratuita</td>

<td>Gratis</td>

</tr>


<tr>
<td>SendGrid</td>

<td>I clienti di Azure possono inviare gratuitamente 25.000 messaggi di posta elettronica al mese</td>

<td>Gratis</td>

</tr>


<tr>
<td  colspan="2">Totale</td>

<td>$ 60,43</td>

</tr>


</table>

Come è evidente, le istanze del ruolo sono un componente importante del costo complessivo. Le istanze del ruolo implicano un costo anche se vengono arrestate. È necessario eliminare un'istanza del ruolo per non incorrere in eventuali costi. Per ridurre i costi, è possibile spostare tutto il codice dal ruolo di lavoro A e dal ruolo di lavoro B in un solo ruolo di lavoro. Per queste esercitazioni è stato scelto deliberatamente di implementare due istanze del ruolo di lavoro per semplificare la scalabilità orizzontale. Il lavoro eseguito dal ruolo di lavoro B viene coordinato dal Servizio di accodamento di Azure e pertanto è possibile scalare orizzontalmente il ruolo di lavoro B semplicemente aumentando il numero di istanze del ruolo. Il ruolo di lavoro B è il fattore limitante per le condizioni di carico elevato. Il lavoro eseguito dal ruolo di lavoro A non viene coordinato dalle code e pertanto non è possibile eseguire più istanze del ruolo di lavoro A. Se i due ruoli di lavoro venissero combinati e si desiderasse abilitare la scalabilità orizzontale, sarebbe necessario implementare un meccanismo per assicurarsi che le attività del ruolo di lavoro A vengano eseguite in una sola istanza. Tale meccanismo è fornito da [CloudFx][14]. Vedere l'[esempio WorkerRole.cs][15].

È inoltre possibile spostare tutto il codice dai due ruoli di lavoro al
ruolo Web, in modo che tutte le operazioni vengano eseguite nel ruolo Web. Tuttavia, l'esecuzione di attività in background in ASP.NET non è supportata né considerata affidabile e questa architettura complicherebbe la scalabilità. Per ulteriori informazioni, vedere la pagina relativa ai [pericoli derivanti dall'implementazione di attività in background ricorrenti in ASP.NET][16]. Vedere inoltre le pagine relative a [come combinare un ruolo di lavoro e un ruolo Web in Azure][17] e alla [combinazione di più ruoli di lavoro di Azure in un ruolo Web di Azure][18].

Un'altra architettura alternativa che ridurrebbe il costo prevede l'utilizzo del [blocco applicazione per la scalabilità automatica](/en-us/develop/net/how-to-guides/autoscaling/) per distribuire automaticamente i ruoli di lavoro solo durante i periodi pianificati ed eliminarli al termine del lavoro. Per ulteriori informazioni sulla scalabilità automatica, vedere i collegamenti alla fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

È possibile che in futuro Azure fornirà meccanismo di notifica per i
riavvii pianificati, che consentirebbe di avviare un'ulteriore istanza del ruolo Web solo per l'intervallo di tempo del riavvio. Non si sarebbe qualificati per il contratto di servizio 99,95, ma sarebbe possibile ridurre i costi di quasi la metà e di essere certi che l'applicazione Web sia disponibile durante l'intervallo di riavvio.

<h2><a name="auth"></a><span  class="short-header">Autenticazione e autorizzazione</span>Autenticazione e autorizzazione</h2>


In un'applicazione di produzione si implementerà un meccanismo di autenticazione e autorizzazione, ad esempio il sistema di appartenenze ASP.NET per il front-end Web ASP.NET MVC, incluso il metodo di servizio API Web ASP.NET. Esistono anche altre opzioni, ad esempio l'utilizzo di un segreto condiviso, per proteggere il metodo di servizio API Web. La funzionalità di autenticazione e autorizzazione è stata omessa dall'applicazione di esempio per poterla configurare e distribuire facilmente. La seconda esercitazione della serie illustra come implementare restrizioni IP, in modo che persone non autorizzate non possano utilizzare l'applicazione quando la si distribuisce nel cloud.

Per ulteriori informazioni su come implementare l'autenticazione e l'autorizzazione in un progetto Web ASP.NET MVC, vedere le seguenti risorse:

* [Autenticazione e autorizzazione nell'API Web ASP.NET][19]
* [Music Store - Parte 7: appartenenza e autorizzazione][20]

**Nota**: era stato previsto di includere un meccanismo per la
protezione del metodo di servizio API Web mediante un segreto condiviso, che tuttavia non è stato completato in tempo per il rilascio iniziale. La terza esercitazione non illustra pertanto come creare il controller API Web per il processo di sottoscrizione. Microsoft spera di includere le istruzioni per l'implementazione di un processo di sottoscrizione sicuro nella prossima versione di questa esercitazione. Fino ad allora,
è possibile testare l'applicazione utilizzando le pagine Web per gli
amministratori per sottoscrivere gli indirizzi di posta elettronica negli elenchi.

<h2><a name="nextsteps"></a><span  class="short-header">Passaggi successivi</span>Passaggi successivi</h2>


Nell'[esercitazione successiva](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/) si scaricherà il progetto di esempio, si configurerà l'ambiente di sviluppo, si configurerà il progetto per l'ambiente e si testerà il progetto localmente e nel cloud. Nelle esercitazioni seguenti verrà illustrato come creare il progetto da zero.

Per collegamenti a risorse aggiuntive per l'utilizzo di tabelle, code e BLOB si Archiviazione di Azure, vedere la fine dell'[ultima esercitazione di questa serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

 
<div><a  href="/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">Esercitazione 2</a></div>

 

[1]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
[2]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336279.aspx
[3]: http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd"
[4]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433059.aspx
[5]: http://msdn.microsoft.com/it-it/library/windowsazure/jj154098.aspx
[6]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433122.aspx
[7]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx
[8]: http://msdn.microsoft.com/it-it/library/windowsazure/ee517253.aspx "VIP swap"
[9]: http://www.windowsazure.com/it-it/manage/windows/fundamentals/compute/
[10]: http://www.windowsazure.com/it-it/pricing/calculator/
[11]: http://sendgrid.com/windowsazure.html
[12]: https://www.windowsazure.com/en-us/support/legal/sla/ "SLA"
[13]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[14]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
[15]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
[16]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
[17]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
[18]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
[19]: http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api
[20]: http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7