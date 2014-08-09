<properties linkid="biztalk-troubleshoot-using-ops-logs" urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="BizTalk Services: Troubleshoot using ops logs | Azure" metaKeywords="" description="BizTalk Services: Troubleshoot using ops logs" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="" solutions="" writer="nitinme" manager="paulettm" editor="cgronlun" />

Servizi BizTalk: risoluzione dei problemi mediante i log operazioni
===================================================================

I log operazioni sono una funzionalità dei servizi di gestione disponibile sul portale di gestione di Azure che consente di visualizzare log cronologici delle operazioni eseguite nei servizi di Azure, incluso il servizio BizTalk. Consente di visualizzare i dati cronologici relativi alle operazioni di gestione nella sottoscrizione dei servizi BizTalk eseguite negli ultimi 180 giorni.

**Nota**

Questa funzionalità acquisisce log solo per le operazioni di gestione effettuate su Servizi BizTalk, ad esempio avvio del servizio, backup e così via. Di tali operazioni viene tenuta traccia indipendentemente dal fatto che vengano eseguite dal portale di gestione di Azure o mediante le [API REST di Servizi BizTalk](http://msdn.microsoft.com/it-it/library/windowsazure/dn232347.aspx). Per un elenco completo delle operazioni di cui viene tenuta traccia tramite i servizi di gestione, vedere [Operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure](#bizops).

Non vengono acquisiti log delle attività correlate al runtime dei servizi BizTalk, ad esempio un messaggio elaborato da bridge. Per visualizzare tali log, è necessario utilizzare la visualizzazione Rilevamento del portale di Servizi BizTalk. Per ulteriori informazioni, vedere [Rilevamento di messaggi](http://msdn.microsoft.com/library/windowsazure/hh949805.aspx).

Visualizzazione dei log operazioni di Servizi BizTalk
-----------------------------------------------------

1.  Nel portale di gestione di Azure Management fare clic su Management Services, quindi sulla scheda Operation Logs.
2.  È possibile filtrare i log in base a diversi parametri quali sottoscrizione, intervallo di date, tipo di servizio (ad esempio Servizi BizTalk), nome del servizio o stato dell'operazione (ad esempio completata o non riuscita).
3.  Fare clic sul segno di spunta per visualizzare l'elenco filtrato. Nell'immagine seguente sono mostrate le attività correlate a testbiztalkservice. 
    ![Visualizzazione dei log operazioni](./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png)

4.  Per visualizzare informazioni più dettagliate su una specifica operazione, selezionare la riga e fare clic su **Details** nella parte inferiore della pagina.

Operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure
-----------------------------------------------------------------------------

Nella tabella seguente sono elencate le operazioni di cui viene tenuta traccia tramite i servizi di gestione di Azure.

<table data-morhtml="true" border="1" cellpadding="5">
<tr data-morhtml="true">
<td data-morhtml="true">CreateBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di creazione di un nuovo servizio BizTalk</td> 
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">DeleteBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di eliminazione di un servizio BizTalk</td>  
</tr> 
<tr data-morhtml="true">
<td data-morhtml="true">RestartBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di riavvio di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">StartBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di avvio di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">StopBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di arresto di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">DisableBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di disabilitazione di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">EnableBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di abilitazione di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">BackupBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di backup di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">RestoreBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di ripristino di un servizio BizTalk dal backup specificato</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">SuspendBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di sospensione di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ResumeBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di ripresa di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ScaleBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di scalabilit&agrave; orizzontale o verticale di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ConfigUpdateBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di aggiornamento della configurazione di un servizio BizTalk</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">ServiceUpdateBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di aggiornamento o downgrade di un servizio BizTalk a una versione diversa</td> 
</tr>
<tr data-morhtml="true">
<td data-morhtml="true">PurgeBackupBizTalkService</td> 
<td data-morhtml="true" align="left">Operazione di cancellazione dei backup del servizio BizTalk che non rientrano nel periodo di conservazione</td> 
</tr>
</table>


Vedere anche
------------

-   [Backup del servizio BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Ripristino del servizio BizTalk da un backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [Servizi BizTalk: tabella delle edizioni Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [Servizi BizTalk: provisioning tramite il portale di gestione di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [Servizi BizTalk: grafico dello stato di provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Servizi BizTalk: schede Dashboard, Monitor e Scale](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [Servizi BizTalk: nome e chiave dell'autorità emittente](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Come iniziare a utilizzare l'SDK di Servizi BizTalk di Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

