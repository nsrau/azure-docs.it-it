---
title: Funzionalità e considerazioni sulle transazioni ‎commerciali del marketplace | Azure
description: Questo articolo illustra le considerazioni relative a prezzi, fatturazione e proventi delle transazioni per un tipo di offerta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: d266b314f19979578b7e7b8de4e7a7090200c9d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445445"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Considerazioni e le funzionalità di transazione commerciale marketplace

Questo articolo illustra gli argomenti seguenti relativi di e-commerce per marketplace commerciale

* Opzioni di pubblicazione nel marketplace
* Panoramica generale sulle transazioni
* Modelli di fatturazione delle transazioni
* Requisiti delle transazioni

## <a name="marketplace-publishing-options"></a>Opzioni di pubblicazione nel marketplace

Le opzioni di pubblicazione seguenti sono disponibili per gli editori di marketplace commerciale.

### <a name="list--trial-publishing-options"></a>Opzioni di pubblicazione di inserzioni e valutazioni

Gli editori possono sfruttare l'elenco, valutazione e opzioni di pubblicazione per BYOL promozionali e a scopo di acquisizione di utente. Con queste opzioni, Microsoft non fanno parte direttamente nelle transazioni di licenza software dell'editore e non sono previste commissioni transazione associata. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente. 

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

Oltre all'elenco e le opzioni di pubblicazione valutazione, l'opzione di pubblicazione transact è disponibile per i server di pubblicazione. Questo consente di sfruttare le funzionalità disponibili a livello globale e-commerce di Microsoft e consente a Microsoft per le transazioni di marketplace cloud host per conto dell'editore.

## <a name="transact-general-overview"></a>Panoramica generale sulle transazioni

Quando si usa l'opzione di pubblicazione transazione, Microsoft ti offre la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta di sottoscrizione di Azure del cliente. Il server di pubblicazione deve prendere in considerazione la fatturazione dei costi di infrastruttura e dell'editore del software costi di licenza, quando si seleziona una fatturazione del modello e tipo di offerta.

L'opzione di pubblicazione Transact è attualmente supportata per i seguenti tipi di offerta: Le macchine virtuali, le applicazioni di Azure e App SaaS.


