---
title: API di evasione SaaS in Microsoft Commercial Marketplace
description: Introduzione alle API di evasione che consentono di integrare le offerte SaaS in Microsoft AppSource e in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: dsindona
ms.openlocfilehash: 70515ca04e870fa435f8e9f46122a8e0dcb9b588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691357"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>API di evasione SaaS in Microsoft Commercial Marketplace

Le API di evasione SaaS consentono agli editori, noti anche come fornitori di software indipendenti (ISV), di pubblicare e vendere le proprie applicazioni SaaS in Microsoft AppSource, Azure Marketplace e portale di Azure. Queste API consentono alle applicazioni ISV di partecipare a tutti i canali abilitati per il commercio: diretto, partner-LED (rivenditore) e LED di campo.  L'integrazione con queste API è un requisito per la creazione e la pubblicazione di un'offerta SaaS transazionale nel centro per i partner.

Gli ISV devono implementare i flussi API seguenti aggiungendo nel codice del servizio SaaS per mantenere lo stesso stato di sottoscrizione per ISV e Microsoft:

* Flusso della pagina di destinazione: Microsoft notifica all'editore che l'offerta SaaS dell'editore è stata acquistata da un cliente nel Marketplace.
* Flusso di attivazione: il server di pubblicazione informa Microsoft che è stato configurato un account SaaS appena acquistato sul lato del server di pubblicazione.
* Flusso di aggiornamento: modifica del piano acquistato e/o del numero di postazioni acquistate.
* Sospendere e ripristinare il flusso: sospendere l'offerta SaaS acquistata se il metodo di pagamento del cliente non è più valido. L'offerta sospesa può essere ripristinata quando viene risolto il problema relativo al metodo di pagamento.
* Flussi webhook: Microsoft invierà una notifica all'editore sulle modifiche alla sottoscrizione SaaS e sull'annullamento attivato dal cliente dal lato Microsoft.

Per l'annullamento della sottoscrizione SaaS acquistata, l'integrazione è facoltativa, perché può essere eseguita dal cliente dal lato Microsoft.

Una corretta integrazione con le API di evasione SaaS è essenziale per assicurarsi che

* i clienti finali che hanno acquistato l'offerta SaaS dell'editore vengono fatturati correttamente da Microsoft.
* i clienti finali ricevono l'esperienza utente corretta per l'acquisto, la configurazione, l'utilizzo e la gestione delle sottoscrizioni SaaS acquistate nel Marketplace.

Queste API consentono alle offerte dell'editore di partecipare a tutti i canali di Commerce abilitati:

* dirette
* partner-LED (rivenditore, CSP)
* LED di campo

Nello scenario Reseller (CSP), un CSP Acquista l'offerta SaaS per conto del cliente finale. Si prevede che un cliente usi l'offerta SaaS, ma il CSP è l'entità che esegue le operazioni seguenti:

* fatturazione del cliente
* modifica dei piani di sottoscrizione/quantità di postazioni acquistate
* annullamento delle sottoscrizioni

Il server di pubblicazione non è necessario per implementare uno dei flussi delle chiamate API in modo diverso per questo scenario.

Per ulteriori informazioni su CSP, consultare https://partner.microsoft.com/en-us/licensing .

>[!Warning]
>La versione corrente di questa API è la versione 2, che deve essere usata per tutte le nuove offerte SaaS. La versione 1 dell'API è deprecata e viene mantenuta per supportare le offerte esistenti.

>[!Note]
>Le API di evasione SaaS sono destinate solo a essere chiamate da un servizio back-end del server di pubblicazione. L'integrazione con le API direttamente dalla pagina Web dell'editore non è supportata. È necessario usare solo il flusso di autenticazione da servizio a servizio.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, registrare l'applicazione SaaS nel [portale di Azure](https://ms.portal.azure.com) come descritto in [registrare un'applicazione Azure ad](./pc-saas-registration.md).  Usare successivamente la versione più recente di questa interfaccia per lo sviluppo: [API di evasione Saas versione 2](./pc-saas-fulfillment-api-v2.md).
