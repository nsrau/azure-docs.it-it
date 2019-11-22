---
title: Domande frequenti su Microsoft Commercial Marketplace
description: Risposte alle domande comuni su Azure Marketplace e Microsoft AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/4/2019
ms.author: ellacroi
ms.openlocfilehash: 6a7a0d5bdd07221be823f8b94bd581db6d966fa8
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306411"
---
# <a name="microsoft-commercial-marketplace-faqs"></a>Domande frequenti sul Marketplace commerciale Microsoft

Risposte alle domande comuni su Azure Marketplace e Microsoft AppSource.

## <a name="faq-for-customers"></a>Domande frequenti per i clienti

### <a name="what-you-need-to-know-about-azure-marketplace-and-microsoft-appsource"></a>Cosa è necessario sapere su Azure Marketplace e Microsoft AppSource

**Che cos'è Azure Marketplace?**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) fornisce accesso e informazioni per le soluzioni e i servizi messi a disposizione da Microsoft e dai partner. I clienti possono individuare, provare o acquistare soluzioni software cloud basate su o per Azure. Il catalogo, che contiene oltre 8.000 inserzioni, fornisce i blocchi predefiniti per Azure, ad esempio macchine virtuali (VM), API, app di Azure, modelli di soluzione e applicazioni gestite, app SaaS, contenitori e servizi di consulenza.

**Chi sono i clienti di Azure Marketplace?**

Azure Marketplace è progettato per i professionisti IT e gli sviluppatori cloud interessati al software e ai servizi IT commerciali.

**Quali tipi di prodotti sono attualmente disponibili in Azure Marketplace?**

Azure Marketplace offre soluzioni tecniche e servizi di Microsoft e dei partner creati per estendere i prodotti e i servizi di Azure. Il catalogo della soluzione si estende su diverse categorie, tra cui:

* sistemi operativi di base
* database
* security
* identity
* networking
* blockchain
* strumenti per sviluppatori

### <a name="azure-marketplace-for-customers"></a>Azure Marketplace per i clienti

**Come si inizia a usare Azure Marketplace?**

