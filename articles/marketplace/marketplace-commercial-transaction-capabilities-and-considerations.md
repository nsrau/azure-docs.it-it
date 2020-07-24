---
title: Funzionalità di transazione del marketplace commerciale di Microsoft
description: Questo articolo illustra le considerazioni relative a prezzi, fatturazione e proventi delle transazioni per l'opzione di transazione del marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 29fa4126d8d0b4f3419c729ee3a73ae315b13122
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001367"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Funzionalità di transazione del marketplace commerciale

## <a name="transactions-by-publishing-option"></a>Transazioni per opzione di pubblicazione

L'editore o Microsoft è responsabile della gestione delle transazioni delle licenze software per le offerte nel marketplace commerciale. L'opzione di pubblicazione scelta per l'offerta determinerà chi gestisce la transazione. Vedere [Determinare l'opzione di pubblicazione](./determine-your-listing-type.md#choose-a-publishing-option) per la disponibilità e le spiegazioni di ogni opzione di pubblicazione.

### <a name="list-trial-and-byol-publishing-options"></a>Opzioni di pubblicazione di inserzioni, valutazioni e BYOL

Gli editori che dispongono di funzionalità commerciali possono scegliere le opzioni di pubblicazione di inserzioni, valutazioni e Bring your own License (BYOL) per scopi promozionali e di acquisizione di utenti. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni delle licenze software dell'editore e non sono previsti costi di transazione. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

L'opzione di pubblicazione delle transazioni sfrutta le funzionalità commerciali di Microsoft e offre un'esperienza completa, dall'individuazione e valutazione all'acquisto e implementazione. Le offerte relative alle transazioni vengono fatturate in base a una sottoscrizione Microsoft esistente o a una carta di credito, consentendo a Microsoft di ospitare transazioni del marketplace cloud per conto dell'editore.

È possibile scegliere l'opzione relativa alle transazioni quando si crea una nuova offerta nel Centro per i partner. Nella pagina **Offer setup** (Configurazione offerta), in **Setup details** (Dettagli di configurazione), selezionare "Sì, desidero vendere tramite Microsoft e avere transazioni host Microsoft personali". Questa opzione verrà visualizzata solo se la transazione è disponibile per il tipo di offerta.

## <a name="transact-overview"></a>Panoramica sulle transazioni

Quando si usa l'opzione di pubblicazione delle transazioni, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta nella sottoscrizione di Azure del cliente. L'editore deve considerare la fatturazione dei costi di infrastruttura e i costi di licenza software quando seleziona un modello di fatturazione e un tipo di offerta.

L'opzione di pubblicazione delle transazioni è attualmente supportata per i tipi di offerte seguenti:

- Macchine virtuali
- Applicazioni di Azure
- Applicazioni SaaS

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

Per le **macchine virtuali** e **le applicazioni Azure**, le tariffe di utilizzo dell'infrastruttura di Azure vengono fatturate alla sottoscrizione di Azure del cliente. Nella fattura del cliente i costi di utilizzo dell'infrastruttura vengono addebitati e visualizzati separatamente dai costi di licenza del provider software.

Per le **app SaaS**, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo,  presentata al cliente come tariffa mensile fissa. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione usata per le transazioni, i costi di licenza software sono riportati di seguito.

- **Gratuito**: non sono previsti addebiti per le licenze software.
- **Bring Your Own License (BYOL)** : eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure Questo vale solo per le macchine virtuali e per le applicazioni Azure.
- **Pagamento in base al consumo**: i costi di licenza software vengono presentati come tariffa oraria, per singolo core (vCPU), basata sull'infrastruttura di Azure usata. Questo vale solo per le macchine virtuali e per le applicazioni Azure.
- **Prezzi per le sottoscrizioni**: le tariffe per le licenze software sono presentate come tariffe mensili o annuali, costi ricorrenti fatturati come tariffe forfettarie o per postazione. Questo vale per le app SaaS (mensili o annuali) e per le app gestite da Azure (mensilmente).
- **Versione di valutazione gratuita del software**: non sono previsti addebiti per le licenze software per 30 o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

Quando si pubblica un'offerta di transazione di sottoscrizione o con pagamento in base al consumo, Microsoft fornisce la tecnologia e i servizi per elaborare gli acquisti, i resi e i chargeback di licenza software. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e usare il proprio software, imponendo il rispetto dei termini e delle condizioni sia del marketplace commerciale di Microsoft che del contratto di licenza con l'utente finale. Quando si crea l'offerta, è necessario fornire il proprio contratto di licenza con l'utente finale o selezionare il [contratto standard](./standard-contract.md).

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, è possibile rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare tipi di offerte e modelli di fatturazione per monetizzare un'offerta, è possibile gestire una versione privata dell'offerta, completa di prezzi negoziati specifici della trattativa e di configurazioni personalizzate. Le offerte private sono supportate da tutte e tre le opzioni di pubblicazione delle transazioni.

Questa opzione consente un prezzo superiore o inferiore rispetto all'offerta pubblicamente disponibile. Le offerte private possono essere usate per applicare uno sconto o un supplemento a un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.

### <a name="examples"></a>Esempi

**Pagamento in base al consumo** 

Con pagamento in base al consumo la struttura dei costi è la seguente:

|Costo della licenza  | € 1,00 all'ora   |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 1,14 all'ora*       |
||

In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 1,14 all'ora  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza|   € 0,80 all'ora     |
|Microsoft trattiene il 20% del costo della licenza  |  € 0,20 all'ora       |
|Microsoft trattiene il 100% del costo dell'utilizzo di Azure | € 0,14 all'ora |
||

**Bring Your Own License (BYOL)**

Con BYOL la struttura dei costi è la seguente:

|Costo della licenza  | Il costo della licenza viene concordato e addebitato dall'utente  |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 0,14 all'ora*       |
||

In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 0,14 all'ora  |
|---------|---------|
|Microsoft trattiene il costo dell'utilizzo di Azure    |   € 0,14 all'ora     |
|Microsoft trattiene lo 0% del costo della licenza   |  € 0,00 all'ora       |
||

**Sottoscrizione app SaaS**

Questa opzione deve essere configurata per la vendita tramite Microsoft e può essere addebitata a una tariffa fissa o per utente su base mensile o annuale. Se si abilita l'opzione di **vendita tramite Microsoft** per un'offerta SaaS, la struttura dei costi è la seguente.

| Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
| Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
| *Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese (l'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza)*  |
||

In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.

I partner idonei per la riduzione della tariffa del servizio marketplace visualizzeranno una tariffa di transazione ridotta per le offerte SaaS da maggio 2019 a giugno 2020.

In questo scenario Microsoft fattura $ 100,00 per la licenza software e paga $ 90,00 all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft paga il 90% del costo di licenza   |   $ 80,00 al mese <br> \* $ 90,00 al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft trattiene il 10% del costo di licenza.  |  $ 20,00 al mese <br> \* 10,00 $     |

Per alcune offerte pubblicate sul Marketplace commerciale, Microsoft ridurrà il costo del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per le offerte idonee, le offerte devono essere state designate da Microsoft come co-selling IP di Azure incetivized. L'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa del servizio Marketplace ridotta per il mese. La tariffa ridotta per il servizio Marketplace si applica a SaaS incentivate, VM, app gestite e a qualsiasi altra offerta IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**: è possibile usare il metodo di fatturazione preferito del cliente per far pervenire i costi di licenza software con pagamento in base al consumo o in abbonamento.

**Contratto Enterprise**: se il metodo di fatturazione preferito del cliente è il contratto Microsoft Enterprise Agreement, le tariffe della licenza software verranno fatturate usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile**: i clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Crediti gratuiti e impegno monetario**: alcuni clienti decidono di pagare in anticipo Azure con un impegno monetario nel contratto Enterprise Agreement o dispongono di crediti gratuiti da usare con Azure. Anche se questi crediti possono essere usati per pagare l'utilizzo di Azure, non possono essere usati per pagare costi di licenza del software dell'editore.

**Fatturazione e raccolta**: la fatturazione della licenza del software dell'editore viene visualizzata usando il metodo di fatturazione scelto dal cliente e segue la tempistica della fatturazione. Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

Quando vengono scelti modelli di determinazione prezzi con pagamento in base al consumo o per la sottoscrizione, Microsoft agisce in funzione di agente dell'editore ed è responsabile di ogni aspetto della fatturazione, del pagamento e della riscossione.

### <a name="publisher-payout-and-reporting"></a>Proventi dell'editore e creazione di report

Eventuali costi di licenza software riscossi da Microsoft in funzione di agente sono soggetti a una commissione sulle transazioni del 20%, se non diversamente specificato, e vengono dedotti al momento del pagamento dell'editore.

I clienti effettuano in genere gli acquisti tramite il contratto Enterprise Agreement o un contratto con pagamento in base al consumo abilitato per le carte di credito. Il tipo di contratto determina le date di fatturazione, riscossione e pagamento.

>[!NOTE]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione di analisi del Centro per i partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per altre informazioni e normative legali, vedere il [Contratto per gli editori](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt) (disponibile nel Centro per i partner).

Per assistenza sulle domande relative alla fatturazione, contattare il [supporto per gli editori del marketplace commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

- Per l'opzione di pubblicazione delle transazioni, sono necessari un account Microsoft e informazioni finanziarie, indipendentemente dal modello di determinazione prezzi dell'offerta.
- Le informazioni finanziarie obbligatorie includono l'account di pagamento e il profilo fiscale.

Per ulteriori informazioni sulla configurazione di questi account, vedere [gestire l'account del Marketplace commerciale nel centro per i partner](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

L'opzione di pubblicazione delle transazioni è disponibile solo per l'uso con i tipi di offerte del marketplace seguenti:

- **Macchina virtuale** : è possibile scegliere tra modelli gratuiti, Bring-your-own-License o con pagamento in base al consumo e presenti come piani definiti a livello di offerta. Nella fattura di Azure del cliente Microsoft indica i costi della licenza del software dell'editore separatamente dai costi dell'infrastruttura di Azure sottostante. I costi dell'infrastruttura di Azure sono determinati dall'uso del software dell'editore.

- **Applicazione Azure: modello di soluzione o app gestita:** è necessario effettuare il provisioning di una o più macchine virtuali ed eseguire il pull della somma dei prezzi della macchina virtuale. Per le app gestite in un singolo piano, come modello di determinazione prezzi è possibile selezionare una sottoscrizione a tariffa fissa mensile invece dei prezzi delle macchine virtuali. In alcuni casi, le spese di utilizzo dell'infrastruttura di Azure vengono trasferite al cliente separatamente dalle spese di licenza software, ma nello stesso estratto conto. Tuttavia, se si configura un'offerta di app gestita per gli addebiti per l'infrastruttura ISV, le risorse di Azure vengono fatturate all'editore e il cliente riceve una tariffa fissa che include il costo dell'infrastruttura, le licenze software e i servizi di gestione.

- **Applicazione SaaS** : deve essere una soluzione multi-tenant, usare [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per l'autenticazione e integrarsi con le [API di evasione Saas](partner-center-portal/pc-saas-fulfillment-api-v2.md). L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'utente (il partner), quindi è necessario tenere conto delle tariffe di utilizzo dell'infrastruttura di Azure e delle tariffe di licenza software come singolo costo. Per istruzioni dettagliate, vedere [la pagina relativa alla creazione di una nuova offerta SaaS nel Marketplace commerciale](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Esaminare i modelli di pubblicazione in base alla vetrina per trovare esempi sul modo in cui la soluzione esegue il mapping a un tipo e a una configurazione di offerta.
