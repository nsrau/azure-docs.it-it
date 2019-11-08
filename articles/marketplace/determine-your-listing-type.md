---
title: Determinare l'opzione di pubblicazione in Azure Marketplace | Azure
description: Questo articolo illustra i criteri di idoneità e i requisiti di pubblicazione per i partner che vogliono comprendere come pubblicare le app in Microsoft Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 967d33cb7a9eb3ec922f9d85cfc2581b85bce537
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825912"
---
# <a name="determine-your-publishing-option"></a>Determinare l'opzione di pubblicazione
L'opzione di pubblicazione scelta per l'offerta è correlata direttamente sia ai requisiti di idoneità che ai vantaggi GMT del marketplace. Ancora più importante, la selezione dell'opzione di pubblicazione e il tipo di offerta definiscono il modo in cui gli utenti interagiranno con l'offerta del Marketplace.

Per configurare l'offerta, è necessario comprendere i concetti chiave del Marketplace seguenti: le opzioni di pubblicazione, i tipi di offerta e la configurazione e le chiamate all'azione che gestiranno come e dove viene presentata l'offerta nelle vetrine del Marketplace.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


Contenuto dell'articolo
<ul><li>    Come determinare la vetrina appropriata per la soluzione </ul></li>
<ul><li>    Opzioni di pubblicazione e inviti all'azione disponibili in ogni vetrina </ul></li>
<ul><li>    Tipi di offerta disponibili per ogni opzione di pubblicazione </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Selezione di una vetrina, opzione di pubblicazione e tipo di offerta per la soluzione

Prima di selezionare un'opzione di pubblicazione, è importante comprendere i requisiti di idoneità Storefront per le soluzioni, le app e i servizi del Marketplace:

Le applicazioni di **Azure Marketplace** sono soluzioni "Building Block" tecniche predefinite o predefinite per Azure e destinate a un pubblico it o di uno sviluppatore. I servizi di consulenza di Azure Marketplace sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Azure o di accelerarne l'uso.

Le applicazioni **AppSource** sono soluzioni line-of-business che possono essere integrate in Azure o predefinite: Dynamics 365, Office 365, Power bi o Power Apps. I servizi di consulenza di AppSource sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Dynamics 365 e Power BI o di accelerarne l'uso.


## <a name="understand-storefront-selection"></a>Informazioni sulla selezione della vetrina

La vetrina in cui verrà presentata l'offerta, Azure Marketplace e/o AppSource, verrà determinata automaticamente in base ai dettagli dell'offerta e ai destinatari, nonché alle categorie e ai settori selezionati dall'utente durante la creazione dell'offerta. 

>[!Note]
>"Cross-listing" (solo per app SaaS): quando un elenco o un'offerta basata sulla versione di valutazione soddisfa i criteri per i destinatari tecnici e aziendali, l'offerta sarà elencata in entrambe le vetrine. Altre informazioni sulle opzioni di pubblicazione sono disponibili di seguito.

## <a name="choose-a-publishing-option"></a>Scegliere un'opzione di pubblicazione

Le opzioni di pubblicazione disponibili offrono un impegno differenziato per i clienti, offrendo al contempo l'accesso ai [vantaggi del mercato commerciale](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits)e della condivisione dei lead.  Si noti l'invito all'azione che corrisponde all'opzione di pubblicazione:

| **Opzione di pubblicazione**    | **Descrizione**  |
| :------------------- | :-------------------|
| **Elenco** | Semplice inserzione dell'applicazione o del servizio che consente a un utente del marketplace di chiedere la connessione con il cliente tramite l'invito all'azione **Contattami**. |
| **Versione di valutazione** | USA Marketplace per migliorare l'individuabilità e automatizzare il provisioning dell'esperienza di valutazione della tua soluzione, consentendo a potenziali utenti di usare l'esperienza SaaS, IaaS o Microsoft in-app senza costi aggiuntivi per un periodo di tempo limitato prima di acquistare. L'invito all'azione usato per l'opzione di pubblicazione correlata alla versione di valutazione può essere di tipo **Versione di valutazione** o **Test drive**. |
|**BYOL**  |USA Marketplace per migliorare l'individuabilità e automatizzare il provisioning della soluzione e completare la transazione finanziaria separatamente. I tipi di offerta BYOL sono ideali per le migrazioni da sito locale a cloud. La chiamata a Action è **ora**disponibile.
| **Transazione** | L'applicazione viene eseguita in Azure e può essere sottoposta a provisioning come risorsa direttamente nella sottoscrizione di Azure del cliente quando il cliente seleziona la chiamata **Get it now** to Action. I costi delle licenze software possono essere acquistati e fatturati tramite lo strumento di pagamento scelto e le condizioni del cliente ed è possibile scegliere di offrire un accesso limitato al tempo per la **versione di valutazione del software gratuita** (disponibile solo per Azure Marketplace). |

>[!Note]
>Quando si usa l'opzione di pubblicazione delle transazioni, è importante avere presenti le considerazioni relative a prezzi, fatturazione e proventi prima di selezionare un tipo di offerta e di creare l'offerta. Vedere l'[articolo sulle considerazioni commerciali e di fatturazione del Marketplace per altre informazioni](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Passaggi successivi

*   Dopo aver deciso un'opzione di pubblicazione, è possibile [selezionare il tipo di offerta](./publisher-guide-by-offer-type.md) da usare per presentare l'offerta stessa.
*   Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
*   Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.



