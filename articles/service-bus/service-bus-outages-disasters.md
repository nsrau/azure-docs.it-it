<properties 
    pageTitle="Isolamento delle applicazioni del bus di servizio da interruzioni ed emergenze del servizio | Microsoft Azure"
    description="Descrive le tecniche che è possibile usare per proteggere le applicazioni da potenziali interruzioni del bus di servizio."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio

Le applicazioni criciali devono funzionare in modo continuo, anche in presenza di interruzioni impreviste o situazioni di emergenza. Questo argomento descrive le tecniche che è possibile usare per proteggere le applicazioni del bus di servizio da una potenziale emergenza o interruzione del servizio.

Il termine "interruzione" indica la temporanea indisponibilità del bus di servizio di Azure. Un'interruzione può interessare alcuni componenti del bus di servizio, ad esempio un archivio di messaggistica, o anche l'intero data center. Una volta risolto il problema, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un esempio di errore di un componente è la mancata disponibilità di un particolare archivio di messaggistica. Un esempio di interruzione a livello di data center è un'interruzione dell'alimentazione o il guasto di un commutatore di rete. Un'interruzione può durare da pochi minuti ad alcuni giorni.

Il termine "emergenza" indica la perdita permanente di un'unità di scala del bus di servizio o di un data center. È possibile che il data center non torni di nuovo disponibile. In genere, un'emergenza determina la perdita di alcuni o di tutti i messaggi o di altri dati. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto.

## Architettura corrente

Il bus di servizio usa più archivi di messaggistica per memorizzare messaggi inviati a code o argomenti. Una coda o un argomento non partizionato viene assegnato a un archivio di messaggistica. Se l'archivio di messaggistica in questione non è disponibile, tutte le operazioni eseguite sulla coda o sull'argomento avranno esito negativo.

Tutte le entità del bus di servizio (code, argomenti, inoltri) risiedono in uno spazio dei nomi di servizio che è affiliato a un data center. Il bus di servizio non abilita la replica geografica automatica dei dati, né consente a uno spazio dei nomi di estendersi su più data center.

## Protezione da interruzioni del servizio di controllo di accesso (ACS)

Se si usano credenziali ACS e il servizio ACS non è disponibile, i client non possono più ottenere i token. I client che dispongono di un token nel momento in cui ACS si arresta possono continuare a usare il bus di servizio fino alla scadenza dei token. La durata predefinita dei token è 3 ore.

Per proteggersi dalle interruzioni del servizio ACS, usare token di firma di accesso condiviso. In questo caso, il client esegue direttamente l'autenticazione con il bus di servizio firmando un token coniato automaticamente con una chiave privata. Non sono più necessarie chiamate ad ACS. Per altre informazioni sulla configurazione dei token di firma di accesso condiviso, vedere [Autenticazione del bus di servizio][].

## Protezione di code e argomenti da errori degli archivi di messaggistica

Una coda o un argomento non partizionato viene assegnato a un archivio di messaggistica. Se l'archivio di messaggistica in questione non è disponibile, tutte le operazioni eseguite sulla coda o sull'argomento avranno esito negativo. Una coda partizionata, invece, è costituita da più frammenti, ciascuno dei quali memorizzato in un archivio di messaggistica differente. Quando un messaggio viene inviato a una coda o a un argomento partizionato, il bus di servizio assegna il messaggio a uno dei frammenti. Se l'archivio di messaggistica corrispondente non è disponibile, il bus di servizio scrive i messaggi in un frammento diverso, se possibile. Per altre informazioni sulle entità partizionate, vedere [Entità di messaggistica partizionate][].

## Protezione da interruzioni o emergenze dei data center

