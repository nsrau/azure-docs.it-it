---
title: Funzionalità e considerazioni sulle transazioni ‎commerciali del marketplace | Azure
description: Questo articolo illustra le considerazioni relative a prezzi, fatturazione e proventi delle transazioni per un tipo di offerta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: dba2582b29d1fc81a39387f5297852245233b708
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731090"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Funzionalità e considerazioni sulle transazioni ‎commerciali di Azure Marketplace

Le opzioni di pubblicazione di Azure Marketplace consentono di mettere in contatto i provider di software e di servizi cloud con i clienti. Questo articolo illustra gli argomenti seguenti, relativi all'aspetto commerciale di Azure Marketplace:

* Opzioni di pubblicazione nel marketplace
* Panoramica generale sulle transazioni
* Modelli di fatturazione delle transazioni
* Requisiti delle transazioni

## <a name="marketplace-publishing-options"></a>Opzioni di pubblicazione nel marketplace

Le opzioni di pubblicazione seguenti sono disponibili per gli editori di Azure Marketplace.

### <a name="list--trial-publishing-options"></a>Opzioni di pubblicazione di inserzioni e valutazioni

In Azure Marketplace gli editori possono sfruttare le opzioni di pubblicazione di inserzioni e valutazioni per scopi promozionali e di acquisizione degli utenti. Con le opzioni di pubblicazione di inserzioni e valutazioni, Microsoft non partecipa direttamente alle transazioni delle licenze software dell'editore e non sono previsti costi di transazione. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente. 

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

Oltre alle opzioni di pubblicazione di inserzioni e valutazioni, per gli editori di Azure Marketplace è disponibile l'opzione di pubblicazione delle transazioni,   che sfrutta le funzionalità di commercializzazione di Microsoft disponibili a livello globale. Questa opzione consente a Microsoft di ospitare le transazioni del marketplace cloud per conto dell'editore.

## <a name="transact-general-overview"></a>Panoramica generale sulle transazioni

Quando si usa l'opzione di pubblicazione delle transazioni, Microsoft consente la vendita e la distribuzione di software di terze parti nella sottoscrizione di Azure del cliente. L'editore deve considerare la fatturazione dei costi di infrastruttura di Azure e i costi di licenza software propri dell'editore, quando si seleziona un modello di fatturazione e il tipo di offerta in Azure Marketplace. 

L'opzione di pubblicazione delle transazioni in Azure Marketplace è attualmente supportata per i tipi di offerte seguenti: macchine virtuali, applicazioni di Azure o app SaaS.

