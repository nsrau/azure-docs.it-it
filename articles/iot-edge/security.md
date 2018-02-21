---
title: Sicurezza in Azure IoT Edge | Microsoft Docs
description: Sicurezza, autenticazione e autorizzazione dei dispositivi IoT Edge
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8a5bf1f35fcdd779cf27edeba7dfd5705cbae205
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="securing-azure-iot-edge---preview"></a>Sicurezza in Azure IoT Edge - anteprima

La sicurezza dei dispositivi perimetrali intelligenti è necessaria per attribuire affidabilità nell'esecuzione di una soluzione IoT end-to-end. Azure IoT Edge è progettato per la sicurezza che può essere estesa a diversi profili di rischi, scenari di distribuzione e offre la stessa protezione che ci si aspetta da tutti i servizi di Azure.

Azure IoT Edge viene eseguito su diversi hardware, supporta sia Windows che Linux ed è applicabile a diversi scenari di distribuzione.  La valutazione del rischio dipende da molte considerazioni che includono la proprietà della soluzione, la geografia di distribuzione, la riservatezza dei dati, la privacy, il segmento verticale dell'applicazione e i requisiti normativi.  Invece di offrire soluzioni concrete per scenari specifici, è opportuno progettare un framework di sicurezza estensibile basato su principi ponderati progettati su larga scala. 
 
Questo articolo offre una panoramica sul framework di sicurezza. Per altre informazioni, vedere [Securing the intelligent edge][lnk-edge-blog] (Protezione dei dispositivi perimetrali intelligenti).

>[!NOTE]
>Il framework di sicurezza descritto di seguito verrà aggiunto al prodotto a breve e sarà disponibile a livello generale con Azure IoT Edge. Il prodotto è attualmente in anteprima pubblica, una versione che consente lo sviluppo e la creazione di prototipi di soluzioni perimetrali, non distribuzioni di produzione complete che richiedono il framework di sicurezza avanzata.   

## <a name="standards"></a>Standard

Gli standard promuovo un controllo e un'implementazione semplice, che sono alla base della sicurezza.  Una soluzione di sicurezza ben strutturata si presta a controlli durante il processo di valutazione per creare affidabilità e non deve essere un ostacolo alla distribuzione.  La progettazione di framework per la protezione di Azure IoT Edge si diffonde da protocolli di sicurezza collaudati e testati nel settore per sfruttare la familiarità e il riutilizzo. 

## <a name="authentication"></a>Authentication

Conoscere gli attori, i dispositivi e i componenti che partecipano alla creazione del valore tramite una soluzione IoT end-to-end è fondamentale nella creazione dell'affidabilità.  Tale conoscenza offre una responsabilità sicura dei partecipanti all'abilitazione della base per l'ammissione.  Azure IoT Edge raggiunge questa conoscenza tramite l'autenticazione.  Il meccanismo principale per l'autenticazione della piattaforma di Azure IoT Edge è l'autenticazione basata su certificato.  Questo meccanismo deriva da un set di standard che regolano l'infrastruttura a chiave pubblica da IETF.     

Il framework di sicurezza di Azure IoT Edge richiama le identità di un certificato univoco per tutti i dispositivi, i moduli, ovvero i contenitori che incapsulano la logica all'interno del dispositivo, e gli attori che interagiscono con il dispositivo di Azure IoT Edge fisicamente o tramite una connessione di rete.  Non tutti gli scenari o i componenti si prestano all'autenticazione basata su certificato per cui l'estensibilità del framework di sicurezza offre vie sicure di facilitazione. 

## <a name="authorization"></a>Authorization

La possibilità di delegare l'autorità e di controllare l'accesso è fondamentale per ottenere un principio di sicurezza molto importante: il principio di privilegio minimo.  I dispositivi, i moduli e gli attori possono ottenere l'accesso solo alle risorse e ai dati nel proprio ambito di autorizzazione e solo quando è consentito a livello di architettura.  Ciò significa che alcune autorizzazioni sono configurabili con privilegi sufficienti e altre vengono applicate a livello di architettura.  Ad esempio, se un modulo può essere autorizzato tramite la configurazione con privilegi per stabilire una connessione all'hub IoT di Azure, non c'è motivo per cui un modulo in un dispositivo di Azure IoT Edge debba accedere a un gemello di un modulo in un altro dispositivo di Azure IoT Edge.  Per questo motivo, quest'ultimo potrebbe essere strutturalmente precluso. 

Altri schemi di autorizzazione includono i diritti di firma del certificato e il controllo di accesso basato sui ruoli.  L'estensibilità del framework di sicurezza consente l'adozione di altri schemi di autorizzazione avanzata. 

## <a name="attestation"></a>Attestazione

L'attestazione garantisce l'integrità dei bit di software.  È importante per il rilevamento e la prevenzione di malware.  Il framework di sicurezza di Azure IoT Edge classifica l'attestazione in tre categorie principali:

* Attestazione statica
* Attestazione di runtime
* Attestazione di software

### <a name="static-attestation"></a>Attestazione statica

L'attestazione statica è la verifica dell'integrità di tutti i bit di software, inclusi i sistemi operativi, tutti i runtime e le informazioni di configurazione all'accensione del dispositivo.  Viene spesso identificata come avvio protetto.  Il framework di sicurezza per i dispositivi di Azure IoT Edge si estende per i fornitori di processori e comprende le funzionalità di sicurezza incorporate nell'hardware per garantire i processi di attestazione statica. Questi processi includono l'avvio protetto e i processi di aggiornamento del firmware sicuro.  La collaborazione con fornitori di processori elimina i livelli di firmware superflui riducendo così al minimo l'esposizione agli attacchi. 

