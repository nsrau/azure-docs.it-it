---
title: Framework di sicurezza - Azure IoT Edge | Microsoft Docs
description: Informazioni sugli standard di sicurezza, autenticazione e autorizzazione che sono stati usati per sviluppare Azure IoT Edge e che devono essere tenuti in considerazione durante la progettazione della soluzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 37d5288389c7b602eb0d13a736e289010d7e0f80
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208193"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standard di sicurezza per Azure IoT Edge

Azure IoT Edge risolve i rischi inerenti quando si trasferiscono dati e analisi al perimetro intelligente. Gli standard di sicurezza IoT Edge di bilanciare la flessibilità per diversi scenari di distribuzione con la protezione prevista da tutti i servizi di Azure. 

IoT Edge viene eseguito in varie marche e modelli di hardware, supporta diversi sistemi operativi e si applica a diversi scenari di distribuzione. Il rischio di uno scenario di distribuzione dipende da fattori che includono la proprietà della soluzione, la geografia della distribuzione, la riservatezza dei dati, la privacy, l'applicazione verticale e i requisiti normativi. Anziché offrire soluzioni concrete per scenari specifici, IoT Edge è un Framework di sicurezza estensibile basato su principi ben progettati per la scalabilità. 
 
Questo articolo offre una panoramica sul framework di sicurezza di IoT Edge. Per altre informazioni, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

## <a name="standards"></a>Standard

Gli standard promuovono un controllo e un'implementazione semplice, che sono alla base della sicurezza. Una soluzione di sicurezza si presta a controlli durante il processo di valutazione per creare affidabilità e non deve essere un ostacolo alla distribuzione. La progettazione di framework per la protezione di Azure IoT Edge è basata su protocolli di sicurezza collaudati e testati nel settore per sfruttare la familiarità e il riutilizzo. 

## <a name="authentication"></a>Authentication

Quando si distribuisce una soluzione Internet delle cose, è necessario tenere presente che solo gli attori, i dispositivi e i moduli attendibili hanno accesso alla soluzione. L'autenticazione basata su certificati è il meccanismo principale per l'autenticazione per la piattaforma Azure IoT Edge. Questo meccanismo deriva da un set di standard che governano l'infrastruttura a chiave pubblica (PKiX) da Internet Engineering Task Force (IETF).     

Tutti i dispositivi, i moduli e gli attori che interagiscono con il dispositivo Azure IoT Edge, fisicamente o tramite una connessione di rete, devono avere un'identità certificato univoca. Non tutti gli scenari o i componenti possono prestare l'autenticazione basata sui certificati, pertanto l'estendibilità del Framework di sicurezza offre alternative sicure. 

Per ulteriori informazioni, vedere [Azure IOT Edge utilizzo del certificato](iot-edge-certs.md).

## <a name="authorization"></a>Authorization

Secondo il principio dei privilegi minimi, gli utenti e i componenti di un sistema devono avere accesso solo al set minimo di risorse e dati necessari per svolgere i rispettivi ruoli. I dispositivi, i moduli e gli attori devono ottenere l'accesso solo alle risorse e ai dati nel proprio ambito di autorizzazione e solo quando è consentito a livello di architettura. Alcune autorizzazioni sono configurabili con privilegi sufficienti e altre sono applicate dall'architettura.  Alcuni moduli, ad esempio, possono essere autorizzati a connettersi all'hub Azure. Tuttavia, non esiste alcun motivo per cui un modulo in un dispositivo IoT Edge deve accedere al dispositivo gemello di un modulo in un altro dispositivo IoT Edge.

Altri schemi di autorizzazione includono i diritti di firma del certificato e il controllo degli accessi in base al ruolo (RBAC). 

## <a name="attestation"></a>Attestation

L'attestazione garantisce l'integrità dei bit software, che è importante per il rilevamento e la prevenzione di malware.  Il framework di sicurezza di Azure IoT Edge classifica l'attestazione in tre categorie principali:

* Attestazione statica
* Attestazione di runtime
* Attestazione di software

### <a name="static-attestation"></a>Attestazione statica

L'attestazione statica verifica l'integrità di tutto il software su un dispositivo durante l'accensione, inclusi il sistema operativo, tutti i runtime e le informazioni di configurazione. Poiché l'attestazione statica si verifica durante l'accensione, viene spesso definita avvio protetto. Il Framework di sicurezza per i dispositivi IoT Edge si estende ai produttori e incorpora le funzionalità hardware sicure che garantiscono i processi di attestazione statica. Questi processi includono l'avvio protetto e l'aggiornamento del firmware sicuro.  La collaborazione con fornitori di processori elimina i livelli di firmware superflui riducendo al minimo l'esposizione agli attacchi. 

### <a name="runtime-attestation"></a>Attestazione di runtime

