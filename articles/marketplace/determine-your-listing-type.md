---
title: Determinare l'opzione di pubblicazione - Marketplace commerciale Microsoft
description: Questo articolo descrive i criteri di idoneità e i requisiti per la pubblicazione di offerte in Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 37647a9591c0f686e4fc3f1fd858baa46e01f7ac
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498073"
---
# <a name="determine-your-publishing-option"></a>Determinare l'opzione di pubblicazione

L'opzione di pubblicazione scelta per l'offerta è correlata direttamente sia ai requisiti di idoneità che ai vantaggi GMT del marketplace commerciale. L'aspetto più importante è che la selezione dell'opzione di pubblicazione e del tipo di offerta definisce il modo in cui gli utenti interagiranno con l'offerta nel marketplace commerciale.

Per configurare l'offerta, è necessario comprendere i concetti chiave del marketplace commerciale riguardanti le opzioni di pubblicazione, i tipi e le configurazioni dell'offerta e gli inviti all'azione che determinano il modo e il punto in cui l'offerta viene presentata nelle vetrine del marketplace commerciale.

Contenuto dell'articolo:

- Come determinare la vetrina appropriata per la soluzione
- Opzioni di pubblicazione e inviti all'azione disponibili in ogni vetrina
- Tipi di offerta disponibili per ogni opzione di pubblicazione

## <a name="commercial-marketplace-publishing-options"></a>Opzioni di pubblicazione del marketplace commerciale

La tabella seguente illustra le opzioni di pubblicazione per i tipi di offerta in Microsoft AppSource e Azure Marketplace.

|   | **Elenco (contatto)**  | **Elenco (versione di valutazione)**  | **Free** | **BYOL** | **Transazione**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Macchina virtuale** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **App di Azure (più VM)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Immagine del contenitore** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Modulo IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Servizi gestiti** |  |  |  | Azure Marketplace |   |
| **Servizi di consulenza** | Entrambe le vetrine |  |  |  |   |
| **App SaaS** | Entrambe le vetrine | Entrambe le vetrine | Entrambe le vetrine |  | Entrambe le vetrine* |
| **App di Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Componente aggiuntivo Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Le offerte di transazioni per app SaaS in Microsoft AppSource sono attualmente disponibili solo tramite carta di credito.

&#42;&#42; Le offerte di Microsoft 365 sono gratuite per l'installazione e possono essere monetizzate tramite l'offerta SaaS come servizio di licenza. Per altre informazioni, vedere [Monetizzare il componente aggiuntivo Office 365 tramite il marketplace commerciale Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Scegliere una chiamata a Action

