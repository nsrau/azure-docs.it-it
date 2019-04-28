---
title: Panoramica delle API per il consumo di Azure | Microsoft Docs
description: Informazioni su come le API per il consumo di Azure consentono l'accesso a livello di codice ai dati sui costi e sull'utilizzo per le risorse di Azure.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: 16e0bdfa0fc70d5239cb4127e61891a013bf54a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615891"
---
# <a name="azure-consumption-api-overview"></a>Panoramica delle API per il consumo di Azure 

Le API per il consumo di Azure consentono l'accesso a livello di codice ai dati sui costi e sull'utilizzo per le risorse di Azure. Queste API attualmente supportano solo le registrazioni Enterprise e le sottoscrizioni Accesso Web diretto, con poche eccezioni. Le API vengono continuamente aggiornate per supportare altri tipi di sottoscrizioni di Azure.

Le API per il consumo di Azure consentono l'accesso a:
- Clienti Enterprise e Accesso Web diretto 
    - Dettagli sull'utilizzo 
    - Addebiti per Marketplace 
    - Raccomandazioni di prenotazione 
    - Dettagli di prenotazione 
    - Riepiloghi di prenotazioni 
- Solo clienti Enterprise 
    - Elenco prezzi 
    - Budget 
    - Saldi 

## <a name="usage-details-api"></a>API Dettagli sull'utilizzo

Usare l'API Dettagli sull'utilizzo per ottenere i dati su addebito e utilizzo per tutte le risorse Azure di prime parti. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per contatore per risorsa. Le informazioni sono utilizzabili per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche. 

L'API include:

-   **Dati di consumo a livello di contatore**: consentono di esaminare i dati relativi al costo di utilizzo, al contatore di generazione dell'addebito e alla risorsa di Azure a cui l'addebito si riferisce. Di tutti i record dei dettagli sull'utilizzo viene eseguito il mapping in un bucket giornaliero.
-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Filtri**: ridurre il set di risultati delle API in un set più piccolo di record di dettagli di utilizzo con i filtri seguenti:
    - Fine utilizzo/Avvio utilizzo
    - Gruppo di risorse
    - Nome risorsa
-   **Aggregazione dati**: usare OData per applicare espressioni per l'aggregazione dei dettagli sull'utilizzo per tag o proprietà di filtro
-   **Utilizzo per tipi di offerte diversi**: le informazioni dettagliate sull'utilizzo sono attualmente disponibili per i clienti Enterprise e Accesso Web diretto.