Una volta che un sistema ha completato un processo di avvio protetto, i sistemi ben progettati dovrebbero rilevare i tentativi di inserire malware e adottare contromisure appropriate. Gli attacchi malware possono avere come destinazione le porte e le interfacce del sistema. Se gli attori dannosi hanno accesso fisico a un dispositivo, possono manomettere il dispositivo stesso o usare attacchi di canale laterale per ottenere l'accesso. Tali contenuti dannosi, sia che si tratti di modifiche di configurazione malware o non autorizzate, non possono essere rilevati dall'attestazione statica perché vengono inseriti dopo il processo di avvio. Le contromisure offerte o applicate dall'hardware del dispositivo aiutano ad allontanare tali minacce.  Il Framework di sicurezza per IoT Edge chiama in modo esplicito le estensioni che combattono le minacce in fase di esecuzione.  

### <a name="software-attestation"></a>Attestazione di software

Tutti i sistemi integri, inclusi i sistemi Edge intelligenti, necessitano di patch e aggiornamenti.  La sicurezza è importante per i processi di aggiornamento, in caso contrario possono essere vettori di minacce potenziali.  Il Framework di sicurezza per IoT Edge richiede gli aggiornamenti tramite pacchetti misurati e firmati per garantire l'integrità e l'autenticazione dell'origine dei pacchetti.  Questo standard è applicabile a tutti i sistemi operativi e ai bit di software dell'applicazione. 

## <a name="hardware-root-of-trust"></a>Radice di attendibilità dell'hardware

Per molti dispositivi Edge intelligenti, in particolare per i dispositivi a cui è possibile accedere fisicamente da potenziali attori dannosi, la protezione hardware è l'ultima difesa per la protezione. Per queste distribuzioni, è fondamentale l'uso di hardware resistente alle manomissioni. Azure IoT Edge incoraggia i fornitori di hardware per processori sicuri a offrire diverse varianti di radice di attendibilità dell'hardware che si adattino ai vari profili di rischio e ai diversi scenari di distribuzione. L'attendibilità hardware può anche provenire da standard comuni del protocollo di sicurezza come Trusted Platform Module (ISO/IEC 11889) e Device Identifier Composition Engine di Trusted Computing Group. Forniscono attendibilità hardware anche tecnologie enclave sicure quali TrustZones e Software Guard Extensions. 

## <a name="certification"></a>Certification

Per consentire ai clienti di prendere decisioni informate quando si procurano Azure IoT Edge dispositivi per la distribuzione, il IoT Edge Framework include i requisiti di certificazione.  Alla base di tali requisiti ci sono le certificazioni che riguardano le attestazioni di sicurezza e le certificazioni relative alla convalida di implementazione della protezione.  Una certificazione per le attestazioni di sicurezza, ad esempio, indica che il dispositivo IoT Edge usa hardware sicuro noto per resistere agli attacchi di avvio. Una certificazione di convalida indica che l'hardware sicuro è stato implementato correttamente per offrire questo valore nel dispositivo.  Fedele al principio di semplicità, il framework prova a mantenere al minimo il carico di lavoro della certificazione.   

## <a name="extensibility"></a>Estendibilità

Grazie alla tecnologia Internet delle cose che conduce a diversi tipi di trasformazioni aziendali, la sicurezza deve evolversi in parallelo per risolvere scenari emergenti.  Il framework di sicurezza di Azure IoT Edge si basa su fondamenta solide su cui si costruisce l'estensibilità in diverse dimensioni per includere: 

* Servizi di sicurezza del produttore ad esemio il Servizio Device Provisioning per l'hub IoT di Azure.
* Servizi di terze parti, ad esempio servizi di sicurezza gestiti per diversi verticali di applicazioni (ad esempio industriale o sanitario) o lo stato attivo della tecnologia, ad esempio il monitoraggio della sicurezza in reti mesh o servizi di attestazione hardware siliconica, tramite una rete avanzata di partner.
* I sistemi legacy includono l'adattamento alle strategie di sicurezza alternative, ad esempio con la tecnologia di protezione diversa dai certificati per l'autenticazione e la gestione delle identità.
* Proteggere l'hardware per l'adozione di nuove tecnologie di protezione dell'hardware e contributi di partner produttori di processori.

Alla fine, la protezione del perimetro intelligente richiede contributi collaborativi di una community aperta basata sull'interesse comune nella protezione delle cose.  Tali contributi potrebbero essere sotto forma di tecnologie o servizi di protezione.  Il framework di sicurezza di Azure IoT Edge offre una base solida per la sicurezza estensibile per avere la massima protezione e offrire lo stesso livello di attendibilità e di integrità nei dispositivi perimetrali intelligenti così come nel cloud di Azure.  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza offerta da Azure IoT Edge, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).
