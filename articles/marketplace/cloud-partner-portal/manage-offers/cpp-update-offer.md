---
title: Aggiornare le offerte del Marketplace - Azure Marketplace | Microsoft Docs
description: Aggiornare le offerte nei marketplace Azure e AppSource usando il portale Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 76b607502324c3ca25b3536d5197a97dbb80399d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729617"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aggiornare le offerte di Azure Marketplace e AppSource

Esistono diversi tipi di aggiornamenti che è possibile applicare all'offerta dopo la pubblicazione.  Il [portale Cloud Partner](https://cloudpartner.azure.com/) consente di modificare correttamente gli attributi di un'offerta, tra cui:

-  Aggiunta di una nuova versione dell'immagine di macchina virtuale o del pacchetto a uno SKU esistente
-  Cambiare le aree in cui è disponibile uno SKU
-  Aggiungere nuovi SKU
-  Aggiornamento dei metadati del marketplace per le offerte o per gli SKU 
-  Aggiornamento dei prezzi sulle offerte con pagamento in base al consumo

Il portale include anche funzionalità, come la possibilità di confrontare le funzionalità e di visualizzare una cronologia delle funzionalità di un'offerta, che facilitano la gestione delle modifiche.  Dopo aver modificato un'offerta o uno SKU, è necessario ripubblicarlo prima che le modifiche apportate vengono pubblicate.  Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta del marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Modifiche non consentite per un'offerta o uno SKU

Esistono alcuni attributi di un'offerta o di uno SKU che non possono essere modificati dopo la pubblicazione nel marketplace.  I campi corrispondenti sono disabilitati nella scheda **Editor** del portale, ad esempio:  

- ID offerta e ID autore
- ID SKU 
- Numero di dischi dati di SKU esistenti
- Modifiche del modello di fatturazione/licenza di SKU esistenti
- Tag di versione, ad esempio: `1.0.1`


## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le sezioni seguenti illustrano come eseguire alcune delle operazioni di aggiornamento più comuni.  Queste operazioni non sono disponibili per tutti i tipi di offerte.  È necessario accedere al portale Cloud Partner per avviare una di queste operazioni.


### <a name="update-offer-contacts"></a>Aggiornare i contatti dell'offerta

Usare la procedura seguente per aggiornare i contatti di supporto per l'offerta.
1. Nella pagina **Tutte le offerte** selezionare l'offerta.
2. Selezionare la scheda **Contatti**. Aggiornare i contatti.
3. Fare clic sul pulsante **Salva**.
4. Selezionare **Pubblica** per avviare il processo di pubblicazione.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Cambiare le aree in cui è disponibile un'offerta o uno SKU

Con il tempo, si potrebbe voler pubblicare l'offerta/SKU in altre aree.
Oppure si potrebbe voler interrompere il supporto per l'offerta/SKU in una determinata area.
Per implementare queste modifiche, seguire questa procedura.

1. Nella pagina **Tutte le offerte** trovare l'offerta che si vuole aggiornare.

Per le offerte di Azure Marketplace:

1. Selezionare la scheda **SKU**.  Selezionare lo SKU da modificare.
1. Fare clic sul pulsante **Selezionare paesi** che si trova sotto il campo **Disponibilità paese**.
1. Nella finestra di dialogo della disponibilità in base all'area geografica aggiungere o rimuovere aree per lo SKU selezionato.

Per le offerte di AppSource:

1. Selezionare la scheda **Dettagli vetrina**.
1. Accanto all'etichetta **Paesi supportati** fare clic su **Paesi supportati**. 
1. Nella finestra di dialogo dei paesi supportati aggiungere o rimuovere le aree per questa offerta.

Per entrambi i marketplace:

1. Fare clic su **Pubblica** per avviare il processo di pubblicazione. 

