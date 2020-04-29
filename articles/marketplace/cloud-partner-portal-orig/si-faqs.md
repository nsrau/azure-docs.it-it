---
title: Domande frequenti su Informazioni dettagliate del venditore
description: Domande frequenti sulla funzionalità Informazioni dettagliate del venditore del portale Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285385"
---
<a name="seller-insights-faq"></a>Domande frequenti su Informazioni dettagliate del venditore
===================

Questo articolo fornisce una guida alle procedure comuni per gli utenti e alle domande relative a Informazioni sul venditore.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Trovare le definizioni per i valori nel file transazione scaricato
------------------------------------------------------------------

Le definizioni dei valori della metrica all'interno del file della transazione sono disponibili nell'articolo [Definizioni Informazioni dettagliate del venditore](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Vedere i dettagli del cliente all'interno delle transazioni per le quali è stato effettuato un pagamento
-------------------------------------------------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **stato di pagamento**e applicare il filtro in modo da visualizzare solo il valore "pagato". Verranno visualizzate le seguenti colonne contenenti i dettagli del cliente: **Nome società**, **E-mail cliente**, **Paese cliente**, **Stato cliente** e **CAP cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcolare i crediti aperti
-------------------------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **stato di pagamento**e applicare il filtro in modo da visualizzare solo il valore "pagamento imminente" e "non pronto per il versamento". Quindi sommare i valori della colonna con denominata **Importo proventi**.


<a name="calculate-revenue-by-customer-usage-period"></a>Calcolare i ricavi per periodo di utilizzo del cliente
------------------------------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **stato della transazione**e filtrare il valore "a pagamento".   Per ogni transazione elencata, la colonna la denominazione **Importo proventi** rappresenta l'importo che è stato pagato.  Per stimare il periodo di utilizzo associato alla transazione, usare la colonna **Data di addebito**, ovvero un'approssimazione molto vicina all'ultimo giorno di utilizzo per il periodo a cui viene applicata la transazione.


<a name="calculate-your-bad-debt"></a>Calcolare i crediti inesigibili
---------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **stato finale raccolta**e applicare il filtro per visualizzare solo il valore "write off". Quindi sommare i valori della colonna con denominata **Importo proventi**.


<a name="view-payout-or-customer-contact-information"></a>Visualizzare le informazioni sui proventi o le informazioni di contatto del cliente
-------------------------------------------

Accedere come utente con il ruolo "proprietario" e non con il ruolo "collaboratore". Solo il ruolo proprietario può visualizzare le informazioni relative ai proventi e ai clienti. È possibile reperire informazioni sui ruoli utente nell'articolo [Gestire gli utenti](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcolare i proventi anticipati
----------------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **tipo di transazione**e applicare il filtro in modo da visualizzare solo il valore "addebito". Individuare quindi la colonna con **lo stato finale della raccolta**e applicare il filtro in modo da visualizzare solo il valore "in corso". Infine, sommare la colonna **Importo proventi** per calcolare tutti gli anticipi che sono stati pagati prima della raccolta dal cliente.


<a name="calculate-customer-refunds"></a>Calcolare i rimborsi dei clienti
--------------------------

Dopo aver scaricato le transazioni dal modulo di pagamento, individuare la colonna con etichetta **stato finale raccolta**e applicare il filtro per visualizzare solo il valore "rimborso". Sommare la colonna **Importo dell'addebito** per calcolare tutti i rimborsi elaborati per i clienti.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificare quali transazioni hanno coinvolto un partner di canale Microsoft
----------------------------------------------------------------

Tutte le transazioni nella colonna **tipo di licenza di Azure** filtrate per visualizzare i valori "Enterprise through Reseller" e "Cloud Solution Provider" coinvolgono un Microsoft Channel partner. Per altre informazioni sul partner, è possibile reperire il **Nome del rivenditore** e l'indirizzo di **Posta elettronica del rivenditore** nella sezione dedicata al download del modulo dei proventi e del modulo clienti.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificare la versione di utilizzo di prova e la versione delle conversioni di prova
------------------------------------------

I download dei moduli Ordine, Uso e Proventi contengono ora una **Data di fine prova** per aiutare l'utente a riconoscere, dove applicabile, quando termina il periodo di prova per un ordine specifico. Per visualizzare l'utilizzo della versione di valutazione e gli ordini, individuare la colonna **tipo di fatturazione SKU** nei download e applicare il filtro in modo da visualizzare solo il valore "versione di valutazione". Per visualizzare le conversioni di valutazione, individuare la colonna della **Data di fine della versione di valutazione** nei download e applicare il filtro per visualizzare solo gli ordini quando la data di fine della **versione** di valutazione è passata alla data odierna e la colonna di **annullamento della data** è vuota o successiva alla data di fine della **versione di valutazione**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando vengono calcolati i proventi mensili
------------------------------------

I proventi vengono emessi entro il 15 di ogni mese per tutti gli importi pronti per il pagamento entro l'ultimo giorno di calendario del mese precedente. Il terzo giorno del mese, Microsoft calcolerà il numero di versamenti del mese precedente e aggiornerà tutte le transazioni addebitate applicabili nel download con "versamento imminente" nella colonna **stato dei pagamenti** . Tali transazioni rimarranno in tale stato fino a quando la richiesta di pagamento non viene inviata al conto bancario, a quel punto il loro **stato** di pagamento verrà aggiornato a "pagato" e la "data di pagamento" verrà aggiornata per indicare la data di invio della richiesta di pagamento alla banca.


<a name="calculate-customer-acquisition-and-loss"></a>Calcolare acquisizione e perdita dei clienti
---------------------------------------

È possibile visualizzare la data in cui il cliente ha acquistato per la prima volta una delle offerte, individuando la colonna **Data di acquisizione** nella sezione dedicata al download del cliente. Analogamente, è possibile vedere la data dopo la quale non è stata più visualizzata alcuna offerta pubblicata dall'utente, individuando la colonna **Data di perdita** nella sezione dedicata al download del cliente.


<a name="finding-more-help"></a>Altre informazioni di supporto
-----------------

- [Definizioni di Informazioni dettagliate del venditore](./si-insights-definitions-v4.md) - Trovare le definizioni per metrica e dati

- [Introduzione a Informazioni dettagliate del venditore](./si-getting-started.md):-Introduzione alla funzionalità Informazioni dettagliate del venditore.

