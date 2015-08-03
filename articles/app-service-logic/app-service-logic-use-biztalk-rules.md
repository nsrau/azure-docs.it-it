<properties
   pageTitle="BizTalk Rules"
   description="Questo argomento illustra le funzionalità di BizTalk Rules e fornisce istruzioni sul suo utilizzo"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/02/2015"
   ms.author="andalmia"/>

#BizTalk Rules

Le regole di business incapsulano i criteri e le decisioni che controllano i processi di business. Tali criteri possono essere definiti formalmente all'interno di manuali di procedure o contratti oppure possono esistere sotto forma di conoscenze o competenze dei dipendenti. Questi criteri sono dinamici e soggetti a modifiche nel tempo, come conseguenza di modifiche dei piani aziendali oppure delle normative o per altri motivi.

L'implementazione di questi criteri nei linguaggi di programmazione tradizionali richiede tempo e coordinazione. La creazione e la manutenzione dei criteri di business sono riservate ai programmatori. Le regole di business di BizTalk consentono di implementare rapidamente questi criteri e di separare il resto del processo di business. Ciò consente di apportare le modifiche necessarie ai criteri di business senza alcun impatto sul resto del processo di business.

##Perché le regole

Esistono tre motivi principali che giustificano l'uso delle regole di business di BizTalk nel processo di business:

* Separare la logica di business dal codice dell'applicazione
- Consentire agli analisti aziendali di avere maggiore controllo sulla gestione della logica di business
+ Le modifiche alla logica di business passano in produzione più velocemente

##Concetti delle regole

##Vocabolario

I termini usati per definire azioni e condizioni delle regole vengono solitamente espressi tramite il dominio o la nomenclatura specifica del settore. Ad esempio, un utente di posta elettronica scrive regole in termini di messaggi "ricevuti da" e di messaggi "ricevuti dopo", mentre un business analyst assicurativo scrive regole in termini di "fattori di rischio" e "importo di copertura". Alla base di questa terminologia specifica del dominio vi sono gli elementi della tecnologia (oggetti, tabelle di database e documenti XML) che implementano le condizioni e azioni delle regole. I vocabolari sono progettati per colmare il divario tra la semantica di business e l'implementazione.

Ad esempio, un data binding per uno stato di approvazione potrebbe puntare a una determinata colonna di una determinata riga in un determinato database, rappresentato come una query SQL. Anziché inserire questo tipo di rappresentazione complessa in una regola, è invece possibile creare una definizione del vocabolario, associata a tale data binding, con un nome descrittivo "Stato". Successivamente è possibile includere "Stato" in un numero qualsiasi di regole e il motore di regole può recuperare i dati corrispondenti dalla tabella. Un _vocabolario_ è una raccolta di definizioni costituite da nomi descrittivi per gli oggetti di elaborazione usati nelle condizioni e azioni. Le definizioni del vocabolario semplificano la lettura, la comprensione e la condivisione delle regole da parte degli utenti in un dominio aziendale specifico.

##Regola

Le regole di business sono istruzioni dichiarative che regolano la gestione dei processi di business. Una regola è costituita da una condizione e da azioni. La condizione viene valutata e, se il risultato è true, il motore delle regole avvia una o più azioni. Le regole Business Rules Framework vengono definite usando il formato seguente:

_IF_ _condizione_ _THEN_ _azione_

Si consideri l'esempio seguente:

*IF l'importo è inferiore o uguale ai fondi disponibili* *THEN condurre la transazione e stampare la ricevuta*

Questa regola determina se verrà eseguita una transazione applicando la logica di business, in base al confronto tra due valori monetari, costituiti dall'importo di una transazione e dai fondi disponibili. È possibile usare la regola di business per creare, modificare e distribuire regole di business. In alternativa, è possibile eseguire le attività precedenti a livello di codice.

###Condizioni

Una condizione è un'espressione true o false (booleana) costituita da uno o più predicati. Nell'esempio riportato in questa pagina viene applicato il predicato minore o uguale a per la quantità e i fondi disponibili. Questa condizione restituisce sempre true o false. I predicati possono essere combinati con gli operatori logici AND, OR e NOT per formare un'espressione logica che è potenzialmente di grandi dimensioni, ma restituiscono sempre true o false.

###Actions

