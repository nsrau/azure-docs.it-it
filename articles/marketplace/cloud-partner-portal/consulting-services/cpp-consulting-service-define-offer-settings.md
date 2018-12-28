---
title: Offerta di servizi di consulenza per Azure e Dynamics 365 - Definizione delle impostazioni dell'offerta | Microsoft Docs
description: Guida alla definizione delle impostazioni dell'offerta di servizi di consulenza per Azure o Dynamics 365 nel portale Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345350"
---
# <a name="offer-settings-tab"></a>Scheda Impostazioni dell'offerta

Nella schermata **Nuova offerta**, il primo passaggio consiste nel creare l'identità dell'offerta. L'identità dell'offerta è costituita da tre parti: **ID offerta**, **ID editore** e **Nome**. Ciascuna di queste parti viene trattata nelle sezioni seguenti.

![Creare una nuova offerta di servizi di consulenza - scheda Impostazioni dell'offerta](media/consultingoffer-settings-tab.png)

*ID offerta*

Questo identificatore è un nome univoco che viene creato quando si invia l'offerta per la prima volta. Deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. L'**ID offerta** sarà visibile nell'URL e avrà effetto sui risultati dei motori di ricerca. Ad esempio, *yourcompanyname_exampleservice*

Come mostrato nell'esempio, l'**ID offerta** viene accodato all'ID editore per creare un identificatore univoco. Questo viene esposto come collegamento permanente che può essere prenotato ed è indicizzato dai motori di ricerca.

*Dopo che un'offerta è diventata attiva, il relativo identificatore non può essere aggiornato*

*ID editore*

Questo identificatore è correlato all'account. Una volta effettuato l'accesso con l'account dell'organizzazione, l'ID **editore** verrà visualizzato nel menu a discesa.

*Nome*

Questa stringa è il testo che verrà visualizzato come nome dell'offerta in AppSource o in Azure Marketplace. Il campo *Nome* prevede un limite di 50 caratteri.  Il revisore potrebbe dover modificare il titolo per consentire l'accodamento del nome dell'offerta con la durata e il tipo di offerta.

>[!Note]
>Importante: immettere solo il nome del servizio effettivo. Non includere la durata e il tipo di servizio.

Nell'esempio seguente di Edgewater Fullscope viene visualizzata la modalità di assemblaggio del nome dell'offerta. Il nome dell'offerta viene visualizzato nel modo seguente:

![Creare una nuova offerta di servizi di consulenza](media/cppsampleconsultingoffer.png)

Il nome dell'offerta è costituito da quattro parti:

-   **Durata:** viene definita nella scheda **Dettagli vetrina** dell'editore. La durata può essere espressa in ore, giorni o settimane.
-   **Tipo di servizio**: viene definito nella scheda **Dettagli vetrina** dell'editore. I tipi di servizio sono `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposizione:** inserita dal revisore.
-   **Nome:** definito nella pagina **Impostazioni dell'offerta**.

>[!Note]
>Il campo del nome prevede un limite di 50 caratteri. Il nome inviato potrebbe essere modificato dal revisore per consentirne l'accodamento della durata e del tipo di offerta al nome.

L'elenco seguente propone diversi nomi di offerta appropriati:

-   Aspetti essenziali per servizi professionali: Briefing di 1 ora
-   Piattaforma di migrazione cloud: Briefing di 1 ora
-   PowerApps e Microsoft Flow: Workshop di 1 giorno
-   Servizi di Azure Machine Learning: Modello di verifica di 3 settimane
-   Soluzione di vendita al dettaglio tutto incluso: Briefing di 1 ora
-   Inserimento di dati personali: Workshop di 1 settimana
-   Cloud Analytics: Workshop di 3 giorni
-   Training di Power BI: Workshop di 3 giorni
-   Soluzione di gestione delle vendite: Implementazione di 1 settimana
-   Avvio rapido CRM: Workshop di 1 giorno
-   Dynamics 365 for Sales: Valutazione di 2 giorni

Dopo aver compilato la scheda **Impostazioni dell'offerta**, è possibile salvare l'invio. Il nome dell'offerta verrà visualizzato sopra all'editor e sarà possibile trovarlo in Tutte le offerte.

**Passaggi successivi**

Immettere i [dati da visualizzare nella vetrina e la preferenza in merito alla pubblicazione in Azure Marketplace o in AppSource](./cpp-consulting-service-storefront-details.md).