![[Gestione di offerte aziendali in Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

**Per macchine virtuali e applicazioni Azure**

Per le macchine virtuali e le applicazioni Azure, i costi di utilizzo dell'infrastruttura di Azure vengono fatturati alla sottoscrizione di Azure del cliente.  Nella fattura del cliente i costi di utilizzo dell'infrastruttura vengono addebitati e visualizzati separatamente dai costi di licenza del provider software.

**Per le app SaaS**

Per le app SaaS, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo,  Essa viene rappresentata come una tariffa fissa al cliente. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner.  I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente.  Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software.  I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione usata per le transazioni, i costi di licenza software dell'editore possono essere presentati come indicato di seguito:  

* Gratuito: non sono previsti addebiti per le licenze software. 

* Bring Your Own License (BYOL): eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure (solo macchine virtuali e applicazioni Azure).

* Pagamento in base al consumo: i costi di licenza software vengono presentati come tariffa oraria, per singolo core (vCPU), basata sull'infrastruttura di Azure usata. Si applica solo alle macchine virtuali e alle applicazioni Azure.

* • Prezzi delle sottoscrizioni: Costi delle licenze software vengono presentati come una mensile o annuale, ricorrente tariffa fatturata come una tariffa fissa o per postazione. Si applica solo alle app SaaS e alle applicazioni di Azure (app gestite).

* Versione di valutazione gratuita del software: non sono previsti addebiti per le licenze software per 30 o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

WPay come a consumo e prezzi delle sottoscrizioni quando si pubblica un'offerta di transazione con pagamento a consumo o sottoscrizione, Microsoft offre la tecnologia e servizi per l'elaborazione di licenza software per gli acquisti, restituisce e chargeback. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordine, la licenza e software per server di pubblicazione di uso, sottopone a termini e condizioni di mercato commerciale di Microsoft e contratto di licenza dell'utente finale dell'editore. I server di pubblicazione deve specificare il contratto di licenza dell'utente finale o selezionare il [contratto Standard](https://docs.microsoft.com/azure/marketplace/standard-contract) durante la creazione dell'offerta.


### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, l'editore può rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare i tipi di offerta e modelli di fatturazione monetizzare un'offerta, i server di pubblicazione possono eseguire transazioni un'offerta privata, usufruendo negoziato, quantità specifiche sui prezzi o le configurazioni personalizzate. Le offerte private sono supportate da tutte e 3 le opzioni di pubblicazione delle transazioni.

Questa opzione consente di prezzi è superiore o inferiore rispetto all'offerta disponibile pubblicamente. Le offerte private possono essere usate per applicare uno sconto o un supplemento a un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.


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

**Bring Your Own License (BYOL)**

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

**Sottoscrizione di App SaaS**

Questa opzione deve essere configurata per vendere tramite Microsoft e può essere applicata a una tariffa fissa o per ogni utente su base mensile o annua.
• Se si abilita l'origine della vendita tramite l'opzione di Microsoft per un'offerta SaaS, si avrà la seguente struttura dei costi.

|Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
|*Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese. Nota: l'editore deve calcolare i costi di infrastruttura sostenuti o trasmessi nei costi di licenza*  |

* In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
* I partner che sono qualificati per la tariffa del servizio Marketplace ridotto visualizzeranno che una tariffa ridotta su SaaS offre da maggio 2019 fino a giugno 2020. In questo scenario Microsoft addebita $100.00 la licenza del software e paga out $90,00 al server di pubblicazione.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \* Microsoft paga il 90% del costo della licenza per le app SaaS completo   |   $ 80,00 al mese <br> \* $90,00 al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \* Microsoft trattiene il 10% del costo della licenza per le app SaaS completo.  |  $ 20,00 al mese <br> \* $10.00     |

* **Tariffa del servizio Marketplace ridotta:** Per determinati prodotti SaaS che si pubblica nel Marketplace commerciale, Microsoft ridurrà la tariffa del servizio Marketplace dal 20%, come descritto nel contratto per la pubblicazione Microsoft, al 10%.  Affinché il prodotto qualificare, almeno uno dei tuoi prodotti deve essere designato da Microsoft come IP CO-Selling o la priorità di CO-Selling di IP. Per ricevere questa tariffa del servizio Marketplace ridotto per il mese, idoneità deve soddisfare almeno cinque (5) giorni prima della fine del mese del calendario. Riduzione del servizio Marketplace tariffa non verrà applicate alle macchine virtuali, le app gestite o qualsiasi altro prodotto reso disponibile tramite Marketplace commerciale.  Questa tariffa ridotta del servizio Marketplace saranno disponibile per le offerte completo, con costi di licenza raccolti da Microsoft tra il 1 maggio 2019 e il 30 giugno 2020.  Dopo tale periodo, la tariffa del servizio Marketplace restituirà al relativo al normale.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**

L'editore può usare il metodo di fatturazione preferito del cliente per far pervenire i costi di licenza software con pagamento in base al consumo o in abbonamento.

**Enterprise Agreement** 

Se il metodo di fatturazione preferito del cliente è il contratto Microsoft Enterprise Agreement, le tariffe della licenza software verranno fatturate usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile** 

I clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

Se ad esempio il cliente effettua un acquisto con la carta di credito:

|Descrizione    |    Date  |
|----------|----------|
|Periodo dell'ordine   | 15 ago 2018 - 30 ago 2018 |
|Fine periodo (mese)   | 30 ago 2018 |
|Data di fatturazione | 1 set 2018 |
|Data pagamento cliente | 1 set 2018 |
|Periodo deposito (solo carte di credito, 30 giorni) | 1 set 2018 - 30 set 2018 |
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
>Tutti i report e informazioni dettagliate per l'opzione di pubblicazione transazione sono disponibili tramite la sezione Insights della sezione portale per Cloud Partner o Analitica del centro per i Partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per altre informazioni e normative legali, vedere il [contratto per la pubblicazione](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponibile nel portale Cloud Partner).

Per ottenere assistenza su domande sulla fatturazione, contattare [supporto di server di pubblicazione di marketplace commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

- Un account Microsoft e le informazioni finanziarie sono obbligatori per opzione, indipendentemente dal fatto che l'offerta di pubblicazione transazione del modello di prezzi.
- Finanziari obbligatori includono profilo account e imposte relative ai proventi.

Per altre informazioni sull'impostazione di questi account, vedere [gestire Your Account Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

L'opzione di pubblicazione delle transazioni è disponibile solo per l'uso con i tipi di offerte del marketplace seguenti: 

**Macchina virtuale** 

Scegliere tra i modelli di determinazione prezzi Gratuito, Bring Your Own License o con pagamento in base al consumo e indicarli come SKU definiti a livello di offerta. Nella fattura di Azure del cliente Microsoft indica i costi della licenza del software dell'editore separatamente dai costi dell'infrastruttura di Azure sottostante. I costi dell'infrastruttura di Azure sono determinati dall'uso del software dell'editore.

**Applicazioni di Azure: modello di soluzione o app gestita** 

È necessario effettuare il provisioning di una o più macchine virtuali e calcolare la somma dei prezzi delle macchine virtuali. Per le app gestite in un singolo piano, come modello di determinazione prezzi è possibile selezionare una sottoscrizione a tariffa fissa mensile invece dei prezzi delle macchine virtuali. In alcuni casi, spese di utilizzo dell'infrastruttura di Azure vengono passati al cliente separatamente da spese di licenza software, ma nella stessa istruzione fatturazione. Tuttavia, se si configura un'App gestita che offre alle spese di infrastruttura ISV, vengono fatturate le risorse di Azure al server di pubblicazione e il cliente riceve una tariffa fissa che include i costi di infrastruttura, le licenze software e servizi di gestione.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
* Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
