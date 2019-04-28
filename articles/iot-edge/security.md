---
title: Framework di sicurezza - Azure IoT Edge | Microsoft Docs
description: Informazioni sugli standard di sicurezza, autenticazione e autorizzazione che sono stati usati per sviluppare Azure IoT Edge e che devono essere tenuti in considerazione durante la progettazione della soluzione
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612037"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standard di sicurezza per Azure IoT Edge

Azure IoT Edge è progettato per soddisfare gli scenari di rischio che vengono svolte quando si spostano i dati e analitica in dispositivi perimetrali intelligenti. Gli standard di sicurezza IoT Edge offrono una flessibilità per scenari di distribuzione e diversi profili di rischi, offrendo comunque la protezione che ci si aspetta da tutti i servizi di Azure. 

Azure IoT Edge può essere eseguito su diverse marche e modelli di hardware, supporta vari sistemi operativi e si applica a scenari di distribuzione differenti. Il rischio di uno scenario di distribuzione dipende da molte considerazioni che includono la proprietà della soluzione, la geografia di distribuzione, la riservatezza dei dati, la privacy, il segmento verticale dell'applicazione e i requisiti normativi. Invece di offrire soluzioni concrete per scenari specifici, IoT Edge è un framework di sicurezza estensibile basato su principi ponderati progettati per la scalabilità. 
 
Questo articolo offre una panoramica sul framework di sicurezza di IoT Edge. Per altre informazioni, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

## <a name="standards"></a>Standard

Gli standard promuovono un controllo e un'implementazione semplice, che sono alla base della sicurezza. Una soluzione di sicurezza si presta a controlli durante il processo di valutazione per creare affidabilità e non deve essere un ostacolo alla distribuzione. La progettazione di framework per la protezione di Azure IoT Edge è basata su protocolli di sicurezza collaudati e testati nel settore per sfruttare la familiarità e il riutilizzo. 

## <a name="authentication"></a>Authentication

Quando si distribuisce una soluzione IoT, è necessario sapere che solo gli attori affidabili, dispositivi e i moduli hanno accesso alla soluzione. Tale conoscenza offre una responsabilità sicura dei partecipanti. Azure IoT Edge raggiunge questa conoscenza tramite l'autenticazione. L'autenticazione basata su certificati è il meccanismo principale per l'autenticazione per la piattaforma Azure IoT Edge. Questo meccanismo deriva da un set di standard che regolano l'infrastruttura a chiave pubblica da IETF.     

Tutti i dispositivi, i moduli e gli attori che interagiscono con il dispositivo Azure IoT Edge, fisicamente o tramite una connessione di rete, devono avere un'identità certificato univoca. Tuttavia, non tutti gli scenari o i componenti si prestano all'autenticazione basata su certificato. In questi scenari, l'estendibilità del framework di sicurezza offre alternative sicure. 

## <a name="authorization"></a>Authorization

Secondo il principio dei privilegi minimi, gli utenti e i componenti di un sistema devono avere accesso solo al set minimo di risorse e dati necessari per svolgere i rispettivi ruoli. I dispositivi, i moduli e gli attori devono ottenere l'accesso solo alle risorse e ai dati nel proprio ambito di autorizzazione e solo quando è consentito a livello di architettura. Alcune autorizzazioni sono configurabili con i privilegi e gli altri vengono applicati a livello di architettura.  Ad esempio, un modulo può essere autorizzato tramite la configurazione con privilegi per stabilire una connessione all'hub IoT di Azure. Tuttavia, non c'è motivo per cui un modulo in un dispositivo Azure IoT Edge debba accedere al gemello di un modulo in un altro dispositivo Azure IoT Edge.

Altri schemi di autorizzazione includono i diritti di firma del certificato e il controllo di accesso basato sui ruoli (RBAC). 

## <a name="attestation"></a>Attestazione

L'attestazione garantisce l'integrità dei bit di software.  È importante per il rilevamento e la prevenzione di malware.  Il framework di sicurezza di Azure IoT Edge classifica l'attestazione in tre categorie principali:

* Attestazione statica
* Attestazione di runtime
* Attestazione di software

### <a name="static-attestation"></a>Attestazione statica

L'attestazione statica verifica l'integrità di tutto il software in un dispositivo, inclusi sistema operativo, tutti i runtime e le informazioni di configurazione all'accensione del dispositivo. Viene spesso identificata come avvio protetto. Il framework di sicurezza per i dispositivi Azure IoT Edge si estende ai produttori e comprende funzionalità di sicurezza incorporate nell'hardware per garantire i processi di attestazione statica. Questi processi includono l'avvio protetto e l'aggiornamento del firmware sicuro.  La collaborazione con fornitori di processori elimina i livelli di firmware superflui riducendo al minimo l'esposizione agli attacchi. 

### <a name="runtime-attestation"></a>Attestazione di runtime

