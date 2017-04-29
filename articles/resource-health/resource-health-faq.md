---
title: "Domande frequenti su Integrità risorse di Azure | Microsoft Docs"
description: "Panoramica su Integrità risorse di Azure"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 570688ae363e223d792bec8e6b13d4ec50412130
ms.lasthandoff: 04/04/2017


---

# <a name="azure-resource-health-faq"></a>Domande frequenti su Integrità risorse di Azure
Risposte alle domande comuni su Integrità risorse di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti
* [Informazioni su Integrità risorse di Azure](#what-is-azure-resource-health)
* [Quali sono le finalità di Integrità risorse?](#what-is-the-resource-health-intended-for)
* [Quali controlli di integrità vengono eseguiti da Integrità risorse?](#what-health-checks-are-performed-by-resource-health)
* [Cosa significa ciascuno stato di integrità delle risorse?](#what-does-each-of-the-health-status-mean)
* [Che cosa significa lo stato Sconosciuto? Indica un problema relativo alla risorsa?](#what-does-the-unknown-status-mean-is-something-wrong-with-my-resource)
* [Come è possibile ottenere supporto per una risorsa che non è disponibile?](#how-can-i-get-help-for-a-resource-that-is-unavailable)
* [Integrità risorse è in grado di differenziare tra una mancata disponibilità dovuta a problemi della piattaforma e un'operazione eseguita dall'utente?](#does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did)
* [È possibile aggiungere Integrità risorse agli strumenti di monitoraggio personali?](#can-i-integrate-resource-health-with-my-monitoring-tools)
* [Dove si trova Integrità risorse?](#where-do-i-find-resource-health)
* [Integrità risorse è disponibile per tutti i tipi di risorse?](#is-resource-health-available-for-all-resource-types)
* [Quale operazione è necessario eseguire se la risorsa risulta disponibile ma si ritiene che non lo sia?](#what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not)
* [Integrità risorse è disponibile in tutte le aree di Azure?](#is-resource-health-available-for-all-azure-regions)
* [In che modo Integrità risorse differisce dal Dashboard per l'integrità dei servizi o dalle notifiche sui servizi del portale di Azure?](#how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications)
* [È necessario attivare Integrità risorse per ogni risorsa?](#do-i-need-to-activate-resource-health-for-each-resource)
* [È necessario abilitare Integrità risorse per l'organizzazione?](#do-we-need-to-enable-resource-health-for-my-organization)
* [Integrità risorse è disponibile gratuitamente?](#is-resource-health-available-free-of-charge)
* [Quali sono i suggerimenti offerti da Integrità risorse?](#what-are-the-recommendations-that-resource-health-provides)


## <a name="what-is-azure-resource-health"></a>Informazioni su Integrità risorse di Azure
Integrità risorse consente di eseguire una diagnosi e di ottenere supporto quando un problema di Azure ha effetto sulle risorse. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Quali sono le finalità di Integrità risorse?
Quando viene rilevato un problema relativo a una risorsa, Integrità risorse consente di diagnosticarne la causa principale. Offre suggerimenti per attenuare il problema e supporto tecnico quando è necessaria ulteriore assistenza per risolvere problemi legati ai servizi di Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quali controlli di integrità vengono eseguiti da Integrità risorse?
Integrità risorse esegue vari controlli in base al [tipo di risorsa](resource-health-checks-resource-types.md). Questi controlli sono progettati per implementare tre tipi di problemi: 
1. Eventi non pianificati, ad esempio un riavvio imprevisto dell'host
2. Eventi pianificati, ad esempio aggiornamenti pianificati del sistema operativo host
3. Eventi attivati dalle azioni dell'utente, ad esempio il riavvio di una macchina virtuale

## <a name="what-does-each-of-the-health-status-mean"></a>Cosa significa ciascuno stato di integrità delle risorse?
Esistono tre stati di integrità diversi:
1. Disponibile: non si sono verificati problemi noti nella piattaforma Azure che potrebbero influire sulla risorsa
2. Non disponibile: Integrità risorse ha rilevato problemi che influiscono sulla risorsa
3. Sconosciuto: Integrità risorse può determinare lo stato di integrità di una risorsa perché non riceve più informazioni su di essa. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Che cosa significa lo stato Sconosciuto? Indica un problema relativo alla risorsa?
Lo stato di integrità è impostato su Sconosciuto quando Integrità risorse non riceve più informazioni su una risorsa specifica. Sebbene non si tratti di un'indicazione definitiva dello stato della risorsa, in caso di errata esecuzione, potrebbe indicare la presenza di un problema di Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Come è possibile ottenere supporto per una risorsa che non è disponibile?
È possibile inviare una richiesta di supporto dal pannello di Integrità risorse. Se la risorsa non è disponibile a causa di eventi della piattaforma, non è necessario un contratto di supporto con Microsoft per aprire una richiesta.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Integrità risorse è in grado di differenziare tra una mancata disponibilità dovuta a problemi della piattaforma e un'operazione eseguita dall'utente?
Sì, quando una risorsa non è disponibile, Integrità risorse identifica la causa principale tra una delle categorie seguenti: 
1.    Azione avviata dall'utente
2.    Evento pianificato 
3.    Evento non pianificato

Nel portale le azioni avviate dall'utente sono indicate da un'icona di notifica blu, mentre gli eventi pianificati e non pianificati sono indicati da un'icona di avviso rossa. Ulteriori dettagli sono disponibili in [Panoramica su Integrità risorse di Azure](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>È possibile aggiungere Integrità risorse agli strumenti di monitoraggio personali?
Integrità risorse è un servizio progettato per facilitare la diagnosi e attenuare i problemi relativi ai servizi di Azure che influiscono sulle risorse. Sebbene sia possibile usare l'API di Integrità risorse per ottenere lo stato di integrità a livello di codice, è consigliabile usare le metriche per monitorare le risorse. Quando viene rilevato un problema, Integrità risorse consente di determinarne la causa principale e suggerisce all'utente le azioni da eseguire per risolverlo. Per ulteriori informazioni sull'uso delle metriche per il controllo delle risorse, visitare la pagina [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/).

## <a name="where-do-i-find-resource-health"></a>Dove si trova Integrità risorse?
Dopo avere eseguito l'accesso al portale di Azure, è possibile accedere a Integrità risorse in diversi modi:
1. Passare alla risorsa. Nel riquadro di spostamento a sinistra fare clic su **Integrità risorse**.
2. Passare al pannello Monitoraggio di Azure.  Nel riquadro di spostamento a sinistra fare clic su **Integrità risorse**.
3. Aprire il pannello **Guida e supporto** facendo clic sul punto interrogativo nell'angolo in alto a destra del portale e quindi selezionando **Guida e supporto**. Quando il pannello si apre, fare clic su **Integrità risorse**.

È anche possibile usare l'API di Integrità risorse per ottenere informazioni sullo stato delle risorse.

## <a name="is-resource-health-available-for-all-resource-types"></a>Integrità risorse è disponibile per tutti i tipi di risorse?
L'elenco dei controlli di integrità e dei tipi di risorse supportati per Integrità risorse è reperibile [qui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Quale operazione è necessario eseguire se la risorsa risulta disponibile ma si ritiene che non lo sia?
Durante il controllo dell'integrità di una risorsa, immediatamente sotto lo stato di integrità è possibile fare clic su **Segnala stato di integrità non corretto**. Prima di inviare il report, è possibile specificare ulteriori dettagli sul motivo per cui si ritiene che lo stato di integrità corrente non sia corretto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Integrità risorse è disponibile in tutte le aree di Azure? 
Integrità risorse è disponibile in tutte le aree geografiche di Azure, ad eccezione delle aree seguenti:
* Governo degli Stati Uniti - Virginia
* Governo degli Stati Uniti - Iowa
* Dipartimento della difesa Stati Uniti orientali
* Dipartimento della difesa Stati Uniti centrali
* Germania centrale
* Germania nord-orientale
* Cina orientale
* Cina settentrionale

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>In che modo Integrità risorse differisce dal Dashboard per l'integrità dei servizi o dalle notifiche sui servizi del portale di Azure?
Le informazioni messe a disposizione da Integrità risorse sono più specifiche di quelle messe a disposizione dal Dashboard per l'integrità dei servizi di Azure.

Diversamente dallo [stato di Azure](https://status.azure.com) e dalle notifiche sui servizi del portale che segnalano la presenza di problemi che interessano un elevato numero di clienti, ad esempio quelli di una determinata area di Azure, Integrità risorse espone eventi più granulari che sono rilevanti solo per la risorsa specifica. Se, ad esempio, un host viene riavviato in modo imprevisto, Integrità risorse avvisa solo i clienti le cui macchine virtuali sono in esecuzione su tale host.

È importante notare che per garantire la visibilità completa degli eventi che incidono sulle risorse, Integrità risorse segnala anche gli eventi pubblicati nelle notifiche dei servizi e sul Dashboard per l'integrità dei servizi.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>È necessario attivare Integrità risorse per ogni risorsa?
No, le informazioni di integrità sono disponibili per tutti i tipi di risorse supportati da Integrità risorse. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>È necessario abilitare Integrità risorse per l'organizzazione?
No.  Integrità risorse di Azure è accessibile dal portale di Azure e non prevede requisiti di installazione.

## <a name="is-resource-health-available-free-of-charge"></a>Integrità risorse è disponibile gratuitamente?
Sì.  Integrità risorse di Azure è gratuito.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quali sono i suggerimenti offerti da Integrità risorse?
In base allo stato di integrità, Integrità risorse offre consigli allo scopo di ridurre il tempo impiegato per la risoluzione dei problemi. Per le risorse disponibili, i consigli hanno l'obiettivo di risolvere i problemi più comuni riscontrati dai clienti. Se la risorsa non è disponibile a causa di un evento non pianificato di Azure, l'obiettivo sarà quello di assistere l'utente durante e dopo il processo di ripristino. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Integrità risorse, consultare le risorse seguenti:
-  [Panoramica su Integrità risorse di Azure](Resource-health-overview.md)
-  [Tipi di risorse e controlli integrità disponibili in Integrità risorse di Azure](resource-health-checks-resource-types.md)

