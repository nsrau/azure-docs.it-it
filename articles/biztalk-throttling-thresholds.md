<properties linkid="manage-services-biztalk-services-throttling" urlDisplayName="Throttling" pageTitle="Throttling thresholds in BizTalk Services | Azure" metaKeywords="BizTalk Services, throttling, Azure" description="Learn about throttling thresholds and resulting runtime behaviors for BizTalk Services. Throttling is based on memory usage and number of simultaneous messages." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Throttling" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

Servizi BizTalk: Limitazione
============================

Servizi BizTalk di Azure implementa la limitazione dei servizi in base a due condizioni: l'utilizzo della memoria e il numero di messaggi simultanei in fase di elaborazione. In questo argomento sono elencate le soglie di limitazione e viene descritto il comportamento in fase di esecuzione quando si verifica una condizione di limitazione.

Soglie di limitazione
---------------------

Nella tabella seguente sono elencate le origini e le soglie di limitazione:

<table data-morhtml="true" border="1">

<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <td data-morhtml="true"><strong data-morhtml="true">Descrizione</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Soglia inferiore</strong></td>
        <td data-morhtml="true"><strong data-morhtml="true">Soglia superiore</strong></td>
</tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Memoria</td>
        <td data-morhtml="true">Percentuale di memoria totale del sistema disponibile/byte file di paging. <br data-morhtml="true" /><br data-morhtml="true" /> I byte totali disponibili del file di paging &egrave; il doppio della RAM del sistema.</td>
        <td data-morhtml="true">60%</td>
        <td data-morhtml="true">70%</td>
    </tr>
    <tr data-morhtml="true">
        <td data-morhtml="true">Elaborazione di messaggi</td>
        <td data-morhtml="true">Numero di messaggi elaborati simultaneamente</td>
        <td data-morhtml="true">40 * numero di memorie centrali</td>
        <td data-morhtml="true">100 * numero di memorie centrali</td>
    </tr>
</table>

Quando viene raggiunta una soglia superiore, Servizi BizTalk di Azure inizia la limitazione. La limitazione viene interrotta quando viene raggiunta la soglia inferiore. Se ad esempio il servizio utilizza il 65% della memoria di sistema, non viene applicata la limitazione. Se invece il servizio inizia a utilizzare il 70% della memoria di sistema, viene applicata la limitazione, che continua fino a quando il servizio non utilizzerà il 60% (soglia inferiore) della memoria di sistema.

Servizi BizTalk di Azure registra lo stato di limitazione (normale o limitato) e la durata della limitazione.

Comportamento in fase di esecuzione
-----------------------------------

Quando Servizi BizTalk di Azure entra nello stato di limitazione, si verifica quanto segue:

-   La limitazione viene applicata a ogni istanza del ruolo, ad esempio:
IstanzaRuoloA è limitata. IstanzaRuoloB non è limitata. In questa situazione, i messaggi in IstanzaRuoloB vengono elaborati come previsto. I messaggi in IstanzaRuoloA vengono rimossi e non vengono eseguiti con l'errore seguente:<br/><br/>
Server is busy. Please try again.<br/><br/>
-   Nessuna origine di pull esegue il polling o scarica un messaggio, ad esempio:<br/>
Una pipeline effettua il pull dei messaggi da un'origine FTP esterna. L'istanza del ruolo che effettua il pull entra in stato limitato. In questa situazione, la pipeline interrompe il download di altri messaggi fino a quando l'istanza del ruolo non interrompe la limitazione.
-   Al client viene inviata una risposta in modo che possa inviare di nuovo il messaggio.
-   È necessario attendere che la limitazione sia risolta. In particolare, occorre attendere che venga raggiunta la soglia inferiore.

Note importanti
---------------

-   Non è possibile disabilitare la limitazione.
-   Non è possibile modificare le soglie di limitazione.
-   La limitazione viene implementata nell'intero sistema.
-   Anche il server di database SQL di Azure ha la funzionalità di limitazione incorporata.

Argomenti aggiuntivi su Servizi BizTalk di Azure
------------------------------------------------

-   [Installazione dell'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)
-   [Esercitazioni: Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)

Vedere anche
------------

-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: Grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servizi BizTalk: Schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servizi BizTalk: Nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)

