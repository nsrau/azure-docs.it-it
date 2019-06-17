---
title: Aggiornare un'offerta di macchina virtuale esistente in Azure Marketplace
description: Spiega come aggiornare un'offerta di macchina virtuale esistente in Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: 4a75d706d55512201786b2b74376047ff75380a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938128"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aggiornare un'offerta di macchina virtuale esistente in Azure Marketplace

Questo articolo descrive in dettaglio i diversi aspetti dell'aggiornamento di un'offerta di macchina virtuale (VM) nel [portale Cloud Partner](https://cloudpartner.azure.com/) e della successiva ripubblicazione dell'offerta. 

I motivi più comuni dell'aggiornamento dell'offerta sono svariati, ad esempio:

-  L'aggiunta di una nuova versione dell'immagine della macchina virtuale a SKU esistenti
-  La modifica delle aree in cui uno SKU è disponibile
-  L'aggiunta di nuovi SKU
-  L'aggiornamento dei metadati del marketplace per l'offerta o per singoli SKU
-  L'aggiornamento dei prezzi per le offerte con pagamento in base al consumo

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.  

>[!Note]
>Cloud Solution Provider (CSP) partner canale acconsentire esplicitamente a questo punto è disponibile.  Vedi [Cloud Solution Provider](../../cloud-solution-providers.md) per altre informazioni sul marketing dell'offerta tramite Microsoft CSP partner canali.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Modifiche all'offerta di macchina virtuale o allo SKU non consentite

Dopo la pubblicazione di un'offerta di macchina virtuale in Azure Marketplace, alcuni attributi dell'offerta o dello SKU non possono essere modificati. I principali sono:

-  **ID offerta** e **ID editore** dell'offerta
-  **ID SKU** di SKU esistenti
-  Numero di dischi dati di SKU esistenti
-  Modello di fatturazione o di licenza per SKU esistenti
-  Prezzo di uno SKU pubblicato, se in aumento


## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le caratteristiche di un'offerta di macchina virtuale che è possibile modificare sono molto numerose. Le operazioni più comuni, tuttavia, sono le seguenti.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aggiornamento della versione dell'immagine della macchina virtuale per uno SKU

È normale che l'immagine di una macchina virtuale venga aggiornata periodicamente con patch di sicurezza, funzionalità aggiuntive e così via.  In tali scenari, è necessario aggiornare l'immagine della macchina virtuale a cui fa riferimento lo SKU tramite la procedura seguente:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** individuare l'offerta da aggiornare.

3.  Nella scheda **SKU** fare clic sullo SKU associato all'immagine della macchina virtuale da aggiornare.

4.  In **Disk version** (Versione disco) fare clic su **+New Disk Version** (+Nuova versione disco) per aggiungere una nuova immagine di macchina virtuale.

5.  Specificare la **versione del disco** delle immagini di macchina virtuale. La versione del disco deve seguire il formato della [versione semantica](https://semver.org/). Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Verificare che la nuova versione specificata sia successiva a tutte le versioni precedenti. In caso contrario, dopo la nuova pubblicazione la nuova versione non verrà visualizzata né nel portale né in Azure Marketplace.

6.  Per **OS VHD URL** (URL disco rigido virtuale sistema operativo) immettere l'[URI della firma di accesso condiviso](./cpp-get-sas-uri.md) creato per il disco rigido virtuale del sistema operativo. 

    > [!WARNING] 
    > Il numero di dischi dati non può cambiare da una versione di SKU a un'altra. Se le versioni precedenti avevano un certo numero di dischi dati configurati, anche la nuova versione deve avere lo stesso numero di dischi dati.

7.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione della nuova versione della macchina virtuale in Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Modifica della disponibilità in base all'area geografica di uno SKU

Con il tempo, si potrebbe voler pubblicare l'offerta/SKU in altre aree.  Oppure si potrebbe voler interrompere il supporto per l'offerta/SKU in una determinata area.
Per modificare la disponibilità, seguire questa procedura:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** individuare l'offerta da aggiornare.

3.  Nella scheda **SKU** fare clic sullo SKU di cui si vuole modificare la disponibilità.

4.  Fare clic sul pulsante **Selezionare paesi** che si trova sotto il campo **Disponibilità paese**.

5.  Nella finestra popup della disponibilità in base all'area geografica, aggiungere o rimuovere aree per lo SKU selezionato.

6.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione e aggiornare la disponibilità in base all'area geografica degli SKU.

Se uno SKU è stato reso disponibile in una nuova area, sarà possibile specificare i prezzi per quella area specifica tramite la funzionalità di **esportazione dei dati sui prezzi**. Se si aggiunge di nuovo un'area che era stata disponibile in passato, l'opzione di aggiornamento del prezzo non è disponibile, poiché le modifiche di prezzo non sono consentite.


### <a name="add-a-new-sku"></a>Aggiunta di un nuovo SKU

Per rendere disponibile un nuovo SKU in un'offerta esistente, seguire questa procedura: 

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** individuare l'offerta da aggiornare.

3.  Nella scheda **SKU** fare clic su **Add new SKU** (Aggiungi nuovo SKU) e specificare un **ID SKU** nella finestra popup.

4.  Ripubblicare la macchina virtuale come descritto in dettaglio nell'articolo [Pubblicare una macchina virtuale in Azure Marketplace](./cpp-publish-offer.md).

5.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione del nuovo SKU.


### <a name="update-offer-marketplace-metadata"></a>Aggiornamento dei metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace, ad esempio società nome, logo e così via, associati all'offerta, usare la procedura seguente: 

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** individuare l'offerta da aggiornare.

3.  Passare alla scheda **Marketplace** e quindi per eseguire le modifiche ai metadati seguire le istruzioni nell'articolo [Pubblicare una macchina virtuale in Azure Marketplace](./cpp-publish-offer.md).

4.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione delle modifiche.


### <a name="update-pricing-on-published-offers"></a>Aggiornamento dei prezzi di offerte pubblicate

Dopo la pubblicazione di un'offerta con pagamento in base al consumo, non è possibile aumentare direttamente i prezzi dello SKU.  È tuttavia possibile creare un nuovo SKU all'interno della stessa offerta, eliminare lo SKU precedente e quindi ripubblicare l'offerta per i nuovi clienti.  È invece possibile ridurre il prezzo di un'offerta pubblicata tramite la procedura seguente:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  In **Tutte le offerte** individuare l'offerta da aggiornare.

3.  Fare clic sullo SKU per il quale si vuole ridurre il prezzo.

4.  Se i prezzi sono stati impostati nell'interfaccia utente grafica 1x1, è possibile modificare il prezzo direttamente nell'interfaccia utente. Se i prezzi vengono impostati tramite un foglio di calcolo di importazione/esportazione, è possibile diminuire i prezzi solo tramite la funzionalità di importazione/esportazione.

3.  Fare clic su **Save**.

4.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione delle modifiche.

I nuovi prezzi diminuiti saranno visibili per i nuovi clienti dopo la pubblicazione nel sito Web.  Il nuovo prezzo interesserà i clienti nei modi seguenti:

- La nuova tariffa verrà addebitata ai nuovi clienti. 
- Per i clienti esistenti, la riduzione dei prezzi si rifletterà retroattivamente all'inizio del ciclo di fatturazione durante il quale la riduzione dei prezzi diventa effettiva.
Se hanno già ricevuto fatture per il ciclo durante il quale si è verificata una riduzione di prezzo, i clienti riceveranno un rimborso a copertura della riduzione del prezzo al momento del ciclo di fatturazione successivo.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Prezzi in valuta semplificati

A partire dal 1° settembre 2018, nel portale verrà aggiunta una nuova sezione denominata **Simplified Currency Pricing** (Prezzi in valuta semplificati). Microsoft sta semplificando il business di Azure Marketplace, consentendo prezzi più prevedibili e modalità di riscossione più semplici dai clienti internazionali. Questa semplificazione prevede anche la riduzione del numero delle valute in cui verranno emesse le fatture ai clienti.

La nuova sezione avrà i prezzi espressi nelle nuove valute. Dopo la migrazione di tutti i clienti verso queste nuove valute di pagamento, la sezione originale relativa ai prezzi verrà ritirata e rimarrà solo la sezione con i prezzi in valuta semplificati.

Per le aree in cui è in corso la modifica della valuta di pagamento sarà possibile impostare nuovi prezzi fino al 1 novembre 2018. Non sarà possibile aumentare il prezzo per le aree in cui non è prevista la modifica della valuta di pagamento.

> [!NOTE] 
> Se si usano API per pubblicare l'offerta, è possibile visualizzare una nuova sezione all'interno del JSON dell'offerta, contrassegnata da `virtualMachinePricingV2` o `monthlyPricingV2`, in base al tipo di offerta. 

Per domande in merito a questa modifica, contattare l'[assistenza di Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche a un'offerta già pubblicata, è possibile controllare le modifiche apportate grazie alla funzionalità **Confronta**. Per usare questa funzionalità:

1.  In qualsiasi momento durante il processo di modifica fare clic sul pulsante **Confronta** per l'offerta.

    ![Pulsante della funzionalità Confronta](./media/publishvm_037.png)


2.  Visualizzare affiancate le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare la cronologia di eventuali attività di pubblicazione precedenti, fare clic sulla voce **Cronologia** sulla barra dei menu di spostamento a sinistra del portale Cloud Partner. Qui sarà possibile visualizzare le azioni con timestamp che sono state eseguite durante il ciclo di vita delle offerte in Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