Le azioni sono le conseguenze funzionali della valutazione della condizione. Se viene soddisfatta una condizione della regola, vengono avviate una o più azioni corrispondenti. Nell'esempio riportato in questa pagina la transazione e la stampa della ricevuta sono azioni che vengono eseguite se e solo quando la condizione (in questo caso, "IF quantità è minore o uguale ai fondi disponibili") è true. Le azioni sono rappresentate in Business Rules Framework eseguendo le operazioni set su documenti XML.

##Criterio

Un criterio è un raggruppamento logico di regole. È possibile comporre, salvare, testare un criterio e, quando si è soddisfatti dei risultati, è possibile usarlo in un ambiente di produzione.

###Composizione dei criteri

È possibile comporre i criteri nel portale delle regole. Un criterio può contenere un insieme di regole arbitrariamente grande, ma in genere un criterio viene composto mediante regole che si riferiscono a un dominio all'interno del contesto dell'applicazione che userà tale criterio.

###Test dei criteri
È possibile effettuare un'esecuzione dei test del criterio prima di usarlo in un ambiente di produzione. Il portale delle regole consente di fornire input a un criterio, eseguirlo e visualizzarne l'output. L'output include log, l'esecuzione delle regole, valutazione della condizione e output risultanti.

##Scenario di esempio - Richieste di risarcimenti assicurativi
Verrà esaminato uno scenario di esempio e verrà descritto come si compone la logica di business per tale scenario.

![Alt text][1]

In uno scenario di richiesta di risarcimento assicurativo realmente semplice, il richiedente invia la richiesta tramite qualsiasi client, ad esempio sito Web, app telefonica e così via. La richiesta viene inviata all'unità di elaborazione delle richieste di risarcimento e, in base al risultato dell'elaborazione, può essere approvata, rifiutata o inviata per un'ulteriore elaborazione manuale. In questo scenario l'unità di elaborazione della richiesta sarebbe quella che include la logica di business per il sistema. Esaminando più in dettaglio questa unità, è possibile osservare quanto segue:

![Alt text][2]

Ora verranno usate le regole di business per implementare questa logica di business.


##Creazione dell'app per le API delle regole


1. Accedere al portale di Azure e passare alla home page.
1. Fare clic su Nuovo -> Azure Marketplace->App per le API->BizTalk Rules->Crea ![Alt text][3]
1. Nel nuovo pannello che viene visualizzato, immettere le informazioni seguenti:  
	1. Nome: specificare un nome per l'app per le API delle regole
	1. Piano di hosting app: selezionare o creare un piano di hosting Web
	1. Piano tariffario: scegliere il piano tariffario desiderato per l'app
	1. Gruppo di risorse: selezionare o creare il gruppo di risorse desiderato per quest'app
	1. Località: scegliere la località geografica in cui si vuole distribuire l'app.
4.	Fare clic su Crea. Entro pochi minuti verranno create le app per le app per le API di BizTalk Rules.

##Creazione del vocabolario
Dopo aver creato un'app per le API di BizTalk Rules, il passaggio successivo prevede la creazione di vocabolari. Questo esercizio viene in genere eseguito dallo sviluppatore. Per effettuare questa operazione, eseguire i passaggi seguenti:


1. Passare all'app per le API creata selezionando Sfoglia->App per API-><Your Rules API App>. Dovrebbe venire visualizzato un dashboard dell'app per le API delle regole simile al seguente:

   ![Alt text][4]

2.Fare quindi clic su "Definizioni vocabolario". Viene visualizzata la schermata Creazione vocabolario. Fare clic su "Aggiungi" per iniziare ad aggiungere nuove definizioni del vocabolario. Attualmente sono supportati due tipi di definizioni del vocabolario: Valore letterale e XML.

##Definizione Valore letterale
1.	Dopo aver fatto clic su "Aggiungi", viene visualizzato il pannello "Aggiungi definizione". Immettere i valori seguenti
  1.	Nome: sono previsti solo caratteri alfanumerici senza caratteri speciali. Il nome deve essere univoco per l'elenco di definizioni del vocabolario esistente.
  2.	Descrizione: campo facoltativo.
  3.	Tipo: sono supportati 2 tipi. In questo esempio scegliere Valore letterale
  4.	Tipo di input: consente agli utenti di selezionare il tipo di dati della definizione. Attualmente sono selezionabili 4 tipi di dati: i. Stringa: questi valori devono essere immessi tra virgolette ("Stringa di esempio") ii. Booleano: può essere true o false iii. Numero: può essere qualsiasi numero decimale iv. DateTime: la definizione è di tipo data. I dati devono essere immessi con il seguente formato: gg/mm/aaaa hh:mm:ss AM\PM v. Input: immettere il valore della definizione in questo campo. I valori immessi devono essere conformi al tipo di dati scelto. L'utente può immettere un singolo valore, un set di valori separati da virgole o un intervallo di valori usando le parole chiave "da" e "a". L'utente può, ad esempio, immettere il valore univoco 1, un set 1, 2, 3 o un intervallo "da 1 a 5". Si noti che l'intervallo è supportato solo per il numero.

