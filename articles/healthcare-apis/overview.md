---
title: Che cos'è l'API di Azure per FHIR? - API di Azure per FHIR
description: L'API di Azure per FHIR consente lo scambio rapido di dati tramite le API di FHIR. Inserire, gestire e salvare in modo permanente le informazioni sanitarie protette (PHI, Protected Health Information) con un servizio cloud gestito.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "84819932"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Che cos'è l'API di Azure per FHIR&reg;?

L'API di Azure per FHIR consente lo scambio di dati rapido tramite le API per FHIR® (Fast Healthcare Interoperability Resources) ed è supportata da un'offerta PaaS (Platform-as-a-Service) gestita nel cloud. Dedicata agli operatori del settore sanitario, semplifica l'inserimento, la gestione e il salvataggio permanente delle informazioni sanitarie protette ([PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)) nel cloud: 

- Servizio FHIR gestito, con provisioning nel cloud in pochi minuti 
- Endpoint basato su FHIR® di livello aziendale in Azure per l'accesso ai dati e L'archiviazione in formato FHIR®
- Prestazioni elevate, bassa latenza
- Gestione sicura dei dati sanitari protetti (PHI) in un ambiente cloud conforme
- SMART on FHIR per implementazioni Web e per dispositivi mobili
- Controllo dei dati su larga scala con il controllo degli accessi in base al ruolo
- Monitoraggio dei log di controllo per l'accesso, la creazione, la modifica e le letture in ogni archivio dati

L'API di Azure per FHIR consente di creare e distribuire un servizio FHIR in pochi minuti per sfruttare la scalabilità elastica del cloud.  Verranno addebitati solo i costi per la velocità effettiva e lo spazio di archiviazione necessari. I servizi di Azure su cui è basata l'API di Azure per FHIR sono progettati per prestazioni rapide indipendentemente dalle dimensioni dei set di dati gestiti.

L'API per FHIR e l'archivio dati conforme consentono di connettersi e interagire in modo sicuro con qualsiasi sistema che usa le API per FHIR.  Microsoft gestisce automaticamente le operazioni, la manutenzione, gli aggiornamenti e i requisiti di conformità nell'offerta PaaS, per consentire all'utente di liberare risorse operative e di sviluppo. 

Il video seguente presenta una panoramica dell'API di Azure per FHIR:

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Leveraging the power of your data with FHIR (Sfruttare la potenza dei dati con FHIR)

