<properties 
	pageTitle="Enterprise Integration con EDIFACT | Microsoft Azure" 
	description="Informazioni su come usare i contratti EDIFACT per creare app per la logica" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="jonfan"/>

# Enterprise Integration con EDIFACT 

> [AZURE.NOTE] Questa pagina illustra le funzionalità EDIFACT delle app per la logica. Per informazioni su X12, fare clic [qui](app-service-logic-enterprise-integration-x12.md).

## Creare un contratto EDIFACT 
Prima di poter scambiare messaggi EDIFACT, è necessario creare un contratto EDIFACT e archiviarlo nell'account di integrazione. La procedura seguente illustra il processo di creazione di un contratto EDIFACT.

### Di seguito sono riportati i prerequisiti necessari per iniziare
- Un [account di integrazione](./app-service-logic-enterprise-integration-accounts.md) definito nella sottoscrizione di Azure
- Almeno due [partner](./app-service-logic-enterprise-integration-partners.md) già definiti nell'account di integrazione

>[AZURE.NOTE]Quando si crea un contratto, il contenuto dei messaggi che verranno ricevuti e inviati da e verso il partner deve corrispondere al tipo di contratto.


Dopo aver [creato un account di integrazione](./app-service-logic-enterprise-integration-accounts.md) e aver [aggiunto i partner](./app-service-logic-enterprise-integration-partners.md), è possibile creare un contratto EDIFACT seguendo questa procedura.

### Nella home page del portale di Azure

Dopo aver eseguito l'accesso al [portale di Azure](http://portal.azure.com "Portale di Azure"):
1. Selezionare **Esplora** nel menu a sinistra.

>[AZURE.TIP]Se non viene visualizzato il collegamento **Esplora**, potrebbe essere prima necessario espandere il menu. A tale scopo, selezionare il collegamento **Visualizza menu** nella parte superiore sinistra del menu compresso.

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)
2. Digitare *integrazione* nella casella di ricerca Filtro e quindi selezionare **Account di integrazione** nell'elenco dei risultati. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)
3. Nel pannello **Account di integrazione** visualizzato selezionare l'account di integrazione in cui verrà creato il contratto. Se non viene visualizzato alcun account di integrazione, [crearne prima uno](./app-service-logic-enterprise-integration-accounts.md "Tutte le informazioni sugli account di integrazione"). ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)
4.  Selezionare il riquadro **Accordi**. Se il riquadro Accordi non viene visualizzato, aggiungerlo. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)
5. Selezionare il pulsante **Aggiungi** nel pannello Accordi visualizzato. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)
6. Nel pannello Accordi visualizzato immettere un **nome** per il contratto e quindi selezionare EDIFACT come **Tipo di contratto**, **Partner host**, **Identità host**, **Partner guest** e **Identità guest**. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)
7. Dopo aver impostato le proprietà del contratto, selezionare **Impostazioni di ricezione** per configurare la gestione dei messaggi ricevuti tramite questo contratto.
8. Il controllo Impostazioni di ricezione è suddiviso nelle sezioni Identificatori, Acknowledgment, Schemi, Numeri di controllo, Convalida, Impostazioni interne ed elaborazione batch. Configurare queste proprietà in base al contratto con il partner con cui si scambieranno i messaggi. Ecco una visualizzazione di questi controlli. Configurarli in base a come si vuole che il contratto identifichi e gestisca i messaggi in ingresso: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)
9. Selezionare il pulsante **OK** per salvare le impostazioni.

### Identificatori

|Proprietà|Descrizione |
|---|---|
|UNB6.1 (password di riferimento destinatario)|Immettere un valore alfanumerico costituito da un numero di caratteri compreso tra 1 e 14.|
|UNB6.2 (qualificatore di riferimento destinatario)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo due.|

### Ringraziamenti 

|Proprietà|Descrizione |
|----|----|
|Conferma recapito messaggi (CONTRL)|Selezionare questa casella di controllo per restituire un acknowledgment tecnico (CONTRL) al mittente dell'interscambio. L'acknowledgment viene inviato al mittente dell'interscambio in base alle impostazioni di invio del contratto.|
|Acknowledgement (CONTRL)|Selezionare questa casella di controllo per restituire un acknowledgment funzionale (CONTRL) al mittente dell'interscambio. L'acknowledgment viene inviato al mittente dell'interscambio in base alle impostazioni di invio del contratto.|

### Schemi

