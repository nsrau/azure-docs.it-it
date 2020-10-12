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
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269710"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>È possibile creare una risorsa FHIR personalizzata?

Non sono consentite risorse FHIR personalizzate. Se è necessaria una risorsa FHIR personalizzata, è possibile creare una risorsa personalizzata sulla [risorsa di base](http://www.hl7.org/fhir/basic.html) con le estensioni. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Le [estensioni](https://www.hl7.org/fhir/extensibility.html) sono supportate nell'API di Azure per FHIR?

È possibile caricare tutti i dati JSON FHIR validi nel server. Se si desidera archiviare la definizione della struttura che definisce l'estensione, è possibile salvarla come una risorsa di definizione della struttura. Attualmente non è possibile eseguire ricerche nelle estensioni.

### <a name="what-is-the-limit-on-_count"></a>Qual è il limite per _count?

Il limite corrente per il conteggio è 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Esistono limitazioni alla funzionalità di esportazione del gruppo?

Per l'esportazione del gruppo vengono esportati solo i riferimenti inclusi dal gruppo, non tutte le caratteristiche della [risorsa del gruppo](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>È possibile pubblicare un bundle nell'API di Azure per FHIR?

È attualmente supportata la pubblicazione di [bundle di batch](https://www.hl7.org/fhir/valueset-bundle-type.html) , ma non è supportata la pubblicazione di bundle di transazioni nell'API di Azure per FHIR. È possibile usare il server FHIR Open Source supportato da SQL per inviare i bundle di transazione.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Come è possibile ottenere tutte le risorse per un singolo paziente nell'API di Azure per FHIR?

È supportata la [ricerca di raggruppamento](https://www.hl7.org/fhir/compartmentdefinition.html) nell'API di Azure per FHIR. In questo modo è possibile ottenere tutte le risorse correlate a un determinato paziente. Si noti che in questo momento il raggruppamento include tutte le risorse correlate al paziente ma non il paziente stesso, quindi è necessario eseguire una ricerca anche per ottenere il paziente se è necessaria la risorsa paziente nei risultati.

Di seguito sono riportati alcuni esempi:

* Ottieni paziente/<id>/*
* GET patient/ <id> /Observation
* GET patient/ <id> /Observation? code = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Dove è possibile vedere alcuni esempi di uso dell'API di Azure per FHIR all'interno di un flusso di lavoro?

È disponibile una raccolta di architetture di riferimento nella pagina di [GitHub sull'architettura dell'integrità](https://github.com/microsoft/health-architectures).

## <a name="azure-iot-connector-for-fhir-preview"></a>Connettore Azure IoT per FHIR (anteprima)

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

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.