<properties 
   pageTitle="Monitoraggio di Gestione traffico"
   description="In questo articolo vengono fornite le istruzioni per comprendere e configurare il monitoraggio di Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Informazioni sul monitoraggio di Gestione traffico

Gestione traffico di Azure esegue il monitoraggio degli endpoint, tra cui i servizi cloud e i siti Web, per assicurarne la disponibilità. Affinché il monitoraggio funzioni correttamente, è necessario configurarlo nello stesso modo per tutti gli endpoint specificati nel profilo di Gestione traffico. Dopo aver configurato il monitoraggio, nel portale di gestione, Gestione traffico visualizza lo stato per endpoint e profilo. È possibile configurare le impostazioni di monitoraggio nel portale di gestione sulla pagina di configurazione per il profilo di Gestione traffico. È possibile specificare le seguenti impostazioni:

- **Protocollo**: scegliere HTTP o HTTPS. È importante notare che il monitoraggio HTTPS non verifica la validità del certificato SSL, ne verifica solo la presenza.

- **Porta**: scegliere la porta usata per la richiesta. È possibile scegliere tra porte HTTP e HTTPS standard.

- **Nome file e percorso relativo**: Fornire il percorso e il nome file a cui il sistema di monitoraggio tenterà di accedere. Considerare che una barra "/" è una voce valida per il percorso relativo e implica che il file sia nella directory radice (predefinito).

## Informazioni sullo stato di integrità del monitoraggio

Nel portale di gestione, Gestione traffico di Azure visualizza il profilo e l'integrità del servizio endpoint. La colonna dello stato per il profilo e l'endpoint visualizza lo stato di monitoraggio più recente. È possibile utilizzare tale stato per verificare l'integrità dei profili in base alle impostazioni di monitoraggio di Gestione traffico. Quando il profilo è integro, le query DNS verranno distribuite ai servizi in base alle impostazioni di routing del traffico per il profilo (Round Robin, prestazioni o failover). Se il sistema di monitoraggio di Gestione traffico rileva una modifica nello stato di monitoraggio, la voce relativa allo stato viene aggiornata nel portale di gestione. L'aggiornamento della modifica relativa allo stato può richiedere fino a cinque minuti.

### Stato monitoraggio endpoint

Lo stato di monitoraggio dell'endpoint nella seguente tabella è il risultato di una combinazione dei risultati del test sull'integrità dell'endpoint e delle configurazioni dell'endpoint e del profilo.

|Stato profilo|Stato endpoint|Stato monitoraggio endpoint (API e portale)|Note|
|---|---|---|---|
|Disabled|Enabled|Inactive|I profili disabilitati non vengono monitorati. Tuttavia, è possibile gestire lo stato dell'endpoint con i profili disabilitati.|
|&lt;qualsiasi&gt;|Disabled|Disabled|I profili disabilitati non vengono monitorati. Tuttavia, è possibile gestire lo stato dell'endpoint con i profili disabilitati.|
|Enabled|Enabled|Online|Endpoint monitorato e integro.|
|Enabled|Enabled|Degraded|Endpoint monitorato e non integro.|
|Enabled|Enabled|CheckingEndpoint|Endpoint monitorato, ma i risultati del primo test non sono ancora stati ricevuti. Subito dopo l'aggiunta di un nuovo endpoint al profilo o subito dopo l'abilitazione di un endpoint o profilo, questo stato è temporaneo.|
|Enabled|Enabled|Stopped|Il servizio cloud o sito Web sottostante non è in esecuzione.|

### Stato monitoraggio del profilo

Lo stato di monitoraggio del profilo nella seguente tabella è il risultato della combinazione tra lo stato di monitoraggio dell'endpoint e dello stato del proprio profilo configurato.

