---
title: Aggiornare l'offerta di applicazione SaaS di Azure esistente | Azure Marketplace
description: Come aggiornare un'offerta esistente per un'applicazione SaaS in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: 8817c8aa7d5d589be5e03cdd04e3a505d7e2be21
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941653"
---
# <a name="update-an-existing-saas-application-offer"></a>Aggiornare un'offerta esistente per un'applicazione SaaS

Sono disponibili diversi aggiornamenti che è possibile eseguire per l'offerta dopo che è stata pubblicata e attivata. Qualsiasi modifica apportata alla nuova versione dell'offerta deve essere salvata e ripubblicata per essere riportata in Marketplace. Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta SaaS nel [portale Cloud Partner](https://cloudpartner.azure.com/).

Esistono diversi motivi per cui è possibile aggiornare l'offerta, ad esempio:

- Aggiunta di una nuova versione a un'app esistente.
- Aggiornamento di un'app.
- Aggiunta di nuove funzionalità a un'app.
- Aggiornamento dei metadati del marketplace per l'offerta.

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Modifiche non consentite per un'offerta SaaS

Sono presenti attributi di un'offerta SaaS che non possono essere modificati dopo la pubblicazione dell'offerta in Azure Marketplace. Non è possibile modificare le impostazioni seguenti:

- ID offerta e ID editore dell'offerta
- Tag di versione, ad esempio: `1.0.1`
- Modifiche del modello di fatturazione/licenza per offerte esistenti.

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento
 
Le operazioni di aggiornamento seguenti sono comuni.

### <a name="update-offer-contacts"></a>Aggiornare i contatti dell'offerta

Usare la procedura seguente per aggiornare i contatti di supporto per l'offerta.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3. Andare alla scheda **Contatti**. Aggiornare i contatti.
4. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.


### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace associati all'offerta, seguire questa procedura. I metadati possibili sono ad esempio il nome della società, i logo e così via.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3. Andare alla scheda **Dettagli vetrina**. Usare le istruzioni nell'articolo [Pubblicare l'offerta SaaS](./cpp-publish-offer.md) per apportare modifiche ai metadati.
4. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.

## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche a un'offerta pubblicata, è possibile usare la funzionalità Confronta per controllare le modifiche apportate. La schermata successiva mostra l'opzione Confronta per un'offerta pubblicata.

![Usare Confronta per vedere le modifiche dell'offerta nel portale Cloud Partner](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Per usare la funzionalità Confronta:

1. In qualsiasi momento durante il processo di modifica selezionare Confronta per l'offerta.
2. Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.

## <a name="publishing-history"></a>Cronologia di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** scheda nella barra del menu di spostamento a sinistra del portale Cloud Partner. È possibile visualizzare le azioni con timestamp eseguite durante il ciclo di vita delle offerte di Azure Marketplace.

![Vedere la cronologia dell'offerta nel portale Cloud Partner](./media/saas-offer-history.png)

È possibile usare la pagina della cronologia di controllo per cercare un'offerta specifica e applicare filtri come Editore, Offerta e Tipo di evento (ad esempio, OfferGoLiveRequested). È anche possibile scaricare la cronologia di controllo come file CSV.


## <a name="next-steps"></a>Passaggi successivi

[Offerta di applicazioni SaaS](./cpp-saas-offer.md)