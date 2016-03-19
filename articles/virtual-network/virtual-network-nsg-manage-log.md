<properties 
   pageTitle="Monitorare operazioni, eventi e contatori per i gruppi di sicurezza di rete | Microsoft Azure"
   description="Informazioni su come abilitare la registrazione di contatori, eventi e operazioni per i gruppi di sicurezza di rete"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#Analisi dei log per i gruppi di sicurezza di rete

In Azure è possibile usare diversi tipi di log per gestire e risolvere i problemi dei gruppi di sicurezza di rete. Alcuni di questi log sono accessibili dal portale e tutti i log possono essere estratti da un archivio BLOB di Azure e visualizzati in diversi strumenti, ad esempio Excel e PowerBI. L'elenco seguente contiene altre informazioni sui diversi tipi di log.

- **Log di controllo:** è possibile usare i [log di controllo di Azure](insights-debugging-with-events.md) (noti in precedenza come log operativi) per visualizzare tutte le operazioni da inviare alle sottoscrizioni di Azure e il relativo stato. I log di controllo sono abilitati per impostazione predefinita e possono essere visualizzati nel portale di anteprima di Azure.
- **Registri eventi:** è possibile usare questi log per visualizzare quali regole dei gruppi di sicurezza di rete sono applicate alle VM e ai ruoli delle istanze in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi. 
- **Registri contatori:** è possibile usare questi log per visualizzare quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico.

>[AZURE.WARNING] I log sono disponibili solo per le risorse distribuite nel modello di distribuzione di Gestione risorse. Non è possibile usare i log per le risorse nel modello di distribuzione classica. Per altre informazioni sui due modelli, vedere l'articolo [Comprendere la distribuzione di Gestione risorse e distribuzione classica](resource-manager-deployment-model.md).

##Abilitazione della registrazione
La registrazione di controllo viene abilitata automaticamente in qualsiasi momento per ogni risorsa di Gestione risorse. È necessario abilitare la registrazione eventi e contatori per iniziare a raccogliere i dati disponibili in tali log. Per abilitare la registrazione, seguire questa procedura.

1.  Accedere al [portale di anteprima di Azure](https://portal.azure.com). Se non ne esiste già uno, è possibile [creare un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-ps.md) prima di continuare. 

2.  Nel portale di anteprima fare clic su **Sfoglia>** >> **Gruppi di sicurezza di rete**.

	![Portale di anteprima - Gruppi di sicurezza di rete](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. Selezionare un gruppo di sicurezza di rete esistente.

	![Portale di anteprima - Impostazioni dei gruppi di sicurezza di rete](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. Nel pannello **Impostazioni** fare clic su **Diagnostica** e quindi nel riquadro **Diagnostica** fare clic su **OK** accanto a **Stato**
5. Nel pannello **Impostazioni** fare clic su **Account di archiviazione** e selezionare un account di archiviazione esistente o crearne uno nuovo.  

>[AZURE.INFORMATION] I log di controllo non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione eventi e regole è previsto un addebito.

6. Nell'elenco a discesa sotto **Account di archiviazione** selezionare l'opzione per registrare gli eventi, i contatori o entrambi e quindi fare clic su **Salva**.

	![Portale di anteprima - Log di diagnostica](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## Log di controllo
Questi log (noti in precedenza come "log operativi") vengono generati da Azure per impostazione predefinita. I log vengono conservati per 90 giorni nell'archivio dei registri eventi di Azure. Per altre informazioni su questi log, vedere l'articolo [Visualizzare eventi e log di controllo](insights-debugging-with-events.md).

## Registri contatori
Questi log vengono generati solo se sono stati abilitati per ogni gruppo di sicurezza di rete, come indicato sopra. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Ogni regola applicata alle risorse viene registrata in formato JSON, come illustrato di seguito.

	{
		"time": "2015-09-11T23:14:22.6940000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupRuleCounter",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupCounters",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"DenyAllOutBound",
			"direction":"Out",
			"type":"block",
			"matchedConnections":0
			}
	}

## Registro eventi
Questi log vengono generati solo se sono stati abilitati per ogni gruppo di sicurezza di rete, come indicato sopra. I dati vengono archiviati nell'account di archiviazione specificato quando è stata abilitata la registrazione. Vengono registrati i dati seguenti:

	{
		"time": "2015-09-11T23:05:22.6860000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupEvent",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupEvents",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"AllowVnetOutBound",
			"direction":"Out",
			"priority":65000,
			"type":"allow",
			"conditions":{
				"destinationPortRange":"0-65535",
				"sourcePortRange":"0-65535",
				"destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
				"sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
			}
		}
	}

##Visualizzare e analizzare il log di controllo
È possibile visualizzare e analizzare i dati del log di controllo con uno dei metodi seguenti:

- **Strumenti di Azure:** recuperare le informazioni dai log di controllo tramite Azure PowerShell, l'interfaccia della riga di comando di Azure, l'API REST di Azure o il portale di anteprima di Azure. Per istruzioni dettagliate per ogni metodo, vedere l'articolo [Operazioni di controllo con Gestione risorse](resource-group-audit.md).
- **Power BI:** se non esiste ancora un account [Power BI](https://powerbi.microsoft.com/pricing), è possibile crearne uno di prova gratuitamente. Con il [pacchetto di contenuto dei log di controllo di Azure per Power BI](https://support.powerbi.com/knowledgebase/articles/742695) è possibile analizzare i dati con dashboard preconfigurati utilizzabili così come sono o personalizzabili.

##Visualizzare e analizzare il registro contatori ed eventi 
È necessario connettersi all'account di archiviazione e recuperare le voci di log JSON per i registri eventi e contatori. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

>[AZURE.TIP] Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in Github.

##Risorse aggiuntive

- Post di blog sulla [visualizzazione dei log di controllo di Azure con Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Post di blog su [visualizzazione e analisi dei log di controllo di Azure in Power BI e altri strumenti](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0128_2016-->