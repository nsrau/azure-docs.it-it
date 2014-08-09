<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlling Windows Azure Web Sites Traffic with Azure Traffic Manager" pageTitle="Controlling Azure Web Sites Traffic with Azure Traffic Manager" metaKeywords="Azure Web Sites, Traffic Manager, request routing, round robin, failover, performance" description="This article provides summary information for  Azure Traffic Manager as it relates to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Controlling Azure Web Sites Traffic with Azure Traffic Manager" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Controllo del traffico dei siti Web di Azure con Gestione traffico di Azure
===========================================================================

> [WACOM.NOTE] In questo articolo vengono fornite informazioni di riepilogo per Gestione traffico di Microsoft Azure in relazione a Siti Web di Azure. Per ulteriori informazioni su Gestione traffico di Azure, visitare i collegamenti forniti alla fine di questo articolo.

Introduzione
------------

È possibile utilizzare Gestione traffico di Azure per controllare il modo in cui le richieste dai client Web vengono distribuite in Siti Web di Azure. Quando si aggiungono endpoint di siti Web di Azure in un profilo di Gestione traffico di Azure, quest'ultimo tiene traccia dello stato dei siti Web (in esecuzione, arrestati o eliminati) in modo da poter decidere quali di questi endpoint devono ricevere traffico.

Metodi di bilanciamento del carico.
-----------------------------------

Gestione traffico di Azure utilizza tre metodi diversi per il bilanciamento del carico. Questi vengono descritti nell'elenco seguente, in quanto pertinenti a Siti Web di Azure.

-   **Failover**: se sono presenti cloni di siti Web in aree diverse, è possibile utilizzare questo metodo per configurare un sito Web in modo da gestire tutto il traffico dei client Web e un altro, in un'area diversa, per gestire tale traffico nel caso in cui il primo sito Web risulti non disponibile.

-   **Round Robin**: se sono presenti cloni di siti Web in aree diverse, è possibile utilizzare questo metodo per distribuire equamente il traffico tra siti Web in aree diverse.

-   **Prestazioni**: il metodo Prestazioni consente di distribuire il traffico in base al tempo di round trip più breve per il raggiungimento dei client. Questo metodo può essere utilizzato sia per siti Web all'interno della stessa area che per siti in aree diverse.

Per ulteriori informazioni sul bilanciamento del carico in Gestione traffico di Azure, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/dn339010.aspx).

Siti Web di Azure e profili di Gestione traffico
------------------------------------------------

Per configurare il controllo del traffico dei siti Web, è possibile creare un profilo in Gestione traffico di Azure che utilizzi uno dei tre metodi di bilanciamento del carico, descritti in precedenza, e quindi aggiunga gli endpoint (in questo caso i siti Web) per i quali si desidera controllare il traffico tramite il profilo. Lo stato dei siti Web (in esecuzione, arrestato o eliminato) viene comunicato regolarmente al profilo in modo che Gestione traffico di Azure possa instradare correttamente il traffico.

Quando si utilizza Gestione traffico con Azure, è opportuno tenere presenti i fattori seguenti:

-   Per distribuzioni di soli siti Web all'interno della stessa area, in Siti Web di Azure sono già fornite funzionalità di failover e round robin che non tengono conto della modalità dei siti Web.

-   Per distribuzioni nella stessa area in cui Siti Web di Azure viene utilizzato insieme a un altro servizio cloud di Azure, è possibile combinare entrambi tipi di endpoint per abilitare scenari ibridi.

-   È possibile specificare solo un endpoint del sito Web per area in un profilo. Quando si seleziona un sito Web come endpoint per un'area, i rimanenti siti Web non saranno più disponibili per la selezione in quel profilo.

-   Gli endpoint dei siti Web che si specificano in un profilo di Gestione traffico di Azure verranno visualizzati nella sezione **Domain Names** della pagina Configure per i siti Web nel profilo, ma non saranno configurabili in quel contesto.

-   Dopo l'aggiunta di un sito Web a un profilo, in **Site URL** nel dashboard della pagina del portale del sito Web verrà visualizzato l'URL del dominio personalizzato del sito Web, se configurato. In caso contrario, verrà visualizzato l'URL del profilo di Gestione traffico, ad esempio `contoso.trafficmgr.com`. Sia il nome di dominio diretto del sito Web che l'URL di Gestione traffico saranno visibili nella pagina Configure del sito Web nella sezione **Domain Names**.

-   I nomi di dominio personalizzato funzioneranno come previsto, ma oltre ad aggiungerli ai siti Web sarà necessario configurare il mapping DNS in modo da puntare all'URL di Gestione traffico. Per informazioni sulla configurazione di un nome di dominio personalizzato per un sito Web di Azure, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](https://www.windowsazure.com/it-it/documentation/articles/web-sites-custom-domain-name/).

-   È possibile aggiungere a Gestione traffico di Azure solo siti Web che si trovano in modalità standard.

Passaggi successivi
-------------------

Per una panoramica concettuale e tecnica di Gestione traffico di Azure, vedere [Panoramica di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/hh744833.aspx).

Per informazioni sulla configurazione di Gestione traffico di Azure, anche per l'utilizzo con Siti Web di Azure, vedere [Attività di configurazione di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/hh744830.aspx).

Per ulteriori informazioni sul bilanciamento del carico in Gestione traffico di Azure, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](http://msdn.microsoft.com/it-it/library/windowsazure/dn339010.aspx).

