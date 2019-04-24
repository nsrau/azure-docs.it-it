---
title: Come compilare il modulo delle informazioni tecniche | Microsoft Docs
description: Illustra come immettere i valori per il modulo delle informazioni tecniche per una nuova app Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5aa118815146287d00a3bb8ee7d5fce57a6ad9ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397805"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Come compilare il modulo delle informazioni tecniche
===========================================

1.  Nella sezione **Choose Application Type** (Scegli il tipo di applicazione) caricare il file del pacchetto dell'estensione (.app) ed eventuali file del pacchetto di estensione da cui l'estensione ha una dipendenza.

    ![Informazioni sul pacchetto dell'applicazione](./media/d365-financials/image015.png)

-   **Extension Package File** (File pacchetto estensione): obbligatorio, è il file del pacchetto dell'estensione (.app).

-   **Dependency package file** (File pacchetto dipendenza): obbligatorio se l'app ha una dipendenza da un'altra app pubblicata in AppSource. È il file .app di un'estensione già pubblicata in AppSource, da cui dipende l'app corrente. 

-   **Library Package File** (File pacchetto libreria): obbligatorio se l'app ha una dipendenza da un'altra app che *non* è stata pubblicata in AppSource. È il file .app di un'app esistente, ma che non è stata e non sarà pubblicata in AppSource.

-   **App Test Automation** (Automazione test app): obbligatorio, è il pacchetto di test con VS Code che è necessario creare per il test automatico delle estensioni.

1. Nella sezione **Additional information for the app extension** (Informazioni aggiuntive per estensione app) caricare le informazioni aggiuntive per l'estensione. Queste informazioni vengono usate durante la convalida.

   ![Modulo delle informazioni aggiuntive per l'estensione dell'app](./media/d365-financials/image016.png)


-   **URL to Product Documentation** (URL documentazione prodotto): obbligatorio, è l'URL della documentazione per l'estensione.

-   **Key Usage Scenarios** (Scenari di utilizzo chiave): obbligatorio, è un documento che elenca i dettagli di configurazione e di utilizzo dell'estensione. Un esempio è disponibile nell'articolo [User Scenario Documentation](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/) (Documentazione sullo scenario utente).

-   **Target Release** (Versione di destinazione): obbligatorio, selezionare la versione in cui distribuire l'app. Selezionare **current** (corrente) per eseguire la distribuzione nella versione corrente. Selezionare **next minor** (secondaria successiva) per eseguire la distribuzione nella versione secondaria successiva da rilasciare. Selezionare **next major** (principale successiva) per eseguire la distribuzione nella versione principale successiva da rilasciare.

-   **Requires Premium SKU** (SKU Premium necessario): facoltativo, selezionare il pulsante Premium se l'app richiede il codice SKU Premium. Gestione assistenza e Produzione sono disponibili solo con Premium. Informazioni dettagliate sulle differenze tra i piani Essential e Premium sono disponibili nell'articolo [Modifica delle funzionalità visualizzate](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Explanation for Code Analysis Errors** (Spiegazione errori di analisi codice): facoltativo, è un documento che elenca e spiega eventuale codice che non soddisfa i requisiti.

-   **Explanation of Impacted Core Functionality** (Spiegazione funzionalità principali interessate): facoltativo, è un documento che elenca e spiega tutte le funzionalità principali limitate dall'estensione.

-   **Test Accounts** (Account di test): facoltativo, account utente per servizi remoti, siti Web e così via che saranno necessari per completare il test di utilizzo finale.

-   **UX requirements exceptions** (Eccezioni requisiti UX): facoltativo, è un documento che elenca e spiega eventuali requisiti dell'esperienza utente non soddisfatti dall'estensione.

Il passaggio successivo consiste nell'aggiungere i dati da visualizzare nella vetrina per l'offerta.
