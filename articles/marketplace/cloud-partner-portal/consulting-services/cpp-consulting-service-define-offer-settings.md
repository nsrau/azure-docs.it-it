---
title: Definizione delle impostazioni dell'offerta per un'offerta di servizio di consulenza Azure Marketplace
description: Definire le impostazioni dell'offerta in un'offerta di servizio di consulenza di Azure o Dynamics 365 nel portale Cloud Partner per Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 6a3c168d0bd13e7c335841ac4016f18464cd50d5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985117"
---
# <a name="offer-settings-tab"></a>Scheda Impostazioni dell'offerta

>[!Important]
>A partire dal 13 aprile 2020, inizieremo a trasferire la gestione delle offerte di servizi di consulenza al Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in Panoramica sulla creazione di servizi di [consulenza](https://aka.ms/AzureCreateConsultingService) per gestire le offerte migrate.

Nella schermata **Nuova offerta,** il primo passaggio consiste nel creare l'identità dell'offerta. L'identità offerta è costituita da tre parti: **Offer ID** (ID offerta), **Publisher ID** (ID editore) e **Name** (Nome). Ciascuna di queste parti viene trattata nelle sezioni seguenti.

![Creare una nuova offerta di servizi di consulenza - scheda Impostazioni dell'offerta](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID dell'offerta

Questo identificatore è un nome univoco che viene creato quando si invia l'offerta per la prima volta. Deve contenere solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. L'**ID offerta** è visibile nell'URL e ha effetto sui risultati dei motori di ricerca. Ad esempio, *nomeazienda_servizioesempio*.

Come mostrato nell'esempio, l'**ID offerta** viene accodato all'ID editore per creare un identificatore univoco. Quest'ultimo viene esposto come collegamento permanente che può essere prenotato ed è indicizzato dai motori di ricerca.

>[!Note]
>Dopo che un'offerta è diventata attiva, il relativo identificatore non può essere aggiornato.


### <a name="publisher-id"></a>ID dell'editore

Questo identificatore è correlato all'account. Dopo che si è eseguito l'accesso con l'account dell'organizzazione, l'**ID editore** viene visualizzato nel menu a discesa.


### <a name="name"></a>Nome e cognome

Questa stringa è il testo che viene visualizzato come nome dell'offerta in AppSource o in Azure Marketplace. La casella **Nome** prevede un limite di 50 caratteri. Il revisore potrebbe avere la necessità di modificare il titolo per aggiungere informazioni sulla durata e sul tipo di offerta.

L'esempio seguente mostra come viene assemblato il nome dell'offerta. 

![Creare una nuova offerta di servizi di consulenza](media/cppsampleconsultingoffer.png)

Il nome dell'offerta è costituito da quattro parti:

-   **Durata:** Definito nella scheda **Dettagli Storefront** dell'editor. La durata può essere espressa in ore, giorni o settimane.
-   **Tipo di servizio:** Definito nella scheda **Dettagli Storefront** dell'editor. I tipi di servizio sono `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposizione:** Inserito dal revisore.
-   **Nome:** Definito nella pagina **Impostazioni offerta.**

>[!Note]
>La casella **Nome** prevede un limite di 50 caratteri. Il revisore potrebbe avere la necessità di modificare il titolo per aggiungere informazioni sulla durata e sul tipo di offerta.

L'elenco seguente propone diversi nomi di offerta appropriati:

-   Aspetti essenziali per servizi professionali: briefing di un'ora
-   Piattaforma di migrazione cloud: briefing di un'ora
-   PowerApps e Microsoft Flow: workshop di 1 giorno
-   Azure Machine Learning: 3-Wk PoC
-   Soluzione di vendita al dettaglio tutto incluso: briefing di un'ora
-   Porta i tuoi dati: officina 1-Wk
-   Analisi cloud: workshop di 3 giorni
-   Formazione di Power BI: workshop di 3 giorni
-   Soluzione di gestione delle vendite: implementazione di 1 settimana
-   Guida introduttiva al CRM: Workshop di 1 giorno
-   Dynamics 365 for Sales: valutazione di 2 giorni

Dopo aver completato la scheda **Impostazioni dell'offerta**, salvare l'offerta da inviare. Il nome dell'offerta viene ora visualizzato al di sopra dell'editor ed è possibile trovarlo in **Tutte le offerte**.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile immettere i [dati da visualizzare nella vetrina e decidere se pubblicare in Azure Marketplace o in AppSource](./cpp-consulting-service-storefront-details.md).
