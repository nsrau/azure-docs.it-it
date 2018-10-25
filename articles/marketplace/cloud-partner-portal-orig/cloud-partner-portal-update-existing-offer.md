---
title: Aggiornare un'offerta esistente per Azure Marketplace | Microsoft Docs
description: Vengono illustrate le modalità per l'aggiornamento di un'offerta esistente di Azure Marketplace tramite il portale Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807457"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aggiornare un'offerta esistente per Azure Marketplace
==============================================

Esistono diversi tipi di aggiornamenti che è possibile eseguire quando un'offerta è pubblicata.   Il [portale Cloud Partner](https://cloudpartner.azure.com/) include diverse funzionalità che facilitano la modifica di un'offerta per garantire che tali cambiamenti siano corretti, tra cui:

-  Aggiungere nuova versione dell'immagine macchina virtuale (VM) a uno SKU esistente
-  Cambiare le aree in cui è disponibile uno SKU
-  Aggiungere nuovi SKU
-  Aggiornamento dei metadati del marketplace per le offerte o per gli SKU 
-  Aggiornamento dei prezzi sulle offerte con pagamento in base al consumo
-  Confronto delle funzionalità
-  Cronologia delle funzionalità

Dopo aver modificato un'offerta o uno SKU, devono essere ripubblicati prima che le modifiche apportate vengono pubblicate.
Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta della macchina virtuale.


<a name="unpermitted-changes-to-vm-offersku"></a>Modifiche non consentite per l'offerta/SKU della macchina virtuale
-----------------------------------

Esistono alcuni attributi dell'offerta/SKU della macchina virtuale che non possono essere modificati dopo che un'offerta è pubblicata in Azure Marketplace.

-  ID dell'offerta e ID dell'ente di pubblicazione dell'offerta.
-  ID SKU di SKU esistenti.
-  Numero di dischi dati di SKU esistenti.
-  Modifiche del modello di fatturazione/licenza per SKU esistenti.


<a name="updating-the-vm-image-version-for-a-sku"></a>Aggiornamento della versione dell'immagine di macchina virtuale per uno SKU
---------------------------------------

L'immagine di una macchina virtuale per uno SKU dell'offerta potrebbe essere stata aggiornata con funzionalità aggiuntive, patch di sicurezza e così via. In tali scenari, si vuole aggiornare l'immagine di una macchina virtuale a cui fa riferimento lo SKU. Usare la procedura seguente per aggiornare un'immagine di macchina virtuale. 

