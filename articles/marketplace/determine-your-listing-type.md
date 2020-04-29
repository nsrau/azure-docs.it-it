---
title: Determinare l'opzione di pubblicazione nelle vetrine di Microsoft Commercial Marketplace
description: Questo articolo descrive i criteri di idoneità e i requisiti di pubblicazione per i partner che tentano di comprendere come pubblicare app in Microsoft AppSource e in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e91ca0334276dbe9118eb0e40d042f598e8b3c0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415729"
---
# <a name="determine-your-publishing-option"></a>Determinare l'opzione di pubblicazione

L'opzione di pubblicazione scelta per l'offerta si riferisce direttamente ai requisiti di idoneità e ai vantaggi GTM del Marketplace commerciale. Ancora più importante, la selezione dell'opzione di pubblicazione e il tipo di offerta definiscono il modo in cui gli utenti interagiranno con l'offerta del Marketplace commerciale.

Per configurare l'offerta, è necessario comprendere i seguenti concetti chiave del Marketplace commerciale: le opzioni di pubblicazione, i tipi di offerta e la configurazione e le chiamate all'azione che determineranno come e dove viene presentata l'offerta nelle vetrine del Marketplace commerciale.

Contenuto dell'articolo:

- Come determinare la vetrina appropriata per la soluzione
- Opzioni di pubblicazione e inviti all'azione disponibili in ogni vetrina
- Tipi di offerta disponibili per ogni opzione di pubblicazione

## <a name="commercial-marketplace-publishing-options"></a>Opzioni di pubblicazione del Marketplace commerciale

La tabella seguente illustra le opzioni di pubblicazione per i tipi di offerta in Microsoft AppSource e Azure Marketplace.

|   | **Elenco (contatto)**  | **Elenco (versione di valutazione)**  | **Free** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Macchina virtuale** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **App di Azure (più macchine virtuali)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Immagine del contenitore** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Modulo IoT Edge** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Servizi gestiti** |  |  |  | Azure Marketplace |   |
| **Servizi di consulenza** | Entrambe le vetrine |  |  |  |   |
| **App SaaS** | Entrambe le vetrine | Entrambe le vetrine | Entrambe le vetrine |  | Entrambe le vetrine * |
| **App Microsoft 365** | AppSource | AppSource |  |  | AppSource * *  |
| **Componente aggiuntivo Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; offerte Transact app SaaS in Microsoft AppSource attualmente sono solo carte di credito.

&#42;&#42; Microsoft 365 offerte sono gratuite per l'installazione e possono essere monetizzate tramite l'offerta SaaS come servizio di gestione delle licenze. Per ulteriori informazioni, vedere [monetizzare il componente aggiuntivo di Office 365 tramite Microsoft Commercial Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Selezione di una vetrina

Prima di selezionare un'opzione di pubblicazione, è importante comprendere i requisiti di idoneità Storefront per le soluzioni, le app e i servizi del Marketplace commerciale. Ogni vetrina offre requisiti univoci per i clienti e si rivolge a destinatari specifici. Il tipo di offerta, le funzionalità di transazione e la categoria o il settore determineranno dove pubblicare l'offerta.

**Microsoft AppSource** applicazioni sono soluzioni line-of-business che possono essere integrate in Azure o predefinite: Dynamics 365, Office 365, Power bi o Power Apps. I servizi di consulenza di AppSource sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Dynamics 365 e Power BI o di accelerarne l'uso.

Le applicazioni di **Azure Marketplace** sono soluzioni "Building Block" tecniche predefinite o predefinite per Azure e destinate a un pubblico it o di uno sviluppatore. I servizi di consulenza di Azure Marketplace sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Azure o di accelerarne l'uso.

>[!Note]
>Elenco incrociato (solo per app SaaS): quando un elenco o un'offerta basata sulla versione di valutazione soddisfa i criteri per i destinatari tecnici e aziendali, l'offerta sarà elencata in entrambe le vetrine. Altre informazioni sulle opzioni di pubblicazione sono disponibili di seguito.

## <a name="choose-a-publishing-option"></a>Scegliere un'opzione di pubblicazione

Le opzioni di pubblicazione disponibili offrono un impegno differenziato per i clienti, offrendo al contempo l'accesso ai [vantaggi del mercato commerciale](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)e della condivisione dei lead. Si noti l'invito all'azione che corrisponde all'opzione di pubblicazione:

| **Opzione di pubblicazione**    | **Descrizione**  |
| :------------------- | :-------------------|
| **Elenco** | Elenco semplice dell'applicazione o del servizio che consente a un utente del Marketplace commerciale di richiedere la connessione al cliente tramite la chiamata all'azione **contattami** . |
| **Valutazione** | Usa il Marketplace commerciale per migliorare l'individuabilità e automatizzare il provisioning dell'esperienza di valutazione della tua soluzione, consentendo agli utenti potenziali di usare la tua esperienza in-app SaaS, IaaS o Microsoft senza costi aggiuntivi per un periodo di tempo limitato prima dell'acquisto. Le chiamate a Action usate per l'opzione di pubblicazione della versione di valutazione sono **versione di valutazione gratuita** o **test drive**. |
| **BYOL** | Usa il Marketplace commerciale per migliorare l'individuabilità e automatizzare il provisioning della soluzione e completare la transazione finanziaria separatamente. I tipi di offerta BYOL sono ideali per le migrazioni da sito locale a cloud. La chiamata a Action è **ora**disponibile.
| **Transact** | Le offerte Transact vengono vendute attraverso il Marketplace commerciale. Microsoft è responsabile della fatturazione e delle raccolte. La chiamata a Action è **ora**disponibile.|

> [!Note]
> Quando si usa l'opzione di pubblicazione delle transazioni, è importante avere presenti le considerazioni relative a prezzi, fatturazione e proventi prima di selezionare un tipo di offerta e di creare l'offerta. Per altre informazioni, vedere l'articolo relativo alle [funzionalità di transacting del Marketplace commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver deciso un'opzione di pubblicazione, è possibile [selezionare il tipo di offerta](./publisher-guide-by-offer-type.md) da usare per presentare l'offerta stessa.
- Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
