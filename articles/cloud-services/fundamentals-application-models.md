<properties 
	pageTitle="Opzioni di hosting di calcolo fornite da Azure" 
	description="Informazioni sulle opzioni di hosting di calcolo di Azure e sul relativo funzionamento: Macchine virtuali, Siti Web, Servizi cloud e altre opzioni." 
	headerExpose="" 
	footerExpose="" 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Opzioni di hosting di calcolo fornite da Azure

In Azure sono disponibili diversi modelli di hosting per l'esecuzione di applicazioni. Ognuno fornisce un diverso set di servizi, pertanto quello da scegliere dipende dalle attività che si tenta di eseguire. In questo articolo vengono esaminate le varie opzioni, descrivendo ciascuna tecnologia e fornendo esempi di quando è opportuno usarla.

| Opzioni di calcolo | Destinatari |
| ------------------ | --------   |
| [Servizio app] | App per dispositivi mobili, app per le API, app per la logica e app Web scalabili per qualsiasi dispositivo |
| [Servizi cloud] | App per cloud a più livelli scalabili e altamente disponibili con maggiore controllo del sistema operativo |
| [Macchine virtuali] | Macchine virtuali Windows e Linux personalizzate con controllo completo del sistema operativo |

[AZURE.INCLUDE [content](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [content](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [content](../../includes/virtual-machines-choose-me-content.md)]

## Altre opzioni

Azure inoltre offre altri modelli di hosting di calcolo per scopi più specifici, ad esempio:

* [Servizi mobili](/services/mobile-services/)  
  Ottimizzato per fornire un back-end del cloud per le app eseguite su dispositivi mobili.
* [Batch](/services/batch/)  
  Ottimizzato per l'elaborazione di volumi elevati di operazioni simili, idealmente carichi di lavoro che si prestano all'esecuzione di attività parallele su più computer.
* [HDInsight (Hadoop)](/services/hdinsight/)  
  Ottimizzato per l'esecuzione di processi [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) nei cluster Hadoop. 

## Scelta del modello da usare

Tutti e tre i modelli di hosting di calcolo di Azure per scopi generici consentono di creare applicazioni scalabili e affidabili nel cloud. La scelta del modello da usare, pertanto,

Servizio app è la scelta migliore per la maggior parte delle app Web. La distribuzione e la gestione sono integrate nella piattaforma ed è possibile scalare rapidamente i siti per gestire carichi di traffico elevato; inoltre, il bilanciamento del carico e la gestione del traffico predefiniti offrono disponibilità elevata. È possibile spostare facilmente siti esistenti in Servizio app con uno [strumento di migrazione online](https://www.migratetoazure.net/), usare un'app open source della raccolta di applicazioni Web oppure creare un nuovo sito tramite il framework e gli strumenti di propria scelta. La funzionalità [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) consente di aggiungere facilmente l'elaborazione di processi in background all'applicazione, nonché di eseguire un carico di lavoro di calcolo che non è un'app Web.

Per esercitare un controllo maggiore sull'ambiente server Web, ad esempio poter accedere in remoto al server o configurare attività di avvio del server, Servizi cloud di Azure è in genere l'opzione migliore.

Se è presente un'applicazione esistente la cui esecuzione in Siti Web di Azure o Servizi cloud di Azure richiederebbe modifiche sostanziali, è possibile scegliere Macchine virtuali di Azure allo scopo di semplificare la migrazione al cloud. Tuttavia, la corretta configurazione, la protezione e la gestione delle VM richiede molto più tempo e competenza IT rispetto a Siti Web di Azure e a Servizi cloud. Se si sta valutando la possibilità di usare Macchine virtuali di Azure, assicurarsi di tenere conto delle operazioni di manutenzione continua necessarie per le attività di applicazione di patch, aggiornamento e gestione relative all'ambiente di VM.

In alcuni casi nessuna opzione singola è corretta ed è pertanto possibile combinare diverse opzioni. Ad esempio, si sta creando un'applicazione per la quale si desiderano i vantaggi a livello di gestione offerti dai ruoli Web di Servizi cloud ma è anche necessario usare SQL Server standard ospitato in una macchina virtuale per motivi di compatibilità o prestazioni.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[Servizio app]: #tellmeas
[Macchine virtuali]: #tellmevm
[Servizi cloud]: #tellmecs

## Passaggi successivi

* [Confronto](../choose-web-site-cloud-service-vm/) tra Servizio app, Servizi cloud e Macchine virtuali
* Informazioni su [Servizio app](../app-service-web-overview.md)
* Informazioni su [Servizio cloud](services/cloud-services/)
* Informazioni su [Macchine virtuali](https://msdn.microsoft.com/library/azure/jj156143.aspx) 

<!---HONumber=Oct15_HO3-->