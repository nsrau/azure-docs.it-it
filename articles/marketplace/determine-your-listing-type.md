---
title: Determinare l'opzione di pubblicazione in Azure Marketplace | Azure
description: Questo articolo illustra i criteri di idoneità e i requisiti di pubblicazione per i partner che vogliono comprendere come pubblicare le app in Microsoft Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: a85cd5287f6947ec17dc19291a70d6dd081e1827
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035861"
---
# <a name="determine-your-publishing-option"></a>Determinare l'opzione di pubblicazione
L'opzione di pubblicazione scelta per l'offerta è correlata direttamente sia ai requisiti di idoneità che ai vantaggi GMT del marketplace. L'aspetto più importante è che la selezione dell'opzione di pubblicazione e del tipo di offerta nel portale Cloud Partner definisce il modo in cui gli utenti interagiranno con l'offerta nel marketplace.

Per abilitare le opzioni di pubblicazione, selezionare un tipo di offerta e quindi creare un'offerta del marketplace nel portale Cloud Partner. A tale scopo, è necessario comprendere i concetti chiave del marketplace seguenti, ovvero le opzioni di pubblicazione, i tipi e le configurazioni dell'offerta e gli inviti all'azione che determinano il modo e il punto in cui l'offerta viene presentata nelle vetrine del marketplace.

![](https://github.com/ellacroi/azure-docs-pr/blob/options-table/articles/marketplace/media/marketplace-publishers-guide/storefronts_options_table.png?raw=true)

>[!Note]
>La tabella precedente descrive il modo in cui la soluzione o il servizio esegue il mapping alla vetrina, nonché l'opzione di pubblicazione e il tipo di offerta usati per presentare l'offerta stessa nel marketplace.


Contenuto dell'articolo
<ul><li>    Come determinare la vetrina appropriata per la soluzione </ul></li>
<ul><li>    Opzioni di pubblicazione e inviti all'azione disponibili in ogni vetrina </ul></li>
<ul><li>    Tipi di offerta disponibili per ogni opzione di pubblicazione </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Selezione di una vetrina, opzione di pubblicazione e tipo di offerta per la soluzione

Prima di selezionare un'opzione di pubblicazione, è importante comprendere i requisiti di idoneità vetrina per le soluzioni, le app e i servizi del marketplace.

Le applicazioni **Azure Marketplace** sono blocchi predefiniti tecnici integrati o compilati per Azure. I servizi di consulenza di Azure Marketplace sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Azure o di accelerarne l'uso.

Le applicazioni **AppSource** sono soluzioni line-of-business che possono essere integrate in Azure o compilate per Dynamics 365, Office 365, PowerBI o PowerApps. I servizi di consulenza di AppSource sono offerte di servizi professionali che consentono ai clienti di iniziare a usare Dynamics 365 e Power BI o di accelerarne l'uso.

Dopo aver completato la registrazione per diventare un editore e aver ricevuto le autorizzazioni di accesso al portale Cloud Partner per la creazione, la configurazione e la pubblicazione di offerte del marketplace, sarà possibile eseguire queste operazioni:

<ul>1.  Comprendere come viene determinata la vetrina per l'offerta</ul>
<ul>2.  Scegliere un'opzione di pubblicazione per l'offerta</ul>
<ul>3.  Selezionare un tipo di offerta ed esaminare i requisiti di idoneità</ul>
<ul>4.  Creare e configurare l'offerta per la pubblicazione nel portale Cloud Partner</il></ul>

## <a name="understand-storefront-selection"></a>Informazioni sulla selezione della vetrina

La vetrina in cui viene presentata l'offerta, Azure Marketplace e/o AppSource, viene determinata in modo automatico in base ai dettagli dell'offerta e al pubblico di destinazione, nonché in base alle categorie e ai settori selezionati al momento della creazione dell'offerta nel portale Cloud Partner. 

>[!Note]
>Offerta in più elenchi (solo per app SaaS): quando un'offerta basata su elenco o su una versione di valutazione soddisfa i criteri sia per un pubblico di utenti tecnici che aziendali, l'offerta verrà elencata in entrambe le vetrine. Altre informazioni sulle opzioni di pubblicazione sono disponibili di seguito.

## <a name="choose-a-publishing-option"></a>Scegliere un'opzione di pubblicazione

Le opzioni di pubblicazione disponibili offrono un engagement differenziato dei clienti e consentono nel contempo di accedere alla condivisione dei clienti potenziali e ai [vantaggi del programma Go-To-Market](https://partner.microsoft.com/en-US/reach-customers/gtm). Prima di usare il portale Cloud Partner per creare un'offerta del marketplace, selezionare una delle tre opzioni di pubblicazione possibili. Si noti l'invito all'azione che corrisponde all'opzione di pubblicazione:

**Elenco** - semplice elenco dell'applicazione o del servizio che consente a un utente del marketplace di chiedere la connessione con il cliente tramite l'invito all'azione **Contattami**.

**Versione di valutazione** - Usare il marketplace per migliorare l'individuabilità e automatizzare il provisioning dell'esperienza di valutazione dell'applicazione, consentendo agli utenti potenziali di provare l'esperienza in-app SaaS, IaaS o Microsoft senza costi aggiuntivi per un periodo di tempo limitato prima dell'acquisto. L'invito all'azione usato per l'opzione di pubblicazione correlata alla versione di valutazione può essere di tipo **Versione di valutazione** o **Test drive**.

**Transazione** - L'applicazione viene eseguita in Azure e può essere sottoposta a provisioning come una risorsa direttamente nella sottoscrizione di Azure del cliente quando quest'ultimo seleziona l'invito all'azione per eseguire immediatamente il download. Le tariffe per la licenza software possono essere facoltativamente acquistate e fatturate in base al metodo e alle condizioni di pagamento scelti dal cliente ed è possibile scegliere di offrire l'accesso a tempo limitato alla **versione di valutazione software gratuita** (disponibile solo per Azure Marketplace). 

## <a name="select-an-offer-type"></a>Selezionare un tipo di offerta

Dopo aver deciso un'opzione di pubblicazione, è possibile selezionare il tipo di offerta da usare per presentare l'offerta stessa. 

L'offerta è l'oggetto di pubblicazione che verrà creato, pubblicato e gestito nel portale Cloud Partner. La struttura dell'offerta, ad esempio metadati, elementi e altri contenuti usati per presentare l'offerta nel marketplace, è definita dal tipo di offerta.

Prima di creare un'offerta, è necessario sceglierne il tipo. Il tipo di offerta corrisponde al tipo di soluzione, app o servizio da pubblicare, nonché all'allineamento a prodotti e servizi Microsoft. 

Un unico tipo di offerta può essere configurato in modo diverso nel portale Cloud Partner per abilitare opzioni di pubblicazione, inviti all'azione, operazioni di provisioning o prezzi differenti. L'opzione di pubblicazione e la configurazione del tipo di offerta si allineano anche ai requisiti tecnici e di idoneità. 

Prima di creare l'offerta, esaminare la vetrina e i requisiti di idoneità del tipo di offerta nella [Guida alla pubblicazione per Azure Marketplace e AppSource](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide) e i requisiti tecnici di pubblicazione.

## <a name="next-steps"></a>Passaggi successivi

*   Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
*   Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
*   Accedere al [portale Cloud Partner](https://cloudpartner.azure.com) per creare e configurare l'offerta.


