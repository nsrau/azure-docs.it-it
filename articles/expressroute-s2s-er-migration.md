<properties
   pageTitle="Migrazione della rete VPN da sito a sito a ExpressRoute"
   description="Questo articolo fornisce informazioni utili per la migrazione della rete VPN da sito a sito a ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# Migrazione della connessione: da rete da sito a sito a ExpressRoute

I clienti di Azure possono connettersi alla rete virtuale in due modi, ovvero tramite rete VPN da sito a sito e tramite ExpressRoute. La connessione tramite rete VPN da sito a sito viene effettuata tramite Internet pubblico. Per crittografare il traffico di rete viene usato IPsec. ExpressRoute è una connessione privata ad Azure. È possibile connettersi ad Azure tramite un [Provider di Exchange (EXP)](expressroute-exchange-providers.md) o un [Provider di servizi di rete (NSP)](expressroute-network-service-providers.md).

Se è già disponibile una connessione di tipo rete VPN da sito a sito alla rete virtuale, sarà possibile eseguire i passaggi seguenti per ottenere la migrazione alla connessione tramite ExpressRoute.

1) Accedere al portale di Azure.

2) Nel riquadro di spostamento fare clic su **RETI** e quindi selezionare la rete virtuale con connessione VPN.

3) Fare clic su **DASHBOARD**. Nella parte inferiore della pagina fare clic su **ELIMINA GATEWAY** e quindi su **SÌ**.

  Questo passaggio permette di rimuovere il gateway VPN da sito a sito.

4) Quando nel portale viene visualizzato il messaggio "GATEWAY NON CREATO" per la rete virtuale, fare clic sulla pagina **CONFIGURA**.

a) Selezionare la casella di controllo "Usa ExpressRoute".

b) Modificare il valore CIDR per la subnet del gateway in /28

![Condivisione della sottoscrizione](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5) Nella parte inferiore della pagina fare clic su **SALVA** e quindi su **SÌ**.

6) Fare clic su **DASHBOARD**. Nella parte inferiore della pagina fare clic su **CREA GATEWAY** e quindi su **SÌ**.

 Questo passaggio permette di creare il gateway ExpressRoute.
 
Dopo la creazione del nuovo gateway, la rete virtuale è pronta per la connessione a un circuito ExpressRoute. Tutte le macchine virtuali della rete virtuale sono attive e in esecuzione durante il processo. Non è necessario spostarle o arrestarle.

Procedere all'ultimo passaggio della configurazione di ExpressRoute tramite l'esercitazione relativa ai [Provider di Exchange](expressroute-configuring-exps.md) / [Provider di servizi di rete](expressroute-configuring-nsps.md) per collegare la rete virtuale al circuito ExpressRoute.



<!--HONumber=54-->