|Stato profilo (come configurato)|Stato monitoraggio endpoint|Stato monitoraggio profilo (API e portale)|Note|
|---|---|---|---|
|Disabled|&lt;qualsiasi&gt; o un profilo senza endpoint definiti.|Disabled|Gli endpoint non vengono monitorati.|
|Enabled|Lo stato di almeno un endpoint è "Degraded".|Degraded|Si tratta di un contrassegno per cui è richiesta l'azione del cliente.|
|Enabled|Lo stato di almeno un endpoint è "Online". Nessun endpoint è "Degraded".|Online|Il servizio sta accettando il traffico e non sono richieste azioni da parte del cliente.|
|Enabled|Lo stato di almeno un endpoint è "CheckingEndpoint". Nessun endpoint è "Online" o "Degraded".|CheckingEndpoints|Stato di transizione. In genere si verifica quando un profilo è stato appena abilitato e l'integrità dell'endpoint è in corso di verifica.|
|Enabled|Lo stato di tutti gli endpoint definiti nel profilo è "Disabled" o "Stopped" oppure il profilo non dispone di endpoint definiti.|Inactive|Non ci sono endpoint attivi, ma il profilo è abilitato.|

## Funzionamento del monitoraggio

Di seguito viene visualizzata una sequenza temporale di esempio con un processo di monitoraggio a un solo servizio cloud. In questo scenario viene illustrato quanto segue:

- Il servizio cloud è disponibile e riceve il traffico SOLO tramite il presente profilo di Gestione traffico.

- Il servizio cloud diventa non disponibile.

- Il servizio cloud resta non disponibile per un tempo maggiore rispetto alla durata (TTL) DNS.

- Il servizio cloud diventa nuovamente disponibile.

- Il servizio cloud riprende la ricezione del traffico SOLO tramite il presente profilo di Gestione traffico.

![Sequenza monitoraggio di Gestione traffico](./media/traffic-manager-monitoring/IC697947.jpg)

**Figura 1**: Esempio di sequenza di monitoraggio. I numeri nel diagramma corrispondono alla seguente descrizione numerata.

1. **GET**: il sistema di monitoraggio di Gestione traffico esegue un GET sul percorso e sul file specificati nelle impostazioni di monitoraggio.
2. **200 OK**: il sistema di monitoraggio prevede un messaggio HTTP 200 OK restituito entro 10 secondi. Alla ricezione della risposta, si presuppone che il servizio cloud è disponibile. 

>[AZURE.NOTE]Gestione traffico considera che l'endpoint sarà online solo se il messaggio restituito è un 200 OK. Se viene ricevuta una risposta diversa da 200, si presuppone che l'endpoint non sia disponibile e il controllo non riesce. Per informazioni più dettagliate sulla risoluzione dei problemi relativi ai controlli non riusciti, vedere [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md).

3. **30 secondi tra i controlli**: questo controllo viene eseguito ogni 30 secondi.
4. **Servizio cloud non disponibile**: il servizio cloud non è disponibile. Gestione traffico non dispone di informazioni fino al successivo controllo di monitoraggio.
5. **Tentativi di accesso al file di monitoraggio (4 tentativi)**: il sistema di monitoraggio esegue un GET, ma non riceve risposte entro un massimo di 10 secondi. Esegue poi altri 3 tentativi a intervalli di 30 secondi. Ciò significa che il sistema di monitoraggio impiega massimo un minuto e mezzo per rilevare quando un servizio diventa non disponibile. Se uno dei tentativi ha esito positivo, il conteggio viene azzerato. Anche se non è illustrato nel diagramma, se i messaggi 200 OK vengono restituiti dopo 10 secondi dal GET, il sistema di monitoraggio lo considera un controllo non riuscito.
6. **Contrassegnato come danneggiato**: dopo il quarto tentativo non riuscito in una riga, il sistema di monitoraggio contrassegna il servizio cloud non disponibile come danneggiato. 