Vistando [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps), è possibile trovare un'ampia gamma di soluzioni e applicazioni aziendali certificate e ottimizzate per l'esecuzione in Azure. È possibile accedere ad Azure Marketplace anche tramite il [portale di Azure] quando si [**Crea una risorsa**](https://portal.azure.com/#create/hub).

**Quali sono i vantaggi principali di Azure Marketplace?**

In Azure Marketplace i clienti possono trovare applicazioni tecniche create per Azure o basate su Azure. Questa piattaforma riunisce l'ecosistema di soluzioni e servizi di Microsoft Azure in un'unica posizione dove trovare, provare, acquistare o distribuire soluzioni con pochi clic.

**Come è possibile acquistare i prodotti da Azure Marketplace?**

Le offerte di Azure Marketplace possono essere acquistate tramite:

* [Vetrina basata sul Web](https://azuremarketplace.microsoft.com/marketplace/apps)
* [Portale di Azure][Portale di Azure]
* [Interfaccia della riga di comando di Azure (CLI)](/cli/azure/?view=azure-cli-latest)

>[!Note]
>non è possibile usare crediti prepagati e altre forme di impegno monetario per pagare le tariffe per le licenze software, ma è possibile usarli per pagare gli addebiti per l'utilizzo di Azure associati. Le eccezioni sono indicate nella pagina relativa all'[impegno monetario di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

**È possibile scegliere le aree di Azure per distribuire l'acquisto di Azure Marketplace?**

Il cliente ha la possibilità di distribuire in qualsiasi area di Azure data center abilitata. È consigliabile selezionare le località dei data center più vicine ai propri servizi in modo da ottimizzare le prestazioni e tenere sotto controllo il budget.

**Se elimino accidentalmente un acquisto di Azure Marketplace, posso *annullare* l'azione?**

No, non è possibile annullare le operazioni di eliminazione. Se una sottoscrizione viene eliminata accidentalmente, è possibile effettuare di nuovo l'acquisto. Eventuali funzionalità inutilizzate o servizi prepagati andranno persi.

**Si riceve un avviso se si cerca di eliminare un acquisto di Azure Marketplace in uso dalle applicazioni?**

No, Azure non invia avvisi quando si elimina un acquisto, anche se è attualmente in uso o se un'applicazione dipende da esso.

**Se sono presenti relazioni di dipendenza tra l'acquisto di Azure Marketplace e altri asset, come un sito Web di Azure, è necessario gestirle?**

Le dipendenze per le offerte di Azure Marketplace non vengono gestite automaticamente. Esaminare con attenzione la descrizione di ogni acquisto di Azure Marketplace prima dell'uso, in modo da determinare se sono presenti dipendenze di cui tenere conto prima della distribuzione della soluzione.

**È possibile acquistare soluzioni di Azure Marketplace da un Azure Cloud Solution Provider?**

Se il server di pubblicazione ha configurato l'offerta per essere disponibile tramite il canale Cloud Solution Provider (CSP), i partner Cloud Solution Provider hanno la possibilità di rivendere la soluzione.

**Quali paesi/aree sono supportati per l'acquisto di applicazioni e servizi venduti/sottoposti a provisioning tramite Azure Marketplace?**

Azure Marketplace è disponibile per i clienti di Azure nei paesi/aree geografiche elencate nelle [politiche di partecipazione](/legal/marketplace/participation-policy).

**Quali valute sono supportate da Azure Marketplace?**

Le transazioni possono essere eseguite nelle 17 valute seguenti: AUD, BRL, CAD, CHF, DKK, EUR, GBP, INR, JPY, KRW, NOK, NZD, RUB, SEK, TWD, USD, RMB.

### <a name="deploying-a-solution-from-azure-marketplace"></a>Distribuzione di una soluzione da Azure Marketplace

**Ho distribuito una macchina virtuale (VM) di Azure Marketplace in una sottoscrizione e ora voglio eseguire la migrazione della sottoscrizione da un account Azure a un altro. Questo è attualmente supportato?**

Per eseguire la migrazione di una sottoscrizione di Azure, inclusi i servizi e le macchine virtuali di Azure Marketplace, è necessario eliminare o annullare ogni sottoscrizione di Azure precedente prima di eseguire l'associazione al nuovo account Azure. Al termine della migrazione, i costi di utilizzo risultanti vengono fatturati utilizzando il metodo di pagamento del nuovo account registrato.

**Si vuole eseguire la migrazione di una sottoscrizione di macchina virtuale (VM) di Azure Marketplace a Enterprise Agreement. Questo è attualmente supportato?**

Per eseguire la migrazione di una sottoscrizione di macchina virtuale di Azure Marketplace a un contratto Enterprise Agreement, arrestare o annullare ogni sottoscrizione precedente prima di procedere. Una volta completata la migrazione dell'account Azure e delle sottoscrizioni associate, è possibile riacquistare la VM o il servizio di Azure Marketplace.  Le tariffe di utilizzo risultanti vengono fatturate su base trimestrale nel contratto Enterprise Agreement.

### <a name="pricing-and-payment"></a>Prezzi e pagamento

**Come vengono stabiliti i prezzi delle sottoscrizioni di Azure Marketplace?**

I prezzi variano in base ai tipi di prodotto e alle specifiche dell'editore. Le tariffe per le licenze software e i costi di utilizzo di Azure vengono addebitati separatamente tramite la sottoscrizione di Azure.

*Non in bundle:*

+ *Modello Bring your own License (BYOL)* : quando si ottiene una licenza software direttamente dall'editore o da un rivenditore, non sono previsti costi aggiuntivi o costi correlati al software.

*In bundle:*

La sottoscrizione di Azure è inclusa nei prezzi della soluzione fornitori di software indipendenti (ISV) dell'editore.

*A pagamento:*

+ *Gratuito:* SKU gratuito. Non vengono addebitati costi per le licenze software o l'utilizzo dell'offerta.

+ *Versione di valutazione software gratuita:* Offerta gratuita per un periodo di tempo limitato. Non sono previsti addebiti per i costi di licenza software dell'editore per l'utilizzo durante il periodo di valutazione. Alla scadenza, viene automaticamente eseguita la conversione in offerta a pagamento in base alle tariffe standard previste dall'editore.

+ *Basato sull'utilizzo:* Le tariffe vengono addebitate o fatturate in base all'utilizzo dell'offerta. Per le immagini di macchine virtuali, viene addebitata una tariffa oraria. Per i servizi per sviluppatori e le API, viene addebitata una tariffa per unità di misura definita dall'offerta.

+ *Tariffa flat:* Le sottoscrizioni SaaS possono essere addebitate come tariffe flat fatturate mensilmente o annualmente. Questo può includere anche dimensioni di fatturazione aggiuntive che vengono addebitate in base all'utilizzo (ad esempio, larghezza di banda, messaggi di posta elettronica o ticket). 

+ *Per utente:* Le sottoscrizioni SaaS possono essere valutate in base ai singoli utenti, fatturate mensilmente o annualmente. 

Per informazioni dettagliate sui prezzi specifici dell'offerta, vedere la pagina dei dettagli della soluzione in [https://azure.microsoft.com/pricing/](https://azure.microsoft.com/pricing/) o all'interno del [portale di Azure].

> [!Note]
> Gli addebiti per l'utilizzo di Azure, ad eccezione delle tariffe mensili, sono applicabili a tutti i modelli di determinazione dei prezzi, se non diversamente indicato.

**Come si deve fornire il codice di licenza software per le soluzioni BYOL del marketplace e qual è il ruolo di Azure Marketplace?**

L'acquisizione e l'applicazione delle credenziali di licenza per le soluzioni BYOL sono di responsabilità dell'editore. Per le offerte di macchine virtuali, l'acquisizione della chiave di licenza si verifica in genere nell'applicazione dell'editore dopo l'avvio dell'applicazione. Quando si usa un'offerta di macchina virtuale distribuita tramite un modello di soluzione applicazione Azure, il modello di Gestione risorse può essere configurato in modo da richiedere all'utente un intervallo di input, incluse le credenziali di licenza.

Ecco le opzioni più comuni per ogni tipo di offerta:

*Offerta di macchina virtuale:*

+ *Opzione 1:* L'acquisizione della chiave di licenza si verifica in genere nell'applicazione dell'editore dopo che l'applicazione è stata avviata.

+ *Opzione 2:* Il codice di licenza viene immesso dall'utente finale, tramite la riga di comando o l'interfaccia Web fornita dall'offerta, dopo la distribuzione dell'offerta della macchina virtuale nella sottoscrizione selezionata. La licenza può essere costituita da un codice e/o un file, a discrezione dell'editore.

*App di Azure (modello di soluzione e app gestite):*

+ *Opzione 1:* Il modello di Gestione risorse può essere configurato in modo da richiedere un intervallo di input, incluse le credenziali di licenza. Le credenziali possono essere fornite sotto forma di file di licenza (caricamento di file) o di codice (immissione di testo), prima della distribuzione dell'offerta, nella sottoscrizione dell'utente finale.

+ *Opzione 2:* È possibile immettere il codice di licenza tramite la riga di comando o l'interfaccia Web fornita dall'offerta.  Questa operazione viene eseguita dopo la distribuzione dell'offerta di app di Azure nella sottoscrizione selezionata. La licenza può essere costituita da un codice e/o un file, a discrezione dell'editore.

**Quali tipi di test sono supportati?**

Gli editori possono aggiungere un mese gratuito per le offerte SaaS a pagamento e uno o tre mesi di utilizzo gratuiti per le immagini di macchina virtuale. Le offerte di valutazione gratuite sono elenchi con una chiamata a Action per avviare una versione di valutazione. Questi conducono il cliente a un sito Web definito dall'editore per configurare l'esperienza di valutazione. Le versioni di valutazione possono essere aggiunte anche alle offerte a pagamento dove il primo mese è gratuito. 

**È necessario avere registrato uno strumento di pagamento (ad esempio una carta di credito) per distribuire offerte del livello gratuito o Bring Your Own License (BYOL)?**

No. Non è necessario alcuno strumento di pagamento per distribuire offerte del livello gratuito o Bring Your Own License (BYOL). Al contrario, le offerte in versione di valutazione gratuita richiedono uno strumento di pagamento. Le inserzioni che includono i pulsanti **Scarica adesso** o **Versione di valutazione gratuita del software** vengono distribuite nella sottoscrizione di Azure selezionata.  Questi elenchi vengono fatturati utilizzando il metodo di pagamento registrato dell'account selezionato. Gli addebiti per l'utilizzo di Azure vengono fatturati separatamente dalle tariffe di licenza software.

**A chi deve rivolgersi un cliente indiretto con contratto Enterprise Agreement per domande sui prezzi per le offerte vendute in Azure Marketplace?**

I clienti indiretti con contratto Enterprise Agreement devono rivolgersi al loro LSP (Licensing Solution Provider) per informazioni sui prezzi di Azure Marketplace.

**È possibile controllare l'accesso dei dipendenti ad Azure Marketplace e i privilegi di acquisto?**

Sì, per i clienti con contratto Enterprise Agreement l'amministratore delle registrazioni può disattivare i privilegi di acquisto per tutti gli account della registrazione e riattivarli per il tempo necessario per effettuare un acquisto.

**Quali metodi di pagamento sono supportati per gli acquisti nel Marketplace commerciale?**

I clienti possono acquistare offerte da Azure Marketplace e Microsoft AppSource usando carte di credito. Se si dispone di una sottoscrizione di Azure esistente, gli acquisti da Azure Marketplace utilizzeranno il metodo di pagamento configurato per l'account e verranno visualizzati nella stessa fattura di una voce separata. Alcune offerte utilizzano un impegno monetario di Azure, ma la maggior parte degli acquisti di Marketplace commerciali non inducono Enterprise Agreement impegni anche se il consumo dell'infrastruttura di Azure.

**È possibile applicare i crediti della sottoscrizione di Azure o i fondi dell'impegno monetario dell'account alle offerte di Azure Marketplace?**

Alcune offerte specifiche di Azure Marketplace possono usare i crediti delle sottoscrizioni di Azure o i fondi dell'impegno monetario. Per un elenco completo dei prodotti che partecipano al programma, vedere la pagina relativa all'[impegno monetario di Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/). Queste offerte non includono le opzioni BYOL e BYOS. Tutte le altre offerte di Azure Marketplace non possono usare i crediti della sottoscrizione di Azure o l'impegno monetario: come il credito di valutazione gratuito di un mese, i crediti MSDN mensili, i crediti delle promozioni di Azure, i saldi degli impegni monetari e qualsiasi altro credito gratuito fornito da Azure.

**Gli sconti per i contratti multilicenza si applicano agli acquisti in Azure Marketplace?**

No. I prezzi vengono stabiliti dall'editore proprietario delle soluzioni in Azure Marketplace.  Gli sconti standard per contratti multilicenza Microsoft non sono applicabili agli acquisti in Azure Marketplace.

**Dove è possibile visualizzare i dettagli della sottoscrizione di Azure Marketplace e le informazioni di fatturazione?**

MOSP-i clienti di [Microsoft Online Subscription Program](https://azure.microsoft.com/support/legal/subscription-agreement/?country=us&language=en) (Web Direct) possono visualizzare i dettagli di acquisto del Marketplace nella scheda fatture della sezione Gestione costi e fatturazione della [portale di Azure].

I clienti Enterprise Agreement (EA) possono visualizzare i dettagli di acquisto del Marketplace nella scheda fatture della sezione Gestione costi e fatturazione della [portale di Azure].

I partner Cloud Solution Provider (CSP) possono visualizzare i dettagli di acquisto del Marketplace per ogni cliente nel centro per i partner nella scheda Cronologia ordini dopo aver selezionato una società nella visualizzazione clienti.

**Come si annulla un componente aggiuntivo di Azure Marketplace per una macchina virtuale di Azure?**

Poiché il componente aggiuntivo è associato alla macchina virtuale di Azure, per annullare l'acquisto di Azure Marketplace è prima necessario arrestare la macchina virtuale eliminandola. In questo modo vengono interrotti l'utilizzo e gli addebiti di tutte le sottoscrizioni per l'acquisto di Azure Marketplace.

**Qual è la frequenza di emissione delle fatture per gli acquisti di Azure Marketplace?**

Per tutte le offerte di Azure Marketplace non deducibili dall'impegno monetario, la fatturazione viene effettuata ogni mese in modo posticipato. Le sottoscrizioni SaaS annuali vengono fatturate una volta per i servizi di un anno completo.

MOSP-ai clienti di [Microsoft Online Subscription Program](https://azure.microsoft.com/support/legal/subscription-agreement/) (Web Direct) viene addebitato un importo mensile rispetto alla stessa carta di credito che si trova nel file per il proprio profilo di sottoscrizione di Azure. Le sottoscrizioni SaaS annuali vengono fatturate una volta per i servizi di un anno completo.

**Come è possibile spostare gli acquisti di Azure Marketplace dalla sottoscrizione MOSP alla sottoscrizione diretta con contratto Enterprise Agreement?**

Benché la maggior parte delle sottoscrizioni Microsoft possa essere facilmente convertita nel contratto Enterprise Agreement, per gli acquisti di Azure Marketplace all'interno di tali sottoscrizioni la conversione non è possibile.

Per eseguire la migrazione di altri servizi acquistati da Azure Marketplace a una sottoscrizione con contratto Enterprise Agreement, è prima necessario annullare le applicazioni all'interno della sottoscrizione MOSP esistente e quindi acquistarle di nuovo all'interno della sottoscrizione con contratto Enterprise Agreement. Se si esegue questa operazione, è possibile inviare una richiesta di credito per ottenere un rimborso per il potenziale mese di sovrapposizione della copertura tra le due sottoscrizioni del servizio Marketplace. A questo scopo, creare un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Qual è la differenza tra *Prezzo*, *prezzo software*e *prezzo totale* nella struttura dei costi per le offerte di macchine virtuali in Azure Marketplace?**

Il *Prezzo* si riferisce al costo della macchina virtuale di Azure per l'esecuzione del software. Il *prezzo software* si riferisce al costo del software dell'editore del Marketplace in esecuzione in una macchina virtuale di Azure. Il *prezzo totale* si riferisce al costo totale combinato della macchina virtuale di Azure e del software dell'editore del Marketplace in esecuzione in una macchina virtuale di Azure.

**Come è possibile verificare la quantità di acquisti effettuati in Azure Marketplace?**

Le informazioni di utilizzo stimate sono reperibili nella [portale di Azure]. Queste informazioni possono non includere le attività recenti e possono essere basate sulle proiezioni derivate da dati sull'uso relativi a periodi precedenti. Durante l'anteprima pubblica questa funzionalità potrebbe non essere disponibile per tutti gli acquisti e variare in base al tipo di prodotto.

### <a name="customer-support"></a>Assistenza clienti

**Chi è possibile contattare per problemi di supporto generali relativi ad Azure Marketplace?**

Per il supporto generale per le applicazioni in merito all'utilizzo o alla risoluzione dei problemi, contattare direttamente l'editore dell'applicazione.

Per problemi di fatturazione e sottoscrizione relativi all'acquisto di Azure Marketplace, contattare il [supporto tecnico di Azure](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953).

**Chi posso contattare per ottenere supporto tecnico per una soluzione acquistata in Azure Marketplace?**

Per il supporto tecnico per tutti i prodotti, contattare il provider dell'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli della soluzione in Azure Marketplace.

**Chi è possibile contattare per ottenere supporto per la fatturazione o per domande su una soluzione di terze parti acquistata in Marketplace?**

Contattare il supporto tecnico Microsoft nella pagina del [supporto tecnico di Azure](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953).

**Chi è possibile contattare in caso di domande sui prezzi o sulle condizioni per le soluzioni dei partner vendute in Azure Marketplace?**

Per il supporto tecnico per tutti i prodotti, contattare il provider dell'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli di ogni soluzione in Azure Marketplace.

**È possibile restituire un prodotto se non si è soddisfatti dell'acquisto?**

Gli acquisti effettuati da [Azure Marketplace](https://azuremarketplace.microsoft.com/) non possono essere restituiti, ma possono essere annullati o eliminati. Le offerte basate sul consumo vengono fatturate in base all'utilizzo, quindi, quando si arresta, gli addebiti vengono interrotti. Le sottoscrizioni vengono annullate e non verranno fatturate oltre il periodo di fatturazione corrente. Se una sottoscrizione viene annullata poco dopo l'acquisto (24 ore per il mese e 14 giorni per l'anno), viene fornito un rimborso completo.

Per qualsiasi problema tecnico relativo all'acquisto o al servizio di Marketplace, i clienti devono contattare direttamente l'editore. Le informazioni di contatto dell'editore e/o il collegamento al sito Web di supporto sono disponibili nella pagina dei dettagli della soluzione in Azure Marketplace.

**In che modo vengono gestite le aggiunte alla licenza quando è aumentato a metà periodo?**

Le licenze aggiunte a una sottoscrizione esistente sono ripartite in modo proporzionale per il resto della durata della sottoscrizione.

**In che modo vengono gestite le rimozioni delle licenze a metà periodo?**

Le licenze annullate saranno soggette ai criteri di rimborso disponibili in queste domande frequenti.  Tutte le licenze annullate vengono immediatamente rimosse dall'account e non saranno più disponibili per l'utilizzo.

**I rimborsi sono supportati per le offerte basate sull'utilizzo?**

Eventuali addebiti in base al consumo, sia che si tratti di VM orarie o contatori personalizzati, non sono rimborsabili tramite annullamento. Al termine dell'utilizzo, gli addebiti vengono elaborati dalla piattaforma commerciale di Microsoft e tutte le controversie di qualità dei servizi in cui viene richiesto un rimborso verranno gestite all'esterno dei sistemi Microsoft tra l'editore e il cliente. Le offerte che supportano una tariffa fissa e la fatturazione a consumo seguono i criteri di rimborso standard per i costi forfettari.

**È possibile modificare i piani a metà periodo?**

No, non è possibile eseguire la transizione tra cadenza mensile e annuale.

**Un cliente può acquistare due piani della stessa offerta?**

Sì, è possibile che un cliente disponga di due piani della stessa offerta contemporaneamente.

**I criteri di rimborso e di modifica dei piani sono diversi da storefront?**

No, i criteri di business sono coerenti in Azure Marketplace e Microsoft AppSource. Se rivenduto da un partner nella soluzione cloud fornisce il programma, il partner può applicare un criterio diverso per i clienti.


## <a name="faq-for-publishers"></a>Domande frequenti per gli editori

### <a name="what-you-need-to-know-about-azure-marketplace-and-microsoft-appsource"></a>Cosa è necessario sapere su Azure Marketplace e Microsoft AppSource

**Che cos'è Azure Marketplace?**

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) è un marketplace di servizi e applicazioni online. I clienti (prevalentemente professionisti IT e sviluppatori) possono trovare, provare e acquistare soluzioni software cloud create con o per Azure. Il catalogo contiene oltre 8.000 inserzioni, tra cui i blocchi predefiniti per Azure, ad esempio macchine virtuali (VM), API, modelli di soluzione, applicazioni SaaS e offerte di servizi di consulenza.

Azure Marketplace è il trampolino di lancio per tutte le attività go-to-market in collaborazione con Microsoft.  Lo scopo è quello di aiutare i partner a raggiungere più clienti. È possibile pubblicare nuove inserzioni e usare Azure Marketplace per condurre campagne promozionali e di generazione della domanda, oltre che svolgere attività di vendite/marketing in collaborazione con Microsoft.

**Chi sono i clienti di Azure Marketplace?**

Azure Marketplace si rivolge ai professionisti IT e agli sviluppatori cloud interessati a servizi e software IT commerciali.

### <a name="azure-marketplace-for-publishers"></a>Azure Marketplace per gli editori

**Perché è utile pubblicare un'applicazione in Azure Marketplace e quali sono i vantaggi?**

Azure Marketplace offre ai partner Microsoft un'area commerciale in cui promuovere e vendere i propri prodotti e servizi ai clienti di Azure. Gli editori possono accedere immediatamente ai mercati globali 140, ai nostri 300.000 e ai nostri partner e alla rete di clienti aziendali di Azure.  Il Marketplace include oltre il 90% delle società Fortune 500 e molti degli sviluppatori leader del mondo. Ai nuovi partner di Azure Marketplace viene automaticamente offerto un set di [vantaggi go-to-Market senza costi](gtm-your-marketplace-benefits.md#list-trial-and-consulting-benefits) che consentono di migliorare la conoscenza delle offerte nel Marketplace di Azure.

**Qual è il fattore discriminante che differenzia Azure Marketplace da AppSource?**

I partner Microsoft possono scegliere dove pubblicare in base al loro pubblico di destinazione.

Microsoft offre due vetrine di Marketplace cloud distinti: Azure Marketplace e AppSource. Queste vetrine permettono ai clienti di trovare, provare e acquistare applicazioni e servizi cloud. Ogni vetrina si rivolge a clienti con esigenze specifiche e consente ai partner Microsoft di offrire le soluzioni o i servizi appropriati in base al pubblico di destinazione.

Scegliere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) per rivolgersi a professionisti IT, sviluppatori o utenti tecnici.

Scegliere [AppSource](https://appsource.microsoft.com/) per rivolgersi a decision maker line-of-business e proprietari di aziende.

Esaminare la [Guida alla pubblicazione](marketplace-publishers-guide.md) per informazioni dettagliate sui vantaggi di Azure Marketplace e AppSource.

**È necessario essere membri Microsoft Partner Network (MPN) per pubblicare inserzioni per applicazioni e servizi in Azure Marketplace?**

Sì, per la pubblicazione in Azure Marketplace è necessario essere membri MPN. Visitare la pagina di [Microsoft Partner Network](https://partner.microsoft.com/membership) per iniziare.

**Quali sono i criteri per pubblicare una soluzione in Azure Marketplace?**

Per pubblicare in Azure Marketplace, i partner devono dimostrare che l'applicazione viene eseguita in Azure o ne estende le funzionalità. Gli editori sono tenuti a offrire ai clienti una [contratto di servizio](https://azure.microsoft.com/support/legal/sla/), [informativa sulla privacy](https://privacy.microsoft.com/privacystatement)e il supporto telefonico e online. Diversi carichi di lavoro prevedono requisiti aggiuntivi. Per altre indicazioni, vedere [Azure Marketplace Participation Policies](./marketplace-participation-policy.md) (Criteri di partecipazione ad Azure Marketplace) e la [Guida alla pubblicazione](marketplace-publishers-guide.md).

**Sono previste tariffe per la pubblicazione in Azure Marketplace?**

Non sono previste tariffe di pubblicazione quando si pubblica un'inserzione per un'offerta gratuita, una versione di valutazione o una soluzione BYOL (Bring Your Own License) tramite Azure Marketplace.

**Sono previste tariffe per le transazioni per gli acquisti effettuati tramite Azure Marketplace?**

Quando la licenza di una soluzione viene acquistata tramite Azure Marketplace, i ricavi per la licenza software vengono suddivisi tra l'editore e Microsoft.  Ciò avviene in base ai termini e alle condizioni del [contratto per gli editori di Marketplace](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf). Per le soluzioni Bring Your Own License (BYOL), agli editori non verranno inoltre addebitate tariffe per le transazioni.

**Che cos'è un contratto standard?**

Microsoft offre condizioni contrattuali standard che un editore può scegliere di utilizzare, in modo che i clienti dispongano di un processo semplificato di approvvigionamento e revisione legale.

**Dove è possibile trovare le indicazioni per integrare un'applicazione con Azure Active Directory (AAD)?**

Microsoft usa AAD per autenticare tutti gli utenti di Marketplace.  È possibile ricevere direttamente una versione di valutazione senza effettuare ulteriori passaggi di accesso.  Ad esempio, quando un utente autenticato fa clic su un'inserzione per una versione di valutazione in Marketplace, viene reindirizzato a un ambiente di valutazione.

Per altre informazioni e per iniziare ad abilitare una versione di valutazione con AAD, vedere la [sezione di Azure Active Directory nella Guida alla pubblicazione](enable-appsource-marketplace-using-azure-ad.md).

**Come si avvia la registrazione al Dev Center?**

Per evitare duplicati, gli editori devono prima di tutto verificare che non sia già stato registrato un [account Dev Center](deprecated/register-dev-center.md). Una volta effettuata questa verifica, il passaggio successivo consiste nell'eseguire la registrazione [accedendo](https://account.microsoft.com/account/) con un account Microsoft, che verrà associato all'account per sviluppatori.

Se non si dispone già di un account Microsoft, è possibile [creare un account](https://signup.live.com/) , ad esempio contoso_marketplace@live.com.

**Perché è necessario disporre di un account Dev Center?**

L'account Dev Center consente a Microsoft di fatturare ai clienti per conto dell'editore per i tipi di inserzione transazione. La registrazione dell'account Dev Center consente a Microsoft di convalidare le informazioni legali, fiscali e bancarie. Per altre informazioni, vedere [Eseguire la registrazione in Dev Center](./partner-center-portal/create-account.md).

**Che cosa sono i lead e perché sono importanti per chi pubblica in Marketplace?**

I lead sono i clienti che distribuiscono i prodotti dal Marketplace. Che l'inserzione del prodotto sia pubblicata in [Azure Marketplace](https://azuremarketplace.microsoft.com) o [AppSource](https://appsource.microsoft.com), è possibile ricevere lead dei clienti interessati al prodotto.  È possibile definire i lead destinatari per un'offerta. Per altre informazioni, vedere [Diventare un editore del Marketplace cloud](./partner-center-portal/create-account.md).

**Dove è possibile ottenere assistenza per configurare la destinazione dei lead?**

Per altre informazioni, vedere la documentazione relativa ai [Lead dei clienti](./cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads.md) oppure inviare un ticket di supporto tramite [https://aka.ms/marketplacepublishersupport](https://aka.ms/marketplacepublishersupport) selezionando il tipo di offerta e la gestione dei lead.

**È necessario configurare i lead destinatari per pubblicare un'offerta in Marketplace?**

Sì, se si pubblica un'offerta di tipo **Contattami**, **app SaaS** o **servizi di consulenza**, è necessario configurare i lead destinatari.

**Come è possibile verificare che la configurazione dei lead sia corretta?**

Dopo aver completato l'offerta e aver configurato una destinazione lead, l'elenco può essere pubblicato correttamente nel centro per i [partner](https://cloudpartner.azure.com/). Prima della pubblicazione dell'inserzione, è possibile verificare se la configurazione dei lead è corretta.  Inviare un lead di test per i lead destinatari configurati nell'offerta.

**Da quali paesi/aree geografiche gli editori possono vendere in Azure Marketplace?**

Gli autori basati nei paesi/aree geografiche seguenti possono attualmente vendere in Azure Marketplace: Afghanistan, Albania, Algeria, Angola, Antigua e Barbuda, Argentina, Armenia, Australia, Austria, Azerbaijan, Bahrain, Bangladesh, Bielorussia, Belgio, Benin, Bolivia, Bosnia ed Erzegovina, Botswana, Brasile, Bulgaria, Burkina Faso, Burundi, Cambogia, Camerun, Canada, Repubblica Centrafricana, Ciad, Chile, Colombia, Comore, Congo, Repubblica democratica del Congo, Costa Rica, Croazia, Cipro, Repubblica Ceca Danimarca, Dominica, Repubblica Dominicana, Ecuador, Egitto, El Salvador, Eritrea, Estonia, Etiopia, Isole Figi, Finlandia, Francia, Georgia, Germania, Ghana, Grecia, Guatemala, Guinea, Haiti, Honduras, Hong Kong SAR, Ungheria, Islanda, India, Indonesia, Iraq, Irlanda, Israele, Italia, Giamaica, Giappone, Giordania, Kazakistan, Kenya, Corea (Sud), Kuwait, Laos, Lettonia, Libano, Liberia, Liechtenstein, Lituania, Lussemburgo, Madagascar, Malawi, Malaysia, Mali, Malta, Mauritius, Messico, Monaco, Mongolia, Montenegro Marocco, Mozambico, Nepal, Paesi Bassi, Nuova Zelanda, Nicaragua, Niger, Nigeria, Norvegia, Oman, Pakistan, Panama, Paraguay, Perù, Filippine, Polonia, Portogallo, Qatar, Romania, Russia, Rwanda, Arabia Saudita, Senegal, Serbia, Sierra Leone, Singapore, Slovacchia, Slovenia, Somalia, Sudafrica, Spagna, Sri Lanka, Svezia, Svizzera, Tagikistan, Tanzania, Tailandia, Timor-Leste, Togo, Tonga, Trinidad e Tobago, Tunisia, Turchia, Turkmenistan, Uganda, Ucraina, Emirati Arabi Uniti, Regno Unito Stati, Uruguay, Uzbekistan, Venezuela, Vietnam, Zambia e Zimbabwe.

**Come si elimina un'inserzione da Azure Marketplace?**

*Macchine virtuali & app di Azure:*

1. Accedere al centro per i [partner](https://cloudpartner.azure.com/).
1. Selezionare l'offerta dalla scheda **tutte le offerte** .
1. Nel riquadro sul lato sinistro della schermata selezionare la scheda **SKU**.
1. Selezionare lo SKU per l'eliminazione e fare clic sul pulsante **Elimina** per lo SKU.
1. [Ripubblicare](./cloud-partner-portal/manage-offers/cpp-publish-offer.md) l'offerta su Azure Marketplace.

Per altre informazioni, vedere [Eliminare le offerte](./cloud-partner-portal/manage-offers/cpp-delete-offer.md).

*App Web (app SaaS, componenti aggiuntivi) & servizi di consulenza:*

1. In centro per i partner selezionare l'icona del punto interrogativo e quindi fare clic su **supporto**.
1. Passare a <https://go.microsoft.com/fwlink/?linkid=844975>.
1. Nella pagina del supporto selezionare il tipo di offerta.
1. Selezionare **Rimuovi** un'offerta pubblicata.
1. Creare un ticket di supporto.
1. Inviare.

*App O365:*

1.  Accedere per <https://sellerdashboard.microsoft.com> con l'account di sviluppo.
1.  Ritirare il componente aggiuntivo.

    > [!NOTE]
    > Le app scompaiono da un elenco esistente dopo 90 giorni.

**Perché le modifiche non vengono riflesse nell'offerta?**

Le modifiche apportate all'interno del centro per i partner vengono aggiornate nel sistema e archiviano i fronti una volta ripubblicata l'offerta. Assicurarsi di aver inviato l'offerta per la pubblicazione dopo qualsiasi modifica.

### <a name="benefits-and-go-to-market-gtm-resources"></a>Vantaggi e risorse go-to-market

**Quali sono i vantaggi go-to-market offerti agli editori che pubblicano inserzioni in Azure Marketplace?**

Azure Marketplace è un trampolino di lancio per le attività go-to-market in collaborazione con Microsoft e la porta di accesso a opportunità di partnership per il co-selling. A tutti i nuovi elenchi di Azure Marketplace viene automaticamente offerto un set di vantaggi per la [commercializzazione senza costi](https://assetsprod.microsoft.com/mpn/marketplace-gtm-benefits.pdf) per favorire la sensibilizzazione delle offerte ai clienti Microsoft. Una volta pubblicata un'offerta, il team di Microsoft GTM contatta l'utente e inizia a offrire i vantaggi.

Visitare [Microsoft Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm) per altre informazioni sui vantaggi go-to-market e su come favorire la crescita del business nel marketplace.

**Dove vengono promosse le soluzioni di Azure Marketplace nelle proprietà Web Microsoft?**

Le soluzioni di Azure Marketplace sono disponibili in Microsoft [portale di Azure]e nel [sito Web di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Gli sviluppatori cloud e i professionisti IT che usano Azure visualizzano le soluzioni dei partner a ogni accesso. Un sottoinsieme di soluzioni dei partner viene anche presentato a rotazione nella [home page di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) e nella [pagina delle soluzioni di Azure](https://azure.microsoft.com/solutions/).

### <a name="billing-and-payments"></a>Fatturazione e pagamenti

**Come si ricevono i pagamenti per le vendite realizzate su Azure Marketplace?**

Tutti i pagamenti da parte di Microsoft vengono elaborati tramite PayPal o bonifico mensile. I pagamenti vengono effettuati entro due mesi dalla data in cui il cliente ha usato il servizio, ma le tempistiche esatte dipendono dallo strumento di pagamento del cliente. Ai clienti che pagano tramite carta di credito viene applicato un periodo di garanzia di 45 giorni.

**Per le soluzioni basate su macchina virtuale acquistate tramite la fatturazione in base all'utilizzo, quando un cliente aumenta o riduce le dimensioni della macchina virtuale sottostante, il prezzo della licenza software cambia di conseguenza?**

Sì, il nuovo prezzo viene fatturato immediatamente.  I prezzi cambiano quando un cliente modifica le dimensioni della macchina virtuale e specifica prezzi diversi nella tabella dei prezzi, basati sulle dimensioni della macchina virtuale.

**La fatturazione per nodo è disponibile per Azure Marketplace?**

Azure Marketplace al momento non supporta la fatturazione per nodo per le macchine virtuali. Gli editori possono comunque determinare una tariffa di fatturazione per nodo con le tariffe delle macchine virtuali Microsoft.  A tale scopo, è necessario moltiplicare il numero di macchine virtuali per il numero di ore di utilizzo e la tariffa oraria.

**Chi è possibile contattare per domande sulla fatturazione o sulla gestione delle offerte?**

Inviare un ticket al [supporto tecnico Microsoft](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=15635).

### <a name="publisher-support"></a>Supporto tecnico per gli editori

**Chi è possibile contattare per problemi di supporto generali relativi ad Azure Marketplace?**

Per il supporto generale delle applicazioni relative all'usabilità o alla risoluzione dei problemi, contattare il supporto tecnico di [partner](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636565784998876007).

Per problemi di fatturazione e sottoscrizione relativi all'acquisto di Azure Marketplace, contattare il [supporto tecnico di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Chi è possibile contattare per domande sulla pubblicazione o sulla gestione delle offerte?**

Vedere la [Guida alla pubblicazione per Azure Marketplace](marketplace-publishers-guide.md) per risorse e informazioni aggiornate sulle domande frequenti. Inoltre, è possibile registrare un ticket con [supporto tecnico Microsoft nel centro per i partner](https://support.microsoft.com/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfname=productselection&prid=16230&forceorigin=esmc&ccsid=636694515623707953).

### <a name="azure-marketplace-for-publishers"></a>Azure Marketplace per gli editori

**Ricerca per categorie definire la mia disponibilità geografica per consentire la vendita in paesi/aree geografiche diverse?**

1.  Nel centro per i partner passare allo SKU a cui si vogliono aggiungere nuovi paesi/aree.  Nei **dettagli dello SKU**passare a **disponibilità paese/area geografica** e fare clic su **Seleziona aree**.

    ![Seleziona aree](media/marketplace-publishers-guide/FAQ-choose-geo.png)

1.  Viene visualizzato un elenco con tutti i paesi o le aree geografiche disponibili per la vendita.  Fare clic sulla casella di controllo accanto a ogni paese/area geografica in cui si vuole rendere disponibile questo SKU. Fare clic su **OK**.

    ![Seleziona elenco paese/area geografica](media/marketplace-publishers-guide/FAQ-select-countries.png)

1.  Infine, per applicare le modifiche all'offerta Live, fare clic su **pubblica**.  

    > [!Note]
    > Per rendere effettive le modifiche sono necessarie 24 ore.

<!---    ![Publish offer](media/marketplace-publishers-guide/FAQ-publish-offer.png) -->

**In che modo un editore può modificare la disponibilità geografica per un'offerta esistente?**

Il server di pubblicazione può modificare un'offerta esistente, selezionare i nuovi paesi/aree geografiche e usare la funzione di download/caricamento del foglio di calcolo per impostare i prezzi.

**In quali paesi/aree geografiche i clienti possono acquistare offerte di Azure Marketplace?**

Azure Marketplace supporta le aree geografiche di acquisto da 141 in base a quanto definito dall'indirizzo di fatturazione del cliente. Vedere [criteri di partecipazione](/legal/marketplace/participation-policy) per un elenco di paesi/aree geografiche.

**Quali valute sono supportate da Azure Marketplace?**

Le transazioni possono essere eseguite nelle 17 valute seguenti: AUD, BRL, CAD, CHF, DKK, EUR, GBP, INR, JPY, KRW, NOK, NZD, RUB, SEK, TWD e USD.

### <a name="pricing-and-payment"></a>Prezzi e pagamento

**Qual è la differenza tra livello gratuito e versione di valutazione gratuita del software?**

Un'offerta di sottoscrizione con livello gratuito rimane sempre gratuita.  Un'offerta di versione di valutazione gratuita del software (Prova adesso) è una sottoscrizione a pagamento, gratuita solo per un periodo di tempo limitato.

**Qual è il processo per convalidare il flusso di acquisto e provisioning end-to-end?**

Durante il processo di pubblicazione, verrà fornito l'accesso a un'anteprima dell'offerta. L'accesso è limitato agli utenti specificati nella scheda Anteprima e si tratta di un'offerta Live non visibile a chiunque. È possibile acquistare questo e testare il processo; Tuttavia, l'importo totale verrà addebitato in base alla configurazione dell'offerta.

Per completare un acquisto a un prezzo molto basso, Microsoft consiglia di pubblicare un piano privato per impostarlo a un prezzo che è possibile accettare come costo dei test. Zero è supportato, ma non rifletterà l'esperienza completa come una sottoscrizione a pagamento.

**Microsoft fornirà un rimborso al di fuori delle politiche standard?**

Sì, dopo la richiesta tramite il ticket di supporto, Microsoft elaborerà i crediti per il cliente se lo si ritiene appropriato.

## <a name="next-steps"></a>Passaggi successivi

Visitare la pagina della [Guida dell'editore di Azure Marketplace e AppSource](/azure/marketplace/marketplace-publishers-guide) .


[Portale di Azure]: https://portal.azure.com
