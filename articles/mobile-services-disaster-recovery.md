<properties 
	pageTitle="Ripristinare il servizio mobile in caso di emergenza - Servizi mobili di Azure" 
	description="Informazioni su come ripristinare il servizio mobile in caso di emergenza." 
	services="mobile-services" 
	documentationCenter="" 
	authors="brettsam" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="brettsam"/>

# Ripristinare il servizio mobile in caso di emergenza

Quando si usa Servizi mobili di Azure per distribuire un'applicazione, è possibile fare uso delle funzionalità incorporate per garantire la continuità aziendale nel caso in cui si verifichino problemi di disponibilità dovuti, ad esempio, a errori dei server, interruzioni della connettività di rete, perdite di dati e danni diffusi a livello di struttura. Distribuendo l'applicazione tramite Servizi mobili di Azure, sarà possibile sfruttare molte funzionalità di tolleranza di errore e di infrastruttura che sarebbe altrimenti necessario progettare, implementare e gestire se si effettuasse la distribuzione in una soluzione locale tradizionale. Con Azure è possibile mitigare una vasta gamma di potenziali errori a un costo nettamente inferiore.

<h2><a name="prepare"></a>Prepararsi per possibili emergenze</h2>

Per semplificare il ripristino nel caso in cui si verifichi un problema di disponibilità, è possibile prepararsi in anticipo a farvi ricorso: 

+ **Eseguire il backup dei dati nel database SQL del servizio mobile di Azure**
	<br/>I dati dell'applicazione del servizio mobile vengono archiviati in un database SQL di Azure. È consigliabile eseguirne il backup come descritto nelle linee guida disponibili nelle [Linee guida sulla continuità aziendale del database SQL di Azure].
+ **Eseguire il backup degli script del servizio mobile**
	<br/>È consigliabile archiviare gli script del servizio mobile in un sistema di controllo del codice sorgente come [Team Foundation Service] o [GitHub] e non fare affidamento solo sulle copie presenti nel servizio mobile stesso. È possibile scaricare gli script tramite il portale di Azure usando la [funzionalità per il controllo del codice sorgente][funzionalità di controllo del codice sorgente] di Servizi mobili, oppure [usando lo strumento da riga di comando di Azure][uso dello strumento da riga di comando di Azure]. Prestare particolare attenzione alle funzionalità contrassegnare come in anteprima nel portale, poiché il ripristino per tali script non è garantito e potrebbe essere necessario ripristinarli dall'originale nel proprio programma di controllo del codice sorgente.
+ **Riservare un servizio mobile secondario**
	<br/>In caso di problemi di disponibilità del servizio mobile, potrebbe essere necessario ridistribuirlo in un'altra area di Azure. Per garantire la disponibilità della capacità, ad esempio in circostanze rare come la perdita di un'intera area, è consigliabile creare un servizio mobile secondario nell'area alternativa e impostarne la modalità su un valore equivalente o superiore rispetto a quella del servizio primario. Se il servizio primario è in modalità condivisa, è possibile impostare il servizio secondario come condiviso o riservato. Se invece il servizio primario è in modalità riservata, quello secondario dovrà essere impostato nella stessa modalità.


<h2><a name="watch"></a>Controllare i sintomi di eventuali problemi</h2>

Le seguenti circostanze sono indicative di problemi che potrebbero richiedere un'operazione di ripristino:

+ Le applicazioni connesse al servizio mobile non comunicano con questo per un periodo di tempo prolungato.
+ Lo stato del servizio mobile è visualizzato come **Unhealthy** nel [portale di Azure].
+ Nel portale di Azure viene visualizzata un'intestazione **Unhealthy** sopra ogni scheda del servizio mobile e le operazioni di gestione restituiscono messaggi di errore.
+ Il [dashboard dei servizi mobili di Azure][Dashboard servizi di Azure] indica un problema di disponibilità.

<h2><a name="recover"></a>Eseguire un ripristino di emergenza</h2>

Quando si verifica un problema, usare il Dashboard servizi per ricevere indicazioni e aggiornamenti.
 
Se richiesto dal dashboard dei servizi, seguire questa procedura per ripristinare il servizio mobile allo stato di esecuzione in un'area di Azure alternativa. Se è stato preventivamente creato un servizio secondario, la capacità di tale servizio verrà usata per ripristinare il servizio primario. Poiché l'URL e la chiave applicazione del servizio primario resta invariata dopo il ripristino, non sarà necessario aggiornare nessuna delle app che vi fanno riferimento. 

Per ripristinare il servizio mobile in seguito a un'interruzione:

1. Nel portale di Azure verificare che lo stato del servizio corrisponda a **Unhealthy**.

2. Se si è già riservato un servizio mobile secondario, ignorare il prossimo passaggio.

   Se non si è ancora riservato un servizio mobile secondario, crearne uno ora in un'altra area di Azure. Impostare la modalità del nuovo servizio su un livello equivalente o superiore rispetto alla modalità del servizio primario. Se il servizio primario è in modalità condivisa, è possibile impostare il servizio secondario come condiviso o riservato. Se invece il servizio primario è in modalità riservata, quello secondario dovrà essere impostato nella stessa modalità.

3. Configurare gli strumenti da riga di comando di Azure in modo che funzionino correttamente con la sottoscrizione, come descritto nell'articolo [Automatizzare i servizi mobili con gli strumenti da riga di comando].

4. È ora possibile usare il servizio secondario per ripristinare quello primario.

    > [AZURE.IMPORTANT] Quando si esegue il comando in questo passaggio, il servizio secondario viene eliminato per fare in modo che la relativa capacità possa essere usata per il ripristino del servizio primario. Se si intende conservare gli script e le impostazioni, è consigliabile eseguirne il backup prima di eseguire il comando.
    
   A questo punto è possibile eseguire il seguente comando:

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] La visualizzazione delle modifiche del portale dopo il completamento del comando potrebbe richiedere qualche minuto.

5. Verificare che il ripristino di tutti gli script sia avvenuto correttamente confrontandoli con i rispettivi originali nel controllo del codice sorgente. Nella maggior parte dei casi, gli script vengono ripristinati automaticamente senza perdite di dati. Se tuttavia si dovessero riscontrare discrepanze per uno degli script, sarà possibile ripristinarlo manualmente.

6. Verificare che il servizio ripristinato comunichi con il database SQL di Azure. Il comando di ripristino consente di ripristinare il servizio mobile, ma viene mantenuta la connessione al database originale. Se il problema nell'area primaria di Azure riguarda anche il database, è possibile che il servizio ripristinato continui a non funzionare correttamente. È possibile usare il Dashboard servizi di Azure per esaminare lo stato del database per una determinata regione. Se il database originale non è in funzione, sarà possibile ripristinarlo:
	+ Ripristinare il database SQL di Azure nell'area di Azure in cui si è ripristinato il servizio mobile, come descritto nelle [linee guida relative alla continuità aziendale nel database SQL][Linee guida sulla continuità aziendale del database SQL di Azure].
	+ Nella scheda **"Configura"** relativa al servizio mobile del portale di Azure, scegliere "Cambia database", quindi selezionare il database appena ripristinato.

Si avrà ora una situazione in cui il servizio mobile è stato ripristinato in una nuova area di Azure e accetta traffico dalle app dello Store usando il proprio URL originale.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Linee guida sulla continuità aziendale del database SQL di Azure]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[funzionalità di controllo del codice sorgente]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[uso dello strumento da riga di comando di Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Portale di Azure]: http://manage.windowsazure.com/
[Dashboard servizi di Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatizzare i servizi mobili con gli strumenti da riga di comando]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!--HONumber=47-->
