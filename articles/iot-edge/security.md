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
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760114"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standard di sicurezza per Azure IoT Edge

Azure IoT Edge risolve i rischi inerenti quando si spostano i dati e l'analisi nell'edge intelligente. Gli standard di sicurezza IoT Edge bilanciano la flessibilità per diversi scenari di distribuzione con la protezione prevista da tutti i servizi di Azure.The ioT Edge security standards balance flexibility for different deployment scenarios with the protection that you expect from all Azure services.

IoT Edge viene eseguito su varie marche e modelli di hardware, supporta diversi sistemi operativi e si applica a diversi scenari di distribuzione. Anziché offrire soluzioni concrete per scenari specifici, IoT Edge è un framework di sicurezza estensibile basato su principi ben radicati progettati per la scalabilità. Il rischio di uno scenario di distribuzione dipende da molti fattori, tra cui:

* Proprietà della soluzione
* Area geografica di distribuzione
* Sensibilità ai dati
* Privacy
* Applicazione verticale
* Requisiti normativi

Questo articolo offre una panoramica sul framework di sicurezza di IoT Edge. Per altre informazioni, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

## <a name="standards"></a>Standard

Gli standard promuovono un controllo e un'implementazione semplice, che sono alla base della sicurezza. Una soluzione di sicurezza si presta a controlli durante il processo di valutazione per creare affidabilità e non deve essere un ostacolo alla distribuzione. La progettazione di framework per la protezione di Azure IoT Edge è basata su protocolli di sicurezza collaudati e testati nel settore per sfruttare la familiarità e il riutilizzo.

## <a name="authentication"></a>Authentication

Quando si distribuisce una soluzione IoT, è necessario sapere che solo gli attori, i dispositivi e i moduli attendibili hanno accesso alla soluzione. L'autenticazione basata su certificati è il meccanismo principale per l'autenticazione per la piattaforma Azure IoT Edge.Certificate-based authentication is the primary mechanism for authentication for the Azure IoT Edge platform. Questo meccanismo è derivato da una serie di standard che disciplinano Public Key Infrastructure (PKiX) dalla Internet Engineering Task Force (IETF).

Tutti i dispositivi, i moduli e gli attori che interagiscono con il dispositivo Azure IoT Edge devono avere identità di certificato univoche. Questa guida si applica se le interazioni sono fisiche o tramite una connessione di rete. Non tutti gli scenari o i componenti possono prestarsi all'autenticazione basata su certificati, pertanto l'estendibilità del framework di sicurezza offre alternative sicure.

Per altre informazioni, vedere Utilizzo dei certificati di [Azure IoT Edge](iot-edge-certs.md).

## <a name="authorization"></a>Autorizzazione

Secondo il principio dei privilegi minimi, gli utenti e i componenti di un sistema devono avere accesso solo al set minimo di risorse e dati necessari per svolgere i rispettivi ruoli. I dispositivi, i moduli e gli attori devono ottenere l'accesso solo alle risorse e ai dati nel proprio ambito di autorizzazione e solo quando è consentito a livello di architettura. Alcune autorizzazioni sono configurabili con privilegi sufficienti e altre sono applicate a livello di architettura. Ad esempio, alcuni moduli possono essere autorizzati a connettersi all'hub IoT di Azure.For example, some modules may be authorized to connect to Azure IoT Hub. Tuttavia, non vi è alcun motivo per cui un modulo in un dispositivo IoT Edge deve accedere al gemello di un modulo in un altro dispositivo IoT Edge.

Altri schemi di autorizzazione includono i diritti di firma dei certificati e il controllo degli accessi in base al ruolo.

## <a name="attestation"></a>Attestazione

L'attestazione garantisce l'integrità dei bit software, che è importante per rilevare e prevenire il malware. Il framework di sicurezza di Azure IoT Edge classifica l'attestazione in tre categorie principali:

* Attestazione statica
* Attestazione di runtime
* Attestazione di software

### <a name="static-attestation"></a>Attestazione statica

L'attestazione statica verifica l'integrità di tutto il software in un dispositivo durante l'accensione, incluso il sistema operativo, tutti i runtime e le informazioni di configurazione. Poiché l'attestazione statica si verifica durante l'accensionne, viene spesso definita avvio sicuro. Il framework di sicurezza per i dispositivi IoT Edge si estende ai produttori e incorpora funzionalità hardware sicure che assicurano processi di attestazione statici. Questi processi includono l'avvio protetto e l'aggiornamento del firmware sicuro. La collaborazione con fornitori di processori elimina i livelli di firmware superflui riducendo al minimo l'esposizione agli attacchi.

### <a name="runtime-attestation"></a>Attestazione di runtime

