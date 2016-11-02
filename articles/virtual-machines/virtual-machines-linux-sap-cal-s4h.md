<properties 
pageTitle="Distribuzione di S/4 HANA o BW/4 HANA in una macchina virtuale di Azure | Microsoft Azure" 
description="Distribuzione di S/4 HANA o BW/4 HANA in una macchina virtuale di Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 



# <a name="deploying-s/4-hana-or-bw/4-hana-on-microsoft-azure"></a>Distribuire S/4 HANA o BW/4 HANA in Microsoft Azure 

In questo articolo viene descritto come distribuire S/4 HANA in Microsoft Azure tramite SAP Cloud Appliance Library 3.0.
Le schermate illustrano la procedura dettagliata. Sul piano procedurale, per le altre soluzioni basate su SAP HANA, come BW/4 HANA, la distribuzione funziona nello stesso modo. L'unica cosa da fare è selezionare un'altra soluzione.

Per acquisire familiarità con SAP Cloud Appliance Library (SAP CAL), visitare [questa pagina](https://cal.sap.com/). Esiste anche un blog di SAP sul nuovo [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Le schermate seguenti illustrano in dettaglio come distribuire S/4 HANA in Microsoft Azure. Per altre soluzioni BW/4 HANA, la procedura è sempre la stessa.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La prima figura mostra tutte le soluzioni basate su SAP CAL HANA disponibili in Microsoft Azure.
Per eseguire la procedura di esempio, è stata scelta la soluzione "SAP S/4 HANA edizione locale" (la soluzione in basso nella schermata).

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Per prima cosa occorre creare un nuovo account SAP CAL. Attualmente per gli account di Azure sono disponibili due opzioni: Azure standard e Azure in Cina, gestito dal partner 21Vianet.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

È quindi necessario immettere l'ID sottoscrizione di Azure, reperibile nel portale di Azure; per altre informazioni, vedere di seguito. Dopodiché è necessario scaricare un certificato di gestione di Azure.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

Sul lato sinistro del nuovo portale di Azure è presente l'elemento "Sottoscrizioni". Fare clic su di esso per visualizzare tutte le sottoscrizioni attive per l'utente.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Selezionando una delle sottoscrizioni e scegliendo "Certificati di gestione", viene spiegato che esiste un nuovo concetto di uso delle "entità servizio" per il nuovo modello di Azure Resource Manager.
Non essendo ancora adattato a questo nuovo modello, SAP CAL richiede che il modello "classico" e il vecchio portale di Azure continuino a usare i certificati di gestione.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Qui si può osservare il vecchio portale di Azure. Caricando un certificato di gestione, SAP CAL riceve le autorizzazioni necessarie per creare macchine virtuali in una sottoscrizione del cliente. Nella scheda "SUBSCRIPTIONS" (SOTTOSCRIZIONI) è possibile risalire all'ID della sottoscrizione da inserire nel portale SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Nella seconda scheda è quindi possibile caricare il certificato di gestione precedentemente scaricato da SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Viene visualizzata una piccola finestra di dialogo in cui selezionare il file di certificato scaricato.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Una volta caricato il certificato, in SAP CAL è possibile testare la connessione tra SAP CAL e la sottoscrizione Azure del cliente. Un piccolo messaggio popup indicherà se la connessione è valida.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Dopo aver installato un account, è necessario selezionare una soluzione da distribuire e creare un'istanza.
Con la modalità di base, si tratta di un'operazione molto semplice. Immettere un nome da assegnare all'istanza, scegliere un'area di Azure e definire la password master della soluzione.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Dopo alcuni minuti, a seconda della dimensione e della complessità (stimate da SAP CAL), la soluzione viene visualizzata come attiva e pronta all'uso. È molto semplice.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Esaminando alcuni dettagli della soluzione, è possibile conoscere il tipo di VM distribuite. In questo caso sono state create tre VM di Azure diverse per dimensioni e scopo.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Nel portale di Azure è possibile che le macchine virtuali vengano avviate con lo stesso nome di istanza assegnato in SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

A questo punto è possibile connettersi alla soluzione tramite l'apposito pulsante disponibile nel portale di SAP CAL. Nella finestra di dialogo è presente il collegamento a un manuale dell'utente che descrive tutte le credenziali predefinite per l'utilizzo della soluzione.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Un'altra opzione consiste nell'effettuare l'accesso alla VM client di Windows e avviare, ad esempio, la GUI SAP preconfigurata.










<!--HONumber=Oct16_HO2-->


