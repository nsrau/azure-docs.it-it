<properties
	pageTitle="Panoramica dei connettori per le app per la logica | Microsoft Azure"
	description="Panoramica dei connettori utilizzabili in un'app per la logica"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# Uso dei connettori in un'app per la logica

I connettori consentono di accedere rapidamente a eventi, dati e azioni tra servizi, protocolli e piattaforme. L'elenco completo dei connettori supportati dalle app per la logica è [disponibile qui](apis-list.md). I connettori possono essere usati come trigger o azioni in un'app per la logica e possono richiedere una *connessione* configurata da usare (ad esempio l'autorizzazione di un account Twitter a effettuare l'accesso o a postare contenuti per conto del cliente).

## Nozioni di base

I connettori sono servizi ospitati a cui è possibile accedere come parte di un'app per la logica per l'integrazione con altri servizi come Dynamics, Azure, Salesforce e [altri](apis-list.md). Sono distribuiti e gestiti da Microsoft, pertanto è possibile creare flussi di lavoro di integrazione che prevedono scalabilità, velocità e sicurezza. È possibile aggiungere un connettore a un'app per la logica cercando e selezionando un'azione o un trigger connettore in **Show Microsoft managed APIs** (Mostra API gestite da Microsoft).

![Menu delle azioni per la selezione dei trigger][1]

Ogni azione o trigger connettore avrà il suo set di proprietà da configurare. [Per altre informazioni](apis-list.md) sull'azione è possibile fare clic sui pulsanti info o consultare la documentazione di riferimento.

Se si desidera eseguire l'integrazione con un servizio o un'API che non è ancora un connettore, è anche possibile estendere le app per la logica attraverso un [connettore personalizzato](../app-service-logic/app-service-logic-create-api-app.md) o semplicemente chiamare direttamente il servizio tramite un protocollo come HTTP.

## Trigger

Alcuni connettori dispongono di un trigger, perciò un evento del connettore attiverà un'app per la logica e vi passerà dati come parte del trigger. Un trigger è sempre il primo passaggio in un'app per la logica. Trigger comuni includono operazioni quali:
 
 * Ricorrenza - da eseguire ogni ora
 * Quando viene ricevuta una richiesta HTTP
 * Quando un elemento viene aggiunto a una coda
 * Quando viene ricevuto un messaggio di posta elettronica
 
Alcuni trigger vengono attivati nell'istante in cui si verifica un evento mediante una notifica all'app per la logica mentre altri avranno bisogno di un intervallo di ricorrenza configurato che determini la frequenza con cui l'app per la logica controlla se si è verificato un evento nel servizio (al massimo ogni 15 secondi).

Una volta ricevuto un evento, l'esecuzione dell'app per la logica viene attivata e le azioni nel flusso di lavoro si avviano. Sarà inoltre possibile accedere ai dati del trigger in tutto il flusso di lavoro: ad esempio il trigger "On a new tweet" ("In caso di nuovo tweet") passerà il tweet all'esecuzione.

## Azioni

Molti connettori dispongono di una o più azioni che possono essere eseguite come parte del flusso di lavoro. Le azioni sono operazioni che si verificano dopo l'attivazione dell'esecuzione generata da un trigger. Per aggiungere un'azione fare clic sul pulsante **Nuovo passaggio** e cercare il connettore che si desidera usare. Una volta selezionato (e dopo aver configurato le eventuali [connessioni](#connections) necessarie), verrà visualizzata la scheda azione che è possibile configurare. È possibile selezionare dati dai passaggi precedenti facendo clic sui token per gli output o immettere qualsiasi altra configurazione in base alle esigenze.

![Configurazione di un'azione connettore][2]

## Connessioni

Molti connettori richiedono che sia configurata una *connessione* prima di poter usare il connettore. Una *connessione* è qualsiasi configurazione di accesso o connessione necessaria per accedere al connettore. Per i connettori che usano OAuth, creare una connessione significa accedere al servizio (ad esempio Office 365, Salesforce o GitHub) in cui il token di accesso può essere crittografato e archiviato in modo sicuro in un archivio segreto di Azure. Altri connettori (come SQL e FTP) richiedono una connessione che contenga una configurazione come l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro. Le connessioni potranno accedere al servizio fino a quando il servizio lo consente. Per le connessioni OAuth di Azure Active Directory (come Office 365 e Dynamics) è possibile continuare ad aggiornare il token di accesso indefinitamente. Altri servizi possono imporre limiti sul periodo di utilizzabilità di un token senza aggiornamento. In genere determinate azioni quali la modifica di una password invalideranno tutti i token di accesso.

Le connessioni possono essere visualizzate e gestite in Azure facendo clic su **Sfoglia** e selezionando **API Connections** (Connessioni API). Dalla risorsa relativa alle connessioni API è possibile visualizzare, modificare, aggiornare o autorizzare nuovamente tutte le connessioni create.

## Passaggi successivi

- [Creare la prima app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Usi ed esempi comuni sulle app per la logica](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Introduzione ai trigger e alle azioni di integrazione aziendale](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png

<!---HONumber=AcomDC_0727_2016-->