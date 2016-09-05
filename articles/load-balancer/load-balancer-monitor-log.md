<properties 
   pageTitle="Monitorare operazioni, eventi e contatori per il servizio di bilanciamento del carico | Microsoft Azure"
   description="Informazioni su come abilitare gli eventi di avviso e la registrazione dello stato di integrità del probe per il servizio di bilanciamento del carico di Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />  

# Analisi dei log per il servizio di bilanciamento del carico di Azure (anteprima)
In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei bilanciamenti del carico. Alcuni di questi log sono accessibili dal portale e tutti i log possono essere estratti da un archivio BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log.


- **Log di controllo:** è possibile usare i [log di controllo di Azure](../../articles/azure-portal/insights-debugging-with-events.md) (noti in precedenza come log operativi) per visualizzare tutte le operazioni da inviare alle sottoscrizioni di Azure e il relativo stato. I log di controllo sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di Azure.
- **Log eventi di avviso:** è possibile usare questo log per visualizzare gli avvisi generati per il bilanciamento del carico. Le informazioni di stato per il bilanciamento del carico vengono raccolte ogni cinque minuti. Questo log viene scritto solo se viene generato un evento di avviso del bilanciamento del carico.
- **Log del probe di integrità:** è possibile usare questo log per verificare lo stato del controllo integrità del probe, il numero di istanze online nel back-end del bilanciamento del carico e la percentuale di macchine virtuali che riceve il traffico di rete dal bilanciamento del carico. Questo log viene scritto se l'evento dello stato del probe cambia.

>[AZURE.WARNING] I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere la distribuzione di Gestione risorse e distribuzione classica](../../articles/resource-manager-deployment-model.md). <BR> Attualmente l'analisi di log funziona solo per i servizi di bilanciamento del carico con connessione Internet. Questa limitazione è temporanea e può cambiare in qualsiasi momento. Assicurarsi di visualizzare nuovamente questa pagina per verificare le modifiche future.

## Abilitazione della registrazione
La registrazione di controllo viene abilitata automaticamente in qualsiasi momento per ogni risorsa di Gestione risorse. È necessario abilitare la registrazione di eventi e del probe di integrità per iniziare a raccogliere i dati disponibili in tali log. Per abilitare la registrazione, seguire questa procedura.

Accedere al [portale di Azure](http://portal.azure.com). Prima di procedere, [creare un servizio di bilanciamento del carico](load-balancer-get-started-internet-arm-ps.md), se non se ne ha già uno.

Nel portale fare clic su **Sfoglia** >> **Bilanciamenti del carico**.

![portale - bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-browse.png)

Selezionare un bilanciamento del carico esistente >> **Tutte le impostazioni**.

![portale - impostazioni di bilanciamento del carico](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

Nel pannello **Impostazioni** fare clic su **Diagnostica** e quindi nel riquadro **Diagnostica**, accanto a **Stato**, fare clic su **Attivato**. Nel pannello **Impostazioni** fare clic su **Account di archiviazione** e selezionare un account di archiviazione esistente oppure crearne uno nuovo.

Nell'elenco a discesa sotto **Account di archiviazione** selezionare l'opzione per registrare eventi di avviso, lo stato di integrità del probe o entrambi e quindi fare clic su **Salva**.

![Portale di anteprima - Log di diagnostica](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] I log di controllo non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione di eventi e del probe di integrità è previsto un addebito.

## Log di controllo
Questi log (noti in precedenza come "log operativi") vengono generati da Azure per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei registri eventi di Azure. Per altre informazioni su questi log, vedere l'articolo [Visualizzare eventi e log di controllo](../../articles/azure-portal/insights-debugging-with-events.md).

## Log eventi di avviso
Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico, come indicato in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Le informazioni vengono registrate in formato JSON, come illustrato di seguito.

	
	{
    "time": "2016-01-26T10:37:46.6024215Z",
	"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

L'output JSON mostra la proprietà *eventname* che descrive il motivo per cui il bilanciamento del carico ha creato un avviso. In questo caso, l'avviso è stato generato a causa dell'esaurimento delle porte TCP dovuto ai limiti NAT dell'IP di origine (SNAT).

## Log del probe di integrità
Questo log viene generato solo se è stato abilitato per ogni bilanciamento del carico, come indicato in precedenza. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Viene creato un contenitore denominato 'insights-logs-loadbalancerprobehealthstatus' e vengono registrati i dati seguenti:

		{
	    "records":

	    {
	   		"time": "2016-01-26T10:37:46.6024215Z",
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "time": "2016-01-26T10:37:46.6024215Z",
			"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

L'output JSON mostra nel campo proprietà le informazioni di base per lo stato di integrità del probe. La proprietà *dipDownCount* indica il numero totale di istanze nel back-end che non ricevono traffico di rete a causa di risposte del probe non riuscite.

## Visualizzare e analizzare il log di controllo
È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

- **Strumenti di Azure:** recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di anteprima di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](../../articles/resource-group-audit.md).
- **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs) è possibile analizzare i dati con dashboard preconfigurati utilizzabili così come sono o personalizzabili.

## Visualizzare e analizzare il log eventi e del probe di integrità 
È necessario connettersi all'account di archiviazione e recuperare le voci di log JSON per i log eventi e del probe di integrità. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in Github.

## Risorse aggiuntive

- Post di blog sulla [visualizzazione dei log di controllo di Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Post di blog su [visualizzazione e analisi dei log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0824_2016-->