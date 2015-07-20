
<properties 
   pageTitle="Panoramica del bilanciamento del carico Internet | Microsoft Azure"
   description="Panoramica del bilanciamento del carico Internet e delle relative funzionalità. Modalità di funzionamento del bilanciamento del carico per Azure con macchine virtuali e servizi cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />


# Bilanciamento del carico Internet tra più macchine virtuali o servizi

È possibile usare gli endpoint per configurare il bilanciamento del carico di Azure per la distribuzione di un tipo di traffico specifico tra più macchine virtuali o servizi. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web o ruoli Web.

Il bilanciamento del carico di Azure esegue il mapping dell'indirizzo IP pubblico e del numero di porta del traffico in ingresso all'indirizzo IP privato e al numero di porta della macchina virtuale e viceversa per il traffico di risposta proveniente dalla macchina virtuale.

![esempio di bilanciamento del carico pubblico](./media/load-balancer-internet-overview/IC727496.png))

>[AZURE.NOTE]Quando si configura il bilanciamento del carico del traffico tra più macchine virtuali o servizi usando le impostazioni predefinite, viene fornita una distribuzione casuale del traffico in ingresso. Se si vuole ottenere l'affinità di sessione (o sessioni permanenti), vedere l'argomento relativo alla [modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

Per un servizio cloud contenente istanze di ruoli Web o ruoli di lavoro, è possibile definire un endpoint pubblico nella definizione del servizio (file con estensione csdef).
 
Il file servicedefinition.csdef conterrà la configurazione dell'endpoint e, se sono presenti più istanze per la distribuzione di un ruolo Web o di lavoro, il bilanciamento del carico verrà configurato di conseguenza. La modalità di aggiunta di istanze alla distribuzione cloud comporta la modifica del numero di istanze nel file di configurazione del servizio (con estensione csfg).

La figura seguente mostra un endpoint con carico bilanciato per il traffico Web crittografato condiviso tra tre macchine virtuali per la porta TCP 443, pubblica e privata. Queste tre macchine virtuali appartengono a un set con carico bilanciato.

Quando i client Internet inviano richieste di pagine Web all'indirizzo IP pubblico del servizio cloud e alla porta TCP 443, il servizio di bilanciamento del carico di Azure esegue un bilanciamento casuale di queste richieste tra le tre macchine virtuali del set con carico bilanciato.


## Passaggi successivi

[Panoramica del bilanciamento del carico interno](load-balancer-internal-overview.md)

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-internet-getstarted.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)


 

<!---HONumber=July15_HO2-->