Una volta che un sistema ha completato un processo di avvio sicuro, i sistemi ben progettati dovrebbero rilevare i tentativi di iniettare malware e adottare le contromisure appropriate. Gli attacchi malware possono colpire le porte e le interfacce del sistema. Se gli attori malintenzionati hanno accesso fisico a un dispositivo, possono manomettere il dispositivo stesso o utilizzare attacchi side-channel per ottenere l'accesso. Tale malcontento, sia esso malware o modifiche di configurazione non autorizzate, non può essere rilevato da attestazione statica perché viene iniettato dopo il processo di avvio. Le contromisure offerte o applicate dall'hardware del dispositivo aiutano ad allontanare tali minacce. Il framework di sicurezza per IoT Edge richiede esplicitamente estensioni che combattono le minacce di runtime.  

### <a name="software-attestation"></a>Attestazione di software

Tutti i sistemi sani, compresi i sistemi perimetrali intelligenti, necessitano di patch e aggiornamenti. La sicurezza è importante per i processi di aggiornamento, altrimenti possono essere potenziali vettori di minacce. Il framework di sicurezza per IoT Edge richiede aggiornamenti tramite pacchetti misurati e firmati per garantire l'integrità e autenticare l'origine dei pacchetti. Questo standard è applicabile a tutti i sistemi operativi e ai bit di software dell'applicazione.

## <a name="hardware-root-of-trust"></a>Radice di attendibilità dell'hardware

Per molti dispositivi perimetrali intelligenti, in particolare i dispositivi a cui possono accedere fisicamente da potenziali attori malintenzionati, la sicurezza hardware è l'ultima difesa per la protezione. Per queste distribuzioni, è fondamentale l'uso di hardware resistente alle manomissioni. Azure IoT Edge incoraggia i fornitori di hardware per processori sicuri a offrire diverse varianti di radice di attendibilità dell'hardware che si adattino ai vari profili di rischio e ai diversi scenari di distribuzione. L'attendibilità hardware può anche provenire da standard comuni del protocollo di sicurezza come Trusted Platform Module (ISO/IEC 11889) e Device Identifier Composition Engine di Trusted Computing Group. Forniscono attendibilità hardware anche tecnologie enclave sicure quali TrustZones e Software Guard Extensions.

## <a name="certification"></a>Certificazione

Per aiutare i clienti a prendere decisioni informate durante l'acquisto di dispositivi Azure IoT Edge per la distribuzione, il framework IoT Edge include requisiti di certificazione. Alla base di tali requisiti ci sono le certificazioni che riguardano le attestazioni di sicurezza e le certificazioni relative alla convalida di implementazione della protezione. Ad esempio, una certificazione di attestazione di sicurezza significa che il dispositivo IoT Edge utilizza hardware sicuro noto per resistere agli attacchi di avvio. Una certificazione di convalida significa che l'hardware sicuro è stato implementato correttamente per offrire questo valore nel dispositivo. Fedele al principio di semplicità, il framework prova a mantenere al minimo il carico di lavoro della certificazione.

## <a name="extensibility"></a>Estendibilità

Con la tecnologia IoT che guida diversi tipi di trasformazioni aziendali, la sicurezza dovrebbe evolversi in parallelo per affrontare gli scenari emergenti. Il framework di sicurezza di Azure IoT Edge si basa su fondamenta solide su cui si costruisce l'estensibilità in diverse dimensioni per includere:

* Servizi di sicurezza del produttore ad esemio il Servizio Device Provisioning per l'hub IoT di Azure.
* Servizi di terze parti come i servizi di sicurezza gestiti per diversi verticali di applicazioni (come l'industria o l'assistenza sanitaria) o l'attenzione tecnologica (come il monitoraggio della sicurezza in reti mesh o i servizi di attestazione dell'hardware in silicio) attraverso una ricca rete di Partner.
* I sistemi legacy includono l'adattamento alle strategie di sicurezza alternative, ad esempio con la tecnologia di protezione diversa dai certificati per l'autenticazione e la gestione delle identità.
* Proteggere l'hardware per l'adozione di nuove tecnologie di protezione dell'hardware e contributi di partner produttori di processori.

Alla fine, garantire il vantaggio intelligente richiede contributi collaborativi da parte di una comunità aperta guidata dall'interesse comune a proteggere l'IoT. Tali contributi potrebbero essere sotto forma di tecnologie o servizi di protezione. Il framework di sicurezza di Azure IoT Edge offre una base solida per la sicurezza estensibile per avere la massima protezione e offrire lo stesso livello di attendibilità e di integrità nei dispositivi perimetrali intelligenti così come nel cloud di Azure.  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza offerta da Azure IoT Edge, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).
