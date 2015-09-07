<properties
   pageTitle="Gestione endpoint in Gestione traffico di Azure | Microsoft Azure"
	description="Questo articolo aiuterà ad aggiungere, rimuovere, abilitare e disabilitare gli endpoint da Gestione traffico di Azure."
	services="traffic-manager"
	documentationCenter=""
	authors="joaoma"
	manager="adinah"
	editor="tysonn"/>
<tags
   ms.service="traffic-manager"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="joaoma"/>

# Aggiungere, disabilitare, abilitare o eliminare gli endpoint

La funzionalità App Web nell’Azure App Service offre già funzionalità di routing del traffico failover e round robin per i siti Web che si trovano all'interno di un data center, indipendentemente dalla modalità del sito Web. Gestione traffico di Azure consente di specificare il routing del traffico failover e round robin per i siti Web e i servizi cloud che si trovano in data center diversi. Il primo passaggio necessario a fornire tale funzionalità consiste nell'aggiungere l'endpoint del servizio cloud o del sito Web a Gestione traffico.

>[AZURE.NOTE]Non è possibile aggiungere i percorsi esterni o i profili di Gestione traffico come endpoint mediante il portale di Azure. È necessario usare l'API REST [Create Definition](http://go.microsoft.com/fwlink/p/?LinkId=400772) oppure [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774) di Windows PowerShell.

È anche possibile disabilitare singoli endpoint che appartengono a un profilo di Gestione traffico. Gli endpoint includono servizi cloud e siti Web. Quando si disabilita un endpoint questo continua a far parte del profilo, sebbene quest'ultimo si comporti come se non fosse incluso. Questa azione è particolarmente utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo.

>[AZURE.NOTE]La disabilitazione di un endpoint non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un endpoint integro rimane attivo e in grado di ricevere traffico anche se è disabilitato in Gestione traffico. La disabilitazione di un endpoint in un profilo non influisce sul relativo stato in un altro profilo.

## Per aggiungere un servizio cloud o un endpoint del sito Web


1. Nel riquadro di Gestione traffico nel portale di Azure individuare il profilo di Gestione traffico in cui sono contenute le impostazioni degli endpoint da modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Nella parte superiore della pagina fare clic su **Endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella parte inferiore della pagina fare clic su **Aggiungi** per accedere alla pagina **Aggiungi endpoint del servizio**. Per impostazione predefinita, in **Endpoint di servizio** sono elencati i servizi cloud.
4. Per i servizi cloud, selezionare nell'elenco quelli che si desidera abilitare come endpoint per il profilo specificato. Se si cancella il nome del servizio cloud, questo viene rimosso dall'elenco degli endpoint.
5. Per i siti Web fare clic sull'elenco a discesa **Tipo di servizio** e selezionare **app Web**.
6. Selezionare i siti Web nell'elenco e aggiungerli come endpoint per il profilo. Se si cancella il nome del sito Web, questo viene rimosso dall'elenco degli endpoint. È possibile selezionare solo un sito Web per ogni data center (noto anche come area) di Azure. Se si seleziona un sito Web in un data center che ospita più siti Web, una volta selezionato il primo, non è più possibile selezionare gli altri siti Web dello stesso data center. Vengono elencati solo i siti Web standard.
7. Dopo aver selezionato gli endpoint per questo profilo, fare clic sul segno di spunta in basso a destra per salvare le modifiche.

>[AZURE.NOTE]Se si usa il metodo di routing del traffico *Failover*, dopo aver aggiunto o rimosso un endpoint, assicurarsi di regolare l'elenco di priorità del failover nella pagina di configurazione per riflettere l'ordine di failover desiderato per la configurazione. Per ulteriori informazioni, vedere [Configurare il routing del traffico failover](traffic-manager-configure-failover-load-balancing.md).

## Per disabilitare un endpoint

1. Nel riquadro di Gestione traffico nel portale di Azure individuare il profilo di Gestione traffico in cui sono contenute le impostazioni degli endpoint da modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoint** nella parte superiore della pagina.
3. Fare clic sull'endpoint da disabilitare, quindi fare clic su **Disabilita** nella parte inferiore della pagina.
4. Il traffico indirizzato all'endpoint verrà arrestato in base alla durata (TTL) del DNS configurato per il nome di dominio di Gestione traffico. È possibile modificare la durata (TTL) dalla pagina di configurazione del profilo di Gestione traffico.

## Per abilitare un endpoint

1. Nel riquadro di Gestione traffico nel portale di Azure individuare il profilo di Gestione traffico in cui sono contenute le impostazioni degli endpoint da modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoint** nella parte superiore della pagina.
3. Fare clic sull'endpoint da abilitare, quindi fare clic su **Abilita** nella parte inferiore della pagina.
4. Il traffico indirizzato al servizio ospitato riprenderà di nuovo secondo il profilo.

## Per eliminare un servizio cloud o un endpoint del sito Web


1. Nel riquadro di Gestione traffico nel portale di Azure individuare il profilo di Gestione traffico in cui sono contenute le impostazioni degli endpoint da modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
2. Nella parte superiore della pagina fare clic su **Endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella pagina Endpoint fare clic sul nome dell'endpoint che si desidera eliminare dal profilo.
4. Nella parte inferiore della pagina fare clic su **Elimina**.

>[AZURE.NOTE]Non è possibile eliminare i percorsi esterni o i profili di Gestione traffico come endpoint mediante il portale di Azure. È necessario usare Windows PowerShell. Per altre informazioni, vedere [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Risorse aggiuntive

[Panoramica di Gestione traffico](traffic-manager-overview.md)

[Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md)

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!---HONumber=August15_HO9-->