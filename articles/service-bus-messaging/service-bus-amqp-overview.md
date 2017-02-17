---
title: Panoramica del protocollo AMQP 1.0 nel bus di servizio di Azure | Documentazione Microsoft
description: Informazioni sull&quot;uso del protocollo AMQP (Advanced Message Queuing Protocol) 1.0 in Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 91b138ee9590a66c63e2055eaeace82b4e719ae5


---
# <a name="amqp-10-support-in-service-bus"></a>Supporto per il protocollo AMQP 1.0 nel bus di servizio
Il protocollo AMQP (Advanced Message Queueing Protocol) 1.0 è supportato sia nel servizio cloud del bus di servizio di Azure sia nel [bus di servizio per Windows Server (Bus di servizio 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) locale. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard. È possibile creare applicazioni usando componenti creati in linguaggi e framework diversi e in esecuzione su sistemi operativi diversi. Tutti questi componenti possono connettersi al bus di servizio e scambiare in modo lineare ed efficiente messaggi di business strutturati con assoluta fedeltà.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduzione: informazioni sul protocollo AMQP 1.0 e sulla sua rilevanza
I prodotti middleware orientati ai messaggi hanno tradizionalmente usato protocolli proprietari per le comunicazioni tra applicazioni client e broker. Dopo la selezione di un broker di messaggistica di un fornitore specifico, è pertanto necessario utilizzare le librerie di tale fornitore per connettere le applicazioni client a tale broker. Ne risulta un livello di dipendenza rispetto a tale fornitore, poiché il trasferimento di un'applicazione a un prodotto diverso richiede la modifica del codice per tutte le applicazioni connesse. 

La connessione di broker di messaggistica da fornitori diversi è inoltre complicata e richiede in genere bridging a livello di applicazione per lo spostamento dei messaggi da un sistema all'altro e per la conversione nei diversi formati di messaggio proprietari. Questa esigenza è molto comune, ad esempio, quando è necessario fornire una nuova interfaccia unificata per sistemi diversi precedenti oppure integrare sistemi IT diversi dopo una fusione tra società.

Il settore software è caratterizzato da rapido sviluppo. Nuovi linguaggi di programmazione e framework applicazione vengono introdotti a ritmo sorprendente. Analogamente, i requisiti dei sistemi IT si evolvono nel tempo e gli sviluppatori desiderano avvalersi delle possibilità offerte dai linguaggi e dalle funzionalità più recenti della piattaforma. A volte, tuttavia, il fornitore di messaggistica selezionato non supporta tali piattaforme. Poiché vengono usati protocolli di messaggistica proprietari, non è possibile offrire librerie diverse per le nuove piattaforme. Pertanto, è necessario usare approcci come la creazione di gateway o bridge che consentano di continuare a usare il prodotto di messaggistica.

Il protocollo AMQP (Advanced Message Queuing Protocol) 1.0 è stato sviluppato per risolvere questi problemi presso JP Morgan Chase, che, come la maggior parte delle aziende del settore dei servizi finanziari, fa ampio uso di prodotti middleware orientati alla messaggistica. L'obiettivo da raggiungere era semplice: creare un protocollo di messaggistica a standard aperto che consentisse di creare applicazioni basate sui messaggi usando componenti creati in linguaggi, framework e sistemi operativi diversi, avvalendosi dei migliori prodotti offerti da diversi fornitori.

## <a name="amqp-10-technical-features"></a>Caratteristiche tecniche del protocollo AMQP 1.0
AMQP 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare applicazioni di messaggistica multipiattaforma e affidabili. Questo protocollo assolve a uno scopo semplice, ovvero definire i meccanismi per la trasmissione sicura, affidabile ed efficiente dei messaggi tra due parti. I messaggi stessi vengono codificati usando una rappresentazione dati portatile che consente a mittenti e destinatari eterogenei di scambiarsi messaggi di business strutturati con assoluta fedeltà. Di seguito sono riepilogate le caratteristiche più importanti:

* **Efficienza**: AMQP 1.0 è un protocollo orientato alla connessione che usa una codifica binaria per le istruzioni del protocollo e per i messaggi aziendali trasmessi. Integra sofisticati schemi di controllo di flusso per massimizzare l'uso della rete e dei componenti connessi. In ogni caso, il protocollo è stato progettato per assicurare un equilibrio tra efficienza, flessibilità e interoperabilità.
* **Affidabilità**: il protocollo AMQP 1.0 consente lo scambio di messaggi con una serie di garanzie di affidabilità, dal "fire-and-forget" al recapito confermato affidabile di tipo "exactly-once".
* **Flessibilità**: AMQP 1.0 è un protocollo flessibile che consente di supportare diverse topologie. È possibile utilizzare lo stesso protocollo per le comunicazioni client-client, client-broker e broker-broker.
* **Indipendenza dal modello di broker**: il protocollo AMQP 1.0 non prevede requisiti relativi al modello di messaggistica usato da un broker. È pertanto possibile aggiungere con facilità il supporto per il protocollo AMQP 1.0 ai broker di messaggistica esistenti.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 è uno standard affermato
AMQP 1.0 è uno standard internazionale approvato da ISO e IEC come ISO/IEC 19464:2014.

Lo sviluppo di AMQP 1.0 procede dal 2008 a opera di un gruppo fondamentale di oltre 20 società, di cui fanno parte fornitori di tecnologie e aziende utenti finali. In questo lasso di tempo, le aziende utenti hanno comunicato i propri requisiti aziendali reali e i fornitori di tecnologie hanno proseguito lo sviluppo del protocollo con l'intento di soddisfarli. Nel corso del processo i fornitori hanno partecipato a workshop, che hanno permesso loro di collaborare per verificare l'interoperabilità tra le diverse implementazioni.

A ottobre 2011 il lavoro di sviluppo è passato a un comitato tecnico all'interno della Organization for the Advancement of Structured Information Standards (OASIS) e a ottobre 2012 è stato rilasciato lo standard OASIS AMQP 1.0. Del comitato tecnico hanno fatto parte le seguenti aziende durante lo sviluppo dello standard:

* **Fornitori di tecnologie**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Aziende utenti**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Di seguito sono indicati alcuni dei vantaggi più apprezzati degli standard aperti:

* Minore possibilità di blocco da parte del fornitore
* Interoperabilità
* Ampia disponibilità di librerie e strumenti
* Protezione dall'obsolescenza
* Disponibilità di personale competente
* Rischio ridotto e gestibile

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 e bus di servizio
Grazie al supporto del protocollo AMQP 1.0 al bus di servizio di Azure, è ora possibile usare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione del bus di servizio offerte da numerose piattaforme, usando un protocollo binario efficiente. Inoltre, è possibile creare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

La figura riportata di seguito illustra una distribuzione di esempio, in cui i client Java in esecuzione su Linux, scritti usando l'API JMS (Java Message Service) standard, e i client .NET in esecuzione su Windows si scambiano messaggi tramite il bus di servizio usando il protocollo AMQP 1.0.

![][0]

**Figura 1: Scenario di distribuzione di esempio, in cui per la messaggistica tra diverse piattaforme usa il bus di servizio e il protocollo AMQP 1.0**

È attualmente possibile utilizzare le librerie client seguenti con il bus di servizio:

| Linguaggio | Libreria |
| --- | --- |
| Java |Client Apache Qpid Java Message Service (JMS)<br/>Client IIT Software SwiftMQ Java |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .Net Lite |

**Figura 2: Tabella delle librerie client del protocollo AMQP 1.0**

## <a name="summary"></a>Riepilogo
* AMQP 1.0 è un protocollo di messaggistica aperto e affidabile, che può essere utilizzato per creare applicazioni ibride multipiattaforma. AMQP 1.0 è uno standard OASIS.
* Il supporto per il protocollo AMQP 1.0 è ora disponibile nel bus di servizio di Azure e nel bus di servizio per Windows Server (Service Bus 1.1). La determinazione dei prezzi è analoga a quella dei protocolli esistenti.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere i collegamenti seguenti:

* [Uso del bus di servizio da .NET con AMQP]
* [Uso del bus di servizio da Java con AMQP]
* [Uso del bus di servizio da Python con AMQP]
* [Uso del bus di servizio da PHP con AMQP]
* [Installazione di Apache Qpid Proton-C in una macchina virtuale Linux di Azure]
* [AMQP nel bus di servizio per Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Uso del bus di servizio da .NET con AMQP]: service-bus-amqp-dotnet.md
[Uso del bus di servizio da Java con AMQP]: service-bus-amqp-java.md
[Uso del bus di servizio da Python con AMQP]: service-bus-amqp-python.md
[Uso del bus di servizio da PHP con AMQP]: service-bus-amqp-php.md
[Installazione di Apache Qpid Proton-C in una macchina virtuale Linux di Azure]: service-bus-amqp-apache.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx



<!--HONumber=Jan17_HO4-->


