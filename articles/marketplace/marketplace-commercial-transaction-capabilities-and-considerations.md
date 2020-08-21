---
title: Funzionalità di transazione del marketplace commerciale di Microsoft
description: Questo articolo illustra le considerazioni relative a prezzi, fatturazione e proventi delle transazioni per l'opzione di transazione del marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 04a984a2dfa473502fd9e534e52b60b33be52757
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88704961"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Funzionalità di transazione del marketplace commerciale

Questo articolo descrive le considerazioni sui prezzi, la fatturazione, la fatturazione e i pagamenti per Microsoft Commercial Marketplace. 

## <a name="transactions-by-listing-option"></a>Opzione Transactions by Listing

L'editore o Microsoft è responsabile della gestione delle transazioni delle licenze software per le offerte nel marketplace commerciale. L'opzione di elenco scelto per l'offerta determina chi gestisce la transazione. Vedere [scegliere un'opzione di elenco](./determine-your-listing-type.md#choose-a-listing-option) per disponibilità e spiegazioni di ogni opzione di pubblicazione.

### <a name="contact-me-free-trial-and-byol-options"></a>Contattaci, versione di valutazione gratuita e opzioni di BYOL

Gli editori possono scegliere il _contatto_ e la _versione di valutazione gratuita_, le opzioni per scopi promozionali e di acquisizione utente. Per alcuni tipi di offerte, i publisher possono scegliere l'opzione Bring your own License (BYOL) per consentire ai clienti di acquistare una sottoscrizione all'offerta usando una licenza acquistata direttamente dall'utente. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni delle licenze software dell'editore e non sono previsti costi di transazione. 

Gli editori sono responsabili del supporto di tutti gli aspetti della transazione di licenza software. Sono inclusi, ad esempio, l'ordine, l'evasione, la misurazione, la fatturazione, la fatturazione, il pagamento e la raccolta. Con l'opzione Contact me Listing, i Publisher mantengono il 100% dei costi di licenza software dell'editore raccolti dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

La scelta di vendere tramite Microsoft sfrutta le funzionalità di Microsoft Commerce e offre un'esperienza end-to-end, da individuazione e valutazione all'acquisto e all'implementazione. Un'offerta transazionale è una delle quali Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore. Le offerte transazionali vengono fatturate in base a una sottoscrizione Microsoft esistente o a una carta di credito, consentendo a Microsoft di ospitare transazioni del Marketplace cloud per conto dell'editore.

È possibile scegliere l'opzione relativa alle transazioni quando si crea una nuova offerta nel Centro per i partner. Questa opzione verrà visualizzata solo se la transazione è disponibile per il tipo di offerta.

## <a name="transact-overview"></a>Panoramica sulle transazioni

Quando si usa l'opzione Transact, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta alla sottoscrizione di Azure del cliente. Quando si seleziona un modello di prezzi per un'offerta, l'autore deve considerare la fatturazione dei costi dell'infrastruttura e i costi di licenza software.

L'opzione di pubblicazione delle transazioni è attualmente supportata per i tipi di offerte seguenti:

- Macchine virtuali
- Applicazioni di Azure
- Applicazioni SaaS

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

Per le **macchine virtuali** e **le applicazioni Azure**, le tariffe di utilizzo dell'infrastruttura di Azure vengono fatturate alla sottoscrizione di Azure del cliente. Nella fattura del cliente i costi di utilizzo dell'infrastruttura vengono addebitati e visualizzati separatamente dai costi di licenza del provider software.

Per le **app SaaS**, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo,  presentata al cliente come tariffa mensile fissa. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'editore. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. Le tariffe per le licenze software non vengono misurate o in base al consumo dell'utente.

## <a name="pricing-models"></a>Modelli di determinazione dei prezzi

A seconda dell'opzione di transazione utilizzata, gli addebiti per la sottoscrizione sono i seguenti:

- **Ottienilo ora (gratuito)** : nessun addebito per le licenze software. Ai clienti non viene addebitata alcuna tariffa di Azure Marketplace per l'uso di un'offerta gratuita. Le offerte gratuite non possono essere convertite in un'offerta a pagamento. I clienti devono ordinare un'offerta a pagamento.
- **Bring Your Own License (BYOL)** : eventuali addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure Se un'offerta è elencata nel Marketplace commerciale, i clienti che ottengono l'accesso o l'utilizzo dell'offerta al di fuori del Marketplace commerciale non vengono addebitati come tariffe commerciali per il Marketplace.
- **Prezzi per le sottoscrizioni** : le tariffe delle licenze software sono presentate come tariffe mensili o annuali, una tariffa di sottoscrizione ricorrente fatturata come tariffa fissa o per postazione. I costi di sottoscrizione ricorrenti non vengono rivalutati in modo prolungato per gli annullamenti dei clienti a metà termine o per i servizi non usati Le tariffe di sottoscrizione ricorrenti possono essere rivalutate se il cliente aggiorna o esegue il downgrade della sottoscrizione al centro del periodo di validità della sottoscrizione.
- **Prezzi basati sull'utilizzo** : per le offerte di macchine virtuali di Azure, i clienti vengono addebitati in base al livello di utilizzo dell'offerta. Per le immagini di macchine virtuali, ai clienti viene addebitata una tariffa oraria di Azure Marketplace, impostata dagli editori, per l'uso di macchine virtuali distribuite dalle immagini di VM. La tariffa oraria può essere uniforme o diversificata in base alle dimensioni delle macchine virtuali. Le ore parziali vengono addebitate in base ai minuti effettivi. I piani vengono fatturati mensilmente.
- **Prezzi** a consumo: per offerte applicazione Azure e offerte SaaS, gli editori possono usare il [servizio di misurazione del Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) per fatturare l'utilizzo in base alle dimensioni del contatore che scelgono. Ad esempio, larghezza di banda, ticket o messaggi di posta elettronica elaborati. Gli editori possono definire una o più dimensioni del contatore per ogni piano. Gli editori sono responsabili del monitoraggio dell'utilizzo dei singoli clienti, con ogni misuratore definito nell'offerta. Gli eventi devono essere segnalati a Microsoft entro un'ora. Il costo che Microsoft addebita ai clienti si basa sulle informazioni sull'utilizzo comunicate dagli editori per il periodo di fatturazione applicabile.
- **Versione di valutazione gratuita** : nessun addebito per le licenze software che variano da 30 giorni fino a sei mesi, a seconda del tipo di offerta. Se i Publisher forniscono una versione di valutazione gratuita per più piani all'interno della stessa offerta, i clienti possono passare a una versione di valutazione gratuita in un altro piano, ma il periodo di valutazione non viene riavviato. Per le offerte di macchine virtuali, ai clienti vengono addebitati i costi di infrastruttura di Azure per l'uso dell'offerta durante un periodo di valutazione. Alla scadenza del periodo di valutazione, ai clienti viene addebitato automaticamente l'ultimo piano effettuato in base alle tariffe standard, a meno che non vengano annullate prima della fine del periodo di valutazione.

> [!NOTE]
> Le offerte fatturate in base all'utilizzo non sono idonee per i rimborsi dopo l'uso di una soluzione.

Gli editori che desiderano modificare le tariffe di utilizzo associate a un'offerta devono prima rimuovere l'offerta (o il piano specifico all'interno dell'offerta) dal Marketplace commerciale. La rimozione deve essere eseguita in base ai requisiti del [Contratto Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Il server di pubblicazione può quindi pubblicare una nuova offerta o pianificare un'offerta che include i nuovi costi di utilizzo. Per informazioni sulla rimozione di un'offerta o di un piano, vedere [interrompere la vendita di un'offerta o di un piano](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti, contattaci e Bring your own License (BYOL)

Quando si pubblica un'offerta con l'opzione Get it Now (gratuita), Contact me o BYOL, Microsoft non svolge alcun ruolo per facilitare la transazione di vendita per i costi di licenza software. Analogamente all'elenco e alle opzioni di pubblicazione della versione di valutazione gratuita, l'editore mantiene il 100% dei costi delle licenze software.

### <a name="usage-based-and-subscription-pricing"></a>Prezzi di sottoscrizione e di utilizzo

Quando si pubblica un'offerta a una transazione basata su utenti o sottoscrizione, Microsoft fornisce la tecnologia e i servizi per l'elaborazione di acquisti di licenze software, restituzioni e chargeback. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e usare il proprio software, imponendo il rispetto dei termini e delle condizioni sia del marketplace commerciale di Microsoft che del contratto di licenza con l'utente finale. Quando si crea l'offerta, è necessario fornire il proprio contratto di licenza con l'utente finale o selezionare il [contratto standard](./standard-contract.md).

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione di transazioni, è possibile rendere disponibile gratuitamente una licenza software da 30 a 120 giorni, a seconda della sottoscrizione. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare tipi di offerte e modelli di fatturazione per monetizzare un'offerta, è possibile gestire una versione privata dell'offerta, completa di prezzi negoziati specifici della trattativa e di configurazioni personalizzate. Le offerte private sono supportate da tutte e tre le opzioni di pubblicazione delle transazioni.

Questa opzione consente un prezzo superiore o inferiore rispetto all'offerta pubblicamente disponibile. Le offerte private possono essere usate per applicare uno sconto o un supplemento a un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.

### <a name="examples"></a>Esempi

**Basato sull'utilizzo** 

I prezzi basati sull'utilizzo hanno la struttura dei costi seguente:

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

In questo scenario Microsoft fattura $ 100,00 per la licenza software e paga $ 90,00 all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft paga il 90% del costo di licenza   |   $ 80,00 al mese <br> \* $ 90,00 al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \* Per le app SaaS idonee, Microsoft trattiene il 10% del costo di licenza.  |  $ 20,00 al mese <br> \* 10,00 $     |

Per alcune offerte pubblicate sul Marketplace commerciale, Microsoft ridurrà il costo del servizio Marketplace dal 20% (come descritto nel [Contratto Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560)) al 10%. Per le offerte idonee, le offerte devono essere state designate da Microsoft come co-selling IP di Azure incentivate. L'idoneità deve essere soddisfatta per almeno cinque giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa per il servizio Marketplace ridotta per il mese. La tariffa ridotta per il servizio Marketplace si applica alle app SaaS incentivate, alle VM, alle app gestite e a qualsiasi altra offerta IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Fatturazione cliente, pagamento e riscossioni

**Fatturazione e pagamento**: è possibile usare il metodo di fatturazione preferito del cliente per far pervenire i costi di licenza software con pagamento in base al consumo o in abbonamento.

**Contratto Enterprise**: se il metodo di fatturazione preferito del cliente è il contratto Microsoft Enterprise Agreement, le tariffe della licenza software verranno fatturate usando questo metodo di fatturazione come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Carte di credito e fattura mensile**: i clienti possono anche pagare con una carta di credito e una fattura mensile. In questo caso, le tariffe della licenza software verranno fatturate come nello scenario Enterprise Agreement, ovvero come costo dettagliato, separato da eventuali costi di utilizzo specifici di Azure.

**Crediti gratuiti e impegno monetario**: alcuni clienti decidono di pagare in anticipo Azure con un impegno monetario nel contratto Enterprise Agreement o dispongono di crediti gratuiti da usare con Azure. Anche se questi crediti possono essere usati per pagare l'utilizzo di Azure, non possono essere usati per pagare costi di licenza del software dell'editore.

**Fatturazione e raccolte** : la fatturazione delle licenze software dell'editore viene presentata usando il metodo di fatturazione selezionato dal cliente e segue la sequenza temporale per la fatturazione. Per i clienti senza un contratto Enterprise Agreement appropriato le licenze del software del marketplace vengono fatturate mensilmente. Per i clienti con Enterprise Agreement viene emessa una fattura mensile inviata ogni trimestre.

Quando vengono scelti modelli di determinazione prezzi con pagamento in base al consumo o per la sottoscrizione, Microsoft agisce in funzione di agente dell'editore ed è responsabile di ogni aspetto della fatturazione, del pagamento e della riscossione.

### <a name="publisher-payout-and-reporting"></a>Proventi dell'editore e creazione di report

Eventuali costi di licenza software riscossi da Microsoft in funzione di agente sono soggetti a una commissione sulle transazioni del 20%, se non diversamente specificato, e vengono dedotti al momento del pagamento dell'editore.

I clienti effettuano in genere gli acquisti tramite il contratto Enterprise Agreement o un contratto con pagamento in base al consumo abilitato per le carte di credito. Il tipo di contratto determina le date di fatturazione, riscossione e pagamento.

>[!NOTE]
>Tutti i report e le informazioni dettagliate per l'opzione di pubblicazione delle transazioni sono disponibili nella sezione di analisi del Centro per i partner.

#### <a name="billing-questions-and-support"></a>Domande sulla fatturazione e supporto

Per ulteriori informazioni e criteri legali, vedere il [contratto di Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560) (disponibile nel centro per i partner).

Per assistenza sulle domande relative alla fatturazione, contattare il [supporto per gli editori del marketplace commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Requisiti delle transazioni

In questa sezione vengono analizzati i requisiti delle transazioni per i diversi tipi di offerte.

### <a name="requirements-for-all-offer-types"></a>Requisiti per tutti i tipi di offerte

- Per l'opzione di pubblicazione delle transazioni, sono necessari un account Microsoft e informazioni finanziarie, indipendentemente dal modello di determinazione prezzi dell'offerta.
- Le informazioni finanziarie obbligatorie includono l'account di pagamento e il profilo fiscale.

Per ulteriori informazioni sulla configurazione di questi account, vedere [gestire l'account del Marketplace commerciale nel centro per i partner](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisiti per tipi di offerte specifici

L'opzione di pubblicazione delle transazioni è disponibile solo per l'uso con i tipi di offerte del marketplace seguenti:

- **Macchina virtuale di Azure** : è possibile scegliere tra modelli di prezzi gratuiti, Bring your own License o basati sull'utilizzo e presenti come piani definiti a livello di offerta. Nella fattura di Azure del cliente Microsoft indica i costi della licenza del software dell'editore separatamente dai costi dell'infrastruttura di Azure sottostante. I costi dell'infrastruttura di Azure sono determinati dall'uso del software dell'editore.

- **Applicazione Azure: modello di soluzione o app gestita:** è necessario effettuare il provisioning di una o più macchine virtuali ed eseguire il pull della somma dei prezzi della macchina virtuale. Per le app gestite in un singolo piano, come modello di determinazione prezzi è possibile selezionare una sottoscrizione a tariffa fissa mensile invece dei prezzi delle macchine virtuali. In alcuni casi, le spese di utilizzo dell'infrastruttura di Azure vengono trasferite al cliente separatamente dalle spese di licenza software, ma nello stesso estratto conto. Tuttavia, se si configura un'offerta di app gestita per gli addebiti per l'infrastruttura ISV, le risorse di Azure vengono fatturate all'editore e il cliente riceve una tariffa fissa che include il costo dell'infrastruttura, le licenze software e i servizi di gestione.

- **Applicazione SaaS** : deve essere una soluzione multi-tenant, usare [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per l'autenticazione e integrarsi con le [API di evasione Saas](partner-center-portal/pc-saas-fulfillment-api-v2.md). L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'utente (il partner), quindi è necessario tenere conto delle tariffe di utilizzo dell'infrastruttura di Azure e delle tariffe di licenza software come singolo costo. Per istruzioni dettagliate, vedere [la pagina relativa alla creazione di una nuova offerta SaaS nel Marketplace commerciale](partner-center-portal/create-new-saas-offer.md).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i requisiti di idoneità nelle opzioni di pubblicazione in base alla sezione del tipo di offerta per finalizzare la selezione e la configurazione dell'offerta.
- Per esempi su come la soluzione è mappata a un tipo di offerta e a una configurazione, vedere la pagina relativa ai modelli di pubblicazione tramite Online Store.
