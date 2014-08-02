<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

Servizi BizTalk: Tabella degli stati del servizio
=================================================

Le operazioni che è possibile eseguire nel servizio BizTalk variano a seconda dello stato corrente del servizio.

Se ad esempio si esegue il provisioning di un nuovo servizio BizTalk nel portale di gestione di Azure, al termine lo stato del servizio BizTalk risulta Active. Se lo stato è attivo, è possibile arrestare il servizio BizTalk. Se l'arresto viene eseguito, il servizio BizTalk passa allo stato Stopped. Se l'arresto non viene eseguito, il servizio BizTalk passa allo stato StopFailed. In questo stato è possibile riavviare il servizio BizTalk. Se si tenta di eseguire un'operazione non consentita, ad esempio riprendere il servizio BizTalk, viene restituito l'errore seguente:

**Operation not allowed**

Per eseguire il provisioning di un servizio BizTalk, vedere [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Nelle tabelle seguenti sono elencate le operazioni consentite quando il servizio BizTalk si trova in uno stato specifico. Il segno di spunta indica che l'operazione può essere eseguita in quel determinato stato. Uno spazio vuoto indica che l'operazione non può essere eseguita in quel determinato stato.

#### Operazioni di avvio, arresto, riavvio, sospensione, ripresa ed eliminazione

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Operazione</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Avvio</th>
        <th data-morhtml="true">Arresto</th>
        <th data-morhtml="true">Riavvio</th>
        <th data-morhtml="true">Sospensione</th>
        <th data-morhtml="true">Ripresa</th>
        <th data-morhtml="true">Eliminazione</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Disabled</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Suspended</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Stopped</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Service Update Failed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
</table>

#### Operazioni di scala, aggiornamento della configurazione e backup

<table data-morhtml="true" border="1">
<tr data-morhtml="true">
        <th data-morhtml="true" colspan="15">Operazione</th>
</tr>

<tr data-morhtml="true">
        <th data-morhtml="true" rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr data-morhtml="true" bgcolor="FAF9F9">
        <th data-morhtml="true"> </th>
        <th data-morhtml="true">Scala</th>
        <th data-morhtml="true">Aggiornamento della configurazione</th>
        <th data-morhtml="true">Backup</th>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Active</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Disabled</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Suspended</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Stopped</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">Service Update Failed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">DisableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">EnableFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">StartFailed<br data-morhtml="true" /> StopFailed<br data-morhtml="true" /> RestartFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">SuspendedFailed<br data-morhtml="true" /> ResumeFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">CreatedFailed<br data-morhtml="true" /> RestoreFailed<br data-morhtml="true" /></b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ConfigUpdateFailed</b></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
</tr>
<tr data-morhtml="true">
<td data-morhtml="true" bgcolor="FAF9F9"><b data-morhtml="true">ScaleFailed</b></td>
<td data-morhtml="true"><center data-morhtml="true">x</center></td>
<td data-morhtml="true"> </td>
<td data-morhtml="true"> </td>
</tr>
</table>

Vedere anche
------------

-   [Servizi BizTalk: Provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: Schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servizi BizTalk: Tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [Servizi BizTalk: Limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servizi BizTalk: Nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

