---
title: Definire le impostazioni dell'offerta per un'offerta del servizio di consulenza | Azure Marketplace
description: Definire le impostazioni dell'offerta in un'offerta di servizio di consulenza Azure o Dynamics 365 nella portale Cloud Partner per Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 8a89c1c548469b568a34521b240683af285c2342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485185"
---
# <a name="offer-settings-tab"></a>Scheda Impostazioni dell'offerta

Nella schermata **Nuova offerta**, il primo passaggio consiste nel creare l'identità dell'offerta. L'identità offerta è costituita da tre parti: **Offer ID** (ID offerta), **Publisher ID** (ID editore) e **Name** (Nome). Ciascuna di queste parti viene trattata nelle sezioni seguenti.

![Creare una nuova offerta di servizi di consulenza - scheda Impostazioni dell'offerta](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID offerta *

Questo identificatore è un nome univoco che viene creato quando si invia l'offerta per la prima volta. Deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. L'**ID offerta** è visibile nell'URL e ha effetto sui risultati dei motori di ricerca. Ad esempio, *nomeazienda_servizioesempio*.

Come mostrato nell'esempio, l'**ID offerta** viene accodato all'ID editore per creare un identificatore univoco. Quest'ultimo viene esposto come collegamento permanente che può essere prenotato ed è indicizzato dai motori di ricerca.

>[!Note]
>Dopo che un'offerta è diventata attiva, il relativo identificatore non può essere aggiornato.


### <a name="publisher-id"></a>ID editore *

Questo identificatore è correlato all'account. Dopo che si è eseguito l'accesso con l'account dell'organizzazione, l'**ID editore** viene visualizzato nel menu a discesa.


### <a name="name"></a>Nome

Questa stringa è il testo che viene visualizzato come nome dell'offerta in AppSource o in Azure Marketplace. La casella **Nome** prevede un limite di 50 caratteri. Il revisore potrebbe avere la necessità di modificare il titolo per aggiungere informazioni sulla durata e sul tipo di offerta.

L'esempio seguente mostra come viene assemblato il nome dell'offerta. 

![Creare una nuova offerta di servizi di consulenza](media/cppsampleconsultingoffer.png)

Il nome dell'offerta è costituito da quattro parti:

-   **Durata:** Definito nella scheda **Dettagli storefront** dell'editor. La durata può essere espressa in ore, giorni o settimane.
-   **Tipo di servizio:** Definito nella scheda **Dettagli storefront** dell'editor. I tipi di servizio sono `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposizione:** Inserito dal revisore.
-   **Nome:** Definito nella pagina **impostazioni offerta** .

>[!Note]
>La casella **Nome** prevede un limite di 50 caratteri. Il revisore potrebbe avere la necessità di modificare il titolo per aggiungere informazioni sulla durata e sul tipo di offerta.

L'elenco seguente propone diversi nomi di offerta appropriati:

-   Aspetti essenziali per servizi professionali: briefing di un'ora
-   Piattaforma di migrazione cloud: briefing di un'ora
-   PowerApps e Microsoft Flow: workshop di 1 giorno
-   Azure Machine Learning: PoC di tre settimane
-   Soluzione di vendita al dettaglio tutto incluso: briefing di un'ora
-   Portare i propri dati: workshop di 1 settimana
-   Analisi cloud: workshop di 3 giorni
-   Formazione di Power BI: workshop di 3 giorni
-   Soluzione di gestione delle vendite: implementazione di 1 settimana
-   Guida introduttiva di CRM: workshop di 1 giorno
-   Dynamics 365 for Sales: valutazione di 2 giorni

Dopo aver completato la scheda **Impostazioni dell'offerta**, salvare l'offerta da inviare. Il nome dell'offerta viene ora visualizzato al di sopra dell'editor ed è possibile trovarlo in **Tutte le offerte**.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile immettere i [dati da visualizzare nella vetrina e decidere se pubblicare in Azure Marketplace o in AppSource](./cpp-consulting-service-storefront-details.md).
