<properties 
	pageTitle="Controllo del traffico dei siti Web di Azure con Gestione traffico di Azure" 
	description="In questo articolo vengono fornite informazioni di riepilogo per Gestione traffico di Azure in relazione a Siti Web di Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Controllo del traffico dei siti Web di Azure con Gestione traffico di Azure

> [AZURE.NOTE] In questo articolo vengono fornite informazioni di riepilogo per Gestione traffico di Microsoft Azure in relazione a Siti Web di Azure. Per altre informazioni su Gestione traffico di Azure, visitare i collegamenti forniti alla fine di questo articolo.

## Introduzione
È possibile usare Gestione traffico di Azure per controllare il modo in cui le richieste dai client Web vengono distribuite in Siti Web di Azure. Quando si aggiungono endpoint di siti Web di Azure in un profilo di Gestione traffico di Azure, quest'ultimo tiene traccia dello stato dei siti Web (in esecuzione, arrestati o eliminati) in modo da poter decidere quali di questi endpoint devono ricevere traffico.

## Metodi di bilanciamento del carico.
Gestione traffico di Azure usa tre metodi diversi per il bilanciamento del carico, descritti nell'elenco seguente in quanto pertinenti a Siti Web di Azure. 

* **Failover**: se sono presenti cloni di siti Web in aree diverse, è possibile usare questo metodo per configurare un sito Web in modo da gestire tutto il traffico dei client Web e un altro, in un'area diversa, per gestire tale traffico nel caso di non disponibilità del primo sito Web. 
	
* **Round robin**: se sono presenti cloni di siti Web in aree diverse, è possibile usare questo metodo per distribuire equamente il traffico tra siti Web in aree diverse. 
	
* **Prestazioni**: il metodo Prestazioni consente di distribuire il traffico in base al tempo di round trip più breve per il raggiungimento dei client. Questo metodo può essere usato sia per siti Web all'interno della stessa area che per siti in aree diverse. 

Per altre informazioni sul bilanciamento del carico in Gestione traffico di Azure, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/dn339010.aspx).

##Siti Web di Azure e profili di Gestione traffico 
Per configurare il controllo del traffico dei siti Web, è possibile creare un profilo in Gestione traffico di Azure che usi uno dei tre metodi di bilanciamento del carico descritti in precedenza e quindi aggiunga gli endpoint (in questo caso i siti Web) per i quali si vuole controllare il traffico tramite il profilo. Lo stato dei siti Web (in esecuzione, arrestato o eliminato) viene comunicato regolarmente al profilo in modo che Gestione traffico di Azure possa instradare correttamente il traffico.

Quando si utilizza Gestione traffico con Azure, è opportuno tenere presenti i fattori seguenti:

* Per distribuzioni di soli siti Web all'interno della stessa area, Siti Web di Azure fornisce già funzionalità di failover e round robin che non tengono conto della modalità dei siti Web.

* Per distribuzioni nella stessa area in cui Siti Web di Azure viene usato insieme a un altro servizio cloud di Azure, è possibile combinare entrambi tipi di endpoint per abilitare scenari ibridi.

* È possibile specificare solo un endpoint del sito Web per area in un profilo. Quando si seleziona un sito Web come endpoint per un'area, i rimanenti siti Web non saranno più disponibili per la selezione in quel profilo.

* Gli endpoint dei siti Web specificati in un profilo di Gestione traffico di Azure verranno visualizzati nella sezione **Nomi di dominio** della pagina Configura per i siti Web nel profilo, ma non saranno configurabili in quel contesto.

* Dopo l'aggiunta di un sito Web a un profilo, in **URL sito** nel dashboard della pagina del portale del sito Web verrà visualizzato l'URL del dominio personalizzato del sito Web, se configurato. In caso contrario, verrà visualizzato l'URL del profilo di Gestione traffico, ad esempio `contoso.trafficmgr.com`. Sia il nome di dominio diretto del sito Web che l'URL di Gestione traffico saranno visibili nella pagina Configura del sito Web nella sezione **Nomi di dominio**.

* I nomi di dominio personalizzato funzioneranno come previsto, ma oltre ad aggiungerli ai siti Web è necessario configurare il mapping DNS in modo da puntare all'URL di Gestione traffico. Per informazioni sulla configurazione di un nome di dominio personalizzato per un sito Web di Azure, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](https://www.windowsazure.com/it-it/documentation/articles/web-sites-custom-domain-name/).

* È possibile aggiungere a Gestione traffico di Azure solo siti Web in modalità Standard.

## Passaggi successivi

Per informazioni generali di carattere concettuale e tecnico su Gestione traffico di Azure, vedere [Panoramica di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/hh744833.aspx). 

Per informazioni sulla configurazione di Gestione traffico di Azure, anche per l'uso con Siti Web di Azure, vedere [Attività di configurazione di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/hh744830.aspx).

Per altre informazioni sul bilanciamento del carico in Gestione traffico di Azure, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/dn339010.aspx).

Per altre informazioni sull'uso di Gestione traffico con Siti Web di Azure, vedere i post di blog relativi all' 
[uso di Gestione traffico di Azure con Siti Web di Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) e all'[integrazione di Gestione traffico di Azure con Siti Web di Azure](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).


<!--HONumber=42-->
