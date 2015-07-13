<properties 
	pageTitle="Guida per sviluppatori di Azure Government" 
	description="Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="1/21/2014" 
	ms.author="jharve"/>


#  Guida per gli sviluppatori di Microsoft Azure Government 

<p> Microsoft Azure Government è un'istanza fisica e con isolamento di rete di Microsoft Azure. Questa guida per sviluppatori fornisce i dettagli sulle differenze che gli sviluppatori e gli amministratori di applicazioni devono conoscere per interagire e usare queste aree separate di Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Contenuto dell'argomento


+ [Panoramica](#Overview)
+ [Linee guida per gli sviluppatori](#Guidance)
+ [Funzionalità attualmente disponibili in Microsoft Azure Government](#Features)
+ [Mapping degli endpoint](#Endpoint)
+ [Passaggi successivi](#next)


## <a name="Overview"></a>Panoramica

Microsoft Azure Government è un'istanza separata del servizio Microsoft Azure creata per soddisfare i requisiti di sicurezza e conformità delle agenzie federali statunitensi, degli enti pubblici statali e locali e dei relativi provider di soluzioni. Azure Government offre isolamento fisico e di rete da distribuzioni non governative ed è gestita da cittadini statunitensi selezionati.

Microsoft fornisce una serie di strumenti per creare e distribuire le applicazioni cloud nel servizio globale di Microsoft Azure (“Servizio globale”) e nei servizi di Microsoft Azure Government.

Al momento della creazione e della distribuzione delle applicazioni nei servizi di Azure Government, è necessario che gli sviluppatori conoscano le differenze principali tra questi servizi e il Servizio globale, con particolare riferimento all'installazione e alla configurazione dell'ambiente di programmazione, alla configurazione degli endpoint, alla scrittura delle applicazioni e alla distribuzione delle stesse come servizi di Azure Government.

Questo documento presenta un riepilogo di tali differenze e integra le informazioni disponibili nel sito [Azure Government](http://www.azure.com/gov "Azure Government") e nella [raccolta di documentazione tecnica di Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") su MSDN. Potrebbero essere disponibili informazioni ufficiali anche in molti altri percorsi, come il [Centro protezione Microsoft Azure](http://azure.microsoft.com/support/trust-center/ "Centro protezione Microsoft Azure"), il [Centro documentazione di Azure](http://azure.microsoft.com/documentation/) e nei [blog su Azure](http://azure.microsoft.com/blog/ "Blog di Azure").

Questo contenuto è destinato ai partner e agli sviluppatori che distribuiscono applicazioni in Microsoft Azure Government.



## <a name="Guidance"></a>Linee guida per gli sviluppatori
La maggior parte dei contenuti tecnici attualmente disponibili presuppone che le applicazioni siano sviluppate per il Servizio globale e non per Microsoft Azure Government, pertanto è importante assicurarsi che gli sviluppatori conoscano le differenze principali per le applicazioni sviluppate per essere ospitate in Azure Government.

- In primo luogo, vi sono differenze in termini di servizi e funzionalità. Questo significa che alcune funzionalità presenti in aree specifiche del Servizio globale potrebbero non essere disponibili in Azure Government.

- In secondo luogo, le funzionalità offerte in Azure Government presentano differenze di configurazione rispetto al Servizio globale. È pertanto necessario esaminare il codice di esempio, le configurazioni e i passaggi per assicurarsi che la creazione e l'esecuzione avvengano all'interno dell'ambiente dei servizi cloud di Azure Government.


## <a name="Features"></a> Funzionalità attualmente disponibili in Microsoft Azure Government
Attualmente Azure Government offre i seguenti servizi disponibili nelle agenzie governative statunitensi dell'Iowa e della Virginia:

- Macchine virtuali
- Microsoft Azure
- Archiviazione
- Active Directory
- Utilità di pianificazione
- Reti virtuali
- Database SQL

Altri servizi sono già disponibili e altri ancora verranno aggiunti regolarmente. Per l'elenco più aggiornato dei servizi, vedere la [pagina aree](http://azure.microsoft.com/regions/#services) in cui sono evidenziate tutte le aree disponibili e i relativi servizi.

Attualmente, le agenzie governative statunitensi dell'Iowa e della Virginia sono i data center di supporto di Azure Government. Fare riferimento alla pagina aree riportata sopra per l'elenco dei centri dati e dei servizi disponibili.

## <a name="Endpoint"></a>Mapping degli endpoint

Usare la seguente tabella come guida quando si esegue il mapping di endpoint pubblici di Microsoft Azure e del database SQL a endpoint specifici di Azure Government.

<table>
<tr style='font-weight:bold'><td>
Tipo di servizio</td><td>	Azure Public</td><td>	Azure Government
</td></tr><tr><td>
Home page di Azure Government</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
Portale di gestione</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
Generale</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
Core	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
Calcolo	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
Archiviazione BLOB</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
Archiviazione di accodamento	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
Archiviazione tabelle</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
Gestione dei servizi</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>Database SQL</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table>

## <a name="next"></a>Passaggi successivi
Per altre informazioni su Azure Government e su come un'organizzazione può qualificarsi per l'accesso, passare alla pagina <A href="http://azure.com/gov">http://www.azure.com/gov</a>

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO1-->