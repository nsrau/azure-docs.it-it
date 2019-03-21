---
title: Come creare un'offerta Dynamics 365 tramite il portale Cloud Partner | Microsoft Docs
description: Come creare un'offerta Dynamics 365 tramite il portale Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877327"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Come creare un'offerta Dynamics 365 tramite il portale Cloud Partner

Il portale di pubblicazione offre l'accesso in base al ruolo al portale, per consentire a più utenti di collaborare alla pubblicazione di un'offerta. Per altre informazioni, vedere [Gestione utenti nel portale Cloud Partner](./cloud-partner-portal-manage-users.md).

Prima di poter pubblicare un'offerta per conto di un account server di pubblicazione, uno degli utenti con ruolo \"proprietario\" deve accettare di essere conforme a: [Condizioni per l'utilizzo](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Informativa sulla privacy di Microsoft](https://www.microsoft.com/privacystatement/default.aspx) e [Contratto relativo al programma Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Come creare una nuova offerta Dynamics 365 for Operations

Dopo aver soddisfatto tutti i prerequisiti, è possibile avviare la creazione dell'offerta Dynamics 365 for Operations.

1. Accedere al [Portale per Cloud Partner](https://cloudpartner.azure.com/).
2. Nella barra di spostamento a sinistra scegliere \"+ Nuova offerta\" e selezionare \"Dynamics 365 for Operations\".
3. Viene ora visualizzato l'\"Editor\" della nuova offerta in cui è possibile iniziare la creazione.
4. I \"moduli\" da compilare sono a sinistra nell'\"Editor\". Ogni \"modulo\" è costituito da un set di campi da compilare. I campi obbligatori sono contrassegnati con un asterisco rosso (\*).

I moduli principali per creare un'offerta Dynamics 365 for Operations sono quattro:

- Impostazioni dell'offerta
- Informazioni tecniche
- Dati da visualizzare nella vetrina
- Contatti

## <a name="how-to-fill-out-the-offer-settings-form"></a>Come compilare il modulo delle impostazioni dell'offerta

Il modulo delle impostazioni dell'offerta è un modulo di base in cui specificare le impostazioni di offerta. I diversi campi sono descritti di seguito.

### <a name="offer-id"></a>Offer ID (ID offerta)

Identificatore univoco dell'offerta in un profilo di pubblicazione. Questo ID sarà visibile negli URL dei prodotti. Può essere composto solo da caratteri alfanumerici minuscoli o trattini (-). L'ID non può terminare con un trattino e può contenere massimo 50 caratteri. Questo campo è bloccato dopo la pubblicazione dell'offerta.

ad esempio, se un server di pubblicazione di contoso di server di pubblicazione viene creata un'offerta con ID offerta *esempio: Dynamics 365 per le operazioni*, verrà visualizzata in AppSource come `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`.

### <a name="publisher-id"></a>Publisher ID (ID editore)

Questo elenco a discesa consente di scegliere il profilo di pubblicazione in cui si desidera pubblicare l'offerta. Questo campo è bloccato dopo la pubblicazione dell'offerta.

NOME

Nome visualizzato dell'offerta. Nome che viene visualizzato in [AppSource](https://appsource.microsoft.com). Può contenere massimo 50 caratteri.

Fare clic su \"Save\" (Salva) per salvare le voci immesse. Il passaggio successivo consiste nell'aggiungere le informazioni tecniche dell'offerta.

## <a name="how-to-fill-out-the-technical-info-form"></a>Come compilare il modulo delle informazioni tecniche

Il modulo delle informazioni tecniche consente di fornire informazioni tecniche che vengono visualizzate nella pagina dell'offerta. Di seguito sono descritte le istruzioni per compilare i diversi campi.

![Schermata Nuova offerta](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identificatore soluzione

Si tratta dell'identificatore della soluzione.

1. Per individuarlo, passare a Life Cycle Services (Servizi del ciclo di vita) e selezionare Solution Management (Gestione soluzioni).
2. Dopo aver selezionato la soluzione appropriata, viene visualizzato l'identificatore soluzione nella panoramica del pacchetto. \*\*Se l'identificatore è vuoto, scegliere Modifica e ripubblicare il pacchetto per visualizzare l'identificatore soluzione.

### <a name="validation-assets"></a>Risorse per la convalida

Caricare qui i report di analisi della personalizzazione.

### <a name="does-solution-enable-translations"></a>La soluzione consente le traduzioni?

Selezionare \'Sì\' o \'No\'

### <a name="does-solution-include-localizations"></a>La soluzione prevede localizzazioni?

Selezionare \'Sì\' o \'No\'

### <a name="product-version"></a>Versione prodotto

Selezionare New AX (Nuovo AX) Infine, fare clic su Salva.

## <a name="how-to-fill-out-storefront-details-form"></a>Come compilare il modulo Storefront Details (Dettagli vetrina).

Si tratta dei dettagli dell'offerta.

1. **Offer Summary** (Riepilogo offerta)

    \- Immettere un breve riepilogo della soluzione (massimo 100 caratteri).

2. **Offer Description**(Descrizione dell'offerta)

    \- Immettere una breve descrizione della soluzione. La descrizione deve includere la footprint funzionale della soluzione e deve essere direttamente allineata alla libreria BPM. Se, ad esempio, nel contenuto di marketing si afferma la disponibilità delle funzionalità x, y e z, durante la revisione finale è necessario verificare che queste siano documentate nella libreria BPM all'interno di LCS.

![Schermata dei dettagli della vetrina](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Elenco dei dettagli

![Schermata dei dettagli della vetrina](./media/publish_d365_new_offer/storefront_details.png)

1. **Industries** (Settori) - Selezionare un massimo di due settori dalle opzioni specificate.
2. **Categories**(Categorie) - Selezionare un massimo di tre categorie dalle opzioni specificate.
3. **App Type** (Tipo di app) - Selezionare dalle opzioni specificate.
4. **Help link for your App** (Collegamento alla Guida per l'app) - Immettere il collegamento alla guida della soluzione.
5. **Supported countries/regions** (Paesi supportati) - Selezionare dalle opzioni specificate.
6. **Supported Languages** (Lingue supportate) - Selezionare dalle opzioni specificate.
7. **App Version** (Versione dell'app) - Immettere la versione della soluzione che viene rilasciata, ad esempio 1.0.0.0.
8. **App Release Date** (Data di rilascio dell'app) - Immettere la data di rilascio della soluzione (mm/gg/aaaa).
9. **Product your app works with** (Prodotti interrelati con l'app) - Elencare i prodotti specifici interrelati con l'app. È possibile elencare fino a tre prodotti. Per elencare un prodotto, fare clic sul segno più (accanto a Nuovo). Viene creato un nuovo campo di testo aperto in cui è possibile immettere il nome di un prodotto interrelato con l'app.
10. **Search Keywords** (Parole chiave di ricerca) - Immettere i termini comuni che gli utenti possono usare per trovare la soluzione durante una ricerca. \*\*Queste parole chiave non vengono visualizzate nel marketplace.
11. **Hide Key** (Chiave nascosta) - È una chiave che verrà combinata con l'URL di anteprima dell'offerta che impedisce la visualizzazione pubblica dell'offerta. Non è una password. È possibile immettere qualsiasi stringa.

### <a name="marketing-artifacts"></a>Artefatti di marketing

1. Caricare quindi il **logo** e le **schermate**. \*\*Fare attenzione alle dimensioni di ogni caricamento. Tutte le immagini devono essere in formato PNG.
2. **Demo videos** (Video demo) - Fare clic su \"+ Nuovo\". Caricare un video relativo alla soluzione (solo collegamenti YouTube o Vimeo). \*\*. Tenere presente che è necessario caricare un'anteprima della dimensione specificata per visualizzare il video in gestione temporanea.
3. **Documents** (Documenti) - Caricare i documenti relativi alla soluzione, immettendo un nome per il documento.

### <a name="legal"></a>Note legali

Queste informazioni indirizzano l'utente all'Informativa sulla Privacy e alle Condizioni per l'utilizzo. Immettere l'URL dell'Informativa sulla privacy della soluzione e delle Condizioni per l'utilizzo. \*\*Il cliente potrà visualizzare queste informative nel portale.

### <a name="customer-support"></a>Customer support (Assistenza clienti)

L'URL del supporto viene visualizzato nel portale soltanto dagli utenti.

### <a name="leads-management"></a>Leads Management (Gestione clienti potenziali)

Selezionare un sistema CRM nel quale verranno archiviati i clienti potenziali. Selezionare \"tabelle di Azure\" qui se hai uno dei seguenti sistemi CRM: Salesforce, Marketo, Microsoft Dynamics CRM. Nel sistema CRM selezionato verranno scritti i dettagli degli utenti finali che provano l'app specifica in AppSource (clienti potenziali). In base al sistema CRM selezionato fare clic sull'URL corrispondente di seguito per informazioni su come completare il set di campi successivo.

![Dettagli di Lead Management (Gestione clienti potenziali)](./media/publish_d365_new_offer/leads.png)

1. Per Tabella di Azure, vedere [qui](https://aka.ms/leadsettingforazuretable)
2. Per Dynamics CRM Online, vedere [qui](https://aka.ms/leadsettingfordynamicscrm)
3. Per Marketo, vedere [qui](https://aka.ms/leadsettingformarketo)
4. Per Salesforce, vedere [qui](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Come compilare il modulo dei contatti.

Queste informazioni vengono usate da Microsoft e dal supporto clienti. Immettere il contatto e il supporto tecnico dell'azienda e l'URL del supporto della soluzione. Queste informazioni verranno usate come unico punto di contatto nel caso in cui Microsoft abbia domande sulla soluzione, nonché per il supporto ai clienti.

![Schermata dei contatti dell'offerta](./media/publish_d365_new_offer/Contacts.png)