Quando un sistema completa un processo di avvio sicuro ed è attivo e in esecuzione, i sistemi progettati correttamente rilevano i tentativi di inserire malware e prendono contromisure appropriate. Gli attacchi di malware può essere destinata a porte e interfacce per accedere al sistema del sistema. In alternativa, se utenti malintenzionati hanno accesso fisico a un dispositivo, possono manometterlo o usare attacchi side-channel per ottenere l'accesso. Tali contenuti dannosi, che possono essere sotto forma di malware o modifiche alla configurazione non autorizzati, non è possibile rilevare dall'attestazione statica perché si è inserito dopo il processo di avvio. Le contromisure offerte o applicate dall'hardware del dispositivo aiutano ad allontanare tali minacce.  Il framework di sicurezza di Azure IoT Edge chiama in modo esplicito le estensioni che combattono le minacce di runtime.  

### <a name="software-attestation"></a>Attestazione di software

Tutti i sistemi integri, inclusi i sistemi perimetrali intelligenti, devono accettare le patch e aggiornamenti.  La sicurezza è importante per i processi di aggiornamento; in caso contrario questi possono essere vettori di potenziali minacce.  Il framework di sicurezza di Azure IoT Edge richiama gli aggiornamenti tramite pacchetti misurati e firmati per assicurare l'integrità della e autenticare l'origine dei pacchetti.  Questo standard è applicabile a tutti i sistemi operativi e ai bit di software dell'applicazione. 

## <a name="hardware-root-of-trust"></a>Radice di attendibilità dell'hardware

Per molti dispositivi perimetrali intelligenti, specialmente quelli nelle posizioni in cui potenziali utenti malintenzionati hanno accesso fisico al dispositivo, la sicurezza hardware è l'ultima difesa per la protezione. Per queste distribuzioni, è fondamentale l'uso di hardware resistente alle manomissioni. Azure IoT Edge incoraggia i fornitori di hardware per processori sicuri a offrire diverse varianti di radice di attendibilità dell'hardware che si adattino ai vari profili di rischio e ai diversi scenari di distribuzione. L'attendibilità hardware può anche provenire da standard comuni del protocollo di sicurezza come Trusted Platform Module (ISO/IEC 11889) e Device Identifier Composition Engine di Trusted Computing Group. Forniscono attendibilità hardware anche tecnologie enclave sicure quali TrustZones e Software Guard Extensions. 

## <a name="certification"></a>Certificazione

Per consentire ai clienti di prendere decisioni informate quando si servono di dispositivi di Azure IoT Edge per la distribuzione, il framework di Azure IoT Edge include i requisiti di certificazione.  Alla base di tali requisiti ci sono le certificazioni che riguardano le attestazioni di sicurezza e le certificazioni relative alla convalida di implementazione della protezione.  Ad esempio, una certificazione di attestazione di sicurezza potrebbe comunicare che il dispositivo di IoT Edge usa un hardware sicuro che notoriamente resiste agli attacchi all'avvio. Una certificazione di convalida potrebbe informare che l'hardware sicuro è stato implementato correttamente per offrire questo valore nel dispositivo.  Fedele al principio di semplicità, il framework prova a mantenere al minimo il carico di lavoro della certificazione.   

## <a name="extensibility"></a>Estensibilità

Se la tecnologia IoT guida i diversi tipi di trasformazioni aziendali, è logico che la sicurezza deve essere evolversi parallelamente agli scenari emergenti.  Il framework di sicurezza di Azure IoT Edge si basa su fondamenta solide su cui si costruisce l'estensibilità in diverse dimensioni per includere: 

* Servizi di sicurezza del produttore ad esemio il Servizio Device Provisioning per l'hub IoT di Azure.
* Servizi di terze parti come i servizi di sicurezza gestiti per diversi segmenti verticali dell'applicazione, ad esempio industria o settore sanitario, o i fulcri della tecnologia, ad esempio il monitoraggio in reti mesh o servizi di attestazione dell'hardware del processore, attraverso una ricca rete di partner.
* I sistemi legacy includono l'adattamento alle strategie di sicurezza alternative, ad esempio con la tecnologia di protezione diversa dai certificati per l'autenticazione e la gestione delle identità.
* Proteggere l'hardware per l'adozione di nuove tecnologie di protezione dell'hardware e contributi di partner produttori di processori.

Infine, il più grande successo nella protezione dei dispositivi perimetrali deriva dalla collaborazione di una community aperta guidata dall'interesse comune nella protezione dell'IoT.  Tali contributi potrebbero essere sotto forma di tecnologie o servizi di protezione.  Il framework di sicurezza di Azure IoT Edge offre una base solida per la sicurezza estensibile per avere la massima protezione e offrire lo stesso livello di attendibilità e di integrità nei dispositivi perimetrali intelligenti così come nel cloud di Azure.  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla sicurezza offerta da Azure IoT Edge, vedere [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).
