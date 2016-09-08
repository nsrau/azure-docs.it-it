
<properties 
   pageTitle="Panoramica del bilanciamento del carico Internet | Microsoft Azure "
   description="Panoramica del bilanciamento del carico Internet e delle relative funzionalità. Modalità di funzionamento del bilanciamento del carico per Azure con macchine virtuali e servizi cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />


# Bilanciamento del carico Internet tra più macchine virtuali o servizi

È possibile usare gli endpoint per configurare il bilanciamento del carico di Azure per la distribuzione di un tipo di traffico specifico tra più macchine virtuali o servizi. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web o ruoli Web.

Il bilanciamento del carico di Azure esegue il mapping dell'indirizzo IP pubblico e del numero di porta del traffico in ingresso all'indirizzo IP privato e al numero di porta della macchina virtuale e viceversa per il traffico di risposta proveniente dalla macchina virtuale.

>[AZURE.NOTE] Il servizio di bilanciamento del carico di Azure fornirà un traffico di rete di distribuzione hash tra più istanze di macchine virtuali utilizzando le impostazioni predefinite (ulteriori informazioni sulla distribuzione hash in [funzionalità del servizio di bilanciamento carico](load-balancer-overview.md#load-balancer-features). Se si vuole ottenere l'affinità di sessione, vedere l'argomento relativo alla [modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md).

Per un servizio cloud contenente istanze di ruoli Web o ruoli di lavoro, è possibile definire un endpoint pubblico nella definizione del servizio (file con estensione csdef).
 
Il file servicedefinition.csdef conterrà la configurazione dell'endpoint e, se sono presenti più istanze per la distribuzione di un ruolo Web o di lavoro, il bilanciamento del carico verrà configurato di conseguenza. La modalità di aggiunta di istanze alla distribuzione cloud comporta la modifica del numero di istanze nel file di configurazione del servizio (con estensione csfg).

La figura seguente mostra un endpoint con carico bilanciato per il traffico Web crittografato condiviso tra tre macchine virtuali per la porta TCP 443, pubblica e privata. Queste tre macchine virtuali appartengono a un set con carico bilanciato.


![esempio di bilanciamento del carico pubblico](./media/load-balancer-internet-overview/IC727496.png))

Quando i client Internet inviano richieste di pagine Web all'indirizzo IP pubblico del servizio cloud e alla porta TCP 443, il servizio di bilanciamento del carico di Azure esegue un bilanciamento del carico basato su hash di queste richieste tra le tre macchine virtuali del set con carico bilanciato. È possibile ottenere ulteriori informazioni sull'algoritmo di bilanciamento di carico in [pagina panoramica del bilanciamento di carico](load-balancer-overview.md#load-balancer-features).


## Passaggi successivi

Dopo aver ottenuto informazioni su un bilanciamento del carico con connessione Internet, è anche possibile leggere informazioni sul [bilanciamento del carico interno](load-balancer-internal-overview.md) e verificare quale bilanciamento del carico è più adatto alla distribuzione cloud.

È anche possibile [iniziare a creare un bilanciamento del carico con connessione Internet](load-balancer-get-started-internet-arm-ps.md) e configurare il tipo di [modalità di distribuzione](load-balancer-distribution-mode.md) per il comportamento specifico del traffico di rete per il bilanciamento del carico.

Se l'applicazione deve mantenere attive le connessioni per i server dietro il servizio di bilanciamento del carico, è possibile ottenere altre informazioni sulle [impostazioni di timeout delle connessioni TCP inattive per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Ciò consente di ottenere informazioni sul comportamento delle connessioni inattive quando si usa il servizio di bilanciamento del carico di Azure.


 

<!---HONumber=AcomDC_0824_2016-->