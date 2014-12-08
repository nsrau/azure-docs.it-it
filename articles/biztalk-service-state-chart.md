<properties urlDisplayName="BizTalk Services: Service state chart" pageTitle="Servizi BizTalk: Tabella degli stati del servizio | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />



# Servizi BizTalk: Tabella degli stati del servizio
Le operazioni che è possibile eseguire nel servizio BizTalk variano a seconda dello stato corrente del servizio.

Se ad esempio si esegue il provisioning di un nuovo servizio BizTalk nel portale di gestione di Azure, al termine lo stato del servizio BizTalk risulta Active. Se lo stato è attivo, è possibile arrestare il servizio BizTalk. Se l'arresto viene eseguito, il servizio BizTalk passa allo stato Stopped. Se l'arresto non viene eseguito, il servizio BizTalk passa allo stato StopFailed. In questo stato è possibile riavviare il servizio BizTalk. Se si tenta di eseguire un'operazione non consentita, ad esempio riprendere il servizio BizTalk, viene restituito l'errore seguente:

**Operation not allowed**

Per eseguire il provisioning di un servizio BizTalk, vedere [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Nelle tabelle seguenti sono elencate le operazioni consentite quando il servizio BizTalk si trova in uno stato specifico. Il segno di spunta indica che l'operazione può essere eseguita in quel determinato stato. Uno spazio vuoto indica che l'operazione non può essere eseguita in quel determinato stato.

#### Operazioni di avvio, arresto, riavvio, sospensione, ripresa ed eliminazione
<table border="1">
<tr>
        <th colspan="15">Operazione</th>
</tr>

<tr>
        <th rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Inizia</th>
        <th>Arresto</th>
        <th>Riavvio</th>
        <th>Sospensione</th>
        <th>Ripresa</th>
        <th>Eliminazione</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Stopped</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

####Operazioni di scala, aggiornamento della configurazione e backup
<table border="1">
<tr>
        <th colspan="15">Operazione</th>
</tr>

<tr>
        <th rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Scala</th>
        <th>Aggiornamento della configurazione</th>
        <th>Backup</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Active</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disabled</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspended</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Stopped</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Service Update Failed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## Vedere anche
- [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servizi BizTalk: schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi BizTalk: tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)