Se uno SKU è stato reso disponibile in una nuova area, è possibile specificare i prezzi per tale area specifica tramite la funzionalità di **esportazione dei dati sui prezzi**. Se si aggiunge nuovamente un'area disponibile in precedenza, non è possibile aggiornarne i prezzi perché le modifiche ai prezzi non sono consentite.


### <a name="add-a-new-sku"></a>Aggiungere un nuovo SKU 

Per rendere disponibile un nuovo SKU per un'offerta esistente, seguire questa procedura:

1. Nella pagina **Tutte le offerte** trovare l'offerta.
3. Nel modulo **SKU** fare clic su **Aggiungi nuovo SKU** e specificare un **ID SKU** nell'elemento popup.
4. Seguire i restanti passaggi illustrati in dettaglio in [Pubblicare un'offerta di macchina virtuale](../virtual-machine/cpp-publish-offer.md).
5. Fare clic su **Pubblica** per avviare il processo di pubblicazione.


### <a name="update-offer-marketplace-assets"></a>Aggiornare gli asset del marketplace dell'offerta

In alcuni scenari potrebbe essere necessario aggiornare gli asset di immagini e basati su testo del marketplace, ad esempio logo aziendali, descrizione dell'offerta e così via. Usare la procedura seguente per aggiornare questi asset.

1. Nella pagina **Tutte le offerte** trovare l'offerta. 
2. Selezionare la scheda **Marketplace** e seguire le istruzioni nell'argomento *Scheda Marketplace* dell'offerta.
3. Fare clic su **Pubblica** per avviare il processo di pubblicazione.


### <a name="update-pricing-on-published-offers"></a>Aggiornamento dei prezzi di offerte pubblicate

Dopo la pubblicazione di un'offerta con pagamento in base al consumo, non è possibile aumentare il prezzo di uno SKU esistente.  Creare invece uno SKU all'interno della stessa offerta, eliminare lo SKU precedente e quindi ripubblicare l'offerta. È possibile ridurre il prezzo per le offerte pubblicate in precedenza. Per ridurre il prezzo di offerta:

1. Selezionare lo SKU per il quale si vuole ridurre il prezzo.
2. È necessario impostare il prezzo inferiore con lo stesso meccanismo usato in origine: direttamente nell'interfaccia utente del portale o con l'importazione/esportazione nel foglio di calcolo.
3. Fare clic su **Save**.
4. Fare clic su **Pubblica** per avviare il processo di pubblicazione.

I prezzi sono visibili ai nuovi clienti dopo la pubblicazione nel marketplace e tutti i nuovi clienti pagheranno il nuovo prezzo ridotto.  Per i clienti esistenti, la riduzione dei prezzi rispecchia retroattivamente l'inizio del ciclo di fatturazione durante il quale la riduzione dei prezzi diventa effettiva.  Se hanno già ricevuto fatture per il ciclo durante il quale si è verificata una riduzione di prezzo, i clienti riceveranno un rimborso a copertura della riduzione del prezzo al momento del ciclo di fatturazione successivo.


## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche in un'offerta pubblicata, è possibile usare la funzionalità *Confronta* per controllare le modifiche. Per utilizzare questa funzionalità:

1. In qualsiasi momento durante il processo di modifica, è possibile fare clic sul pulsante **Confronta** nella scheda **Editor** dell'offerta.
2. Le versioni delle modifiche salvate apportate a questa offerta vengono visualizzate in una finestra di confronto accanto a quelle dell'offerta del marketplace. 

![Pulsante per confrontare le offerte nella scheda Editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** nella barra dei menu verticale a sinistra del portale Cloud Partner.  La pagina Cronologia fornisce filtri flessibili basati su diverse caratteristiche e supporta l'ordinamento delle colonne.  A ogni evento di pubblicazione viene aggiunto un timestamp.  Per altre informazioni, vedere [Pagina Cronologia dei controlli](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare il portale Cloud Partner per [eliminare uno SKU o un'offerta pubblicata](./cpp-delete-offer.md).