Le opzioni di pubblicazione disponibili offrono un engagement differenziato dei clienti e consentono nel contempo di accedere alla condivisione dei lead e ai [vantaggi del marketplace commerciale](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Si noti l'invito all'azione che corrisponde all'opzione di pubblicazione:

| **Opzione di pubblicazione**    | **Descrizione**  |
| :------------------- | :-------------------|
| **Elenco** | Semplice inserzione dell'applicazione o del servizio che consente a un utente del marketplace commerciale di chiedere di essere contattato tramite l'invito all'azione **Contattami**. |
| **Versione di valutazione** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning dell'esperienza di valutazione della soluzione, consentendo agli utenti potenziali di provare l'esperienza in-app SaaS, IaaS o Microsoft senza costi aggiuntivi per un periodo di tempo limitato prima dell'acquisto. L'invito all'azione usato per l'opzione di pubblicazione correlata alla versione di valutazione può essere di tipo **Versione di valutazione gratuita** o **Test drive**. |
| **BYOL** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning della soluzione, per poi completare la transazione finanziaria separatamente. I tipi di offerta BYOL sono ideali per le migrazioni di ambienti locali al cloud. L'invito all'azione è **Scarica adesso**.
| **Transazione** | Le offerte di transazioni vengono vendute tramite il marketplace commerciale. Microsoft è responsabile della fatturazione e delle riscossioni. L'invito all'azione è **Scarica adesso**.|

> [!Note]
> Quando si usa l'opzione di pubblicazione delle transazioni, è importante avere presenti le considerazioni relative a prezzi, fatturazione e proventi prima di selezionare un tipo di offerta e di creare l'offerta. Per altre informazioni, vedere l'articolo [Funzionalità per le transazioni del marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-a-storefront"></a>Selezione di una vetrina

Ogni vetrina risponde a requisiti univoci dei clienti ed è destinata a target specifici. Il tipo di offerta, le funzionalità di transazione e la categoria determineranno la posizione in cui verrà pubblicata l'offerta. Le categorie e le sottocategorie sono mappate a ogni storefront in base ai destinatari:

**Microsoft AppSource** è destinato agli utenti aziendali che cercano soluzioni line-of-business o di settore e servizi di consulenza per Dynamics 365, Microsoft 365 e Power Platform.

**Azure Marketplace** è destinato a professionisti IT e sviluppatori che cercano soluzioni compilate per o in Azure, oltre a servizi di consulenza che accelerano l'uso di Azure.

Selezionare la categoria e la sottocategoria che meglio si allineano con i destinatari di destinazione. Ad esempio, una web application firewall deve essere pubblicata in Azure Marketplace, sotto la categoria sicurezza, in quanto i destinatari desiderati sono professionisti IT. Un'app di gestione del contratto deve invece essere pubblicata in AppSource nella categoria Sales, perché i destinatari sono utenti aziendali. La selezione della categoria o della sottocategoria non corretta può comportare la pubblicazione dell'offerta nella vetrina errata.

### <a name="publishing-to-both-storefronts-saas-offers-only"></a>Pubblicazione in entrambe le vetrine (solo offerte SaaS)

Le offerte SaaS possono essere pubblicate in Azure Marketplace o in AppSource. Se l'offerta SaaS è destinata *a un pubblico* tecnico (Azure Marketplace) e a un pubblico aziendale (AppSource), selezionare una categoria e/o una sottocategoria applicabile a ogni storefront. Le offerte pubblicate in entrambe le vetrine devono avere una proposta di valore che si estende ai professionisti IT *e* agli utenti aziendali.

> [!IMPORTANT]
> Le offerte SaaS con fatturazione a consumo sono disponibili tramite Azure Marketplace e il portale di Azure. Le offerte SaaS con solo piani privati sono disponibili tramite il portale di Azure.

| Fatturazione a consumo | Piano pubblico | Piano privato | Disponibile in: |
|---|---|---|---|
| sì             | Sì         | No           | Azure Marketplace e portale di Azure |
| sì             | sì         | sì          | Azure Marketplace e portale di Azure * |
| Sì             | No          | Sì          | Solo portale di Azure |
| No              | No          | Sì          | Solo portale di Azure |

&#42; il piano privato dell'offerta sarà disponibile solo tramite il portale di Azure

Ad esempio, un'offerta con fatturazione a consumo e solo un piano privato (nessun piano pubblico) viene acquistata dai clienti nel portale di Azure. Scopri di più sulle [offerte private in Microsoft Commercial Marketplace](private-offers.md).

### <a name="categories"></a>Categorie

Le categorie e le sottocategorie sono mappate a ogni storefront in base ai destinatari. Selezionare le categorie e le sottocategorie che meglio si allineano con l'offerta e con i destinatari desiderati. È possibile selezionare:

- Almeno una delle due categorie. È possibile scegliere una categoria primaria e secondaria.
- Fino a due sottocategorie per ogni categoria primaria e/o secondaria. Se non si seleziona alcuna sottocategoria, l'offerta sarà comunque individuabile nella categoria selezionata.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver deciso un'opzione di pubblicazione, è possibile [selezionare il tipo di offerta](./publisher-guide-by-offer-type.md) da usare per presentare l'offerta stessa.
- Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
