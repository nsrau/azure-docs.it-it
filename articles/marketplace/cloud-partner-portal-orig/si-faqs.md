---
title: Domande frequenti su Informazioni dettagliate del venditore | Microsoft Docs
description: Domande frequenti sulla funzionalità Informazioni dettagliate del venditore del portale Cloud Partner.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362374"
---
<a name="seller-insights-faq"></a>Domande frequenti su Informazioni dettagliate del venditore
===================

Questo articolo fornisce una guida alle procedure comuni per gli utenti e alle domande relative a Informazioni sul venditore.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Trovare le definizioni per i valori nel file transazione scaricato
------------------------------------------------------------------

Le definizioni dei valori della metrica all'interno del file della transazione sono disponibili nell'articolo [Definizioni Informazioni dettagliate del venditore](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Vedere i dettagli del cliente all'interno delle transazioni per le quali è stato effettuato un pagamento
-------------------------------------------------------------

Dopo aver scaricato le transazioni dal modulo relativo ai proventi, individuare la colonna denominata **Stato dei proventi**, dopodiché applicare il filtro per visualizzare solo il valore "Pagato". Le colonne seguenti verranno visualizzati contenente i dettagli del cliente: **Nome società**, **posta elettronica dei clienti**, **paese del cliente**, **stato cliente**, e **codice postale del cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcolare i crediti aperti
-------------------------------------

Dopo aver scaricato le transazioni dal modulo relativo ai proventi, individuare la colonna denominata **Stato proventi**, dopodiché applicare il filtro per visualizzare solo i valori "Prossimi proventi" e "Non pronto per i proventi". Quindi sommare i valori della colonna con denominata **Importo proventi**.


<a name="calculate-revenue-by-customer-usage-period"></a>Calcolare i ricavi per periodo di utilizzo del cliente
------------------------------------------

Dopo aver scaricato le transazioni dal modulo relative ai proventi, individuare la colonna con etichettata **lo stato della transazione**e filtrare il valore "Paid".   Per ogni transazione elencato, la colonna con etichetta **proventi quantità (PC)** rappresenta l'importo è stato pagato.  Per stimare il periodo di utilizzo associato alla transazione, usare la colonna **data di addebito**, ovvero un'approssimazione molto vicina all'ultimo giorno di utilizzo per il periodo a cui viene applicata la transazione.


<a name="calculate-your-bad-debt"></a>Calcolare i crediti inesigibili
---------------------

Dopo aver scaricato le transazioni dal modulo relativo ai proventi, individuare la colonna denominata **Stato di raccolta finale**, dopodiché applicare il filtro per visualizzare solo il valore "Annullare". Quindi sommare i valori della colonna con denominata **Importo proventi**.


<a name="view-payout-or-customer-contact-information"></a>Visualizzare le informazioni sui proventi o le informazioni di contatto del cliente
-------------------------------------------

Accedere come utente con ruolo "proprietario" e non con ruolo "collaboratore". Solo il ruolo proprietario può visualizzare le informazioni relative ai proventi e ai clienti. È possibile reperire informazioni sui ruoli utente nell'articolo [Gestire gli utenti](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcolare i proventi anticipati
----------------------------

Dopo aver scaricato le transazioni dal modulo relativo ai proventi, individuare la colonna denominata **Stato proventi**, dopodiché applicare il filtro per visualizzare solo il valore "Addebito". Successivamente, individuare la colonna denominata **Stato di raccolta finale**, dopodiché applicare il filtro per visualizzare solo il valore "In corso". Infine, sommare la colonna **Importo proventi** per calcolare tutti gli anticipi che sono stati pagati prima della raccolta dal cliente.


<a name="calculate-customer-refunds"></a>Calcolare i rimborsi dei clienti
--------------------------

Dopo aver scaricato le transazioni dal modulo relativo ai proventi, individuare la colonna denominata **Stato di raccolta finale**, dopodiché applicare il filtro per visualizzare solo il valore "Rimborso". Sommare la colonna **Importo dell'addebito** per calcolare tutti i rimborsi elaborati per i clienti.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificare quali transazioni hanno coinvolto un partner di canale Microsoft
----------------------------------------------------------------

Tutte le transazioni nella colonna **Tipo di licenza di Azure** che vengono filtrate per visualizzare i valori "Enterprise tramite rivenditori" e "Provider di soluzioni cloud" prevedono un partner di canale Microsoft. Per altre informazioni sul partner, è possibile reperire il **Nome del rivenditore** e l'indirizzo di **Posta elettronica del rivenditore** nella sezione dedicata al download del modulo dei proventi e del modulo clienti.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificare la versione di utilizzo di prova e la versione delle conversioni di prova
------------------------------------------

I download dei moduli Ordine, Uso e Proventi contengono ora una **Data di fine prova** per aiutare l'utente a riconoscere, dove applicabile, quando termina il periodo di prova per un ordine specifico. Per visualizzare la versione di utilizzo di prova e gli ordini, individuare la colonna **Tipo di SKU di fatturazione** nella sezione dedicata al download e applicare il filtro per visualizzare solo il valore "Prova". Per visualizzare le versioni delle conversioni di prova, individuare la colonna **Data di fine prova** nella sezione dedicata al download e applicare il filtro per visualizzare gli ordini solo quando la colonna **Data di fine prova** è passata alla data odierna e la colonna **Data di annullamento** è vuota o successiva alla **Data di fine prova**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando vengono calcolati i proventi mensili
------------------------------------

I proventi vengono emessi entro il 15 di ogni mese per tutti gli importi pronti per il pagamento entro l'ultimo giorno di calendario del mese precedente. Il terzo giorno del mese, Microsoft calcolerà l'importo del pagamento del mese precedente e aggiornerà tutte le transazioni di addebito applicabili nel download con "Prossimi proventi" nella colonna **Stato proventi**. Le transazioni rimangono in tale stato fino a quando non viene inviata la richiesta di pagamento al conto corrente bancario, momento in cui lo **Stato proventi** viene aggiornato a "Pagato" e "Data proventi" viene aggiornata per visualizzare la data in cui è stata inviata la richiesta di pagamento alla banca.


<a name="calculate-customer-acquisition-and-loss"></a>Calcolare acquisizione e perdita dei clienti
---------------------------------------

È possibile visualizzare la data in cui il cliente ha acquistato per la prima volta una delle offerte, individuando la colonna **Data di acquisizione** nella sezione dedicata al download del cliente. Analogamente, è possibile vedere la data dopo la quale non è stata più visualizzata alcuna offerta pubblicata dall'utente, individuando la colonna **Data di perdita** nella sezione dedicata al download del cliente.


<a name="finding-more-help"></a>Altre informazioni di supporto
-----------------

- [Definizioni di Informazioni dettagliate del venditore](./si-insights-definitions-v4.md) - Trovare le definizioni per metrica e dati

- [Introduzione a Informazioni dettagliate del venditore](./si-getting-started.md):-Introduzione alla funzionalità Informazioni dettagliate del venditore.