1.  Accedere al [portale per Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte**, trovare l'offerta che si vuole aggiornare.
3.  Nel modulo **SKU**, fare clic sullo SKU del quale si vuole aggiornare l'immagine di macchina virtuale.
4.  In **Versione disco**, fare clic su **+Nuova versione disco** per aggiungere una nuova immagine di macchina virtuale.
5.  Specificare la **versione del disco** delle immagini di macchina virtuale. La versione del disco deve seguire il formato della [versione semantica](http://semver.org/). Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Assicurarsi che la nuova versione specificata sia più grande delle versioni precedenti, altrimenti il nuovo numero di versione non verrà visualizzato nel portale di Azure o in Azure Marketplace al momento della nuova pubblicazione.
6.  Per **URL disco rigido virtuale del sistema operativo** immettere l'URI della firma di accesso condiviso creato per il disco rigido virtuale del sistema operativo. Si noti che il numero di dischi dati non può cambiare tra versioni diverse dello SKU. Se le versioni precedenti avevano dischi dati configurati, anche questa nuova versione deve avere dischi dati configurati.
7.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione per rendere live la nuova versione di macchina virtuale nel portale di Azure e in Azure Marketplace.


<a name="changing-regions-a-sku-is-available-in"></a>Modifica delle aree in cui è disponibile uno SKU
--------------------------------------

Con il tempo, si potrebbe voler pubblicare l'offerta/SKU in altre aree.
Oppure si potrebbe voler interrompere il supporto per l'offerta/SKU in una determinata area.
Per implementare queste modifiche, attenersi alla procedura seguente.

1.  Accedere al [portale per Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3.  Nel modulo **SKU**, fare clic sullo SKU per il quale si vuole aggiornare l'area di disponibilità.
4.  Fare clic sul pulsante **Selezionare paesi** che si trova sotto il campo **Disponibilità paese**.
5.  Nella finestra pop-up delle aree disponibili, aggiungere o rimuovere le aree per questo SKU.
6.  Fare clic su **Pubblica** per avviare il flusso di lavoro di pubblicazione per aggiornare la disponibilità delle aree degli SKU.

Se uno SKU è stato reso disponibile in una nuova area, sarà possibile specificare i prezzi per quella area specifica tramite la funzionalità di **esportazione dei dati sui prezzi**. Se si aggiunge nuovamente un'area disponibile in precedenza, l'opzione di aggiornamento dei rispettivi prezzi non è disponibile.


<a name="adding-a-new-sku"></a>Aggiunta di un nuovo SKU
----------------

Per rendere un nuovo SKU disponibile per un'offerta esistente, seguire la procedura che segue.

1.  Accedere al [portale per Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3.  Nel modulo **SKU** fare clic su **Aggiungi nuovo SKU** e specificare un **ID SKU** nella finestra pop-up.
4.  Seguire i restanti passaggi dettagliatamente illustrati in [Pubblicare una macchina virtuale in Azure Marketplace](./cloud-partner-portal-publish-virtual-machine.md).
5.  Fare clic su **Pubblica** per avviare il flusso di lavoro per rendere il nuovo SKU live.


<a name="updating-offer-marketplace-metadata"></a>Aggiornamento dei metadati del marketplace dell'offerta
------------------------------------

È possibile avere scenari in cui è necessario aggiornare i metadati del marketplace associati all'offerta, ad esempio l'aggiornamento del logo della società e così via. Seguire i passaggi successivi per aggiornare i metadati dell'offerta.

1.  Accedere al [portale per Cloud Partner](https://cloudpartner.azure.com/).
2.  In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3.  Passare al modulo **Marketplace** e seguire le istruzioni presenti per apportare modifiche.
4.  Fare clic su **Pubblica** per avviare il flusso di lavoro per rendere le modifiche live.


<a name="updating-pricing-on-published-offers"></a>Aggiornamento dei prezzi di offerte pubblicate
------------------------------------

Una volta pubblicata l'offerta con pagamento in base al consumo, non è possibile aumentare il prezzo di uno SKU esistente, ma è possibile creare un nuovo SKU per la stessa offerta, eliminare lo SKU precedente e quindi ripubblicare l'offerta. È inoltre consentito ridurre il prezzo di offerte già pubblicate. Per ridurre il prezzo di offerta:

1.  Fare clic sullo SKU per il quale si desidera ridurre il prezzo.
2.  Se sono stati impostati i prezzi in GUI 1x1, è possibile modificare il prezzo direttamente nell'interfaccia utente. Se si impostano i prezzi tramite il foglio di calcolo importazione/esportazione, è possibile solo diminuire i prezzi tramite la funzionalità importazione/esportazione.
3.  Fare clic su **Save**.
4.  Ripubblicare l'offerta pubblicamente.

I prezzi sono visibili ai nuovi clienti una volta resi pubblicati interamente sul sito Web; tutti i nuovi clienti pagano il nuovo prezzo ridotto.

Per i clienti esistenti, la riduzione dei prezzi rispecchierà retroattivamente l'inizio del ciclo di fatturazione durante il quale la riduzione dei prezzi diventa effettiva.
Se sono già state emesse fatture per il ciclo durante il quale si è verificata una riduzione del prezzo, i clienti riceveranno un rimborso per coprire la riduzione del prezzo al momento del ciclo di fatturazione successivo.


<a name="simplified-currency-pricing"></a>Prezzi di valuta semplificata
---------------------------

A partire da settembre 2018, l'utente potrà visualizzare una nuova sezione denominata **Prezzi di valuta semplificata**. Microsoft sta semplificando il business di Azure Marketplace, consentendo prezzi più prevedibili e raccolte di clienti internazionali. Questa semplificazione si otterrà riducendo il numero delle valute in cui emettiamo le fatture dei clienti.

La nuova sezione avrà i prezzi espressi nelle nuove valute. Una volta eseguita la migrazione dei clienti verso queste nuove valute di pagamento, la sezione originale relativa ai prezzi verrà ritirata e rimarrà solo la sezione "Prezzi di valuta semplificata".

È possibile impostare nuovi prezzi per le aree in cui sta avvenendo la modifica della valuta di pagamento fino al 1 novembre 2018. Non sarà possibile aumentare il prezzo per le aree in cui la valuta di pagamento non subisce modifiche. Di seguito sono riportate le aree in cui avverrà la modifica alla valuta:

| Paese                  | Codice ISO2 | Valuta di fatturazione semplificata |
|--------------------------|-----------|-----------------------------|
| **Algeria**              | DZ        | USD                         |
| **Argentina**            | AR        | USD                         |
| **Bahrain**              | BH        | USD                         |
| **Bielorussia**              | BY        | USD                         |
| **Brasile**               | BR        | BRL                         |
| **Bulgaria**             | BG        | EUR                         |
| **Cile**                | CL        | USD                         |
| **Colombia**             | CO        | USD                         |
| **Costa Rica**           | CR        | USD                         |
| **Croazia**              | HR        | EUR                         |
| **Repubblica Ceca**       | CZ        | EUR                         |
| **Egitto**                | EG        | USD                         |
| **Guatemala**            | GT        | USD                         |
| **RAS di Hong Kong**            | HK        | USD                         |
| **Ungheria**              | HU        | EUR                         |
| **Islanda**              | IS        | EUR                         |
| **Indonesia**            | ID        | USD                         |
| **Israele**               | IL        | USD                         |
| **Giordania**               | JO        | USD                         |
| **Kazakistan**           | KZ        | USD                         |
| **Kenya**                | KE        | USD                         |
| **Kuwait**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Macedonia (FYRO)**     | MK        | USD                         |
| **Malesia**             | MY        | USD                         |
| **Messico**               | MX        | USD                         |
| **Montenegro**           | ME        | USD                         |
| **Marocco**              | MA        | USD                         |
| **Nigeria**              | NG        | USD                         |
| **Oman**                 | OM        | USD                         |
| **Pakistan**             | PK        | USD                         |
| **Paraguay**             | PY        | USD                         |
| **Perù**                 | PE        | USD                         |
| **Filippine**          | PH        | USD                         |
| **Polonia\***             | PL        | EUR                         |
| **Qatar**                | QA        | USD                         |
| **Romania**              | RO        | EUR                         |
| **Arabia Saudita**         | SA        | USD                         |
| **Serbia**               | RS        | USD                         |
| **Singapore**            | SG        | USD                         |
| **Sud Africa**         | ZA        | USD                         |
| **Thailandia**             | TH        | USD                         |
| **Trinidad e Tobago**  | TT        | USD                         |
| **Tunisia**              | TN        | USD                         |
| **Turchia**               | TR        | USD                         |
| **Ucraina**              | UA        | USD                         |
| **Emirati Arabi Uniti** | AE        | USD                         |
| **Uruguay**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Se si usano API per pubblicare l'offerta, potrebbe essere visualizzabile una nuova sezione all'interno dell'offerta denominata **JSON**. Ciò potrebbe essere annotato come `virtualMachinePricingV2` o `monthlyPricingV2`, in base al tipo di offerta.

Per domande in merito a questa modifica, contattare l'assistenza di Azure Marketplace.


<a name="compare-feature"></a>Funzionalità Confronta
---------------

Quando si apportano modifiche a un'offerta già pubblicata, è possibile usare la funzionalità *Confronta* per controllare le modifiche apportate. Per usare Confronta:

1.  In qualsiasi momento durante il processo di modifica, è possibile fare clic sul pulsante **Confronta** per confrontare l'offerta.

2.  Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.


<a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione
-----------------------------

Per visualizzare eventuali attività di pubblicazione cronologica, fare clic sulla scheda **Cronologia** nella barra di spostamento a sinistra del portale Cloud Partner. Qui sarà possibile visualizzare le azioni con timestamp che sono state eseguite durante il ciclo di vita delle offerte del Marketplace di Azure.