Per altre informazioni, vedere le specifiche tecniche per l'[API Dettagli sull'utilizzo](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API Addebiti per Marketplace

Usare l'API Addebiti per Marketplace per ottenere dati sull'utilizzo e sui costi per tutte le risorse Marketplace (offerte Azure di terze parti). Questi dati sono utilizzabili per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche. 

L'API include:

-   **Dati di consumo a livello di contatore**: consentono di esaminare i dati relativi al costo di utilizzo di Marketplace, al contatore di generazione dell'addebito e alla risorsa a cui l'addebito si riferisce. Di tutti i record dei dettagli sull'utilizzo viene eseguito il mapping in un bucket giornaliero.
-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Filtri**: ridurre il set di risultati delle API in un set più piccolo di record su Marketplace con i filtri seguenti:
    - Avvio utilizzo/Fine utilizzo
    - Gruppo di risorse
    - Nome risorsa
-   **Utilizzo per tipi di offerte diversi**: le informazioni su Marketplace sono attualmente disponibili per i clienti Enterprise e Accesso Web diretto.

Per altre informazioni, vedere le specifiche tecniche per l'[API Addebiti per Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API Saldi

I clienti Enterprise possono usare l'API Saldi per ottenere un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche e da pagare in eccedenza. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per eseguire un confronto con il riepilogo degli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Solo clienti Enterprise** Questa API è disponibile solo per i clienti EA. 
    - Per chiamare l'API, i clienti devono avere autorizzazioni di Amministratore aziendale 

Per altre informazioni, vedere le specifiche tecniche per l'[API Saldi](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>API Budget

I clienti Enterprise possono usare questa API per creare budget di costo o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione. Dopo aver determinato queste informazioni, è possibile configurare alcuni avvisi per l'invio di notifiche quando le soglie definite dall'utente per i budget vengono superate. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Solo clienti Enterprise** Questa API è disponibile solo per i clienti EA.
-   **Notifiche configurabili**: specificare gli utenti a cui inviare una notifica quando viene attivato il budget.
-   **Budget basati su costo o su utilizzo**: creare il budget in base al consumo o al costo, a seconda delle esigenze dello scenario.
-   **Filtri**: filtrare il budget per ottenere un subset più piccolo di risorse tramite i filtri configurabili seguenti
    - Gruppo di risorse
    - Nome risorsa
    - Contatore
-   **Periodi budget configurabili**: specificare la frequenza di reimpostazione del budget e la durata della validità di quest'ultimo.

Per altre informazioni, vedere le specifiche tecniche per l'[API Budget](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API Raccomandazioni di prenotazione

Usare questa API per ottenere consigli per l'acquisto di istanze di macchina virtuale riservate. Le raccomandazioni hanno lo scopo di consentire ai clienti l'analisi dei risparmi previsti sui costi e degli importi degli acquisti. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Filtri**: personalizzare i risultati relativi alle raccomandazioni tramite i filtri seguenti:
    - Scope
    - Periodo di ricerca posticipata
-   **Informazioni sulle prenotazioni per tipi di offerte diversi**: le informazioni sulle prenotazioni sono attualmente disponibili per i clienti Enterprise e Accesso Web diretto.

Per altre informazioni, vedere le specifiche tecniche per l'[API Raccomandazioni di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API Dettagli di prenotazione

Usare l'API Dettagli di prenotazione per visualizzare le informazioni relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Filtri**: ridurre il set di risultati delle API in un set più piccolo di prenotazioni con i filtri seguenti:
    - Intervallo di date
-   **Informazioni sulle prenotazioni per tipi di offerte diversi**: le informazioni sulle prenotazioni sono attualmente disponibili per i clienti Enterprise e Accesso Web diretto.

Per altre informazioni, vedere le specifiche tecniche per l'[API Dettagli di prenotazione](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API Riepiloghi di prenotazioni

Usare questa API per visualizzare informazioni aggregate relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo nell'aggregazione. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Filtri**: personalizzare i risultati della granularità giornaliera con il filtro seguente:
    - Data utilizzo
-   **Informazioni sulle prenotazioni per tipi di offerte diversi**: le informazioni sulle prenotazioni sono attualmente disponibili per i clienti Enterprise e Accesso Web diretto.
-   **Aggregazioni giornaliere o mensili**: i chiamanti possono specificare se vogliono i propri dati di riepilogo delle prenotazioni nella granularità giornaliera o mensile.

Per altre informazioni, vedere le specifiche tecniche per l'[API Riepiloghi di prenotazioni](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>API Elenco prezzi
I clienti Enterprise possono usare questa API per recuperare i prezzi personalizzati per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo dei Marketplace, le aziende sono in grado di calcolare i costi. 

L'API include:

-   **Controllo degli accessi in base al ruolo di Azure**: configurare criteri di accesso nel [portale di Azure](https://portal.azure.com), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) o tramite [cmdlet di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per specificare quali utenti o applicazioni possono accedere ai dati di utilizzo della sottoscrizione. Per l’autenticazione, i chiamanti devono utilizzare i token standard di Azure Active Directory. Per ottenere l'accesso ai dati di uso per una determinata sottoscrizione di Azure aggiungere il chiamante al ruolo Lettore della fatturazione, Proprietario o Collaboratore. 
-   **Solo clienti Enterprise** Questa API è disponibile solo per i clienti EA. Per ottenere i prezzi, i clienti Accesso Web diretto devono usare l'API RateCard. 

Per altre informazioni, vedere le specifiche tecniche per l'[API Elenco prezzi](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenari

Ecco alcuni degli scenari resi possibili dalle API per il consumo:

-   **Riconciliazione di fatture**: Microsoft ha addebitato l'importo corretto?  Qual è la fattura? È possibile calcolarla personalmente?
-   **Cross-charge**: dopo la comunicazione dell'importo da pagare, chi all'interno dell'organizzazione deve pagare?
-   **Ottimizzazione dei costi**: conoscendo l'importo dell'addebito, come è possibile sfruttare al massimo il denaro investito in Azure?
-   **Verifica dei costi**: si vuole verificare quanto si spende per Azure e quanto si usa questo servizio nel tempo. Quali sono le tendenze? Come si può ottenere una maggiore efficienza?
-   **Spesa di Azure durante il mese**: qual è la spesa mensile corrente fino a oggi? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?
-   **Impostazione di avvisi**: se si vogliono impostare avvisi relativi al consumo basato sulle risorse o basati sull'importo monetario, in base a un budget.

## <a name="next-steps"></a>Fasi successive

- Per informazioni sull'uso delle API di fatturazione di Azure ottenere informazioni dettagliate a livello di codice sull'utilizzo di Azure, vedere [Panoramica dell'API di fatturazione di Azure](billing-usage-rate-card-overview.md).



