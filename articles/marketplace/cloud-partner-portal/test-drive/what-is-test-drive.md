---
title: Informazioni sul test drive | Microsoft Docs
description: Spiegazione sulla funzionalità test drive di Marketplace
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
ms.openlocfilehash: 0bdff77a4be6a28e2799d4f481492d2c7ba6bda0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457426"
---
<a name="what-is-test-drive"></a>Informazioni sul test drive
===================

Il test drive è un'ottima soluzione per presentare la propria offerta a potenziali clienti, dando loro la possibilità di \'provare prima di acquistare\', ottenendo così un aumento della conversione e la generazione di lead altamente qualificati.

Dopo aver fornito le informazioni di contatto, i clienti possono accedere all'esperienza di test drive predefinita: una versione di valutazione pratica e autonoma delle funzionalità principali del prodotto e dei vantaggi dimostrati in uno scenario di implementazione in un ambiente reale.

Il test drive consente di inserire il prodotto in un ambiente reale e di generare lead altamente qualificati durante il processo.

<a name="how-does-a-test-drive-work"></a>Funzionamento di un test drive
---------------------------

Un potenziale cliente individua un'applicazione su Marketplace, accede e accetta le condizioni d'uso. Il cliente riceve quindi un ambiente preconfigurato per provare il prodotto per un determinato numero di ore, mentre si riceve un lead altamente qualificato di cui eseguire il follow-up.

![Primo passaggio. Presentazione dell'offerta su Marketplace](./media/what-is-test-drive/step1.png)

![Secondo passaggio. Schermata di accesso di Marketplace](./media/what-is-test-drive/step1andahalf.png)

![Terzo passaggio. Schermata del contratto dell'editore dell'offerta su Marketplace](./media/what-is-test-drive/step2.png)

![Quarto passaggio. Schermata di configurazione del test drive](./media/what-is-test-drive/step3.png)

Qui di seguito, un esempio di come viene visualizzata un'offerta quando è richiesto tempo per distribuire i seguenti elementi:

![Offerta di Marketplace in distribuzione](./media/what-is-test-drive/step4.png)

![Schermata di Marketplace del test drive pronto](./media/what-is-test-drive/step5.png)

![Schermata di Marketplace del test drive completo](./media/what-is-test-drive/step6.png)

Indipendentemente dalla complessità dell'applicazione, il test drive Microsoft consente al cliente di provare il prodotto in un ambiente reale. Oggi, sono disponibili tre tipi di test drive, ognuno basato sul tipo di prodotto, scenario e marketplace.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**: Un test drive di Azure Resource Manager è un modello di distribuzione che contiene tutte le risorse di Azure che includono una soluzione compilata dall'editore. I prodotti che rientrano in questo scenario usano solo risorse di Azure.
- **[App per la logica](./logic-app-test-drive.md)**: Una versione di Test di App per la logica è un modello di distribuzione che deve includere tutte le architetture delle soluzioni complesse. Tutte le applicazioni di Dynamics e i prodotti personalizzati dovrebbero usare questo tipo di test drive.
- **Power BI**: Un'unità di Power BI Test è semplicemente un collegamento a un dashboard personalizzato compilato incorporato. Questo tipo di test drive dovrebbe essere usato per qualsiasi prodotto per il quale si vuole offrire un oggetto visivo Power BI interattivo.
    È sufficiente caricare l'URL di Power BI incorporato qui.

<a name="what-goes-on-in-the-background"></a>Elementi pubblicati sullo sfondo
-------------------------------

Il servizio di test drive è stato progettato per essere fruibile dai clienti senza che si debba effettuare alcuna operazione manuale. L'editore ha il compito di gestire e configurare le impostazioni del test drive dal portale Cloud Partner. Le impostazioni saranno poi direttamente a disposizione dei clienti.

Questo perché, dopo aver impostato la configurazione del test drive, ogni test drive diventa un'istanza gestita la cui distribuzione viene effettuata solo per i clienti che ne fanno richiesta. Una volta che l'istanza di test drive è stata assegnata, il test drive è disponibile per il periodo di tempo impostato. In un secondo momento, viene eliminato per fare spazio a un altro cliente.

<a name="next-steps"></a>Passaggi successivi
----------

Ora che è chiaro che cosa sia un test drive, consultare il tipo di test drive specifico che si vuole pubblicare per ottenere informazioni circa i campi obbligatori richiesti.

- **[Azure Resource   Manager](./azure-resource-manager-test-drive.md)**
- **[App per la logica](./logic-app-test-drive.md)**

In caso di domande, richieste di suggerimenti per la risoluzione dei problemi o per rendere più efficiente il test drive, visitare [Domande frequenti, risoluzione dei problemi e procedure consigliate](./marketing-and-best-practices.md).
