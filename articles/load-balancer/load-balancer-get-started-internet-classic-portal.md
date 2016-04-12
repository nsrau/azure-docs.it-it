
<properties 
   pageTitle="Introduzione alla creazione del servizio di bilanciamento del carico Internet nel modello di distribuzione classica tramite il portale di Azure classico | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet nel modello di distribuzione classica mediante il portale di Azure classico"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) nel portale di Azure classico

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## Configurazione del servizio di bilanciamento del carico Internet per le macchine virtuali

Per bilanciare il carico del traffico di rete Internet tra le macchine virtuali di un servizio cloud, è necessario creare un set con carico bilanciato. Questa procedura presuppone che le macchine virtuali siano già state create e che si trovino tutte nello stesso servizio cloud.

**Per configurare un set con carico bilanciato per le macchine virtuali**

1. Nel portale di Azure classico fare clic su **Macchine virtuali** e quindi sul nome di una macchina virtuale nel set con carico bilanciato.
2.	Far clic su **Endpoints**, quindi su **Add**.

4.	Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic sulla freccia destra.

4.	Nella pagina **Specificare i dettagli dell'endpoint**:
	- In **Nome** digitare un nome per l'endpoint o selezionarne uno dall'elenco di endpoint predefiniti per i protocolli comuni.
	-  In **Protocol** selezionare il protocollo richiesto dal tipo di endpoint, ossia TCP o UDP.
 	-  In **Porta pubblica e Porta privata** digitare il numero di porta da utilizzare per la macchina virtuale. È possibile usare le regole relative a porte private e firewall sulla macchina virtuale per reindirizzare il traffico in modo appropriato per l'applicazione. La porta privata può essere uguale alla porta pubblica. Per un endpoint per il traffico Web (HTTP), è ad esempio possibile assegnare la porta 80 sia come porta pubblica che privata.

5.	Selezionare **Create a load-balanced set** e quindi fare clic sulla freccia destra.

6.	Nella pagina **Configura il set con carico bilanciato** immettere un nome per il set con carico bilanciato e quindi assegnare i valori per il comportamento del probe del servizio di bilanciamento del carico di Azure. Il servizio di bilanciamento del carico usa i probe per determinare se le macchine virtuali del set con carico bilanciato sono disponibili a ricevere traffico in ingresso.

7.	Fare clic sul segno di spunta per creare l'endpoint con carico bilanciato. Nella colonna **Load-balanced set name** della pagina **Endpoints** relativa alla macchina virtuale verrà visualizzata l'indicazione **Yes**.

8.	Nel portale fare clic su **Macchine virtuali**, quindi sul nome di una macchina virtuale aggiuntiva nel set con carico bilanciato, fare clic su **Endpoint** e infine su **Aggiungi**.

9.	Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi un endpoint a un set con carico bilanciato esistente**, selezionare il nome del set con carico bilanciato e quindi fare clic sulla freccia destra.

10.	Nella pagina **Specificare i dettagli dell'endpoint** digitare un nome per l'endpoint e quindi fare clic sul segno di spunta. Per le macchine virtuali aggiuntive nel set con carico bilanciato, ripetere i passaggi da 8 a 10.



## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0323_2016-->