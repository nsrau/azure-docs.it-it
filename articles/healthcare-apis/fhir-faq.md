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
ms.openlocfilehash: d013af82a862f1bc6b5324e7523aeea5593a4f94
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030663"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Domande frequenti sull'API di Azure per FHIR

## <a name="azure-api-for-fhir-the-basics"></a>API di Azure per FHIR: Nozioni di base

### <a name="what-is-fhir"></a>Che cos'è FHIR?
Le risorse di interoperabilità sanitaria veloci (FHIR "Fire") sono uno standard di interoperabilità progettato per consentire lo scambio di dati sanitari tra diversi sistemi di integrità. Questo standard è stato sviluppato dall'organizzazione HL7 e viene adottato dalle organizzazioni sanitarie in tutto il mondo. La versione più recente di FHIR disponibile è R4 (versione 4). L'API di Azure per FHIR supporta R4 e supporta anche la versione precedente STU3 (standard per l'uso della versione di valutazione 3). Per ulteriori informazioni su FHIR, visitare [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>I dati sottostanti le API FHIR sono archiviati in Azure?

Sì, i dati vengono archiviati in database gestiti in Azure. L'API di Azure per FHIR non fornisce accesso diretto all'archivio dati sottostante.

### <a name="what-identity-provider-do-you-support"></a>Quale provider di identità supporta?

Attualmente è supportato Microsoft Azure Active Directory come provider di identità.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Qual è l'obiettivo del punto di ripristino (RPO) per l'API di Azure per FHIR?
L'API di Azure per FHIR è supportata da Cosmos DB come provider di persistenza. Per questo motivo, il RPO per il servizio è uguale [Cosmos DB (area singola)](https://docs.microsoft.com/azure/cosmos-db/consistency-levels) e è < 240 minuti.

### <a name="what-fhir-version-do-you-support"></a>Quale versione di FHIR è supportata?

Sono supportate le versioni 4.0.0 e 3.0.1 sia nell'API di Azure per FHIR (PaaS) che nel server FHIR per Azure (Open Source).

Per informazioni dettagliate, vedere [funzionalità supportate](fhir-features-supported.md). Informazioni sulle modifiche apportate tra le versioni di FHIR, ad esempio STU3 e R4, nella [cronologia delle versioni per HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Il connettore Azure per FHIR (anteprima) attualmente supporta solo FHIR versione R4 ed è visibile solo nelle istanze R4 dell'API di Azure per FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual è la differenza tra "Microsoft FHIR Server for Azure" e "Azure API for FHIR"?

L'API di Azure per FHIR è una versione ospitata e gestita del server Microsoft FHIR open source per Azure. Nel servizio gestito Microsoft fornisce tutti gli aggiornamenti e la manutenzione. 

Quando si esegue il server FHIR per Azure, è possibile accedere direttamente ai servizi sottostanti, ma è responsabile della manutenzione e dell'aggiornamento del server e di tutte le operazioni di conformità necessarie se si archiviano i dati di PHI.

Per un punto di vista dello sviluppo, ogni funzionalità che non si applica solo al servizio gestito viene innanzitutto distribuita nel server Microsoft FHIR open source per Azure. Una volta convalidata in open source, verrà rilasciata alla soluzione API PaaS di Azure per FHIR. Il tempo tra il rilascio in open source e PaaS dipende dalla complessità della funzionalità e da altre priorità di roadmap. Si tratta dello stesso processo per tutti i servizi, ad esempio il connettore Azure per FHIR (anteprima).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Dove è possibile vedere cosa si sta rilasciando nell'API di Azure per FHIR?

Per visualizzare alcuni elementi che vengono rilasciati nell'API di Azure per FHIR, fare riferimento alla [versione](https://github.com/microsoft/fhir-server/releases) del server FHIR open source. A partire dal 2020 novembre, sono stati contrassegnati gli elementi con Azure-API-for-FHIR se l'elemento Open Source rilascerà il servizio gestito. Queste funzionalità sono in genere disponibili due settimane dopo che sono state rilasciate nella pagina versione in open source. Sono inoltre disponibili istruzioni su come testare la build [qui] (se si desidera eseguire il https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) test nel proprio ambiente. Si sta valutando come condividere meglio gli aggiornamenti del servizio gestito aggiuntivi.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>In quali aree è disponibile l'API di Azure per FHIR?

Attualmente è disponibile la disponibilità generale per enti pubblici e governativi in [più aree](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)geografiche. Per informazioni sui servizi cloud per enti pubblici in Microsoft, consultare i [servizi di Azure di FedRAMP](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Dove è possibile vedere cosa si sta rilasciando nell'API di Azure per FHIR?

Per visualizzare alcuni elementi che vengono rilasciati nell'API di Azure per FHIR, fare riferimento alla [versione](https://github.com/microsoft/fhir-server/releases) del server FHIR open source. Si è lavorato per contrassegnare gli elementi con Azure-API-for-FHIR se verranno rilasciati al servizio gestito e sono in genere disponibili due settimane dopo esse nella pagina di rilascio in open source. Sono inoltre disponibili istruzioni su come testare la compilazione [qui](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) se si desidera eseguire il test nel proprio ambiente. Si sta valutando come condividere meglio gli aggiornamenti del servizio gestito aggiuntivi.

### <a name="what-is-smart-on-fhir"></a>Che cos'è SMART in FHIR?

Le applicazioni medicali intelligenti (sostituibili e la tecnologia riutilizzabile) in FHIR sono un set di specifiche aperte per integrare le applicazioni partner con i server FHIR e altri sistemi IT di integrità, ad esempio i record di integrità elettronici e gli scambi di informazioni sull'integrità. Grazie alla creazione di un'applicazione SMART on FHIR, è possibile assicurarsi che l'applicazione sia accessibile e sfruttata da una pletora di sistemi diversi.
Autenticazione e API di Azure per FHIR. Per ulteriori informazioni su SMART, vedere l'articolo relativo all' [integrità intelligente](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Dove è possibile trovare la versione di FHIR in esecuzione nel database. 

È possibile trovare la versione esatta di FHIR esposta nell'istruzione capability nella proprietà "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>Specifiche e implementazioni di FHIR

### <a name="can-i-create-a-custom-fhir-resource"></a>È possibile creare una risorsa FHIR personalizzata?

Non sono consentite risorse FHIR personalizzate. Se è necessaria una risorsa FHIR personalizzata, è possibile creare una risorsa personalizzata sulla [risorsa di base](http://www.hl7.org/fhir/basic.html) con le estensioni. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Le [estensioni](https://www.hl7.org/fhir/extensibility.html) sono supportate nell'API di Azure per FHIR?

È possibile caricare tutti i dati JSON FHIR validi nel server. Se si desidera archiviare la definizione della struttura che definisce l'estensione, è possibile salvarla come una risorsa di definizione della struttura. Attualmente non è possibile eseguire ricerche nelle estensioni.

### <a name="what-is-the-limit-on-_count"></a>Qual è il limite per _count?

Il limite corrente _count è 100. Se si imposta _count su un valore superiore a 100, verrà visualizzato un avviso nel bundle in cui verranno visualizzati solo i record 100.

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

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Qual è l'ordinamento predefinito durante la ricerca di risorse nell'API di Azure per FHIR?

È supportata l'ordinamento in base alla data dell'ultimo aggiornamento: _sort = _lastUpdated. Per ulteriori informazioni su altri parametri di ricerca supportati, vedere la [pagina relativa alle funzionalità supportate](./fhir-features-supported.md#search).

### <a name="how-does-export-work"></a>In che modo $export funziona?

$export fa parte della specifica FHIR: https://hl7.org/fhir/uv/bulkdata/export/index.html . Se il servizio FHIR è configurato con un'identità gestita e un account di archiviazione e se l'identità gestita ha accesso a tale account di archiviazione, è possibile chiamare semplicemente $export sull'API FHIR e tutte le risorse FHIR verranno esportate nell'account di archiviazione. Per altre informazioni, vedere l' [articolo $Export](./export-data.md).

## <a name="using-azure-api-for-fhir"></a>Uso dell'API di Azure per FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Ricerca per categorie abilitare log Analytics per l'API di Azure per FHIR?

Si Abilita la registrazione diagnostica e si consente la revisione delle query di esempio per questi log. Per informazioni dettagliate sull'abilitazione dei log di controllo e delle query di esempio, vedere [questa sezione](./enable-diagnostic-logging.md). Se si desidera includere informazioni aggiuntive nei log, vedere [utilizzo di intestazioni HTTP personalizzate](./use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Dove è possibile vedere alcuni esempi di uso dell'API di Azure per FHIR all'interno di un flusso di lavoro?

È disponibile una raccolta di architetture di riferimento nella pagina di [GitHub sull'architettura dell'integrità](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Dove è possibile vedere un esempio di connessione di un'applicazione Web ad API di Azure per FHIR?

È disponibile una [pagina di GitHub sull'architettura dell'integrità](https://aka.ms/health-architectures) che contiene applicazioni e scenari di esempio. Viene illustrato come connettere un'applicazione Web all'API di Azure per FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Funzionalità e servizi di API di Azure per FHIR 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Esiste un modo per crittografare i dati usando la chiave personale non una chiave predefinita?

Sì, l'API di Azure per FHIR consente di configurare le chiavi gestite dal cliente, sfruttando il supporto di Cosmos DB. Per altre informazioni sulla crittografia dei dati con una chiave personale, vedere [questa sezione](./customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>API di Azure per FHIR: funzionalità di anteprima

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>È possibile configurare la capacità di scalabilità per il connettore Azure per FHIR (anteprima)?

Poiché il connettore Azure per FHIR è gratuito durante l'anteprima pubblica, la capacità di scalabilità è fissa e limitata. Il connettore Azure per la configurazione FHIR disponibile in versione di anteprima pubblica è previsto per fornire una velocità effettiva di circa 200 messaggi al secondo. Una qualche forma di configurazione della capacità delle risorse verrà resa disponibile a livello generale (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Perché non è possibile installare il connettore Azure per FHIR (anteprima) quando è abilitato il collegamento privato nell'API di Azure per FHIR?

Il connettore Azure per FHIR non supporta al momento la funzionalità di collegamento privato. Di conseguenza, se si dispone di un collegamento privato abilitato nell'API di Azure per FHIR, non è possibile installare il connettore Azure per FHIR e viceversa. Questa limitazione si prevede di scomparire quando il connettore Azure per FHIR è disponibile per la disponibilità a livello generale (GA).