|Proprietà|Descrizione |
|----|----|
|UNH2.1 (tipo)|Selezionare un tipo di set di transazioni.|
|UNH2.2 (versione)|Immettere il numero di versione del messaggio (da un minimo di un carattere a un massimo di tre).|
|UNH2.3 (versione)|Immettere il numero di rilascio del messaggio (da un minimo di un carattere a un massimo di tre).|
|UNH2.5 (codice assegnato associazione)|Immettere il codice assegnato (deve contenere al massimo sei caratteri, che devono essere alfanumerici).|
|UNH2.1 (ID mittente APP)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35.|
|UNH2.2 (qualificatore codice mittente APP)|Immettere un valore alfanumerico con lunghezza massima di quattro caratteri.|
|SCHEMA|Selezionare lo schema caricato in precedenza che si vuole usare dall'account di integrazione associato.|

### Numeri di controllo

|Proprietà|Descrizione |
|----|----|
|Disallow Interchange Control Number duplicates (Non consentire duplicati di numeri di controllo interscambio)|Selezionare questa casella di controllo per bloccare gli interscambi duplicati. Se questa opzione è selezionata, l'azione di decodifica EDIFACT verifica che il numero di controllo (UNB5) dell'interscambio ricevuto non corrisponda al numero di controllo di un interscambio elaborato in precedenza. Se viene rilevata una corrispondenza, l'interscambio non viene elaborato.
|Verifica UNB5 duplicati ogni (giorni)|Se si è scelto di non consentire numeri di controllo di interscambio duplicati, è possibile specificare l'intervallo di giorni con cui verrà eseguito il controllo assegnando il valore appropriato all'opzione **Verifica UNB5 duplicati ogni (giorni)**.|
|Disallow Group control number duplicates (Non consentire duplicati di numeri di controllo di gruppo)|Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di gruppo duplicati (UNG5).|
|Disallow Transaction set control number duplicates (Non consentire duplicati di numeri di controllo set di transazioni)|Selezionare questa opzione per bloccare gli interscambi con numeri di controllo di set di transazioni duplicati (UNH1).|
|Numero di controllo acknowledgement EDIFACT|Per designare i numeri di riferimento di set di transazioni da usare in un acknowledgment, immettere un valore per il prefisso, un intervallo di numeri di riferimento e un suffisso.|

### Convalide

|Proprietà|Descrizione |
|----|----|
|Tipo messaggio|Specificare il tipo di messaggio. Quando viene completata una riga di convalida, ne viene aggiunta automaticamente un'altra. Se non è specificata alcuna regola, per la convalida verrà usata la riga contrassegnata come predefinita.|
|EDI Validation (Convalida EDI)|Selezionare questa casella di controllo per eseguire la convalida EDI sui tipi di dati in base a quanto definito da proprietà EDI dello schema, restrizioni di lunghezza, elementi dati vuoti e separatori finali.|
|Convalida estesa|Selezionare questa casella di controllo per abilitare la convalida estesa (XSD) degli interscambi ricevuti dal mittente dell'interscambio, che include la convalida della lunghezza dei campi, la facoltatività e il conteggio ripetizioni, oltre alla convalida dei tipi di dati XSD.|
|Consenti zeri iniziali e finali|Selezionare **Consenti** per consentire zero iniziali e finali, **Non consentiti** per non consentirli o **Taglia** per rimuoverli.|
|Rimuovi zero iniziali e finali|Selezionare questa casella di controllo per rimuovere tutti gli zero iniziali e finali.|
|Criterio separatori finali|Selezionare **Non consentiti** per non consentire delimitatori e separatori finali in un interscambio ricevuto dal mittente dell'interscambio. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido. Selezionare **Facoltativi** per accettare interscambi con o senza delimitatori e separatori finali. Selezionare **Obbligatori** se l'interscambio ricevuto deve contenere delimitatori e separatori finali.|

### Impostazioni interne

|Proprietà|Descrizione |
|----|----|
|Crea tag XML vuoti se sono consentiti separatori finali|Selezionare questa casella di controllo se si vuole che il mittente dell'interscambio includa tag XML vuoti come separatori finali.|
|Inbound batching processing (Elaborazione batch in ingresso)|Le opzioni includono:</br></br>**Suddividi interscambio in set di transazioni - Sospendi set di transazioni in caso di errore**: consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata al set di transazioni. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, vengono sospesi solo i set di transazioni interessati. Suddividi interscambio in set di transazioni - Sospendi interscambio in caso di errore: consente di analizzare ogni set di transazioni di un interscambio in un documento XML separato applicando la busta appropriata. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, viene sospeso l'intero interscambio.</br></br>**Mantieni interscambio - Sospendi set transazioni in caso di errore**: lascia intatto l'interscambio, creando un documento XML per l'intero interscambio batch. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, vengono sospesi solo i set di transazioni interessati e tutti gli altri vengono elaborati.</br></br>**Mantieni interscambio - Sospendi interscambio in caso di errore**: lascia intatto l'interscambio, creando un documento XML per l'intero interscambio batch. Con questa opzione, se la convalida di uno o più set di transazioni dell'interscambio ha esito negativo, viene sospeso l'intero interscambio.|

