---
title: Fatturazione del mercato commerciale Azure Marketplace
description: In questo articolo vengono trattati argomenti relativi al commercio per il mercato commerciale.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279743"
---
# <a name="commercial-marketplace-billing"></a>Fatturazione del mercato commerciale

In questo articolo vengono trattati argomenti relativi al commercio per il mercato commerciale:

- [Opzioni di pubblicazione nel marketplace](#marketplace-publishing-options)
- [Panoramica generale sulle transazioni](#transact-general-overview)
- [Modelli di fatturazione delle transazioni](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opzioni di pubblicazione nel marketplace

Il mercato commerciale offre diverse opzioni di pubblicazione per gli editori.

### <a name="list-and-trial-publishing-options"></a>Opzioni di pubblicazione di elenchi e versioni di valutazione

Gli editori possono sfruttare l'elenco, la versione di valutazione e portare le proprie opzioni di pubblicazione di licenza (BYOL) per scopi promozionali e di acquisizione degli utenti. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni di licenza software dell'editore e non vi è alcuna commissione di transazione associata. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

Oltre alle opzioni di pubblicazione di elenchi e prove, l'opzione di pubblicazione delle transazioni è disponibile per gli editori. Questa opzione sfrutta le funzionalità di commercio disponibili a livello globale di Microsoft e consente a Microsoft di ospitare transazioni del marketplace cloud per conto dell'editore.

## <a name="transact-general-overview"></a>Panoramica generale sulle transazioni

Quando si usa l'opzione di pubblicazione delle transazioni, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta alla sottoscrizione di Azure del cliente. Quando si seleziona un modello di fatturazione e un tipo di offerta, l'editore deve considerare la fatturazione delle tariffe dell'infrastruttura e delle tariffe di licenza del software dell'editore.

L'opzione Pubblicazione transact è attualmente supportata per i tipi di offerta seguenti: macchine virtuali, applicazioni di Azure e applicazioni SaaS.

![Transazione in Azure MarketplaceTransact in Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

#### <a name="for-virtual-machines-and-azure-applications"></a>Per le macchine virtuali e le applicazioni di AzureFor virtual machines and Azure applications

Per le macchine virtuali e le applicazioni di Azure, le tariffe di utilizzo dell'infrastruttura di Azure vengono applicate alla sottoscrizione di Azure del cliente. Le tariffe di utilizzo dell'infrastruttura sono valutate e presentate separatamente dalle spese di licenza del fornitore di software sulla fattura del cliente.

#### <a name="for-saas-apps"></a>Per le app SaaS

Per le app SaaS, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo, È rappresentato come una tariffa fissa per il cliente. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione di transazione utilizzata, i costi di licenza software dell'editore possono essere presentati come segue:

- *Gratuito:* Nessun costo per le licenze software.
- *Portare la propria licenza (BYOL):* tutti gli addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure (solo macchine virtuali e applicazioni Azure).
- *Conttrazione:* i costi di licenza software vengono presentati come una tariffa dei prezzi all'ora per core (vCPU) in base all'infrastruttura di Azure usata. Questo modello si applica solo alle macchine virtuali e alle applicazioni di Azure.This model only applies to virtual machines and Azure applications.
- *Prezzi in abbonamento*: Le tariffe di licenza software sono presentate come una tariffa mensile o annuale, ricorrente fatturata come tariffa forfettaria o per posto. Questo modello si applica solo alle app SaaS e alle applicazioni di Azure - App gestite.
- *Versione di prova del software gratuito*: Nessun costo per le licenze software per 30 o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

Quando pubblichi un'offerta di transazione con pagamento in base al cliente o di sottoscrizione, Microsoft fornisce la tecnologia e i servizi per elaborare gli acquisti, i resi e gli addebiti delle licenze software. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e utilizzare il software editore, in base ai termini e alle condizioni del Marketplace commerciale di Microsoft e del contratto di licenza per l'utente finale dell'editore. Gli editori devono fornire il contratto di licenza per l'utente finale o selezionare il [contratto standard](https://docs.microsoft.com/azure/marketplace/standard-contract) durante la creazione dell'offerta.

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, l'editore può rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a utilizzare i tipi di offerta e i modelli di fatturazione per monetizzare un'offerta, gli editori possono effettuare la transazione di un'offerta privata, completa di prezzi negoziati e specifici dell'affare, o configurazioni personalizzate. Le offerte private sono supportate da tutte e 3 le opzioni di pubblicazione delle transazioni.

Questa opzione consente prezzi superiori o inferiori rispetto all'offerta disponibile pubblicamente. Le offerte private possono essere utilizzate per lo sconto o aggiungere un premio per un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.

### <a name="examples"></a>Esempi

#### <a name="pay-as-you-go"></a>Pagamento in base al consumo

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

### <a name="bring-your-own-license-byol"></a>Porta la tua licenza (BYOL)

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

### <a name="saas-app-subscription"></a>Abbonamento all'app SaaS

Questa opzione deve essere configurata per la vendita tramite Microsoft e può essere valutata a tariffa forfettaria o per utente su base mensile o annuale.

Se si attiva l'opzione Vendi tramite Microsoft per un'offerta SaaS, si dispone della seguente struttura dei costi.

|Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
|*Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese. Nota: l'editore deve calcolare i costi di infrastruttura sostenuti o trasmessi nei costi di licenza*  |

* In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
* I partner che si sono qualificati per la Tassa di servizio del Marketplace ridotto vedranno una commissione di transazione ridotta sulle offerte SaaS da maggio 2019 a giugno 2020. In questo scenario, Microsoft fattura 100,00 USD per la licenza software e paga 90,00 USD all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \*Microsoft ti paga il 90% del costo della licenza per tutte le app SaaS qualificate   |   $ 80,00 al mese <br> \*90,00 USD al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \*Microsoft mantiene il 10% del costo della licenza per tutte le app SaaS qualificate.  |  $ 20,00 al mese <br> \*10,00 USD     |

**Tariffa di servizio Marketplace ridotta:** Per alcuni prodotti SaaS che pubblichi sul nostro Marketplace commerciale, Microsoft ridurrà la commissione per il servizio Marketplace dal 20% (come descritto nel Contratto Microsoft per gli editori) al 10%.  Affinché il Prodotto sia idoneo, almeno uno dei prodotti deve essere designato da Microsoft come pronto per il co-selling IP o con priorità di co-selling IP. Per ricevere questa commissione di servizio Marketplace ridotta per il mese, l'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine del mese di calendario precedente. La tariffa del servizio Marketplace ridotta non si applica alle macchine virtuali, alle app gestite o a qualsiasi altro prodotto reso disponibile tramite il Marketplace commerciale.  Questa tariffa per il servizio Marketplace ridotto sarà disponibile per le offerte qualificate, con i costi di licenza raccolti da Microsoft tra il 1 maggio 2019 e il 30 giugno 2020.  Dopo tale periodo, la commissione di servizio Marketplace tornerà all'importo normale.
