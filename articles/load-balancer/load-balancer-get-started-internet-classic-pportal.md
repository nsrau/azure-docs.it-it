<properties 
   pageTitle="Introduzione alla creazione del servizio di bilanciamento del carico per Internet nel modello di distribuzione classica tramite il portale di anteprima | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico per Internet nel modello di distribuzione classica mediante il portale di anteprima"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="joaoma" />

# Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) nel portale di anteprima

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-ps.md).

 
[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Introduzione alla creazione di un endpoint del servizio di bilanciamento di carico tramite il portale di anteprima	

Per creare un modello di distribuzione (classica) per Internet del servizio di bilanciamento carico dal portale di anteprima, seguire questa procedura.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.

2. Passare al pannello delle macchine virtuali (classico) > selezionare una macchina virtuale.

3. Nel pannello "essentials" delle macchine virtuali > selezionare "tutte le impostazioni"

4. Fare clic su "set con carico bilanciato".

5. Per creare un nuovo bilanciamento del carico, fare clic sull'icona "join" nella parte superiore del pannello dei set di carico bilanciato.

6. Selezionare il "tipo di set di carico bilanciato" pubblico per il bilanciamento del carico con connessione Internet.

7. Fare clic su "configura le impostazioni necessarie" per aprire "scegliere un set con carico bilanciato" e fare clic su "creare un set con carico bilanciato".

8. Nel pannello "creare un set con carico bilanciato", creare un nome per il set di bilanciamento del carico. Riempire con il nome, la porta pubblica, il protocollo di probe, la porta di probe.

9. Modificare l'intervallo e i tentativi di probe se necessario.

10. (facoltativo) se si desidera, è possibile configurare le regole di controllo dell’accesso dal pannello di creazione del set di bilanciamento del carico.

11. Fare clic su ok per tornare al pannello "set con carico bilanciato join".

12. fare clic su ok e attendere la nuova risorsa di bilanciamento del carico da visualizzare nel pannello "set di bilanciamento del carico".
 
## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-internal-getstarted.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->