<properties
 pageTitle="Modelli di app per la logica | Microsoft Azure"
 description="Informazioni su come usare modelli di app per la logica predefiniti per iniziare"
 authors="kevinlam1"
 manager="dwrede"
 editor=""
 services="app-service\logic"
 documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="klam"/>

# Modelli di app per la logica

## Informazioni sui modelli di app per la logica

Un modello di app per la logica è un'app per la logica predefinita che è possibile usare per iniziare subito a creare il proprio flusso di lavoro.

Questi modelli rappresentano un ottimo modo per individuare diversi schemi che possono essere compilati tramite le app per la logica. È possibile usare questi modelli così come sono oppure modificarli per adattarli a uno scenario specifico.

## Panoramica dei modelli disponibili

Esistono molti modelli disponibili attualmente pubblicati nella piattaforma di app per la logica. Alcune categorie di esempio, nonché i tipi di connettori usati, sono elencati di seguito.

### Modelli cloud dell'organizzazione
Modelli che si integrano Dynamics CRM, Salesforce, Box, BLOB di Azure e altri connettori per le esigenze cloud dell'organizzazione. Alcuni esempi di operazioni eseguibili con questi modelli includono l'organizzazione dei lead e il backup dei dati di file aziendali.

### Modelli di Enterprise Integration Pack
Configurazioni di pipeline VETER (convalida, estrazione, trasformazione, arricchimento, routing), ricezione di un documento X12 EDI tramite AS2 e conversione in formato XML, nonché gestione di messaggi X12 e AS2.

### Modelli di schema di protocollo
Questi modelli sono costituiti da app per la logica che contengono schemi di protocollo, ad esempio richiesta-risposta HTTP e integrazioni tra FTP e SFTP. È possibile usarli senza apportare modifiche oppure come base per creare schemi di protocollo più complessi.

### Modelli per la produttività personale
Gli schemi per migliorare la produttività personale includono modelli che impostano promemoria giornalieri, trasformano importanti elementi di lavoro in elenchi di attività e automatizzano le attività di lunga durata fino a un passaggio di approvazione di un singolo utente.

### Modelli cloud di livello consumer
Modelli semplici che si integrano con servizi di social media come Twitter, Slack e posta elettronica, in grado di potenziare le iniziative di marketing sui social media. Includono anche modelli di copia per il cloud, che consentono di aumentare la produttività risparmiando il tempo necessario per attività tradizionalmente ripetitive.

## Come creare un'app per la logica usando un modello 

Per iniziare a usare i modelli di app per la logica, accedere alla finestra di progettazione delle app per la logica. Se si accede alla finestra di progettazione aprendo un'app per la logica esistente, l'app per la logica viene caricata automaticamente nella visualizzazione di progettazione. Se tuttavia si crea una nuova app per la logica, verrà visualizzata la schermata seguente. ![](../../includes/media/app-service-logic-templates/template7.png)

In questa schermata è possibile scegliere di iniziare con un'app per la logica vuota o con un modello predefinito. Se si seleziona uno dei modelli, vengono fornite informazioni aggiuntive. In questo esempio viene usato lo schema *Quando un nuovo file viene creato in Dropbox, copialo in OneDrive*. ![](../../includes/media/app-service-logic-templates/template2.png)

Se si sceglie di usare il modello, è sufficiente fare clic sul pulsante *Usa questo modello*. Verrà chiesto di accedere agli account in base ai connettori usati dal modello. Se è stata precedentemente stabilita una connessione con questi connettori, è anche possibile selezionare Continua come illustrato di seguito. ![](../../includes/media/app-service-logic-templates/template3.png)

Dopo avere stabilito la connessione e aver selezionato *Continua*, l'app per la logica verrà aperta nella visualizzazione di progettazione. ![](../../includes/media/app-service-logic-templates/template4.png)

Nell'esempio precedente, come nel caso di molti modelli, alcuni dei campi di proprietà obbligatori possono essere compilati all'interno dei connettori. Alcuni potrebbero comunque richiedere un valore prima di poter distribuire correttamente l'app per la logica. Se si prova a distribuire senza immettere alcuni dei campi mancanti, verrà visualizzato un messaggio di errore.

Se si vuole tornare al visualizzatore dei modelli, fare clic sul pulsante *Modelli* nella barra di spostamento superiore. Tornando al visualizzatore dei modelli, eventuali modifiche non salvate andranno perse. Prima di tornare al visualizzatore dei modelli verrà visualizzato un messaggio di avviso a questo riguardo. ![](../../includes/media/app-service-logic-templates/template5.png)

## Come distribuire un'app per la logica creata da un modello

Dopo aver caricato il modello e apportato le modifiche necessarie, fare clic sul pulsante Salva nell'angolo superiore sinistro. In questo modo, l'app per la logica verrà salvata e pubblicata. ![](../../includes/media/app-service-logic-templates/template6.png)

Per altre informazioni su come aggiungere altri passaggi in un modello di app per la logica esistente o apportare modifiche in generale, vedere [Creare una nuova app per la logica](app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0831_2016-->