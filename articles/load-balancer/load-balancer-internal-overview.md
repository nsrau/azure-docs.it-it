
<properties
   pageTitle="Panoramica del bilanciamento del carico interno | Microsoft Azure"
   description="Panoramica del bilanciamento del carico interno e delle relative funzionalità. Modalità di funzionamento del bilanciamento del carico di Azure e possibili scenari per la configurazione di endpoint interni"
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
   ms.date="08/25/2016"
   ms.author="sewhee" />


# Panoramica del bilanciamento del carico interno

A differenza del bilanciamento del carico Internet, è possibile accedere al bilanciamento del carico interno solo dalle risorse all'interno del servizio cloud oppure usando una rete VPN per accedere all'infrastruttura di Azure. L'infrastruttura limita l'accesso agli indirizzi IP virtuali con carico bilanciato di un servizio cloud o una rete virtuale, senza esposizione diretta a un endpoint Internet. Ciò consente di eseguire le applicazioni line-of-business interne in Azure e di accedervi nel cloud o dalle risorse locali.

## Scenari per il servizio di bilanciamento del carico interno

Il bilanciamento del carico interno di Azure consente di bilanciare il carico tra macchine virtuali che si trovano in un servizio cloud o una rete virtuale nell'ambito di un'area. Per informazioni sull'uso e sulla configurazione di reti virtuali nell'ambito di un'area, vedere [Reti virtuali di area](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) nel blog di Azure. Le reti virtuali esistenti che sono state configurate per un gruppo di affinità non possono usare il bilanciamento del carico interno.

Il bilanciamento del carico interno supporta gli scenari seguenti:

- In un servizio cloud, dalle macchine virtuali a un set di macchine virtuali che si trovano nello stesso servizio cloud (vedere la figura 1).
- In una rete virtuale, dalle macchine virtuali nella rete virtuale a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale (vedere la figura 2).
- Per una rete virtuale cross-premise, dai computer locali a un set di macchine virtuali che si trovano nello stesso servizio cloud della rete virtuale (vedere la figura 3).
- Applicazioni multilivello con connessione Internet in cui i livelli di back-end non sono connessi a Internet, ma che richiedono il bilanciamento del carico per il traffico dal livello con connessione Internet.
- Bilanciamento del carico per applicazioni line-of-business (LOB) ospitate in Azure senza la necessità di applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. Inserimento di server locali nel set di computer il cui traffico viene sottoposto a bilanciamento del carico.

## Applicazioni multilivello con connessione Internet


Il livello Web presenta endpoint con connessione Internet per i client Internet e fa parte di un set con carico bilanciato. Il bilanciamento del carico distribuisce il traffico in ingresso dai client Web per la porta TCP 443 (HTTPS) ai server Web.

I server di database si servono di un endpoint di bilanciamento del carico interno usato dai server Web per l'archiviazione. Questo database serve l'endpoint con carico bilanciato, il cui traffico viene sottoposto a bilanciamento del carico tra i server di database nel set di bilanciamento del carico interno.

L'immagine seguente illustra l'applicazione multilivello con connessione Internet all'interno dello stesso servizio cloud.

Figura 1

![Bilanciamento del carico interno di un singolo servizio cloud](./media/load-balancer-internal-overview/IC736321.png)

Un altro scenario possibile per un'applicazione multilivello consiste nella distribuzione del bilanciamento del carico interno in un servizio cloud diverso rispetto a quello che utilizza il servizio per il bilanciamento del carico interno.

I servizi cloud che usano la stessa rete virtuale avranno accesso all'endpoint di bilanciamento del carico interno.

Nell'immagine seguente è possibile vedere che i server Web front-end si trovano in un servizio cloud diverso rispetto al back-end di database e sfruttano l'endpoint di bilanciamento del carico interno nella stessa rete virtuale.

Figura 2

![Bilanciamento del carico interno tra servizi cloud](./media/load-balancer-internal-overview/IC744147.png)

## Applicazioni line-of-business (LOB) Intranet

Il traffico dai client nella rete locale viene sottoposto a bilanciamento del carico nel set di server line-of-business usando la connessione VPN alla rete di Azure.

Il computer client potrà accedere a un indirizzo IP dal servizio VPN di Azure usando la rete VPN da punto a sito. Ciò consentirà di usare l'applicazione line-of-business ospitata servendosi dell'endpoint di bilanciamento del carico interno.

Figura 3

![Bilanciamento del carico interno tramite VPN da punto a sito](./media/load-balancer-internal-overview/IC744148.png)

Un altro scenario di tipo line-of-business prevede l'uso di una rete VPN da sito a sito per la connessione alla rete virtuale in cui è stato configurato l'endpoint di bilanciamento del carico interno. In questo modo, il traffico di rete locale può essere instradato all'endpoint di bilanciamento del carico interno.

Figura 4

![Bilanciamento del carico interno tramite VPN da sito a sito](./media/load-balancer-internal-overview/IC744150.png)


## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->