---
title: Domande frequenti sui servizi FHIR in Azure-API di Azure per FHIR
description: Risposte alle domande frequenti sull'API di Azure per FHIR, ad esempio la posizione di archiviazione dei dati dietro le API FHIR e il supporto della versione.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 252abcac6e9e39930593c1b110bf6d55ffdfc33f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843522"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Domande frequenti sull'API di Azure per FHIR

## <a name="azure-api-for-fhir"></a>API di Azure per FHIR

### <a name="what-is-fhir"></a>Che cos'è FHIR?
Le risorse di interoperabilità sanitaria veloci (FHIR "Fire") sono uno standard di interoperabilità progettato per consentire lo scambio di dati sanitari tra diversi sistemi di integrità. Questo standard è stato sviluppato dall'organizzazione HL7 e viene adottato dalle organizzazioni sanitarie in tutto il mondo. La versione più recente di FHIR disponibile è R4 (versione 4). L'API di Azure per FHIR supporta R4 e supporta anche la versione precedente STU3 (standard per l'uso della versione di valutazione 3). Per ulteriori informazioni su FHIR, visitare [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>I dati sottostanti le API FHIR sono archiviati in Azure?

Sì, i dati vengono archiviati in database gestiti in Azure. L'API di Azure per FHIR non fornisce accesso diretto all'archivio dati sottostante.

### <a name="what-identity-provider-do-you-support"></a>Quale provider di identità supporta?

Attualmente è supportato Microsoft Azure Active Directory come provider di identità.

### <a name="what-fhir-version-do-you-support"></a>Quale versione di FHIR è supportata?

Sono supportate le versioni 4.0.0 e 3.0.1 sia nell'API di Azure per FHIR (PaaS) che nel server FHIR per Azure (Open Source).

Per informazioni dettagliate, vedere [funzionalità supportate](fhir-features-supported.md). Leggere le informazioni sulle modifiche apportate tra le versioni nella [cronologia delle versioni per HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual è la differenza tra il server Microsoft FHIR open source per Azure e l'API di Azure per FHIR?

L'API di Azure per FHIR è una versione ospitata e gestita del server Microsoft FHIR open source per Azure. Nel servizio gestito Microsoft fornisce tutti gli aggiornamenti e la manutenzione. 

Quando si esegue FHIR server per Azure, è possibile accedere direttamente ai servizi sottostanti. Tuttavia, l'utente è anche responsabile della manutenzione e dell'aggiornamento del server e di tutte le operazioni di conformità necessarie se si archiviano dati PHI.

Dal punto di vista dello sviluppo, ogni funzionalità viene distribuita nel server Microsoft FHIR open source per Azure. Una volta convalidata in open source, verrà rilasciata alla soluzione API PaaS di Azure per FHIR. Il tempo tra il rilascio in open source e PaaS dipende dalla complessità della funzionalità e da altre priorità di roadmap. 

### <a name="what-is-smart-on-fhir"></a>Che cos'è SMART in FHIR?

Le applicazioni medicali intelligenti (sostituibili e la tecnologia riutilizzabile) in FHIR sono un set di specifiche aperte per integrare le applicazioni partner con i server FHIR e altri sistemi IT di integrità, ad esempio i record di integrità elettronici e gli scambi di informazioni sull'integrità. Grazie alla creazione di un'applicazione SMART on FHIR, è possibile assicurarsi che l'applicazione sia accessibile e sfruttata da una pletora di sistemi diversi.
Autenticazione e API di Azure per FHIR. Per ulteriori informazioni su SMART, vedere l'articolo relativo all' [integrità intelligente](https://smarthealthit.org/).

## <a name="azure-iot-connector-for-fhir-preview"></a>Connettore Azure per FHIR (anteprima)

### <a name="what-is-iomt"></a>Che cos'è IoMT?
IoMT è l'acronimo di Internet of Medical Things ed è una categoria di dispositivi Internet per l'acquisizione e lo scambio di dati di integrità e wellness con altri sistemi IT di assistenza sanitaria in rete. Alcuni esempi di dispositivi IoMT includono dispositivi indossabili per attività sportive e cliniche, sensori di monitoraggio, GPS, dispositivi point-of-care o persino pillole intelligenti.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Quanti sono I connettori di Azure per FHIR (anteprima) necessari?
Per inserire dati da un numero elevato di diversi tipi di dispositivi, è possibile usare un singolo connettore Azure per FHIR *. È comunque possibile decidere di usare connettori diversi per i motivi seguenti:
- **Scala**: per l'anteprima pubblica, il connettore Azure per la capacità delle risorse FHIR è fisso e prevede una velocità effettiva di circa 200 messaggi al secondo. Se è necessaria una velocità effettiva più elevata, è possibile aggiungere altro connettore Azure per FHIR.
- **Tipo di dispositivo**: è possibile configurare un connettore di Azure per FHIR separato per ogni tipo di dispositivi IoMT disponibili per motivi di gestione dei dispositivi.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>È previsto un limite per il numero di connettore Azure per FHIR (anteprima) durante l'anteprima pubblica?
Sì, è possibile creare solo due connettori Azure per FHIR per ogni sottoscrizione mentre la funzionalità è in anteprima pubblica. Questo limite esiste per evitare spese impreviste perché la funzionalità è disponibile gratuitamente durante l'anteprima. Su richiesta questo limite può essere aumentato fino a un massimo di cinque connettori Azure Internet per FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Quali sono le aree di Azure il connettore Azure per la funzionalità FHIR (anteprima) è disponibile durante l'anteprima pubblica?
Il connettore Azure per FHIR è disponibile in tutte le aree di Azure in cui è disponibile l'API di Azure per FHIR.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>È possibile configurare la capacità di scalabilità per il connettore Azure per FHIR (anteprima)?
Poiché il connettore Azure per FHIR è gratuito durante l'anteprima pubblica, la capacità di scalabilità è fissa e limitata. Il connettore Azure per la configurazione FHIR disponibile in versione di anteprima pubblica è previsto per fornire una velocità effettiva di circa 200 messaggi al secondo. Una qualche forma di configurazione della capacità delle risorse verrà resa disponibile a livello generale (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Quale versione di FHIR è supportata dal connettore Azure per FHIR (anteprima)?
Il connettore Azure per FHIR attualmente supporta solo FHIR versione R4. Questa funzionalità è quindi visibile solo nelle istanze R4 dell'API di Azure per FHIR e Microsoft non prevede di supportare la versione STU3 al momento.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Perché non è possibile installare il connettore Azure per FHIR (anteprima) quando è abilitato il collegamento privato nell'API di Azure per FHIR?
Il connettore Azure per FHIR non supporta al momento la funzionalità di collegamento privato. Di conseguenza, se si dispone di un collegamento privato abilitato nell'API di Azure per FHIR, non è possibile installare il connettore Azure per FHIR e viceversa. Questa limitazione si prevede di scomparire quando il connettore Azure per FHIR è disponibile per la disponibilità a livello generale (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Qual è la differenza tra il connettore IoMT FHIR open source per Azure e il connettore Azure Internet per la funzionalità FHIR (anteprima) dell'API di Azure per il servizio FHIR?
Il connettore Azure per FHIR è una versione ospitata e gestita del connettore open source IoMT FHIR per Azure. Nel servizio gestito Microsoft fornisce tutti gli aggiornamenti e la manutenzione.

Quando si esegue IoMT FHIR Connector per Azure, è possibile accedere direttamente alle risorse sottostanti. Tuttavia, l'utente è anche responsabile della manutenzione e dell'aggiornamento del server e di tutte le operazioni di conformità necessarie se si archiviano dati PHI.

Dal punto di vista dello sviluppo, ogni funzionalità viene distribuita al connettore open source IoMT FHIR per Azure. Una volta convalidata in open source, verrà rilasciata al connettore Azure PaaS per FHIR per la funzionalità dell'API di Azure per il servizio FHIR. Il tempo tra la versione in open source e PaaS dipende dalla complessità della funzionalità e da altre priorità della mappa stradale.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha letto alcune delle domande frequenti sull'API di Azure per FHIR. Leggere le informazioni sulle funzionalità supportate in FHIR server per Azure:
 
>[!div class="nextstepaction"]
>[Funzionalità di FHIR supportate](fhir-features-supported.md)

* Nel portale di Azure, il connettore Azure per FHIR viene indicato come connettore Internet (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.