<properties 
	pageTitle="Report di Azure Multi-Factor Authentication" 
	description="Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere utilizzati dall'utente e dall'organizzazione. Questi report sono accessibili tramite il portale di gestione di Multi-Factor Authentication. Di seguito è riportato un elenco dei report disponibili.

È possibile accedere ai report tramite il portale di gestione di Azure.

Nome| Descrizione
:------------- | :------------- | 
Utilizzo | I report di utilizzo consentono di visualizzare informazioni sull'uso generale, un riepilogo e dettagli sull'utente.
Stato server|Questo report consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account.
Cronologia utenti bloccati|Questi report mostrano la cronologia delle richieste per bloccare o sbloccare gli utenti.
Cronologia utenti bypass|Consente di visualizzare la cronologia delle richieste per disabilitare Multi-Factor Authentication per il numero di telefono dell'utente.
Avviso di illecito|Consente di visualizzare una cronologia di avvisi di illecito inviati durante l'intervallo di date specificato dall'utente.
Queued|Consente di elencare i report accodati da elaborare e i relativi stati. Quando il report è completo, viene fornito un collegamento per scaricare o visualizzare il report.

## Per visualizzare i report

1. Accedere a [http://azure.microsoft.com](http://azure.microsoft.com)
2. A sinistra selezionare Active Directory.
3. Nella parte superiore selezionare Provider di multi-factor authentication. Verrà visualizzato un elenco dei provider di Multi-Factor Authentication.
4. Se si dispone di più di un provider di Multi-Factor Authentication, selezionare quello che si desidera per visualizzare il report degli avvisi di illecito e fare clic su Gestisci nella parte inferiore della pagina. Se si dispone di un solo provider, è sufficiente fare clic su Gestisci. Verrà aperto il portale di gestione di Azure Multi-Factor Authentication.
5. Nel portale di gestione di Azure Multi-Factor Authentication, a sinistra si noterà Visualizza un report. Da lì sarà possibile selezionare i report descritti in precedenza.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Risorse aggiuntive**

* [Per gli utenti](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication su MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=AcomDC_1125_2015-->