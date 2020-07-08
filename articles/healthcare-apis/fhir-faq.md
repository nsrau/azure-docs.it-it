---
title: Domande frequenti sui servizi FHIR in Azure-API di Azure per FHIR
description: Risposte alle domande frequenti sull'API di Azure per FHIR, ad esempio la posizione di archiviazione dei dati dietro le API FHIR e il supporto della versione.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870981"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Domande frequenti sull'API di Azure per FHIR

## <a name="what-is-fhir"></a>Che cos'è FHIR?
Le risorse di interoperabilità sanitaria veloci (FHIR "Fire") sono uno standard di interoperabilità progettato per consentire lo scambio di dati sanitari tra diversi sistemi di integrità. Questo standard è stato sviluppato dall'organizzazione HL7 e viene adottato dalle organizzazioni sanitarie in tutto il mondo. La versione più recente di FHIR disponibile è R4 (versione 4). L'API di Azure per FHIR supporta R4 e supporta anche la versione precedente STU3 (standard per l'uso della versione di valutazione 3). Per ulteriori informazioni su FHIR, visitare [HL7.org](http://hl7.org/fhir/summary.html).

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>I dati sottostanti le API FHIR sono archiviati in Azure?

Sì, i dati vengono archiviati in database gestiti in Azure. L'API di Azure per FHIR non fornisce accesso diretto all'archivio dati sottostante.

## <a name="what-identity-provider-do-you-support"></a>Quale provider di identità supporta?

Attualmente è supportato Microsoft Azure Active Directory come provider di identità.

## <a name="what-fhir-version-do-you-support"></a>Quale versione di FHIR è supportata?

Sono supportate le versioni 4.0.0 e 3.0.1 sia nell'API di Azure per FHIR (PaaS) che nel server FHIR per Azure (Open Source).

Per informazioni dettagliate, vedere [funzionalità supportate](fhir-features-supported.md). Leggere le informazioni sulle modifiche apportate tra le versioni nella [cronologia delle versioni per HL7 FHIR](https://hl7.org/fhir/R4/history.html).

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Qual è la differenza tra il server Microsoft FHIR open source per Azure e l'API di Azure per FHIR?

L'API di Azure per FHIR è una versione ospitata e gestita del server Microsoft FHIR open source per Azure. Nel servizio gestito Microsoft fornisce tutti gli aggiornamenti e la manutenzione. 

Quando si esegue FHIR server per Azure, è possibile accedere direttamente ai servizi sottostanti. Tuttavia, l'utente è anche responsabile della manutenzione e dell'aggiornamento del server e di tutte le operazioni di conformità necessarie se si archiviano dati PHI.

Dal punto di vista dello sviluppo, ogni funzionalità viene distribuita nel server Microsoft FHIR open source per Azure. Una volta convalidata in open source, verrà rilasciata alla soluzione API PaaS di Azure per FHIR. Il tempo tra il rilascio in open source e PaaS dipende dalla complessità della funzionalità e da altre priorità di roadmap. 

## <a name="what-is-smart-on-fhir"></a>Che cos'è SMART in FHIR?

Le applicazioni medicali intelligenti (sostituibili e la tecnologia riutilizzabile) in FHIR sono un set di specifiche aperte per integrare le applicazioni partner con i server FHIR e altri sistemi IT di integrità, ad esempio i record di integrità elettronici e gli scambi di informazioni sull'integrità. Grazie alla creazione di un'applicazione SMART on FHIR, è possibile assicurarsi che l'applicazione sia accessibile e sfruttata da una pletora di sistemi diversi.
Autenticazione e API di Azure per FHIR. Per ulteriori informazioni su SMART, vedere l'articolo relativo all' [integrità intelligente](https://smarthealthit.org/).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha letto alcune delle domande frequenti sull'API di Azure per FHIR. Leggere le informazioni sulle funzionalità supportate in FHIR server per Azure:
 
>[!div class="nextstepaction"]
>[Funzionalità di FHIR supportate](fhir-features-supported.md)