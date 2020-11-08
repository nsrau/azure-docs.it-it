---
title: Come creare piani per l'offerta di applicazione Azure
description: Informazioni su come creare piani per l'offerta di applicazioni Azure nel centro per i partner.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370264"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Come creare piani per l'offerta di applicazione Azure

Le offerte vendute attraverso Microsoft Commercial Marketplace devono avere almeno un piano per elencare l'offerta nel Marketplace commerciale. È possibile creare un'ampia gamma di piani con diverse opzioni all'interno della stessa offerta. Questi piani, detti anche SKU, possono differire in termini di tipo di piano ( _modello di soluzione_ o _applicazione gestita_ ), monetizzazione o destinatari. Per indicazioni generali sui piani, vedere [piani e prezzi per le offerte per Marketplace commerciali](plans-pricing.md).

## <a name="create-a-plan"></a>Creare un piano

1. Nella parte superiore della scheda **panoramica piano** selezionare **+ Crea nuovo piano**.
1. Nella finestra di dialogo visualizzata, nella casella **ID piano** , immettere un ID piano univoco. Questo ID sarà visibile ai clienti nell'URL del prodotto. Usare un massimo di 50 caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. Non è possibile modificare l'ID del piano dopo aver selezionato **Crea**.
1. Nella casella **nome piano** immettere un nome univoco per il piano. Il nome verrà visualizzato dai clienti quando si decide quale piano selezionare nell'offerta. Usare un massimo di 50 caratteri.
1. Selezionare **Crea**.

## <a name="define-the-plan-setup"></a>Definire l'installazione del piano

La scheda **programma installazione** consente di impostare il tipo di piano, se riutilizza la configurazione tecnica da un altro piano e le aree di Azure in cui il piano dovrebbe essere disponibile. Le risposte in questa scheda influiscono sui campi che vengono visualizzati in altre schede per il piano.

### <a name="select-the-plan-type"></a>Selezionare il tipo di piano

- Dall'elenco **tipo di piano** selezionare **modello di soluzione** o **applicazione gestita**.

Un piano **Modello di soluzione** viene gestito interamente dal cliente. Un piano **Applicazione gestita** consente agli editori di gestire l'applicazione per conto del cliente. Per informazioni dettagliate su questi due tipi di piano, vedere [tipi di piani](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Riutilizzare la configurazione tecnica (facoltativo)

Se è stato creato più di un piano dello stesso tipo in questa offerta e la configurazione tecnica è identica tra di essi, è possibile riutilizzare la configurazione tecnica da un altro piano. Questa impostazione non può essere modificata dopo la pubblicazione del piano.

#### <a name="to-re-use-technical-configuration"></a>Per riutilizzare la configurazione tecnica

1. Selezionare la casella **di controllo questo piano riutilizza la configurazione tecnica da un altro piano dello stesso tipo** .
1. Nell'elenco visualizzato selezionare il piano di base desiderato.

> [!NOTE]
> Quando si riutilizzano i pacchetti di un altro piano, l'intera scheda **configurazione tecnica** scompare da questo piano. I dettagli relativi alla configurazione tecnica dell'altro piano, inclusi gli eventuali aggiornamenti in futuro, vengono usati anche per questo piano.

### <a name="select-azure-regions-clouds"></a>Selezionare le aree di Azure (cloud)

Il piano deve essere reso disponibile in almeno un'area di Azure. Dopo che il piano è stato pubblicato e disponibile in un'area di Azure specifica, non è possibile rimuovere tale area dall'offerta.

#### <a name="azure-global-region"></a>Area globale di Azure

Per impostazione predefinita, la casella di controllo **globale di Azure** è selezionata. In questo modo il piano viene reso disponibile ai clienti in tutte le aree globali di Azure con integrazione commerciale con il Marketplace. Per i piani di applicazioni gestite, è possibile selezionare con i mercati in cui si vuole rendere disponibile il piano.

Per rimuovere l'offerta dall'area, deselezionare la casella di controllo **globale di Azure** .

#### <a name="azure-government-region"></a>Area di Azure per enti pubblici

Questa area garantisce l'accesso controllato per i clienti da entità federali, statali, locali o tribali degli Stati Uniti, oltre ai partner idonei per la loro gestione. L'editore è responsabile di tutti i controlli di conformità, le misure di sicurezza e le procedure consigliate. Azure per enti pubblici usa data center e reti fisicamente isolati (situati solo negli Stati Uniti).

I servizi di Azure per enti pubblici gestiscono dati soggetti a determinati requisiti e normative governativi. Ad esempio, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Per aumentare la consapevolezza sulle certificazioni per questi programmi, è possibile specificare fino a 100 collegamenti descrittivi. Questi possono essere collegamenti diretti alla presentazione del programma o collegamenti alle descrizioni della conformità di questi ultimi sui propri siti Web. Questi collegamenti sono visibili solo ai clienti di Azure per enti pubblici.

##### <a name="to-select-the-azure-government-region"></a>Per selezionare l'area di Azure per enti pubblici

1. Selezionare la casella di controllo **Azure per enti pubblici** .
1. In **certificazioni di Azure per enti pubblici** selezionare **+ Aggiungi certificazione (max 100)**.
1. Nelle caselle visualizzate specificare un nome e un collegamento a una certificazione.
1. Per aggiungere un'altra certificazione, ripetere i passaggi 2 e 3.

Selezionare **Salva bozza** prima di continuare con la scheda successiva: elenco del piano.

## <a name="define-the-plan-listing"></a>Definire l'elenco dei piani

Nella scheda **elenco piani** è possibile configurare i dettagli dell'elenco del piano. In questa scheda vengono visualizzate informazioni specifiche che mostrano la differenza tra i piani nella stessa offerta. È possibile definire il nome del piano, il riepilogo e la descrizione che si desidera visualizzare nel Marketplace commerciale.

1. Nella casella **nome piano** il nome specificato in precedenza per questo piano viene visualizzato qui. È possibile modificarlo in qualsiasi momento. Questo nome verrà visualizzato nel Marketplace commerciale come titolo del piano software dell'offerta ed è limitato a 100 caratteri.
1. Nella casella **Riepilogo piano** , fornire un breve riepilogo del piano (non dell'offerta). Questo riepilogo è limitato a 100 caratteri.
1. Nella casella **Descrizione piano** spiegare cosa rende univoco questo piano software e qualsiasi differenza rispetto ad altri piani all'interno dell'offerta. Non descrivere l'offerta, ma solo il piano. Questa descrizione può contenere un massimo di 2.000 caratteri.
1. Prima di continuare, selezionare **Salva bozza**.

## <a name="next-steps"></a>Passaggi successivi

Eseguire una di queste operazioni:

- Se si sta configurando un piano di modello di soluzione, vedere [configurare un piano del modello di soluzione](create-new-azure-apps-offer-solution.md).
- Se si sta configurando un piano di applicazione gestito, vedere [configurare un piano di applicazione gestita](create-new-azure-apps-offer-managed.md).
