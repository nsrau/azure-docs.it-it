<properties
	pageTitle="Distribuire una farm di SharePoint Server 2013 | Microsoft Azure"
	description="Distribuire una farm di SharePoint Server 2013 a disponibilità elevata con gruppi di disponibilità di SQL Server AlwaysOn in Azure in cinque fasi."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="josephd"/>

# Distribuzione di SharePoint con gruppi di disponibilità di SQL Server AlwaysOn in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

In questo argomento vengono forniti collegamenti alle istruzioni dettagliate per la distribuzione di una farm di SharePoint 2013 solo intranet con i gruppi di disponibilità di SQL Server AlwaysOn. La farm contiene questi computer:

- Due server Web di SharePoint
- Due server applicazioni SharePoint
- Due server di database
- Un server del nodo di maggioranza cluster
- Due controller di dominio

Questa è la configurazione, con nomi di segnaposto per ogni server.

![](./media/virtual-machines-windows-sp-intranet-overview/workload-spsqlao_05.png)

Due macchine per ogni ruolo garantiscono una disponibilità elevata. Tutte le macchine virtuali sono in una singola area. Ciascun gruppo di macchine virtuali per un ruolo specifico si trova nel relativo set di disponibilità.

## Distinta base

Questa configurazione di base richiede il set seguente di componenti e servizi di Azure:

- Nove macchine virtuali
- Quattro dischi dati aggiuntivi per i controller di dominio e le istanze di SQL Server
- Quattro set di disponibilità
- Una rete virtuale cross-premise
- Un account di archiviazione
- Una sottoscrizione di Azure

Ecco le macchine virtuali e le relative dimensioni predefinite per questa configurazione.

Elemento | Descrizione della macchina virtuale | Immagine della raccolta | Dimensioni predefinite
--- | --- | --- | ---
1\. | Primo controller di dominio | Windows Server 2012 R2 Datacenter | A2
2\. | Secondo controller di dominio | Windows Server 2012 R2 Datacenter | A2
3\. | Primo server di database | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | Secondo server di database | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | Nodo di maggioranza per il cluster | Windows Server 2012 R2 Datacenter | A1
6\. | Primo server applicazioni di SharePoint | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4.
7\. | Secondo server applicazioni di SharePoint | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4.
8\. | Primo server Web di SharePoint | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4.
9\. | Secondo server Web di SharePoint | Versione di valutazione di Microsoft SharePoint Server 2013 – Windows Server 2012 R2 | A4.

Per calcolare i costi stimati per questa configurazione, vedere il [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

1. In **Moduli**, fare clic su **Calcolo**, quindi fare clic su **Macchine virtuali** fino a creare un elenco di nove macchine virtuali.
2. Per ogni macchina virtuale selezionare:
	- L'area desiderata
	- **Windows** per il tipo
	- **Standard** per il piano tariffario
	- La dimensione predefinita nella tabella precedente o la dimensione desiderata per **Dimensioni istanze**

> [AZURE.NOTE] Il calcolatore dei prezzi di Azure non include i costi aggiuntivi per la licenza di SQL Server per le due macchine virtuali che eseguono SQL Server 2014 Enterprise. Per altre informazioni, vedere [Prezzi macchine virtuali - SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

## Fasi della distribuzione

La configurazione deve essere distribuita nelle seguenti fasi:

- [Fase 1: Configurare Azure](virtual-machines-windows-ps-sp-intranet-ph1.md). Creare un account di archiviazione, set di disponibilità e una rete virtuale cross-premise.
- [Fase 2: configurare i controller di dominio](virtual-machines-windows-ps-sp-intranet-ph2.md). Creare e configurare i controller di dominio dei servizi di dominio Active Directory (AD DS) di replica.
- [Fase 3: Configurare l'infrastruttura di SQL Server](virtual-machines-windows-ps-sp-intranet-ph3.md). Creare e configurare le macchine virtuali di SQL Server, prepararle per l'utilizzo con SharePoint e creare il cluster.
- [Fase 4: Configurare i server SharePoint](virtual-machines-windows-ps-sp-intranet-ph4.md). Creare e configurare le quattro macchine virtuali di SharePoint.
- [Fase 5: creare il gruppo di disponibilità e aggiungere i database SharePoint](virtual-machines-windows-ps-sp-intranet-ph5.md). Preparare i database e creare un gruppo di disponibilità di SQL Server AlwaysOn.


Questa distribuzione di SharePoint con SQL Server AlwaysOn è progettata per accompagnare l'[infografica di SharePoint con SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788) e incorporare i consigli più recenti.

Questa configurazione è una guida prescrittiva, fase per fase per un'architettura predefinita per creare una farm di SharePoint intranet funzionale e ad alta disponibilità nei servizi dell'infrastruttura di Azure. Per ulteriori linee guida architetturali sull'implementazione di SharePoint 2013 in Azure, vedere [Architetture di Microsoft Azure per SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx).

Tenere presente quanto segue:

- Gli utenti esperti nell'implementazione di SharePoint possono adattare le istruzioni delle fasi da 3 a 5 e realizzare la farm che meglio si adatta alle proprie esigenze.
- Se si dispone già di una distribuzione cloud ibrida Azure esistente, è possibile adattare o ignorare le istruzioni delle fasi 1 e 2 per ospitare la nuova farm di SharePoint nella subnet appropriata.
- Tutti i server si trovano in una singola subnet nella rete virtuale di Azure. Se si desidera garantire una maggiore protezione equivalente all'isolamento di subnet, è possibile usare i [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

Per compilare un ambiente di sviluppo e test o un modello di prova di questa configurazione, vedere [Configurazione di una farm Intranet di SharePoint in un cloud ibrido per l'esecuzione di test](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md).

Per ulteriori informazioni su SharePoint con gruppi di disponibilità di SQL Server AlwaysOn, vedere [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

> [AZURE.NOTE] Microsoft ha rilasciato l'anteprima di SharePoint Server 2016 IT. Per rendere questa versione di anteprima semplice da installare e testare, è possibile utilizzare un'immagine della raccolta della macchina virtuale di Azure con l’anteprima di SharePoint Server 2016 IT e i relativi prerequisiti pre-installati. Per ulteriori informazioni, vedere [Testare l’anteprima di SharePoint Server 2016 IT in Azure](https://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/).

## Passaggio successivo

- Iniziare la configurazione di questo carico di lavoro dalla [Fase 1](virtual-machines-windows-ps-sp-intranet-ph1.md)

<!---HONumber=AcomDC_0323_2016-->