7. **Diminuzione del traffico verso il servizio cloud**: il flusso del traffico verso il servizio cloud non disponibile continua. I client riscontrano degli errori perché il servizio non è disponibile. I client e i server DNS secondari hanno memorizzato nella cache il record DNS per l'indirizzo IP del servizio cloud non disponibile. Continuano a risolvere il nome DNS del dominio aziendale sull'indirizzo IP del servizio. Inoltre, i server DNS secondari potrebbero distribuire le informazioni DNS del servizio non disponibile. Durante l'aggiornamento dei client e dei server DNS secondari, il traffico verso l'indirizzo IP del servizio non disponibile è lento. Il sistema di monitoraggio continua l'esecuzione dei controlli a intervalli di 30 secondi. Nell'esempio, il servizio non risponde e rimane non disponibile.
8. **Arresto del traffico verso il servizio cloud**: ormai, molti server e client DNS dovrebbero essere aggiornati e il traffico verso il servizio non disponibile si arresta. Il periodo di tempo massimo prima che il traffico si interrompa completamente dipende dal tempo TTL. La durata (TTL) DNS predefinita è 300 secondi (5 minuti). Utilizzando tale valore, i client interrompono l'uso del servizio dopo 5 minuti. Il sistema di monitoraggio continua a eseguire i controlli a intervalli di 30 secondi e il servizio cloud non risponde.
9. **Il servizio cloud viene riportato online e riceve il traffico**: il servizio diventa disponibile, ma Gestione traffico non dispone delle informazioni fino all'esecuzione di un controllo da parte del sistema di monitoraggio.
10. **Ripresa del traffico al servizio**: gestione traffico invia un GET e riceve un messaggio 200 OK in meno di 10 secondi. La distribuzione del nome DNS del servizio cloud ai server DNS, viene avviata non appena questi richiedono l'aggiornamento. Quindi il traffico inizia a fluire ancora una volta verso il servizio.

## Stato degli endpoint padre e figlio per i profili nidificati

Nella seguente tabella viene descritto il comportamento del monitoraggio di Gestione traffico per i profili padre e figlio di un profilo nidificato e l'impostazione minChildEndpoints. Per altre informazioni, vedere [Gestione traffico di Azure](traffic-manager-overview.md).

|Stato di monitoraggio del profilo figlio|Stato di monitoraggio dell'endpoint padre|Note|
|---|---|---|
|Disabled Il profilo è stato disabilitato dall'utente.|Stopped|Lo stato dell'endpoint padre è Stopped, non Disabled. Lo stato Disabled è usato esclusivamente per indicare che l'utente ha disabilitato l'endpoint nel profilo padre.|
|Degraded Lo stato di almeno uno degli endpoint figlio è Degraded.|Stato Online, se il numero di endpoint con stato Online del profilo figlio corrisponde almeno al valore dell'impostazione minChildEndpoints. Stato CheckingEndpoint, se il numero di endpoint con stato Online e CheckingEndpoint del profilo figlio corrisponde almeno al valore dell'impostazione minChildEndpoints. In caso contrario, lo stato è Degraded.|Il traffico viene indirizzato a un endpoint con stato CheckingEndpoint. Se il valore dell'impostazione minChildEndpoints è troppo alto, lo stato dell'endpoint padre è sempre Degraded.|
|Online Almeno un elemento figlio è nello stato Online e nessuno è nello stato Degraded.|Come sopra.||
|CheckingEndpoints Almeno un elemento è nello stato CheckingEndpoint e nessuno è nello stato Online o Degraded|Come sopra.||
|Inactive Tutti gli endpoint sono Disabled o Stopped oppure si tratta di un profilo senza endpoint|Stopped||

## Per configurare il monitoraggio per un percorso e nome file specifico

1. Creare un file con lo stesso nome in ogni endpoint che si pensa di includere nel profilo.
2. Per ogni endpoint, usare un browser Web per verificare l'accesso al file. L'URL consiste di un nome di dominio dell'endpoint specifico (il servizio cloud o sito Web), il percorso al file e il nome del file. 
3. Nel portale di gestione, in **Impostazioni di monitoraggio**, nel campo **Percorso relativo e Nome file** specificare il percorso e il nome del file.
4. Dopo aver terminato le modifiche alla configurazione, fare clic su **Salva** nella parte inferiore della pagina.

## Vedere anche

[Creare un profilo](traffic-manager-manage-profiles.md)

[Aggiungere un endpoint](traffic-manager-endpoints.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)
 

<!---HONumber=Nov15_HO4-->