![Alt text][5]
##Definizione XML
Se si sceglie XML come tipo di vocabolario, è necessario specificare gli input seguenti a. Schema: facendo clic su questa opzione si aprirà un nuovo pannello che consente all'utente di scegliere da un elenco di schemi già caricati o di caricarne uno nuovo. b. XPATH: questo input si sblocca solo dopo aver scelto uno schema nel passaggio precedente. Facendo clic su questa opzione viene visualizzato lo schema che è stato selezionato e l'utente potrà selezionare il nodo per il quale deve essere creata una definizione del vocabolario. c. FATTI: questo input identifica l'oggetto dati da inserire nel motore delle regole per l'elaborazione. Si tratta di una proprietà avanzata e per impostazione predefinita è impostata sull'elemento padre dell'elemento XPATH selezionato. FATTI diventa particolarmente importante per gli scenari di concatenamento e raccolta.

![Alt text][6]

### Aggiunta in blocco
Nei passaggi precedenti sono state descritte le operazioni di creazione delle definizioni del vocabolario. Una volta create, le definizioni del vocabolario verranno visualizzate in forma di elenco. Per poter generare più definizioni dello schema stesso anziché ripetere ogni volta i passaggi precedenti vi sono dei requisiti. È in questa situazione che la funzionalità di aggiunta in blocco risulta particolarmente utile. Facendo clic su "Aggiungi in blocco" viene visualizzato un nuovo pannello. Il primo passaggio prevede la selezione dello schema per il quale è necessario creare più definizioni. Facendo clic su questa opzione si aprirà un nuovo pannello che consente all'utente di scegliere da un elenco di schemi già caricati o di caricarne uno nuovo. La proprietà XPATHS viene sbloccata. Facendo clic su questa opzione verrà aperto il visualizzatore di schema in cui è possibile selezionare più nodi. Per impostazione predefinita, per i nomi delle varie definizioni create verrà usato il nome del nodo selezionato. Questi nomi possono essere sempre modificati dopo la creazione.

![Alt text][7]

##Creazione di criteri
Una volta che lo sviluppatore ha creato i vocabolari necessari, il business analyst dovrebbe creare i criteri aziendali tramite il portale di Azure. 1. Nell'app delle regole create è presente una sezione Criteri. Facendo clic su di essa l'utente passa alla pagina di creazione dei criteri. 2. Nella pagina viene mostrato l'elenco dei criteri di questa specifica app delle regole. L'utente può aggiungere un nuovo criterio semplicemente digitandone il nome e premendo TAB. Più criteri possono risiedere in una singola app per le API delle regole. 3. Facendo clic sul criterio creato l'utente passerà alla pagina Dettagli criteri dove può osservare le regole presenti nel criterio. ![Alt text][8] 4. Fare clic su "Aggiungi nuovo" per aggiungere una nuova regola. Verrà visualizzato un nuovo pannello.

##Creazione di regole
Una regola è una raccolta di istruzioni relative a condizioni e azioni. Le azioni vengono eseguite se la condizione restituisce true. Nel pannello Crea regola, assegnare un nome univoco della regola (per il criterio) e una descrizione (facoltativa). La casella Condizione può essere usata per creare istruzioni condizionali complesse. Di seguito sono elencate le parole chiave supportate: 1. And: operatore condizionale 2. Or – operatore condizionale 3. does_not_exist 4. exists 5. false 6. is_equal_to 7. is_greater_than 8. is_greater_than_equal_to 9. is_in 10. is_less_than 11. is_less_than_equal_to 12. is_not_in 13. is_not_equal_to 14. mod 15. true

La casella Azione(Then) può contenere più istruzioni, una per riga, per creare azioni da eseguire. Di seguito sono le parole chiave supportate: 1. equals 2. false 3. true 4. halt 5. mod 6. null 7. update

Le caselle Condizione e Azione offrono il supporto IntelliSense che aiuta l'utente a creare una regola rapidamente. Questa può essere attivata premendo CTRL+BARRA SPAZIATRICE o semplicemente iniziando a digitare. Le parole chiave corrispondenti ai caratteri digitati verranno automaticamente filtrate e mostrate. La finestra IntelliSense mostrerà tutte le parole chiave e le definizioni del vocabolario. ![Alt text][9]