### <a name="runtime-attestation"></a>Attestazione di runtime

Quando un sistema completa un processo di avvio convalidato ed è attivo e in esecuzione, i sistemi protetti progettati correttamente rilevano i tentativi di inserire i malware attraverso le porte e le interfacce dei sistemi e prendono contromisure appropriate.  Per i dispositivi perimetrali intelligenti in custodia fisica di attori malintenzionati, è possibile inserire i contenuti dannosi attraverso vie diverse dalle interfacce dei dispositivi quali manomissioni e attacchi da canali laterali.   Tali contenuti dannosi, che possono essere sotto forma di malware o modifiche alla configurazione non autorizzate, in genere non vengono rilevati dal processo di avvio protetto perché si verificano in seguito al processo di avvio.  Le contromisure offerte o applicate dall'hardware del dispositivo contribuiscono notevolmente ad allontanare tali minacce.  Il framework di sicurezza di Azure IoT Edge richiama in modo esplicito le estensioni per combattere le minacce di runtime.     

### <a name="software-attestation"></a>Attestazione di software

Tutti i sistemi integri, inclusi i sistemi perimetrali intelligenti devono essere adatti per gli aggiornamenti e le patch.  La sicurezza è importante per i processi di aggiornamento; in caso contrario questi possono essere vettori di potenziali minacce.  Il framework di sicurezza di Azure IoT Edge richiama gli aggiornamenti tramite pacchetti misurati e firmati per assicurare l'integrità e autenticare l'origine dei pacchetti.  Questo è applicabile a tutti i sistemi operativi e ai bit di software dell'applicazione. 

## <a name="hardware-root-of-trust"></a>Radice di attendibilità dell'hardware

Per molte distribuzioni dei dispositivi intelligenti perimetrali, specialmente quelli distribuiti nelle posizioni in cui i potenziali attori dannosi accedono fisicamente al dispositivo, la sicurezza offerta dall'hardware del dispositivo è l'ultima difesa per la protezione.  Per questo motivo, è fondamentale per tali distribuzioni ritenere affidabili gli hardware resistenti alla manomissione.  Il framework di sicurezza di Azure IoT Edge comporta la collaborazione di fornitori di hardware per processori sicuri per offrire diverse varianti di radice di attendibilità dell'hardware che si adattino ai vari profili di rischio e ai diversi scenari di distribuzione. Questi includono l'uso di processori sicuri conformi agli standard comuni del protocollo di sicurezza come Trusted Platform Module (ISO/IEC 11889) e Device Identifier Composition Engine di Trusted Computing Group.  Sono incluse anche le tecnologie enclave sicure quali TrustZones e Software Guard Extensions. 

## <a name="certification"></a>Certificazione

Per consentire ai clienti di prendere decisioni informate quando si servono di dispositivi di Azure IoT Edge per la distribuzione, il framework di Azure IoT Edge include i requisiti di certificazione.  Alla base di tali requisiti ci sono le certificazioni che riguardano le attestazioni di sicurezza e le certificazioni relative alla convalida di implementazione della protezione.  Ad esempio, una certificazione di attestazione di sicurezza potrebbe comunicare che il dispositivo di IoT Edge usa un hardware sicuro che notoriamente resiste agli attacchi all'avvio. Una certificazione di convalida potrebbe informare che l'hardware sicuro è stato implementato correttamente per offrire questo valore nel dispositivo.  Fedele al principio di semplicità, il framework offre la visione di mantenere al minimo il carico di lavoro della certificazione.   

## <a name="extensibility"></a>Estensibilità

L'estensibilità è una caratteristica di prima classe nel framework di sicurezza di Azure IoT Edge.  Se la tecnologia IoT guida i diversi tipi di trasformazioni aziendali, è logico che la sicurezza deve essere indirizzata agli scenari emergenti.  Il framework di sicurezza di Azure IoT Edge si basa su fondamenta solide su cui si costruisce l'estensibilità in diverse dimensioni per includere: 

* Servizi di sicurezza del produttore ad esemio il Servizio Device Provisioning per l'hub IoT di Azure.
* Servizi di terze parti come i servizi di sicurezza gestiti per diversi segmenti verticali dell'applicazione, ad esempio industria o settore sanitario, o i fulcri della tecnologia, ad esempio il monitoraggio in reti mesh o servizi di attestazione dell'hardware del processore, attraverso una ricca rete di partner.
* I sistemi legacy includono l'adattamento alle strategie di sicurezza alternative, ad esempio con la tecnologia di protezione diversa dai certificati per l'autenticazione e la gestione delle identità.
* Proteggere l'hardware per la facile adozione di nuove tecnologie di protezione dell'hardware e contributi di partner produttori di processori.

Questi sono solo alcuni esempi di dimensioni per l'estensibilità. Il framework di sicurezza di Azure IoT Edge è progettato per essere protetto interamente e per supportare tale estensibilità. 

Infine, il più grande successo nella protezione dei dispositivi perimetrali deriva dalla collaborazione di una community aperta guidata dall'interesse comune nella protezione dell'IoT.  Tali contributi potrebbero essere sotto forma di tecnologie o servizi di protezione.  Il framework di sicurezza di Azure IoT Edge offre una base solida per la sicurezza estensibile per avere la massima protezione e offrire lo stesso livello di attendibilità e di integrità nei dispositivi perimetrali intelligenti così come nel cloud di Azure.  

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come Azure IoT Edge è la [Securing the intelligent edge][lnk-edge-blog](Protezione dei dispositivi perimetrali intelligenti).

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 