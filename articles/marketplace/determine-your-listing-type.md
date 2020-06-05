---
title: Determinare l'opzione di pubblicazione - Marketplace commerciale Microsoft
description: Questo articolo descrive i criteri di idoneità e i requisiti per la pubblicazione di offerte in Microsoft AppSource e Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 92a24a59038eb74a6a2f6a738005ff8aadf35ff0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658069"
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

## <a name="selecting-a-storefront"></a>Selezione di una vetrina

Prima di selezionare un'opzione di pubblicazione, è importante comprendere i requisiti di idoneità della vetrina per le soluzioni, le app e i servizi del marketplace commerciale. Ogni vetrina risponde a requisiti univoci dei clienti ed è destinata a target specifici. Il tipo di offerta, le funzionalità di transazione e la categoria o il settore determineranno dove pubblicare l'offerta.

Le applicazioni **Microsoft AppSource** sono soluzioni line-of-business che possono essere integrate in Azure o realizzate per Dynamics 365, Office 365, Power BI o Power Apps. I servizi di consulenza di AppSource sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Dynamics 365 e Power BI o di accelerarne l'uso.

Le applicazioni **Azure Marketplace** sono blocchi predefiniti tecnici integrati o realizzati per Azure e destinati a responsabili IT o sviluppatori. I servizi di consulenza di Azure Marketplace sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Azure o di accelerarne l'uso.

>[!Note]
>Offerta in più elenchi (solo per app SaaS): quando un'offerta basata su elenco o su una versione di valutazione soddisfa i criteri sia per un pubblico di utenti tecnici che aziendali, l'offerta verrà elencata in entrambe le vetrine. Altre informazioni sulle opzioni di pubblicazione sono disponibili di seguito.

## <a name="choose-a-publishing-option"></a>Scegliere un'opzione di pubblicazione

Le opzioni di pubblicazione disponibili offrono un engagement differenziato dei clienti e consentono nel contempo di accedere alla condivisione dei lead e ai [vantaggi del marketplace commerciale](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Si noti l'invito all'azione che corrisponde all'opzione di pubblicazione:

| **Opzione di pubblicazione**    | **Descrizione**  |
| :------------------- | :-------------------|
| **Elenco** | Semplice inserzione dell'applicazione o del servizio che consente a un utente del marketplace commerciale di chiedere di essere contattato tramite l'invito all'azione **Contattami**. |
| **Versione di valutazione** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning dell'esperienza di valutazione della soluzione, consentendo agli utenti potenziali di provare l'esperienza in-app SaaS, IaaS o Microsoft senza costi aggiuntivi per un periodo di tempo limitato prima dell'acquisto. L'invito all'azione usato per l'opzione di pubblicazione correlata alla versione di valutazione può essere di tipo **Versione di valutazione gratuita** o **Test drive**. |
| **BYOL** | Usare il marketplace commerciale per aumentare la visibilità e automatizzare il provisioning della soluzione, per poi completare la transazione finanziaria separatamente. I tipi di offerta BYOL sono ideali per le migrazioni di ambienti locali al cloud. L'invito all'azione è **Scarica adesso**.
| **Transazione** | Le offerte di transazioni vengono vendute tramite il marketplace commerciale. Microsoft è responsabile della fatturazione e delle riscossioni. L'invito all'azione è **Scarica adesso**.|

> [!Note]
> Quando si usa l'opzione di pubblicazione delle transazioni, è importante avere presenti le considerazioni relative a prezzi, fatturazione e proventi prima di selezionare un tipo di offerta e di creare l'offerta. Per altre informazioni, vedere l'articolo [Funzionalità per le transazioni del marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver deciso un'opzione di pubblicazione, è possibile [selezionare il tipo di offerta](./publisher-guide-by-offer-type.md) da usare per presentare l'offerta stessa.
- Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
