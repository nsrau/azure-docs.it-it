<properties urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="Servizi BizTalk: Risoluzione dei problemi mediante i log operazioni | Azure" metaKeywords="" description="Servizi BizTalk: Risoluzione dei problemi mediante i log operazioni" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="mandia"  solutions="" writer="nitinme" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />


# Servizi BizTalk: Risoluzione dei problemi mediante i log operazioni

I log delle operazioni costituiscono una funzionalità dei servizi di gestione disponibile sul portale di gestione di Azure che consente di visualizzare log cronologici delle operazioni eseguite nei servizi di Azure, incluso il servizio BizTalk. Vengono visualizzati i dati cronologici relativi alle operazioni di gestione nella sottoscrizione dei servizi BizTalk eseguite negli ultimi 180 giorni.

<div class="dev-callout"><b>Nota</b>
<p>Questa funzionalità acquisisce log solo per le operazioni di gestione effettuate su Servizi BizTalk, ad esempio avvio del servizio, backup e così via. Di tali operazioni viene tenuta traccia indipendentemente dal fatto che vengano eseguite dal portale di gestione di Azure o mediante le <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn232347.aspx">API REST di Servizi BizTalk</a>. Per un elenco completo delle operazioni di cui viene tenuta traccia tramite i servizi di gestione, vedere <a href="#bizops">Operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure.</a>.</p>
<p>Non vengono acquisiti log delle attività correlate al runtime dei servizi BizTalk, ad esempio un messaggio elaborato da bridge. Per visualizzare tali log, è necessario usare la visualizzazione Rilevamento del portale di Servizi BizTalk. Per altre informazioni, vedere <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Rilevamento di messaggi</a>.</p>
</div>

##<a name="viewlogs"></a>Visualizzazione dei log operazioni di Servizi BizTalk
1. Nel portale di gestione di Azure fare clic su Management Services, quindi sulla scheda Operation Logs.
2. È possibile filtrare i log in base a diversi parametri quali sottoscrizione, intervallo di date, tipo di servizio (ad esempio Servizi BizTalk), nome del servizio o stato dell'operazione (ad esempio completata o non riuscita).
3. Fare clic sul segno di spunta per visualizzare l'elenco filtrato. Nell'immagine seguente sono mostrate le attività correlate a testbiztalkservice.
	![View operation logs][ViewLogs] 
4. Per visualizzare informazioni più dettagliate su una specifica operazione, selezionare la riga e fare clic su <b>Dettagli</b> nella parte inferiore della pagina.


##<a name="bizops"></a>Operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure
Nella tabella seguente sono elencate le operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">Operazione di creazione di un nuovo servizio BizTalk</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">Operazione di eliminazione di un servizio BizTalk</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">Operazione di riavvio di un servizio BizTalk</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">Operazione di avvio di un servizio BizTalk</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">Operazione di arresto di un servizio BizTalk</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">Operazione di disabilitazione di un servizio BizTalk</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">Operazione di abilitazione di un servizio BizTalk</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">Operazione di backup di un servizio BizTalk</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">Operazione di ripristino di un servizio BizTalk dal backup specificato</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">Operazione di sospensione di un servizio BizTalk</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">Operazione di ripresa di un servizio BizTalk</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">Operazione di scalabilità orizzontale o verticale di un servizio BizTalk</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">Operazione di aggiornamento della configurazione di un servizio BizTalk</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">Operazione di aggiornamento o downgrade di un servizio BizTalk a una versione diversa</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">Operazione di cancellazione dei backup del servizio BizTalk che non rientrano nel periodo di conservazione</td> 
</tr>
</table>


## Vedere anche
- [Backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Ripristino del servizio BizTalk da un backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servizi BizTalk: tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Servizi BizTalk: grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Servizi BizTalk: schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Come iniziare a usare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