![[Gestione di offerte aziendali in Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

**Per macchine virtuali e applicazioni Azure**

Per le macchine virtuali e le applicazioni Azure, i costi di utilizzo dell'infrastruttura di Azure vengono fatturati alla sottoscrizione di Azure del cliente.  Nella fattura del cliente i costi di utilizzo dell'infrastruttura vengono addebitati e visualizzati separatamente dai costi di licenza del provider software.

**Per le app SaaS**

Per le app SaaS, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo,  presentata al cliente come tariffa mensile fissa. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner.  I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente.  Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software.  I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione usata per le transazioni, i costi di licenza software dell'editore possono essere presentati come indicato di seguito:  

* Gratuito: non sono previsti addebiti per le licenze software. 

* Bring Your Own License (BYOL): eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure (solo macchine virtuali e applicazioni Azure).

* Pagamento in base al consumo: i costi di licenza software vengono presentati come tariffa oraria, per singolo core (vCPU), basata sull'infrastruttura di Azure usata. Si applica solo alle macchine virtuali e alle applicazioni Azure.

* Prezzi delle sottoscrizioni (basati sul sito): i costi di licenza software sono presentati come tariffa mensile ricorrente.  Si applica solo alle app SaaS e alle applicazioni di Azure (app gestite).

* Versione di valutazione gratuita del software: non sono previsti addebiti per le licenze software per 30 o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

Quando si pubblica un'offerta di transazione di sottoscrizione o con pagamento in base al consumo, Microsoft fornisce la tecnologia e i servizi per elaborare gli acquisti, i resi e i chargeback di licenza software. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e usare il software di pubblicazione, imponendo il rispetto dei termini e delle condizioni sia di Azure Marketplace che del contratto di licenza con l'utente finale dell'editore. Vedere il portale Cloud Partner. Gli editori devono fornire il contratto di licenza con l'utente finale nell'offerta del marketplace.

Gli ordini elaborati tramite il marketplace vengono fatturati alla sottoscrizione di Azure del cliente in un'unica fattura, adottando quindi lo stesso metodo fatturazione dei costi di infrastruttura di Azure del cliente. I clienti possono usare il metodo di fatturazione e lo strumento di pagamento preferiti usati per la fatturazione della sottoscrizione di Azure.

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, l'editore può rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare tipi di offerte e modelli di fatturazione per monetizzare un'offerta, gli editori possono gestire una versione privata dell'offerta di soluzione, completa di prezzi negoziati specifici della trattiva e di configurazioni personalizzate, usando un'immagine personalizzata. Le offerte private sono supportate da tutte e 3 le opzioni di pubblicazione delle transazioni.

Questa opzione relativa al prezzo può essere superiore o inferiore al prezzo visualizzato pubblicamente.  Le offerte private possono essere usate per applicare uno sconto o un supplemento a un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.

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

**Sottoscrizione dell'app SaaS (vendita tramite Azure)**

Questa opzione deve essere configurata per la vendita tramite Microsoft e il prezzo può essere calcolato usando uno o più piani mensili a tariffa fissa definiti a livello di offerta.

* Se si abilita l'opzione di vendita tramite Azure, la struttura dei costi è la seguente.

|Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
|*Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese. Nota: l'editore deve calcolare i costi di infrastruttura sostenuti o trasmessi nei costi di licenza*  |

* In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza    |   $ 80,00 al mese     |
|Microsoft trattiene il 20% del costo della licenza   |  $ 20,00 al mese       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**

L'editore può usare il metodo di fatturazione preferito del cliente per far pervenire i costi di licenza software con pagamento in base al consumo o in abbonamento.

**Enterprise Agreement** 

Se il metodo di fatturazione preferito del cliente è il contratto Microsoft Enterprise Agreement, le tariffe della licenza software verranno fatturate usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile** 

I clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

Se ad esempio il cliente effettua un acquisto con la carta di credito:

|DESCRIZIONE    |    Data  |
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

| DESCRIZIONE |    Data  |
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
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione Informazioni dettagliate del portale Cloud Partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per altre informazioni e normative legali, vedere il [contratto per la pubblicazione](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponibile nel portale Cloud Partner).

In caso di domande sulla fatturazione, [creare una richiesta di assistenza](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) e scegliere Virtual Machines (Macchine virtuali) o Web Apps (App Web), ovvero le app SaaS, a seconda del tipo di offerta usato.

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

**Account Dev Center e Microsoft** 

* Per l'opzione di pubblicazione delle transazioni, sono necessari sia un account Dev Center che un account Microsoft, indipendentemente dal modello di determinazione prezzi dell'offerta.
* L'account Dev Center contiene tutte le informazioni finanziarie rilevanti necessarie a Microsoft per riscuotere i corrispettivi dal cliente per conto dell'editore e pagare l'editore.
* In entrambi gli account, è possibile usare la stessa dell'organizzazione o i dettagli di accesso Microsoft, Dev Center è un account separato dall'account del portale per Cloud Partner. Per usare l'opzione di pubblicazione delle transazioni, l'editore deve completare il processo di iscrizione all'account Dev Center, oltre a eseguire l'iscrizione per l'accesso al portale Cloud Partner.

*Per altre informazioni sulla configurazione di questi account, vedere [Diventare un editore del Marketplace cloud](https://docs.microsoft.com/azure/marketplace/become-publisher).*

### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

L'opzione di pubblicazione delle transazioni è disponibile solo per l'uso con i tipi di offerte del marketplace seguenti: 

**Macchina virtuale** 

Scegliere tra i modelli di determinazione prezzi Gratuito, Bring Your Own License o con pagamento in base al consumo e indicarli come SKU definiti a livello di offerta. Nella fattura di Azure del cliente Microsoft indica i costi della licenza del software dell'editore separatamente dai costi dell'infrastruttura di Azure sottostante. I costi dell'infrastruttura di Azure sono determinati dall'uso del software dell'editore.

**Applicazioni di Azure: modello di soluzione o app gestita** 

È necessario effettuare il provisioning di una o più macchine virtuali e calcolare la somma dei prezzi delle macchine virtuali. Per le app gestite in un singolo piano, come modello di determinazione prezzi è possibile selezionare una sottoscrizione a tariffa fissa mensile invece dei prezzi delle macchine virtuali. In entrambi i casi, le spese di utilizzo dell'infrastruttura di Azure vengono trasferite al cliente separatamente dalle spese di licenza software, ma nello stesso estratto conto.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
* Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
* Diventare un editore del Marketplace e accedere al [portale Cloud Partner](https://cloudpartner.azure.com) per creare e configurare l'offerta.
