<properties 
	pageTitle="Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure" 
	description="È possibile distribuire SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure in cinque fasi." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure

In questo argomento vengono forniti collegamenti alle istruzioni dettagliate per la distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn nei servizi di infrastruttura di Azure. La farm contiene questi computer:

- Due server Web di SharePoint
- Due server applicazioni SharePoint
- Due server di database
- Un server del nodo di maggioranza cluster
- Due controller di dominio

Questa è la configurazione, con nomi di segnaposto per ogni server.

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)
 
Due macchine per ogni ruolo garantiscono una disponibilità elevata. Tutte le macchine virtuali sono in una singola area. Ogni gruppo di macchine virtuali per un ruolo specifico si trova nel relativo set di disponibilità.

La configurazione deve essere distribuita nelle seguenti fasi:

- [Fase 1: Configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md). Creare un account di archiviazione, servizi cloud e una rete virtuale cross-premise.
- [Fase 2: Configurare i controller di dominio](virtual-machines-workload-intranet-sharepoint-phase2.md). Creare e configurare i controller di dominio dei servizi di dominio Active Directory (AD DS) di replica.
- [Fase 3: Configurare l’infrastruttura di SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md). Creare e configurare le macchine virtuali di SQL Server, prepararle per l'utilizzo con SharePoint e creare il cluster.
- [Fase 4: Configurare i server SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md). Creare e configurare le quattro macchine virtuali di SharePoint.
- [Fase 5: Creare il gruppo di disponibilità e aggiungere i database SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md). Preparare i database e creare un gruppo di disponibilità di SQL Server AlwaysOn.

Questa distribuzione di SharePoint con SQL Server AlwaysOn è progettata per accompagnare l’[infografica di SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporare i consigli più recenti.

Questa configurazione è una guida prescrittiva, fase per fase per un'architettura predefinita per creare una farm di SharePoint intranet funzionale, ad alta disponibilità nei servizi dell'infrastruttura di Azure. Per ulteriori indicazioni architetturali per l'implementazione di SharePoint 2013 in Azure, vedere [Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Tenere presente quanto segue:

- Gli utenti esperti nell'implementazione di SharePoint possono adattare le istruzioni delle fasi da 3 a 5 e compilare la farm che meglio si adatta alle proprie esigenze. 
- Se si dispone già di un'implementazione di cloud ibrida Azure esistente, è possibile adattare o ignorare le istruzioni delle fasi 1 e 2 per ospitare la nuova farm di SharePoint nella subnet appropriata.
- Tutti i server si trovano in una singola subnet nella rete virtuale di Azure. Se si desidera garantire una maggiore protezione equivalente all'isolamento di subnet, è possibile utilizzare [Gruppi di sicurezza di rete](https://msdn.microsoft.com/library/azure/dn848316.aspx).

Per compilare un ambiente di sviluppo e test o un modello di prova di questa configurazione, vedere [Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Per ulteriori informazioni su SharePoint con gruppi di disponibilità di SQL Server AlwaysOn, vedere [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Passaggi successivi

Per avviare la configurazione di questo carico di lavoro, andare alla [Fase 1: Configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).


## Risorse aggiuntive

[SharePoint con infografica SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Farm di SharePoint ospitati nei servizi di infrastruttura di Azure](virtual-machines-sharepoint-infrastructure-services.md)

<!--HONumber=54-->