Per consentire un failover tra due data center, è possibile creare uno spazio dei nomi servizio del bus di servizio in ogni data center. Ad esempio, lo spazio dei nomi servizio del bus di servizio **contosoPrimary.servicebus.windows.net** potrebbe trovarsi nell'area centro-settentrionale degli Stati Uniti, mentre **contosoSecondary.servicebus.windows.net** potrebbe trovarsi in quella centro-meridionale. Se un'entità di messaggistica del bus di servizio deve rimanere accessibile in caso di interruzione del data center, è possibile creare l'entità in entrambi gli spazi dei nomi.

Per altre informazioni, vedere la sezione "Errore del bus di servizio in un data center di Azure" in [Modelli di messaggistica asincrona e disponibilità elevata][].

## Protezione degli endpoint di inoltro da interruzioni o emergenze dei data center

La replica geografica degli endpoint di inoltro consente a un servizio che espone un endpoint di inoltro di essere raggiungibile in presenza di interruzioni del bus di servizio. Per ottenere la replica geografica, il servizio deve creare due endpoint di inoltro in diverse istanze di spazio dei nomi servizio. Le due istanze di spazio dei nomi servizio devono trovarsi in data center diversi e i due endpoint devono avere nomi differenti. Ad esempio, un endpoint primario può essere raggiunto in **contosoPrimary.servicebus.windows.net/myPrimaryService**, mentre il corrispettivo endpoint secondario può essere raggiunto in **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Il servizio rimane quindi in ascolto su entrambi gli endpoint e un client può richiamare il servizio tramite l'uno o l'altro. Un'applicazione client seleziona casualmente uno degli inoltri come endpoint primario e invia la richiesta all'endpoint attivo. Se l'operazione ha esito negativo e viene visualizzato un codice di errore, significa che l'endpoint di inoltro non è disponibile. L'applicazione apre un canale per l'endpoint di backup e invia nuovamente la richiesta. A quel punto, l'endpoint attivo e quello di backup si scambiano i ruoli: l'applicazione client considera l'endpoint attivo precedente come nuovo endpoint di backup e l'endpoint di backup precedente come nuovo endpoint attivo. Se entrambe le operazioni di invio hanno esito negativo, i ruoli delle due entità rimangono invariati e viene restituito un errore.

L'esempio relativo alla [replica geografica con i messaggi inoltrati del bus di servizio][] illustra come replicare gli inoltri.

## Protezione delle code e degli argomenti da interruzioni o emergenze dei data center

Per ottenere resilienza in caso di possibili interruzioni dei data center durante l'uso della messaggistica negoziata, il bus di servizio supporta due approcci: replica *attiva* e replica *passiva*. Per ogni approccio, se una coda o un argomento specifico deve rimanere accessibile in presenza di un'interruzione del data center, è possibile creare tale coda o argomento in entrambi gli spazi dei nomi. Entrambe le entità possono avere lo stesso nome. Ad esempio, una coda primaria può essere raggiunta in **contosoPrimary.servicebus.windows.net/myQueue**, mentre la rispettiva coda secondaria può essere raggiunta in **contosoSecondary.servicebus.windows.net/myQueue**.

Se l'applicazione non richiede una comunicazione mittente-ricevitore permanente, può implementare una coda permanente sul lato client in modo da impedire la perdita di messaggi e proteggere il mittente da eventuali errori temporanei del bus di servizio.

## Replica attiva

La replica attiva usa entità in entrambi gli spazi dei nomi per ogni operazione. Ogni client invia sempre due copie di un messaggio. La prima viene inviata all'entità primaria, ad esempio **contosoPrimary.servicebus.windows.net/sales**, la seconda all'entità secondaria, ad esempio **contosoSecondary.servicebus.windows.net/sales**.

Un client riceve messaggi da entrambe le code. Il ricevitore elabora la prima copia di un messaggio ed elimina la seconda. Per eliminare i messaggi duplicati, il mittente deve contrassegnare ogni messaggio con un identificatore univoco. Entrambe le copie del messaggio devono essere contrassegnate con lo stesso identificatore. Per contrassegnare il messaggio, è possibile usare le proprietà [BrokeredMessage.MessageId][] o [BrokeredMessage.Label][] o una proprietà personalizzata. Il ricevitore deve mantenere l'elenco dei messaggi già ricevuti.

