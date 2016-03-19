<properties
	pageTitle="Panoramica della messaggistica inoltrata del bus di servizio | Microsoft Azure"
	description="Panoramica dell'inoltro del bus di servizio."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="sethm"/>


# Messaggistica inoltrata del bus di servizio

Il componente centrale del bus di servizio è un servizio di inoltro centralizzato, ma con carico altamente bilanciato, che consente di creare applicazioni ibride che vengono eseguite sia in un data center di Azure che nell'ambiente aziendale locale. Il servizio di inoltro supporta un'ampia gamma di protocolli di trasporto e standard dei servizi Web, tra cui SOAP, WS-* e anche REST. L'inoltro del bus di servizio facilita le applicazioni ibride consentendo di esporre in modo sicuro nel cloud pubblico i servizi WCF (Windows Communication Foundation) che risiedono in una rete aziendale, senza dover aprire una connessione firewall o richiedere modifiche di notevole impatto a un'infrastruttura di rete aziendale.

![Concetti relativi all'inoltro](./media/service-bus-relay-overview/sb-relay-01.png)

Il servizio di inoltro supporta la messaggistica unidirezionale tradizionale, la messaggistica richiesta-risposta e la messaggistica peer-to-peer. Supporta inoltre la distribuzione degli eventi nell'ambito di Internet per abilitare scenari di pubblicazione/sottoscrizione e le comunicazioni tramite socket bidirezionale per migliorare l'efficienza point-to-point.

Nel modello di messaggistica inoltrata, un servizio locale si connette al servizio di inoltro attraverso una porta in uscita e crea un socket bidirezionale per la comunicazione associato a un determinato indirizzo rendezvous. Il client può quindi comunicare con il servizio locale inviando messaggi al servizio di inoltro come destinazione l'indirizzo rendezvous. Il servizio di inoltro eseguirà quindi l’"inoltro" dei messaggi al servizio locale attraverso il socket bidirezionale già in uso. Il client non necessita di una connessione diretta al servizio locale, non deve conoscere la posizione in cui risiede il servizio e il servizio locale non richiede porte in ingresso aperte sul firewall.

È possibile avviare la connessione tra il servizio locale e il servizio di inoltro mediante una suite di associazioni di "inoltro" WCF. Dietro le quinte, eseguire il mapping delle associazioni di inoltro ai nuovi elementi di associazione di trasporto progettati per creare i componenti di canale WCF che si integrano con il bus di servizio nel cloud.

## Passaggi successivi

Per informazioni dettagliate sull'inoltro del bus di servizio, vedere gli argomenti seguenti.

- [Panoramica dell'architettura del bus di servizio di Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Come usare il servizio Inoltro del bus di servizio](service-bus-dotnet-how-to-use-relay.md)

 

<!---HONumber=AcomDC_0218_2016-->