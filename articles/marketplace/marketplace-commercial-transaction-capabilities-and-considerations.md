---
title: Funzionalità Transact di Microsoft Commercial Marketplace
description: Questo articolo descrive i prezzi, la fatturazione, la fatturazione e le considerazioni sui pagamenti per l'opzione Microsoft Commercial Marketplace Transact.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: d3e3afecc441a010ff28ef8970a1da7b1e99ffc6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160529"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Funzionalità Transact del Marketplace commerciale

In questo articolo vengono trattati i seguenti argomenti relativi al commercio per Microsoft Commercial Marketplace:

* Responsabilità delle transazioni per diverse opzioni di pubblicazione
* Panoramica generale sulle transazioni
* Modelli di fatturazione delle transazioni
* Requisiti delle transazioni

## <a name="transactions-by-publishing-option"></a>Transactions by publishing-opzione

Il server di pubblicazione o Microsoft è responsabile della gestione delle transazioni delle licenze software per le offerte nel Marketplace commerciale. L'opzione di pubblicazione scelta per l'offerta determinerà chi gestisce la transazione. Vedere [determinare l'opzione di pubblicazione](./determine-your-listing-type.md#choose-a-publishing-option) per la disponibilità e le spiegazioni di ogni opzione di pubblicazione.

### <a name="list-trial-and-byol-publishing-options"></a>Opzioni di pubblicazione list, Trial e BYOL

Gli editori con funzionalità di commercio esistenti possono scegliere le opzioni di pubblicazione list, Trial e Bring your own License (BYOL) per scopi promozionali e di acquisizione utente. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni di licenza software dell'editore e non è prevista alcuna tariffa per le transazioni. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

L'opzione di pubblicazione Transact sfrutta le funzionalità di Microsoft Commerce e offre un'esperienza end-to-end, da individuazione e valutazione all'acquisto e all'implementazione. Le offerte Transact vengono fatturate in base a una sottoscrizione Microsoft esistente o a una carta di credito, consentendo a Microsoft di ospitare transazioni del Marketplace cloud per conto dell'editore.

È possibile scegliere l'opzione Transact quando si crea una nuova offerta nel centro per i partner. Nella pagina **installazione offerta** , in **Dettagli installazione**, selezionare "Sì, desidero vendere tramite Microsoft e avere transazioni host Microsoft per conto dell'utente". Questa opzione verrà visualizzata solo se Transact è disponibile per il tipo di offerta.

## <a name="transact-general-overview"></a>Panoramica generale sulle transazioni

Quando si usa l'opzione di pubblicazione Transact, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta alla sottoscrizione di Azure del cliente. Quando si seleziona un modello di fatturazione e un tipo di offerta, l'autore deve considerare la fatturazione dei costi dell'infrastruttura e i costi di licenza software dell'editore.

L'opzione di pubblicazione Transact è attualmente supportata per i tipi di offerta seguenti: macchine virtuali, applicazioni Azure e app SaaS.

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

**Per le macchine virtuali e le applicazioni Azure**

Per le macchine virtuali e le applicazioni Azure, le tariffe di utilizzo dell'infrastruttura di Azure vengono fatturate alla sottoscrizione di Azure del cliente. I prezzi per l'utilizzo dell'infrastruttura vengono addebitati e presentati separatamente rispetto ai costi di licenza del provider software per la fattura del cliente.

**Per le app SaaS**

Per le app SaaS, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo,  Viene rappresentata come una tariffa fissa per il cliente. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione di transazione utilizzata, i costi di licenza software dell'editore possono essere presentati come segue:  

* Gratuito: non sono previsti addebiti per le licenze software.

* Bring Your Own License (BYOL): eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure Questo vale solo per le macchine virtuali e per le applicazioni Azure.

* Pagamento in base al consumo: i costi di licenza software vengono presentati come tariffa oraria, per singolo core (vCPU), basata sull'infrastruttura di Azure usata. Questo vale solo per le macchine virtuali e per le applicazioni Azure.

* Prezzi per le sottoscrizioni: le tariffe per le licenze software sono presentate come tariffe mensili o annuali, spese ricorrenti fatturate come tariffe forfettarie o per postazione. Si applica solo alle app SaaS e alle applicazioni Azure.

* Versione di valutazione gratuita del software: non sono previsti addebiti per le licenze software per 30 o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

Quando si pubblica un'offerta di transazione di sottoscrizione o con pagamento in base al consumo, Microsoft fornisce la tecnologia e i servizi per elaborare gli acquisti, i resi e i chargeback di licenza software. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e utilizzare il software di pubblicazione, in base alle condizioni del Marketplace commerciale di Microsoft e del contratto di licenza con l'utente finale dell'editore. Gli editori devono fornire il proprio contratto di licenza con l'utente finale o selezionare il [contratto standard](./standard-contract.md) durante la creazione dell'offerta.

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, l'editore può rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare i tipi di offerta e i modelli di fatturazione per monetizzare un'offerta, gli editori possono eseguire transazioni di un'offerta privata, completate con prezzi negoziati, specifici per l'offerta o configurazioni personalizzate. Le offerte private sono supportate da tutte e 3 le opzioni di pubblicazione delle transazioni.

Questa opzione consente un prezzo superiore o inferiore rispetto all'offerta pubblicamente disponibile. Le offerte private possono essere usate per applicare uno sconto o un supplemento a un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.


### <a name="examples"></a>Esempi

**Pagamento in base al consumo** 

* Se si abilita l'opzione con pagamento in base al consumo, la struttura dei costi è la seguente.

|Costo della licenza  | € 1,00 all'ora  |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 1,14 all'ora*       |

* In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 1,14 all'ora  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza|   € 0,80 all'ora     |
|Microsoft trattiene il 20% del costo della licenza  |  € 0,20 all'ora       |
|Microsoft trattiene il 100% del costo dell'utilizzo di Azure | € 0,14 all'ora |

**Bring your own License (BYOL)**

* Se si abilita l'opzione BYOL, la struttura dei costi è la seguente.

|Costo della licenza  | Il costo della licenza viene concordato e addebitato dall'utente  |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 0,14 all'ora*       |

* In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 0,14 all'ora  |
|---------|---------|
|Microsoft trattiene il costo dell'utilizzo di Azure    |   € 0,14 all'ora     |
|Microsoft trattiene lo 0% del costo della licenza   |  € 0,00 all'ora       |

**Sottoscrizione app SaaS**

Questa opzione deve essere configurata per la vendita tramite Microsoft e può essere addebitata a una tariffa fissa o a un utente su base mensile o annuale.
*    Se si Abilita l'opzione Vendi tramite Microsoft per un'offerta SaaS, si avrà la seguente struttura di costo.

|Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
|*Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese. Nota: l'editore deve calcolare i costi di infrastruttura sostenuti o trasmessi nei costi di licenza*  |

* In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
* Ai partner qualificati per la tariffa ridotta del servizio Marketplace verrà visualizzato un importo ridotto delle transazioni sulle offerte SaaS, dal 2019 maggio fino al giugno 2020. In questo scenario Microsoft fattura $100,00 per la licenza software e paga $90,00 all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \*Microsoft paga il 90% del costo della licenza per qualsiasi app SaaS qualificata   |   $ 80,00 al mese <br> \*$90,00 al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \*Microsoft mantiene il 10% del costo della licenza per le app SaaS qualificate.  |  $ 20,00 al mese <br> \*$10,00     |

* **Costo del servizio Marketplace ridotto:** Per determinati prodotti SaaS pubblicati sul Marketplace commerciale, Microsoft ridurrà il costo del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%.  Affinché il prodotto sia idoneo, è necessario che almeno uno dei prodotti sia designato da Microsoft come indirizzo di co-selling IP predisposto o co-selling IP con priorità. Per ricevere la tariffa per il servizio Marketplace ridotta per il mese, è necessario che l'idoneità soddisfi almeno cinque (5) giorni lavorativi prima della fine del mese di calendario precedente. Una tariffa ridotta del servizio Marketplace non si applica alle macchine virtuali, alle app gestite o ad altri prodotti resi disponibili tramite il Marketplace commerciale.  Questa tariffa per il servizio Marketplace ridotta sarà disponibile per le offerte qualificate, con i costi di licenza raccolti da Microsoft tra il 1 ° maggio 2019 e il 30 giugno 2020.  Dopo tale periodo di tempo, la tariffa del servizio Marketplace tornerà al valore normale.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**

Il server di pubblicazione può utilizzare il metodo di fatturazione preferito del cliente per recapitare i costi di licenza software consumo o sottoscrizione.

**Contratto Enterprise Agreement** 

Se il metodo di fatturazione preferito del cliente è la Enterprise Agreement Microsoft, i costi delle licenze software verranno fatturati usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile** 

I clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

Se ad esempio il cliente effettua un acquisto con la carta di credito:

|Descrizione    |    Date  |
|----------|----------|
|Periodo dell'ordine   | 15 ago 2018 - 30 ago 2018 |
|Fine periodo (mese)   | 30 ago 2018 |
|Data di fatturazione | 1 set 2018 |
|Data pagamento cliente | 1 set 2018 |
|Periodo deposito (solo carte di credito, 30 giorni) | 1 settembre 2018-30 settembre, 2018 |
|Inizio periodo riscossione | 1 set 2018 |
|Fine periodo riscossione (massimo 30 giorni) | 30 set 2018 |
|Data calcolo proventi (il giorno 15 di ogni mese) | 1 ott 2018 |
|Payout Date | 15 ott 2018 |

Se il cliente effettua un acquisto con un contratto Enterprise Agreement:

| Descrizione |    Date  |
|----------|----------|
|Periodo dell'ordine | 15 ago 2018 - 30 ago 2018 |
|Fine periodo (trimestre) | 30 set 2018 |
|Data di fatturazione | 15 ott 2018 |
|Periodo deposito (solo carte di credito, 30 giorni) | n/d |
|Inizio periodo riscossione | 15 ott 2018 |
|Fine periodo riscossione (massimo 90 giorni) | 15 gen 2019 |
|Data pagamento cliente | 30 dic 2018 |
|Data calcolo proventi (il giorno 15 di ogni mese) | 15 gen 2019 |
|Payout Date | 15 feb 2019 |

**Crediti gratuiti e impegno monetario** 

Alcuni clienti decidono di pagare in anticipo Azure con un impegno monetario nel contratto Enterprise Agreement o dispongono di crediti gratuiti da usare con Azure. Anche se questi crediti possono essere usati per pagare l'utilizzo di Azure, non possono essere usati per pagare costi di licenza del software dell'editore.

**Fatturazione e riscossioni** 

La fatturazione della licenza del software dell'editore viene visualizzata usando il metodo di fatturazione scelto dal cliente e segue la tempistica della fatturazione. Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

Quando vengono scelti modelli di determinazione prezzi con pagamento in base al consumo o per la sottoscrizione, Microsoft agisce in funzione di agente dell'editore ed è responsabile di ogni aspetto della fatturazione, del pagamento e della riscossione.

### <a name="publisher-payout-and-reporting"></a>Proventi dell'editore e creazione di report

* Eventuali costi di licenza software riscossi da Microsoft in funzione di agente sono soggetti a una commissione sulle transazioni del 20%, se non diversamente specificato, e vengono dedotti al momento del pagamento dell'editore.

* I clienti effettuano in genere gli acquisti tramite il contratto Enterprise Agreement o un contratto con pagamento in base al consumo abilitato per le carte di credito. Il tipo di contratto determina le date di fatturazione, riscossione e pagamento.

>[!NOTE]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili tramite la sezione Analytics del centro per i partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per ulteriori informazioni e criteri legali, vedere il [contratto di pubblicazione](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponibile nel centro per i partner).

Per ottenere assistenza sulle domande di fatturazione, contattare il [supporto tecnico per l'editore del Marketplace commerciale](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

* Per l'opzione di pubblicazione transazioni sono necessari un account Microsoft e informazioni finanziarie, indipendentemente dal modello di determinazione prezzi dell'offerta.
* Le informazioni finanziarie obbligatorie includono account di pagamento e profilo fiscale.

Per ulteriori informazioni sulla configurazione di questi account, vedere [gestire l'account del Marketplace commerciale nel centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account).

### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

L'opzione di pubblicazione delle transazioni è disponibile solo per l'uso con i tipi di offerte del marketplace seguenti:

**Macchina virtuale** 

Scegliere tra i modelli di determinazione prezzi Gratuito, Bring Your Own License o con pagamento in base al consumo e indicarli come SKU definiti a livello di offerta. Nella fattura di Azure del cliente, Microsoft presenta i costi delle licenze software dell'editore separatamente dalle tariffe di infrastruttura di Azure sottostanti. I costi dell'infrastruttura di Azure sono determinati dall'uso del software dell'editore.

**Applicazioni Azure: modello di soluzione o app gestita** 

È necessario effettuare il provisioning di una o più macchine virtuali e calcolare la somma dei prezzi delle macchine virtuali. Per le app gestite in un singolo piano, come modello di determinazione prezzi è possibile selezionare una sottoscrizione a tariffa fissa mensile invece dei prezzi delle macchine virtuali. In alcuni casi, le spese di utilizzo dell'infrastruttura di Azure vengono passate al cliente separatamente dai costi delle licenze software, ma con lo stesso rendiconto di fatturazione. Tuttavia, se si configura un'offerta di app gestita per gli addebiti per l'infrastruttura ISV, le risorse di Azure vengono fatturate all'editore e il cliente riceve una tariffa fissa che include il costo dell'infrastruttura, le licenze software e i servizi di gestione.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
* Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