##Concatenamento diretto esplicito
BizTalk Rules supporta il concatenamento diretto esplicito. Ciò significa che se gli utenti desiderano rivalutare le regole in risposta a determinate azioni, possono attivare questo processo usando determinate parole chiave. Di seguito sono elencate le parole chiave supportate: 1. update <vocabulary definition>: questa parola chiave rivaluta tutte le regole che usano la definizione del vocabolario specificata nella sua condizione. 2. Halt: questa parola chiave interrompe tutte le esecuzioni di regole

##Abilitazione\disabilitazione di regole
Ogni regola del criterio può essere abilitata o disabilitata. Per impostazione predefinita tutte le regole sono abilitate. Le regole disabilitate non verranno eseguite durante l'esecuzione dei criteri. L'abilitazione/disabilitazione delle regole può essere eseguita direttamente dal pannello delle regole, usando i comandi disponibili nella barra dei comandi nella parte superiore del pannello, o dal criterio; nel menu di scelta rapida (visualizzato facendo clic con il pulsante destro del mouse su una regola) è disponibile l'opzione di abilitazione/disabilitazione.

##Priorità delle regole
Tutte le regole dei criteri vengono eseguite in ordine. La priorità di esecuzione è determinata dall'ordine in cui si verificano nel criterio. Questa priorità può essere modificata semplicemente trascinando e rilasciando la regola.

##Testare un criterio
Dopo aver creato il criterio, è possibile testarlo prima di usarlo nell'ambiente di produzione. Usando il comando "Test criterio", gli utenti possono accedere al relativo pannello, dove è disponibile un elenco di definizioni del vocabolario usate nel criterio che richiedono un input dell'utente. Gli utenti possono aggiungere manualmente i valori per questi input per il proprio scenario di test. In alternativa, possono importare XML di test per gli input. Una volta inseriti tutti gli input, il test può essere eseguito e nella colonna di output verranno visualizzati gli output per ogni definizione del vocabolario per un semplice confronto. Per visualizzare log semplici da usare per i business analyst, fare clic su "Visualizza log" per visualizzare i log di esecuzione. Per salvare i log, è disponibile l'opzione "Salva output" che consente di archiviare tutti i dati correlati ai test per l'analisi indipendente.

## Uso delle regole nelle app per la logica
Una volta che il criterio è stato creato e verificato, è pronto per l'uso. Gli utenti possono creare una nuova app per la logica selezionando Nuovo -> App per la logica. Nella finestra di progettazione, BizTalk Rules è disponibile nella raccolta a destra e può essere ora trascinata e rilasciata nell'area di progettazione. Al termine, sarà possibile scegliere l'app per le API delle regole di destinazione. Le azioni includono l'elenco dei criteri da eseguire. Scegliere un criterio specifico dopo il quale occorre inserire gli input necessari per il criterio. Gli utenti possono usare l'output dell'app per le API delle regole a valle per altri processi decisionali.

## Uso di regole tramite le API
L'app per le API delle regole può essere chiamata anche usando una vasta gamma di API disponibili. In questo modo gli utenti non sono costretti a usare solo i flussi e possono usare le regole in qualsiasi applicazione effettuando chiamate REST. Le API REST esatte disponibili possono essere visualizzate facendo clic sulla sezione "Definizione API" nel dashboard delle regole.

![Alt text][10]

Di seguito è riportato un esempio di come è possibile usare questa API in C#

   			// Constructing the JSON message to use in API call to Rules API App

			// xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0="http://tempuri.org/XMLSchema.xsd">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

			// The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
			// In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
			// This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

			// The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Si noti che l'app qui sopra per le API delle regole possiede impostazioni di sicurezza impostate su "Public Anon". Può essere impostato all'interno dell'app per le API usando: Tutte le impostazioni -> Impostazioni applicazione -> Livello di accesso

![Alt text][11]

## Modifica del vocabolario e del criterio
Uno dei principali vantaggi offerti dall'uso delle regole di business risiede nel fatto che le modifiche apportate alla logica di business possono essere distribuite nell'ambiente di produzione molto più velocemente. Qualsiasi modifica apportata al vocabolario e ai criteri viene immediatamente applicata nell'ambiente di produzione. È sufficiente passare alla definizione del vocabolario o al criterio e apportare le modifiche, che diventano effettive.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG

<!---HONumber=July15_HO4-->