Il contratto è pronto per la gestione dei messaggi in ingresso conformi alle impostazioni selezionate.

Per configurare le impostazioni che gestiscono i messaggi inviati ai partner:
10. Selezionare **Impostazioni di invio** per configurare la gestione dei messaggi inviati tramite questo contratto.

Il controllo Impostazioni di invio è suddiviso nelle sezioni Identificatori, Acknowledgment, Schemi, Buste, Numeri di controllo, Set di caratteri e separatori, Numeri di controllo e Convalida.

Ecco una visualizzazione di questi controlli. Effettuare le selezioni in base a come si vogliono gestire i messaggi inviati ai partner tramite il contratto: ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)
11. Selezionare il pulsante **OK** per salvare le impostazioni.

### Identificatori
|Proprietà|Descrizione |
|----|----|
|UNB1.2 (versione sintassi)|Selezionare un valore compreso tra **1** e **4**.|
|UNB2.3 (indirizzo di routing inverso mittente)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14.|
|UNB3.3 (indirizzo di routing inverso destinatario)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14.|
|UNB6.1 (password di riferimento destinatario)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14.|
|UNB6.2 (qualificatore di riferimento destinatario)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo due.|
|UNB7 (ID riferimento applicazione)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14.|

### Acknowledgment (Riconoscimento)
|Proprietà|Descrizione |
|----|----|
|Conferma recapito messaggi (CONTRL)|Selezionare questa casella di controllo se il partner host prevede di ricevere un acknowledgment tecnico (CONTRL). Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest.|
|Acknowledgement (CONTRL)|Selezionare questa casella di controllo se il partner host prevede di ricevere un acknowledgment funzionale (CONTRL). Questa impostazione specifica che il partner host che invia il messaggio richiede un acknowledgment dal partner guest.|
|Genera il ciclo SG1/SG4 per i set di transazioni accettate|Se si è scelto di richiedere un acknowledgement funzionale, selezionare questa casella di controllo per forzare la generazione di cicli SG1/SG4 negli acknowledgement funzionali CONTRL per i set di transazioni accettati.|

### Schemi
|Proprietà|Descrizione |
|----|----|
|UNH2.1 (tipo)|Selezionare un tipo di set di transazioni.|
|UNH2.2 (versione)|Immettere il numero di versione del messaggio.|
|UNH2.3 (versione)|Immettere il numero di rilascio del messaggio.|
|SCHEMA|Selezionare lo schema da usare. Gli schemi si trovano nell'account di integrazione. Per accedere agli schemi, collegare l'account di integrazione alla app per la logica.|

### Buste
|Proprietà|Descrizione |
|----|----|
|UNB8 (codice di priorità elaborazione)|Immettere un valore alfabetico di lunghezza non superiore a un carattere.|
|UNB10 (contratto comunicazioni)|Immettere un valore alfanumerico costituito da almeno un carattere e al massimo 40.|
|UNB11 (indicatore test)|Selezionare questa casella di controllo per indicare che nell'interscambio vengono generati dati di test.|
|Applica segmento UNA (avviso stringa servizio)|Selezionare questa casella di controllo per generare un segmento UNA per l'interscambio da inviare.|
|Applica segmenti UNG (intestazione gruppo funzionale)|Selezionare questa casella di controllo per creare segmenti di raggruppamento nell'intestazione di gruppo funzionale nei messaggi inviati al partner guest. Per creare i segmenti UNG vengono usati i valori seguenti:</br></br>Per **UNG1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo sei.</br></br>Per **UNG2.1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35.</br></br>Per **UNG2.2**, immettere un valore alfanumerico con lunghezza massima di quattro caratteri.</br></br>Per **UNG3.1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 35.</br></br>Per **UNG3.2**, immettere un valore alfanumerico con lunghezza massima di quattro caratteri.</br></br>Per **UNG6**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre.</br></br>Per **UNG7.1**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre.</br></br>Per **UNG7.2**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo tre.</br></br>Per **UNG7.3**, immettere un valore alfanumerico costituito da almeno 1 carattere e al massimo 6.</br></br>Per **UNG8**, immettere un valore alfanumerico costituito da almeno un carattere e al massimo 14.|

### Character Sets and Separators (Set di caratteri e separatori)
Oltre al set di caratteri, è possibile immettere un diverso set di delimitatori da usare per ogni tipo di messaggio. Se per un determinato schema del messaggio non è specificato un set di caratteri, viene usato il set di caratteri predefinito.