Il settore sanitario sta trasformando rapidamente i dati sanitari nello standard emergente [FHIR&reg;](https://hl7.org/fhir) (Fast Healthcare Interoperability Resources). FHIR offre un modello di dati potente ed estendibile con semantica standardizzata e scambio di dati che consente a tutti i sistemi che usano FHIR di collaborare.  La conversione dei dati nel formato FHIR permette di connettere rapidamente origini dati esistenti, ad esempio sistemi di cartelle cliniche elettroniche o database di ricerca. FHIR consente inoltre di scambiare rapidamente i dati nelle implementazioni moderne di sviluppo per dispositivi mobili e Web. In particolare, FHIR può semplificare l'inserimento dei dati e accelerare lo sviluppo con strumenti di analisi e apprendimento automatico.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Gestire in modo sicuro i dati sanitari nel cloud

L'API di Azure per FHIR consente lo scambio di dati tramite API FHIR RESTful coerenti in base alla specifica HL7 FHIR. Supportata da un'offerta PaaS gestita in Azure, fornisce anche un ambiente scalabile e sicuro per la gestione e l'archiviazione dei dati sanitari protetti (PHI) nel formato FHIR nativo.  

### <a name="free-up-your-resources-to-innovate"></a>Liberare risorse per l'innovazione

Grazie all'API di Azure per FHIR è possibile investire le risorse disponibili per creare ed eseguire un servizio FHIR personalizzato, per permettere così a Microsoft di gestire automaticamente il carico di lavoro di operazioni, manutenzione, aggiornamenti e requisiti di conformità, senza appesantire il personale operativo e di sviluppo.

### <a name="enable-interoperability-with-fhir"></a>Abilitare l'interoperabilità con FHIR

L'API di Azure per FHIR consente di connettersi a qualsiasi sistema che sfrutta le API FHIR per la lettura, la scrittura, la ricerca e altre funzioni.  Può essere usato come strumento potente per consolidare, normalizzare e applicare l'apprendimento automatico con dati clinici da cartelle cliniche elettroniche, dashboard di clinici e pazienti, programmi di monitoraggio remoto o con database con API FHIR esterni al proprio sistema.

### <a name="control-data-access-at-scale"></a>Controllare l'accesso ai dati su larga scala

Pieno controllo sui dati. Il controllo degli accessi in base al ruolo consente di gestire la modalità di archiviazione e accesso ai dati.  Garantendo una maggiore sicurezza e riducendo il carico di lavoro amministrativo, è possibile determinare chi ha accesso ai set di dati creati, in base alle definizioni dei ruoli create per l'ambiente.  

### <a name="audit-logs-and-tracking"></a>Log di controllo e monitoraggio 

I log di controllo predefiniti permettono di monitorare facilmente l'utilizzo dei dati per l'accesso, la creazione, la modifica e le letture in ogni archivio dati.

### <a name="secure-your-data"></a>Protezione dei dati

È possibile proteggere le informazioni sanitarie protette (PHI) mediante intelligence sulla sicurezza senza precedenti.  I dati sono isolati in un database univoco per ogni istanza dell'API e protetti con il failover in più aree. L'API di Azure per FHIR implementa una difesa a più livelli e approfondita e la protezione avanzata dalle minacce per i dati.  

## <a name="applications-for-a-fhir-service"></a>Applicazioni per un servizio FHIR

I server FHIR sono strumenti fondamentali per l'interoperabilità dei dati sanitari.  L'API di Azure per FHIR è progettata come un'API e un servizio che è possibile creare, distribuire e iniziare a usare rapidamente.  Man mano che lo standard FHIR si espande nel settore sanitario, i casi d'uso continueranno a crescere, ma di seguito sono indicate alcune tra le prime applicazioni dei clienti che mostrano l'utilità dell'API di Azure per FHIR: 

- **Startup/IoT e sviluppo di app:**  I clienti che sviluppano un'app incentrata su pazienti o fornitori di servizi sanitari (per dispositivi mobili o Web) possono sfruttare l'API di Azure per FHIR come servizio back-end completamente gestito. L'API di Azure per FHIR offre una risorsa preziosa per consentire ai clienti di gestire i dati e scambiarli in un ambiente cloud sicuro progettato per i dati sanitari, di sfruttare le linee guida per l'implementazione di SMART on FHIR e abilitare la tecnologia per l'uso da parte di tutti i sistemi dei fornitori di servizi sanitari, ad esempio per la maggior parte delle cartelle cliniche elettroniche sono abilitate le API di lettura per FHIR.   
- **Ecosistemi sanitari:**  Sebbene le cartelle cliniche elettroniche costituiscano la 'fonte di verità' principale in molti scenari clinici, non è insolito che i fornitori di servizi sanitari dispongano di più database non connessi tra loro o che archivino dati in formati diversi.  L'uso dell'API di Azure per FHIR come servizio oltre a questi sistemi consente di standardizzare i dati nel formato FHIR  e abilitare lo scambio di dati tra più sistemi con un formato di dati coerente. 

- **Ricerca:** I ricercatori del settore sanitario riconosceranno l'utilità dello standard FHIR in generale e dell'API di Azure per FHIR perché normalizza i dati in un modello di dati FHIR comune e riduce il carico di lavoro per l'apprendimento automatico e la condivisione dei dati.
Lo scambio di dati tramite l'API di Azure per FHIR offre log di controllo e controlli di accesso che consentono di controllare il flusso di dati e chi può accedere a quali tipi di dati. 

## <a name="fhir-from-microsoft"></a>FHIR di Microsoft

Le funzionalità FHIR di Microsoft sono disponibili in due configurazioni:

* API di Azure per FHIR: un'offerta PaaS in Azure, con provisioning semplificato nel portale di Azure e gestita da Microsoft.
* Server FHIR per Azure: un progetto open source che può essere distribuito nella sottoscrizione di Azure, disponibile in GitHub all'indirizzo https://github.com/Microsoft/fhir-server.

Per i casi d'uso che richiedono l'estensione o la personalizzazione del server FHIR o richiedono l'accesso ai servizi sottostanti, ad esempio il database, senza passare attraverso le API FHIR, gli sviluppatori dovranno scegliere il server FHIR open source per Azure.   Per l'implementazione di un'API FHIR chiavi in mano pronta per l'ambiente di produzione e un di servizio back-end in cui è possibile accedere ai dati persistenti solo tramite l'API FHIR, gli sviluppatori dovranno scegliere l'API di Azure per FHIR

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare l'API di Azure per FHIR, seguire la guida di avvio rapido di 5 minuti per distribuire l'API di Azure per FHIR.

>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