L'esempio relativo alla [replica geografica con i messaggi negoziati del bus di servizio][] illustra la modalità di replica attiva delle entità di messaggistica.

> [AZURE.NOTE] Nella replica attiva il numero delle operazioni raddoppia. Di conseguenza, questo approccio può comportare costi più elevati.

## Replica passiva

Quando non si verificano errori, nella replica passiva viene usata solo una delle due entità di messaggistica. Un client invia il messaggio all'entità attiva. Se l'operazione sull'entità attiva non riesce e viene restituito un codice di errore per segnalare che il data center che ospita l'entità attiva potrebbe non essere disponibile, il client invia una copia del messaggio all'entità di backup. A quel punto, l'entità attiva e quella di backup si scambiano i ruoli: il client mittente considera l'entità attiva precedente come nuova entità di backup e l'entità di backup precedente come nuova entità attiva. Se entrambe le operazioni di invio hanno esito negativo, i ruoli delle due entità rimangono invariati e viene restituito un errore.

Un client riceve messaggi da entrambe le code. Poiché potrebbe ricevere due copie dello stesso messaggio, il ricevitore deve eliminare i messaggi duplicati. È possibile eliminare i duplicati nel modo descritto per la replica attiva.

In genere, la replica passiva è più vantaggiosa di quella attiva perché nella maggior parte dei casi viene eseguita una sola operazione. La latenza, la velocità effettiva e il costo sono identici a quelli di uno scenario non replicato.

Quando si usa la replica passiva, negli scenari seguenti è possibile che i messaggi vengano persi o ricevuti due volte.

-   **Ritardo o perdita del messaggio**: si supponga che il mittente abbia inviato con esito positivo un messaggio m1 alla coda primaria e che quindi la coda diventi non disponibile prima della ricezione di m1 da parte del ricevitore. Il mittente invia un secondo messaggio m2 alla coda secondaria. Se la coda primaria è temporaneamente non disponibile, m1 viene ricevuto solo quando la coda torna nuovamente disponibile. In caso di emergenza, il destinatario potrebbe non ricevere mai m1.

-   **Ricezione di duplicati**: si supponga che il mittente invii un messaggio m alla coda primaria. Il bus di servizio elabora m ma non riesce a inviare una risposta. Dopo il timeout dell'operazione di invio, il mittente invia una copia identica di m alla coda secondaria. Se la prima copia di m viene ricevuta prima che la coda primaria diventi non disponibile, le due copie di m verranno ricevute quasi contemporaneamente. Se invece la prima copia di m non viene ricevuta prima che la coda primaria diventi non disponibile, il ricevitore riceverà inizialmente solo la seconda copia di m. L'altra copia di m verrà ricevuta quando la coda primaria diventerà disponibile.

L'esempio relativo alla [replica geografica con i messaggi negoziati del bus di servizio][] illustra la modalità di replica passiva delle entità di messaggistica.

## Passaggi successivi

Per altre informazioni sul ripristino di emergenza, vedere gli articoli seguenti:

- [Continuità aziendale del database SQL di Azure][]
- [Indicazioni tecniche sulla resilienza di Azure][]

  [Autenticazione del bus di servizio]: service-bus-authentication-and-authorization.md
  [Entità di messaggistica partizionate]: service-bus-partitioning.md
  [Modelli di messaggistica asincrona e disponibilità elevata]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [replica geografica con i messaggi inoltrati del bus di servizio]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [replica geografica con i messaggi negoziati del bus di servizio]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuità aziendale del database SQL di Azure]: ../sql-database/sql-database-business-continuity.md
  [Indicazioni tecniche sulla resilienza di Azure]: ../resiliency/resiliency-technical-guidance.md

<!---HONumber=AcomDC_0907_2016-->