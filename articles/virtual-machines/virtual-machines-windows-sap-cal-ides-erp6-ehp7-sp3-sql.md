<properties 
pageTitle="Distribuzione di SAP IDES EHP7 SP3 per SAP ERP 6.0 su Microsoft Azure | Microsoft Azure" 
description="Distribuzione di SAP IDES EHP7 SP3 per SAP ERP 6.0 su Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/>
<tags  
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/>


# Distribuzione di SAP IDES EHP7 SP3 per SAP ERP 6.0 su Microsoft Azure 

Questo articolo descrive come distribuire SAP IDES in esecuzione con SQL Server e il sistema operativo Windows in Microsoft Azure tramite SAP Cloud Appliance Library 3.0. Le schermate illustrano la procedura dettagliata. Sul piano procedurale, per le altre soluzioni la distribuzione funziona nello stesso modo. L'unica cosa da fare è selezionare un'altra soluzione.

Per acquisire familiarità con SAP Cloud Appliance Library (SAP CAL), visitare [questa pagina](https://cal.sap.com/). Esiste anche un blog di SAP sul nuovo [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).


Le schermate seguenti illustrano in dettaglio come distribuire SAP IDES in Microsoft Azure. Per altre soluzioni, la procedura è sempre la stessa.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)  

La prima figura mostra tutte le soluzioni disponibili in Microsoft Azure. Per il processo è stata scelta la soluzione evidenziata SAP IDES basata su Windows, disponibile solo in Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)  

Per prima cosa occorre creare un nuovo account SAP CAL. Attualmente per gli account di Azure sono disponibili due opzioni: Azure standard e Azure in Cina, gestito dal partner 21Vianet.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)  

È quindi necessario immettere l'ID sottoscrizione di Azure, reperibile nel portale di Azure; per altre informazioni, vedere di seguito. Dopodiché è necessario scaricare un certificato di gestione di Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)  

Sul lato sinistro del nuovo portale di Azure è presente l'elemento "Sottoscrizioni". Fare clic su di esso per visualizzare tutte le sottoscrizioni attive per l'utente.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)  

Selezionando una delle sottoscrizioni e scegliendo "Certificati di gestione", viene spiegato che esiste un nuovo concetto di uso delle "entità servizio" per il nuovo modello di Azure Resource Manager. Non essendo ancora adattato a questo nuovo modello, SAP CAL richiede che il modello "classico" e il vecchio portale di Azure continuino a usare i certificati di gestione.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)  

Qui si può osservare il vecchio portale di Azure. Caricando un certificato di gestione, SAP CAL riceve le autorizzazioni necessarie per creare macchine virtuali in una sottoscrizione del cliente. Nella scheda "SUBSCRIPTIONS" (SOTTOSCRIZIONI) è possibile risalire all'ID della sottoscrizione da inserire nel portale SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)  

Nella seconda scheda è quindi possibile caricare il certificato di gestione precedentemente scaricato da SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)  

Viene visualizzata una piccola finestra di dialogo in cui selezionare il file di certificato scaricato.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)  

Una volta caricato il certificato, in SAP CAL è possibile testare la connessione tra SAP CAL e la sottoscrizione Azure del cliente. Un piccolo messaggio popup indicherà se la connessione è valida.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)  

Dopo aver installato un account, è necessario selezionare una soluzione da distribuire e creare un'istanza. Con la modalità di base, si tratta di un'operazione molto semplice. Immettere un nome da assegnare all'istanza, scegliere un'area di Azure e definire la password master della soluzione.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)  

Dopo alcuni minuti, a seconda della dimensione e della complessità (stimate da SAP CAL), la soluzione viene visualizzata come attiva e pronta all'uso. È molto semplice.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)  

Esaminando alcuni dettagli della soluzione, è possibile conoscere il tipo di VM distribuite. In questo caso esiste una singola VM di Azure di dimensione D12 creata da SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)  

Nel portale di Azure è possibile che le macchine virtuali vengano avviate con lo stesso nome di istanza assegnato in SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)  

A questo punto è possibile connettersi alla soluzione tramite l'apposito pulsante disponibile nel portale di SAP CAL. Nella finestra di dialogo è presente il collegamento a un manuale dell'utente che descrive tutte le credenziali predefinite per l'utilizzo della soluzione. [Qui](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) è disponibile il collegamento alla guida per la soluzione di IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)  

Un'altra opzione consiste nell'eseguire l'accesso alla VM di Windows e avviare, ad esempio, la GUI SAP preconfigurata.

<!---HONumber=AcomDC_0921_2016-->