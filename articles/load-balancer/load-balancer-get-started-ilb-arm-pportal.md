<properties 
   pageTitle="Informazioni sulla creazione di un servizio di bilanciamento del carico interno in Resource Manager con il portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico interno in Resource Manager con il portale di Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
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
   ms.date="03/04/2016"
   ms.author="sewhee" />

# Introduzione alla creazione di un servizio di bilanciamento del carico interno nel portale di Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)] <BR> [AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Introduzione alla creazione di un servizio di bilanciamento del carico interno tramite il portale di Azure	

Per creare un servizio di bilanciamento del carico interno dal portale di Azure, seguire questa procedura.

1. Da un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Sul lato superiore sinistro della schermata fare clic su **Nuovo** > **Rete** > **Bilanciamento del carico**.
4. Nel pannello **Crea bilanciamento del carico** digitare un **Nome** per il servizio di bilanciamento del carico.
5. In **Schema** fare clic su **Interno**.
5. Fare clic su **Rete virtuale**, quindi selezionare la rete virtuale in cui si desidera creare il servizio di bilanciamento del carico.

>[AZURE.NOTE] Se la rete virtuale che si desidera usare non viene visualizzata, controllare il **Percorso** per il servizio di bilanciamento del carico e modificarlo di conseguenza.

6. Fare clic su **Subnet** e scegliere la subnet in cui creare il servizio di bilanciamento del carico.
6. In **Assegnazione indirizzi IP** fare clic su **Dinamico** o **Statico**, a seconda che si desideri un servizio di bilanciamento del carico con indirizzo IP fisso, cioè statico, o dinamico.

>[AZURE.NOTE] Se si sceglie un indirizzo IP statico, sarà necessario fornire un indirizzo per il servizio di bilanciamento del carico.
    
7. In **Gruppo di risorse** specificare il nome di un nuovo gruppo di risorse per il servizio di bilanciamento del carico oppure fare clic su **Seleziona esistente** e scegliere un gruppo di risorse esistente.
8. Fare clic su **Create**.

## Configurare le regole del servizio di bilanciamento del carico 

Dopo la creazione del servizio di bilanciamento di carico, individuare la risorsa di bilanciamento del carico per configurarla. Prima di configurare una regola di bilanciamento del carico, è necessario innanzitutto configurare un pool di indirizzi back-end e un probe.

### Passaggio 1

Configurare un pool back-end:

1. Nel portale di Azure fare clic su **Sfoglia** > **Bilanciamenti del carico**, quindi fare clic sul servizio di bilanciamento del carico creato in precedenza.
2. Nel pannello **Impostazioni** fare clic su **Pool back-end**.
3. Nel pannello **Probe** fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi pool back-end** digitare un **Nome** per il pool back-end e quindi fare clic su **OK**.

### Passaggio 2 

Configurare un probe:
 
1. Nel portale di Azure fare clic su **Sfoglia** > **Bilanciamenti del carico**, quindi fare clic sul servizio di bilanciamento del carico creato in precedenza.
2. Nel pannello **Impostazioni** fare clic su **Probe**.
3. Nel pannello **Pool di indirizzi back-end** fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi probe** digitare un **Nome** per il probe.
5. In **Protocollo** selezionare **HTTP** (per i siti Web) o **TCP** (per le altre applicazioni basate su TCP).
6. In **Porta** specificare la porta da usare per accedere al probe.
7. Solo per i probe HTTP, in **Percorso** specificare il percorso da usare come probe.
4. In **Intervallo** specificare la frequenza di probe per l'applicazione.
5. In **Soglia di non integrità** specificare il numero di tentativi con esito negativo che si possono fare prima che la macchina virtuale back-end sia contrassegnata come danneggiata.
5. Fare clic su **OK** per creare il probe.

### Passaggio 3

Configurare le regole del servizio di bilanciamento del carico:

1. Nel portale di Azure fare clic su **Sfoglia** > **Bilanciamenti del carico**, quindi fare clic sul servizio di bilanciamento del carico creato in precedenza.
2. Nel pannello **Impostazioni** fare clic su **Regole di bilanciamento del carico**.
3. Nel pannello **Regole di bilanciamento del carico** fare clic su **Aggiungi**.
4. Nel pannello **Aggiungi regola di bilanciamento del carico** digitare un **Nome** per la regola.
5. In **Protocollo** selezionare **HTTP** (per i siti Web) o **TCP** (per le altre applicazioni basate su TCP).
6. In **Porta** specificare la porta del servizio di bilanciamento del carico a cui i client devono connettersi.
7. In **Porta back-end** specificare la porta da usare nel pool di back-end. In genere, la porta del servizio di bilanciamento del carico e la porta di back-end sono la stessa.
8. In **Pool back-end** selezionare il pool back-end creato in precedenza.
9. In **Salvataggio permanente sessione** selezionare la modalità di salvataggio delle sessioni.
10. In **Timeout di inattività (minuti)** specificare il timeout di inattività.
11. In **IP mobile (Direct Server Return)** fare clic su **Disabilitato** o **Abilitato**.
12. Fare clic su **OK**.
 
## Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->