|Proprietà|Descrizione |
|----|----|
|UNB1.1 (identificatore di sistema)|Selezionare il set di caratteri EDIFACT da applicare all'interscambio in uscita.|
|Schema|Selezionare uno schema nell'elenco a discesa. Quando viene completata una riga, ne viene aggiunta una nuova. Selezionare i separatori da usare per lo schema selezionato:</br></br>**Separatore elementi componenti**: immettere un singolo carattere per separare elementi dati compositi.</br></br>**Separatore elementi dati**: immettere un singolo carattere per separare elementi dati semplici all'interno di elementi dati compositi.</br></br></br></br>**Replacement Character** (Carattere di sostituzione): selezionare questa casella di controllo se i dati di payload contengono caratteri usati anche come separatori di dati, segmenti o componenti. È quindi possibile immettere un carattere di sostituzione. Quando si genera il messaggio EDIFACT in uscita, tutte le istanze dei caratteri separatori nei dati di payload vengono sostituite con il carattere specificato.</br></br>**Carattere di terminazione segmento**: immettere un singolo carattere per indicare la fine di un segmento EDI.</br></br>**Suffisso**: selezionare il carattere usato con l'identificatore di segmento. Se si designa un suffisso, l'elemento dati del terminatore di segmenti può essere vuoto. Se il carattere di terminazione segmento viene lasciato vuoto, è necessario designare un suffisso.|

### Numeri di controllo
|Proprietà|Descrizione |
|----|----|
|UNB5 (numero di controllo interscambio)|Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare un interscambio in uscita. Il prefisso e il suffisso sono facoltativi, mentre il numero di controllo è obbligatorio. Il numero di controllo viene incrementato per ogni nuovo messaggio, mentre il prefisso e il suffisso rimangono invariati.|
|UNG5 (numero di controllo gruppo)|Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare il numero di controllo di gruppo. Il prefisso e il suffisso sono facoltativi, mentre il numero di controllo è obbligatorio. Il numero di controllo viene incrementato per ogni nuovo messaggio fino al raggiungimento del valore massimo, mentre il prefisso e il suffisso rimangono invariati.|
|UNH1 (numero di riferimento intestazione messaggio)|Immettere un prefisso, un intervallo di valori per il numero di controllo dell'interscambio e un suffisso. Questi valori verranno usati per generare il numero di riferimento dell'intestazione del messaggio. Il prefisso e il suffisso sono facoltativi, mentre il numero di riferimento è obbligatorio. Il numero di riferimento viene incrementato per ogni nuovo messaggio, mentre il prefisso e il suffisso rimangono invariati.|

### Convalide
|Proprietà|Descrizione |
|----|----|
|Tipo messaggio|Selezionando questa opzione si consente la convalida nel ricevitore dell'interscambio. Questa convalida esegue la convalida EDI sugli elementi di dati del set di transazioni, convalidando tipi di dati, restrizioni della lunghezza ed elementi dati vuoti e sui separatori finali.|
|EDI Validation (Convalida EDI)|Selezionare questa casella di controllo per eseguire la convalida EDI sui tipi di dati in base a quanto definito da proprietà EDI dello schema, restrizioni di lunghezza, elementi dati vuoti e separatori finali.|
|Convalida estesa|Se si seleziona questa opzione, viene abilitata la convalida estesa degli interscambi ricevuti dal mittente dell'interscambio, inclusi la convalida della lunghezza dei campi, la facoltatività e il conteggio ripetizioni, oltre alla convalida dei tipi di dati XSD. È possibile abilitare la convalida dell'estensione senza abilitare la convalida EDI o viceversa.|
|Consenti zero iniziali e finali|Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo quando l'elemento dati in un interscambio EDI non rispetta il requisito di lunghezza a causa della presenza di spazi iniziali o finali, ma lo rispetta quando questi vengono rimossi.|
|Rimuovi zero iniziali e finali|Se si seleziona questa opzione, gli zero iniziali e finali verranno eliminati.|
|Separatore finale|Specifica che la convalida di un interscambio EDI ricevuto dalla parte non ha esito negativo se un elemento dati in un interscambio EDI non rispetta il requisito di lunghezza solo a causa della presenza di zero iniziali (o finali) o di spazi finali e lo rispetta quando questi vengono rimossi.</br></br>Selezionare **Non consentiti** per non consentire delimitatori e separatori finali in un interscambio ricevuto dal mittente dell'interscambio. Se l'interscambio contiene delimitatori e separatori finali, viene dichiarato non valido.</br></br>Selezionare **Facoltativi** per accettare interscambi con o senza delimitatori e separatori finali.</br></br>Selezionare **Obbligatori** se l'interscambio ricevuto deve contenere delimitatori e separatori finali.|

Dopo aver selezionato **OK** nel pannello aperto:
12. Selezionare il riquadro **Accordi** nel pannello Account di integrazione. Nell'elenco verrà visualizzato il contratto appena aggiunto. ![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)

## Altre informazioni
- [Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")

<!---HONumber=AcomDC_0803_2016-->