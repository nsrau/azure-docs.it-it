---
title: Determinare l'opzione di pubblicazione - Marketplace commerciale Microsoft
description: Questo articolo descrive i criteri di idoneità e i requisiti per la pubblicazione di offerte in Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 7acfd5e1a1b1c44a9a109bb2b8bc38c7fcc57593
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484868"
---
# <a name="determine-your-publishing-option"></a>Determinare l'opzione di pubblicazione

L'opzione di pubblicazione scelta per l'offerta è correlata direttamente sia ai requisiti di idoneità che ai vantaggi GMT del marketplace commerciale. L'aspetto più importante è che la selezione dell'opzione di pubblicazione e del tipo di offerta definisce il modo in cui gli utenti interagiranno con l'offerta nel marketplace commerciale.

Per configurare l'offerta, è necessario comprendere i seguenti concetti chiave del Marketplace commerciale: le opzioni di pubblicazione, i tipi di offerta e la configurazione e le opzioni di elenco che definiranno come e dove viene presentata l'offerta nei negozi online del Marketplace commerciale.

Contenuto dell'articolo:

- Come determinare l'archivio online appropriato per la soluzione.
- Quali opzioni di pubblicazione e opzioni di elenco sono disponibili in ogni negozio online.
- Quali tipi di offerta sono disponibili per ogni opzione di pubblicazione.

## <a name="commercial-marketplace-publishing-options"></a>Opzioni di pubblicazione del marketplace commerciale

La tabella seguente illustra le opzioni di pubblicazione per i tipi di offerta in Microsoft AppSource e Azure Marketplace.

|   | **Elenco (contatto)**  | **Elenco (versione di valutazione)**  | **Free** | **BYOL** | **Transazione**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Macchina virtuale** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **App di Azure (più VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Immagine del contenitore** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Modulo IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Servizi gestiti** |  |  |  | Azure Marketplace |   |
| **Servizi di consulenza** | Entrambi gli archivi online |  |  |  |   |
| **App SaaS** | Entrambi gli archivi online | Entrambi gli archivi online | Entrambi gli archivi online |  | Entrambi gli archivi online * |
| **App di Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Componente aggiuntivo Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Le offerte di transazioni per app SaaS in Microsoft AppSource sono attualmente disponibili solo tramite carta di credito.

&#42;&#42; Le offerte di Microsoft 365 sono gratuite per l'installazione e possono essere monetizzate tramite l'offerta SaaS come servizio di licenza. Per altre informazioni, vedere [Monetizzare il componente aggiuntivo Office 365 tramite il marketplace commerciale Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Scegliere un'opzione di elenco

Le opzioni di inserzione disponibili offrono un impegno differenziato per i clienti, offrendo al contempo l'accesso ai vantaggi di condivisione di lead e [Marketplace commerciali](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Si notino le opzioni di inserzione che corrispondono all'opzione di pubblicazione:

| **Opzione di pubblicazione**    | **Descrizione**  |
| :------------------- | :-------------------|
| **Elenco** | Elenco semplice dell'applicazione o del servizio che consente a un utente del Marketplace commerciale di richiedere la connessione al cliente tramite le opzioni per l'elenco **contatti** . |
| **Versione di valutazione** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning dell'esperienza di valutazione della soluzione, consentendo agli utenti potenziali di provare l'esperienza in-app SaaS, IaaS o Microsoft senza costi aggiuntivi per un periodo di tempo limitato prima dell'acquisto. Le opzioni di elenco usate per l'opzione di pubblicazione di valutazione sono **versione di valutazione gratuita** o **test drive**. |
| **BYOL** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning della soluzione, per poi completare la transazione finanziaria separatamente. I tipi di offerta BYOL sono ideali per le migrazioni di ambienti locali al cloud. L'opzione Listing è **Get it now**.
| **Transazione** | Le offerte di transazioni vengono vendute tramite il marketplace commerciale. Microsoft è responsabile della fatturazione e delle riscossioni. L'opzione Listing è **Get it now**.|

> [!Note]
> Quando si usa l'opzione di pubblicazione delle transazioni, è importante avere presenti le considerazioni relative a prezzi, fatturazione e proventi prima di selezionare un tipo di offerta e di creare l'offerta. Per altre informazioni, vedere l'articolo [Funzionalità per le transazioni del marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Selezione di uno Store online

Ogni negozio online offre requisiti diversi per i clienti per le soluzioni aziendali e IT. Il tipo di offerta, le funzionalità di transazione e la categoria determineranno la posizione in cui verrà pubblicata l'offerta. Le categorie e le sottocategorie sono mappate a ogni negozio online in base al tipo di soluzione pubblicata:

**Microsoft AppSource** offre soluzioni aziendali, ad esempio soluzioni di settore e servizi di consulenza, per Dynamics 365, Microsoft 365 e Power Platform.

**Azure Marketplace** offre soluzioni IT compilate per o in Azure, oltre a servizi di consulenza che accelerano l'uso di Azure da clienti.

Selezionare la categoria e la sottocategoria che meglio si allineano con il tipo di soluzione. Ad esempio, un web application firewall è una soluzione IT che deve essere pubblicata in Azure Marketplace, sotto la categoria sicurezza. Un'app di gestione del contratto è una soluzione aziendale che deve essere pubblicata in AppSource nella categoria Sales. La selezione della categoria o della sottocategoria non corretta può comportare la pubblicazione dell'offerta nell'archivio online errato.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Pubblicazione in entrambi gli archivi online (solo offerte SaaS)

Le offerte SaaS possono essere pubblicate in Azure Marketplace o in AppSource. Se l'offerta SaaS è *sia* una soluzione it (Azure Marketplace) che una soluzione aziendale (AppSource), selezionare una categoria e/o una sottocategoria applicabile a ogni negozio online. Le offerte pubblicate in entrambi gli archivi online devono avere una proposta di valore come soluzione IT *e* una soluzione aziendale.

> [!IMPORTANT]
> Le offerte SaaS con [fatturazione a consumo](partner-center-portal/saas-metered-billing.md) sono disponibili tramite Azure Marketplace e il portale di Azure. Le offerte SaaS con solo piani privati sono disponibili tramite il portale di Azure.

| Fatturazione a consumo | Piano pubblico | Piano privato | Disponibile in: |
|---|---|---|---|
| Sì             | Sì         | No           | Azure Marketplace e portale di Azure |
| Sì             | Sì         | Sì          | Azure Marketplace e portale di Azure * |
| Sì             | No          | Sì          | Solo portale di Azure |
| No              | No          | Sì          | Solo portale di Azure |

&#42; il piano privato dell'offerta sarà disponibile solo tramite il portale di Azure

Ad esempio, un'offerta con fatturazione a consumo e solo un piano privato (nessun piano pubblico) viene acquistata dai clienti nel portale di Azure. Scopri di più sulle [offerte private in Microsoft Commercial Marketplace](private-offers.md).

### <a name="categories"></a>Categorie

Le categorie e le sottocategorie sono mappate a ogni negozio online in base al tipo di soluzione. Selezionare le categorie e le sottocategorie che meglio si allineano alla soluzione. È possibile selezionare:

- Almeno una delle due categorie. È possibile scegliere una categoria primaria e una secondaria.
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se non si seleziona alcuna sottocategoria, l'offerta sarà comunque individuabile nella categoria selezionata.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver scelto un'opzione di pubblicazione, rivedere la [Guida alla pubblicazione in base al tipo di offerta](./publisher-guide-by-offer-type.md).
