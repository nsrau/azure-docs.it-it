---
title: Contratti Azure EA e modifiche
description: Questo articolo illustra l'impatto dei contratti Azure EA e delle relative modifiche su Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 35c12118cb906b312114ee7e99e299714e397914
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223979"
---
# <a name="azure-ea-agreements-and-amendments"></a>Contratti Azure EA e modifiche

Questo articolo descrive l'impatto che possono avere i contratti Azure EA e le relative modifiche sull'accesso, l'uso e il pagamento dei servizi di Azure.

## <a name="amendment-status"></a>Stato delle modifiche

Eventuali modifiche per i clienti di Azure devono essere gestite tramite il partner o il team degli account Microsoft ed essere elaborate tramite il centro operativo locale. Se si ritiene che una modifica non sia stata elaborata, rivolgersi al partner, al consulente software o al team degli account Microsoft.

## <a name="enrollment-provisioning-status"></a>Stato di provisioning della registrazione

La data di inizio di un nuovo impegno monetario è definita dalla data in cui è stato elaborato dal centro operativo locale. Poiché gli ordini di impegno monetario tramite Azure EA Portal vengono elaborati nel fuso orario UTC, può verificarsi un ritardo se l'ordine di acquisto dell'impegno monetario è stato elaborato in un'area diversa. La data di inizio della copertura nell'ordine di acquisto in https://www.explore.ms indica l'inizio dell'impegno monetario. La data di inizio della copertura corrisponde a quella in cui l'impegno monetario viene visualizzato in Azure EA Portal.

## <a name="special-pricing-and-azure-commitment-discount-amendments"></a>Prezzi speciali e modifiche dello Sconto per impegno in Azure

Per prezzi speciali e modifiche dello Sconto per impegno in Azure, è necessario contattare il team dell'account. Il team di Azure Ops può applicare lo Sconto per impegno in Azure dopo che una modifica è stata elaborata dal business desk e richiesta al team di Azure Ops. Lo Sconto per impegno in Azure viene applicato solo al mese corrente. Tutti i mesi precedenti vengono compensati con credito retrodatato. 

## <a name="support-offer-not-provisioned"></a>Offerta di supporto senza provisioning

È possibile ordinare il supporto Standard o Pro-Direct acquistando SKU specifici del supporto tecnico. L'ordine è analogo a un ordine di acquisto di SKU monetari di Azure con contratto Enterprise Agreement. Ad esempio, 6QK-00001. I numeri degli SKU di supporto sono W6T-00002 (Pro-Direct) e W6T-00003 (Standard). Verificare le offerte di supporto correnti prima di acquistare SKU di supporto.

Per eseguire il provisioning dell'offerta di supporto, la registrazione deve includere almeno un account attivo.

 L'[offerta del piano di supporto tecnico per i contratti Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/) di Azure è disponibile per alcuni clienti.

## <a name="enrollment-status"></a>Stato della registrazione

A una registrazione possono essere associati uno dei valori di stato seguenti. Ogni valore determina la modalità di utilizzo e accesso a una registrazione. Lo stato della registrazione determina la fase in cui si trova la registrazione. Indica se è necessario attivare la registrazione prima di poterla usare oppure se il periodo iniziale è scaduto e viene addebitata l'eccedenza di utilizzo.

**Pending** (In sospeso): l'amministratore della registrazione deve accedere ad Azure EA Portal. Dopo l'accesso la registrazione passa allo stato **Active** (Attiva).

**Active** (Attiva): la registrazione è accessibile e utilizzabile. È possibile creare account e sottoscrizioni in Azure EA Portal. La registrazione rimane attiva fino alla data di fine del contratto Enterprise Agreement.

**Indefinite Extended Term** (Periodo di validità esteso a tempo indeterminato): questo stato si verifica al raggiungimento della data di fine del contratto Enterprise Agreement. Prima che la registrazione EA raggiunga la data di fine del contratto Enterprise Agreement, l'amministratore della registrazione deve decidere se:

- Rinnovare la registrazione aggiungendo impegno monetario aggiuntivo
- Trasferire la registrazione esistente a una nuova
- Eseguire la migrazione al programma Microsoft Online Subscription Program
- Confermare la disabilitazione di tutti i servizi associati alla registrazione

**Expired** (Scaduta): la registrazione EA scade quando raggiunge la data di fine del Contratto Enterprise. Il cliente EA è escluso dal periodo di validità esteso e tutti i relativi servizi vengono disabilitati.

A partire dal 1° agosto 2019 i nuovi moduli di rifiuto esplicito non sono accettati per i clienti commerciali di Azure. Tutte le registrazioni passano invece allo stato Indefinite Extended Term (Periodo di validità esteso a tempo indeterminato). Se si vuole interrompere l'uso dei servizi di Azure, chiudere la sottoscrizione in [Azure EA Portal](https://portal.azure.com). In alternativa, il partner può inviare una richiesta di terminazione all'indirizzo https://www.explore.ms. Non sono previste modifiche per i clienti con tipi di contratto per enti pubblici.

**Transferred** (Trasferita): questo stato viene applicato alle registrazioni i cui account e servizi associati sono trasferiti a una nuova registrazione. Le registrazioni non vengono trasferite automaticamente se viene generato un nuovo numero di registrazione durante il rinnovo. Per il trasferimento automatico il numero di registrazione precedente deve essere incluso nella richiesta di rinnovo del cliente.

## <a name="partner-markup"></a>Ricarico del partner

In Azure EA Portal la funzione Partner Price Markup (Ricarico prezzi partner) consente di ottimizzare la creazione di report sui costi per i clienti. Azure EA Portal mostra i prezzi e l'utilizzo configurati dai partner per i clienti.

La funzionalità di ricarico consente agli amministratori dei partner di aggiungere una percentuale di ricarico ai Contratti Enterprise indiretti. La percentuale di ricarico si applica a tutte le informazioni relative ai servizi gestiti da Microsoft in Azure EA Portal, ad esempio tariffe dei contatori, impegni monetari e ordini. Dopo la pubblicazione del ricarico da parte del partner, il cliente potrà visualizzare i costi di Azure in Azure EA Portal, ad esempio riepilogo dell'utilizzo, elenchi prezzi e report sull'utilizzo scaricati.

A partire da settembre 2019 i partner possono applicare il ricarico in qualsiasi momento durante un periodo di validità senza attendere la scadenza successiva del periodo di validità.

Microsoft non accederà né utilizzerà il ricarico specificato e i prezzi associati per qualsiasi scopo, tranne nei casi espressamente autorizzati dal partner di canale.

### <a name="how-the-calculation-works"></a>Come funziona il calcolo

Il Licensing Solution Provider (LSP) specifica un singolo valore percentuale in EA Portal.  Tutte le informazioni commerciali sul portale verranno incrementate in base alla percentuale specificata dall'LSP. Esempio:

- Il cliente firma un Contratto Enterprise con un impegno monetario di 100.000 USD.
- La tariffa del contatore per il Servizio A è 10 USD all'ora.
- L'LSP imposta una percentuale di ricarico del 10% in EA Portal.
- L'esempio seguente mostra in che modo le informazioni commerciali saranno visibili al cliente:
    - Saldo monetario: 110.000 USD.
    - Tariffa del contatore per il Servizio A: 11 USD all'ora.
    - Informazioni sull'utilizzo o l'hosting del Servizio A se usato per 100 ore: 1.100 USD.
    - Saldo monetario disponibile per il cliente dopo la deduzione dell'importo relativo all'utilizzo del Servizio A: 108.900 USD.

### <a name="when-to-use-a-markup"></a>Quando usare un ricarico

Usare la funzionalità di ricarico se si imposta la stessa percentuale di ricarico per TUTTE le transazioni commerciali nell'ambito del Contratto Enterprise, ovvero se si applica il ricarico alle informazioni sull'impegno monetario, alle tariffe dei contatori, alle informazioni sugli ordini e così via.

Non usare la funzionalità di ricarico nei casi seguenti:
- Si applicano percentuali diverse per l'impegno monetario e le tariffe dei contatori.
- Si applicano percentuali diverse per i contatori.

Se si applicano percentuali diverse per i contatori, è consigliabile sviluppare una soluzione personalizzata basata sulla chiave API, che può essere fornita dal cliente, per eseguire il pull dei dati sull'utilizzo e fornire report.

### <a name="other-important-information"></a>Altre informazioni importanti

Questa funzionalità ha lo scopo di fornire una stima del costo di Azure al cliente finale. L'LSP è responsabile di tutte le transazioni finanziarie con il cliente in base al Contratto Enterprise.

Assicurarsi di esaminare le informazioni commerciali, ovvero i dati sul saldo monetario, il listino prezzi e così via, prima di rendere pubblici al cliente finale i prezzi con il ricarico applicato.

### <a name="how-to-add-a-price-markup"></a>Come aggiungere un ricarico ai prezzi

**Passaggio 1: Aggiungere la percentuale di ricarico**

1. In Enterprise Portal fare clic su **Report** nel riquadro di spostamento a sinistra.
1. In _Riepilogo utilizzo_ fare clic sulla voce **Markup** (Ricarico) di colore azzurro.
1. Immettere la percentuale di ricarico (compresa tra -100 e 100) e fare clic su **Anteprima**.


**Passaggio 2: Rivedere e convalidare**

Rivedere il prezzo di ricarico in _Riepilogo utilizzo_ per il periodo di impegno nella visualizzazione del cliente. Il prezzo Microsoft rimarrà disponibile nella visualizzazione del partner. È possibile attivare o disattivare le visualizzazioni usando l'interruttore relativo alle "persone" per il ricarico del partner in alto a destra.

1. Rivedere i prezzi nell'elenco.
1. Prima di procedere alla pubblicazione è possibile apportare modifiche selezionando **Modifica** nella scheda _View Usage Summary > Customer View_ (Visualizza riepilogo utilizzo > Visualizzazione cliente).  
  Ai prezzi dei servizi ai saldi dell'impegno verranno applicate le stesse percentuali di ricarico. Se si prevede di applicare percentuali diverse per il saldo monetario e le tariffe dei contatori oppure per servizi diversi, non usare questa funzionalità.

**Passaggio 3: Pubblicare**

Dopo aver esaminato e convalidato i prezzi, fare clic su **Pubblica**.
  
I prezzi con ricarico saranno disponibili per gli amministratori dell'organizzazione immediatamente dopo la selezione del comando di pubblicazione. Non è possibile apportare modifiche al ricarico. Per modificarlo è necessario disabilitarlo e ricominciare dal passaggio 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Per quali registrazioni è abilitato il ricarico?

Per verificare se in una registrazione è stato pubblicato un ricarico, fare clic su **Gestisci** nel riquadro di spostamento a sinistra e quindi fare clic sulla scheda **Registrazione**. Selezionare la casella della registrazione da verificare e visualizzare lo stato del ricarico in _Dettagli della registrazione_. Verrà visualizzato lo stato corrente della funzionalità di ricarico per il Contratto Enterprise, che può essere come Disabilitato, Anteprima o Pubblicato.

### <a name="how-can-the-customer-download-usage-estimates"></a>Come può il cliente scaricare le stime sull'utilizzo?

Dopo la pubblicazione del ricarico del partner, il cliente indiretto avrà accesso ai file CSV pubblicati mensilmente su saldi e addebiti e ai file CSV con i dettagli relativi all'utilizzo, che includono la frequenza d'uso delle risorse e il costo esteso.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>In qualità di partner, come è possibile applicare un ricarico a clienti con Contratto Enterprise esistenti che in precedenza erano gestiti da un altro partner?
I partner possono usare la funzionalità di ricarico (in Azure EA) dopo l'elaborazione di una modifica del partner di canale. Non è necessario attendere fino all'anniversario successivo.


## <a name="resource-commitment-and-requesting-quota-increases"></a>Impegno delle risorse e richiesta di aumenti di quota

**Il sistema applica le seguenti quote predefinite per ogni sottoscrizione:**

| **Risorsa** | **Quota predefinita** | **Commenti** |
| --- | --- | --- |
| Istanze di calcolo di Microsoft Azure | 20 istanze di calcolo piccole simultanee o un numero equivalente di istanze di calcolo di altre dimensioni. | La tabella seguente illustra come calcolare il numero equivalente di istanze rispetto alle istanze piccole:<ul><li> Extra Small: equivalente a 1 istanza piccola </li><li> Small: equivalente a 1 istanza piccola </li><li> Medium: equivalente a 2 istanze piccole </li><li> Large: equivalente a 4 istanze piccole </li><li> Extra Large: equivalente a 8 istanze piccole </li> </ul>|
| VM Istanze di calcolo di Microsoft Azure v2 | EA: 350 core | VM GA IaaS v2:<ul><li> Gruppo A0\_A7: 350 core </li><li> Gruppo B\_A0\_A4: 350 core </li><li> Gruppo A8\_A9: 350 core </li><li> Gruppo DF: 350 core</li><li> Gruppo GF: 350 core </li></ul>|
| Servizi ospitati di Microsoft Azure | 6 servizi ospitati | Il numero di servizi ospitati non può superare il limite di sei per singola sottoscrizione. Se sono necessari altri servizi ospitati, aggiungere altre sottoscrizioni. |
| Archiviazione di Microsoft Azure | 5 account di archiviazione, ciascuno con una dimensione massima di 100 TB. | È possibile aumentare il numero di account di archiviazione fino a 20 per sottoscrizione. Se sono necessari altri account di archiviazione, aggiungere altre sottoscrizioni. |
| SQL Azure | 149 database di entrambi i tipi, ovvero Web Edition o Business Edition. |   |
| Controllo dell’accesso | 50 spazi dei nomi per account. 100 milioni di transazioni di Controllo di accesso al mese |   |
| Bus di servizio | 50 spazi dei nomi per account. 40 connessioni del bus di servizio | I clienti che acquistano connessioni del bus di servizio tramite i pacchetti di connessione avranno quote uguali al punto medio tra il pacchetto di connessioni acquistato e la quantità del pacchetto di connessioni più alto successivo. I clienti che scelgono un pacchetto da 500 avranno una quota pari a 750. |

## <a name="resource-commitment"></a>Impegno delle risorse

Microsoft fornirà servizi almeno fino al livello dell'utilizzo associato incluso nell'impegno mensile acquistato (impegno di servizio), ma tutti gli altri incrementi in termini di livelli di utilizzo delle risorse dei servizi, come l'aumento del numero delle istanze di calcolo in esecuzione o della capacità di archiviazione in uso, sono soggetti alla disponibilità di tali risorse.

Qualsiasi quota sopra descritta non costituisce un impegno di servizio. Al fine di determinare il numero di istanze di calcolo piccole simultanee (o equivalenti) che verranno fornite da Microsoft come parte dell'impegno di servizio, il numero di ore di istanze di calcolo piccole impegnate, acquistate per un mese, verrà diviso per il numero di ore del mese più breve dell'anno, ovvero febbraio (672 ore).

## <a name="requesting-a-quota-increase"></a>Richiesta di un aumento di quota

È possibile richiedere un aumento di quota in qualsiasi momento inviando una [richiesta online](https://g.microsoftonline.com/0WAEP00en/6). Per elaborare la richiesta, fornire le informazioni seguenti:

- L'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato al proprietario dell'account della sottoscrizione. Corrisponde all'indirizzo di posta elettronica usato per accedere al portale di Microsoft Azure per gestire le sottoscrizioni. Verificare anche che l'account sia associato a una registrazione EA.
- Le risorse e la quantità per cui si vuole richiedere un aumento di quota.
- L'ID sottoscrizione del portale per sviluppatori di Azure associato al servizio.
  - Per informazioni su come ottenere l'ID sottoscrizione, [contattare il servizio di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="azure-compute-pre-purchase-plan-cpp"></a>Piano di acquisto anticipato delle risorse di calcolo di Azure (CPP)

Il piano di acquisto anticipato delle risorse di calcolo di Azure (CPP, noto in precedenza come Azure Compute Promo) è un'offerta che consente ai clienti con carichi di lavoro stabili e prevedibili di acquistare in anticipo capacità di calcolo di Azure per 12 mesi usufruendo di sconti significativi. Questo piano è disponibile per il Contratto Enterprise ed è visibile in EA Portal.

Gli SKU di piano CPP devono essere ordinati per un tipo specifico di macchina virtuale in un data center specifico. Allo stato attuale di questa funzionalità, non viene fornito un elenco di tutti gli acquisti di CPP effettuati da un cliente e pertanto non è disponibile alcun elenco di crediti in attesa. Tuttavia, quando si configurano i tipi di macchina virtuale acquistati nel data center in cui sono stati acquistati, l'allocazione mensile sarà visibile nel report di riepilogo sull'utilizzo in EA Portal.

La quantità acquistata in anticipo sarà riportata come credito nella colonna "Unità incluse" e l'utilizzo correlato non comporterà alcun addebito finché le unità incluse non saranno state completamente utilizzate. L'eventuale utilizzo aggiuntivo verrà addebitato in base alle tariffe concordate nell'elenco dei prezzi standard per i clienti del Contratto Enterprise.

La modalità di acquisto dei crediti si basa sul numero di macchine virtuali di una classe per ogni mese. Ad esempio: se sono state acquistate 70 macchine virtuali A2 all'anno nell'area Stati Uniti orientali 2, in EA Portal non saranno visibili le stesse informazioni ricevute al momento dell'acquisto.

Per le macchine virtuali su base mensile sono previste a 744 ore al mese. Convertendo l'intero acquisto in ore si avrebbe un numero di ore pari a 70 per 744, ovvero 52.080 ore acquistate di macchine virtuali A2 al mese. Poiché nel report di riepilogo sull'utilizzo in EA Portal le macchine virtuali A2 sono riportate in base a incrementi di 100 ore, si vedrà un credito di 520,8 (52.080/100) "Unità incluse" al mese, oltre alle unità utilizzate fino a quel momento nel mese. Gli addebiti per l'utilizzo dovrebbero risultare nulli finché le unità mensili utilizzate non superano le unità mensili incluse.

Per tipi diversi di macchine virtuali vengono usate unità di misura diverse. Ad esempio, per le macchine virtuali D3 e D4, nel report di riepilogo sull'utilizzo viene usata un'unità di misura di 10 ore. Di conseguenza, per un acquisto di 70 macchine virtuali di questo tipo verranno visualizzate 5208 unità incluse al mese (52.080/10).

In generale, la formula seguente consente di calcolare il numero di macchine virtuali acquistate in base alle rispettive unità di misura:

<center><b> ("Quantità inclusa" * "Unità di misura") / 744 </b></center>

## <a name="plan-skus"></a>SKU di piano

Gli SKU di piano offrono la possibilità di acquistare un gruppo di servizi integrati a una tariffa scontata. Gli SKU di piano sono progettati per consentire l'integrazione di più offerte e gruppi di prodotti e offrire così un maggiore risparmio sui costi.

Un esempio è dato dalla sottoscrizione di Operations Management Suite (OMS). OMS offre un modo semplice per accedere a un set completo di funzionalità di gestione basate sul cloud, tra cui analisi, configurazione, automazione, sicurezza, backup e ripristino di emergenza. Le sottoscrizioni di OMS includono diritti sui componenti di System Center in modo da fornire una soluzione completa per gli ambienti cloud ibridi.

Gli amministratori dell'organizzazione possono assegnare proprietari di account per effettuare il provisioning degli SKU di piano acquistati in precedenza in Enterprise Portal seguendo questa procedura:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Visualizzare l'elenco dei prezzi per verificare la quantità inclusa

1. Accedere come amministratore dell'organizzazione.
1. Fare clic su **Report** nel riquadro di spostamento a sinistra.
1. Fare clic sulla scheda **Elenco prezzi**.
1. Fare clic sull'icona di download nell'angolo in alto a destra.
1. Trovare i numeri di parte degli SKU di piano corrispondenti impostando un filtro sulla colonna "Quantità inclusa" e selezionare i valori maggiori di "0".

### <a name="provision-the-plan-skus"></a>Effettuare il provisioning degli SKU di piano

Gli amministratori dell'organizzazione possono aggiungere nuovi proprietari di account, associare un proprietario di account esistente o richiedere ai proprietari di account esistenti di accedere a Azure EA Portal per effettuare il provisioning degli SKU di piano acquistati in precedenza in Azure EA Portal seguendo questa procedura.  

**Aggiunta di un nuovo proprietario di account (completato dall'amministratore EA):**

1. Da Azure EA Portal selezionare **Gestisci** nel riquadro di spostamento a sinistra.
1. Fare clic sulla scheda **Account**.
1. Nella pagina Account fare clic su **+Aggiungi account**.
1. Selezionare un reparto o lasciare come non assegnato.
1. Selezionare il tipo di autenticazione desiderato.
1. Specificare un nome descrittivo da usare per identificare l'account nei report.
1. Immettere l'indirizzo di posta elettronica del proprietario da associare al nuovo account.
1. Confermare l'indirizzo di posta elettronica che si vuole associare al nuovo account.
1. Fare clic su **Aggiungi**.
1. È possibile aggiungere un altro account facendo clic su **Aggiungi un altro account** o sul pulsante **Aggiungi** nell'angolo in basso a destra della barra degli strumenti a sinistra.
1. A questo punto il proprietario dell'account può eseguire l'accesso per aggiungere le sottoscrizioni pertinenti per il provisioning degli SKU di piano

**Associare un proprietario di account esistente:**

1. In Enterprise Portal fare clic su **Gestisci**.
1. Fare clic sulla scheda **Account**.
1. Fare clic su **+Aggiungi account**. Immettere l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato all'account esistente.
1. Confermare l'account Microsoft oppure l'account aziendale o dell'istituto di istruzione associato all'account esistente.
1. Specificare un nome da usare per identificare l'account nei report.
1. Fare clic su **Aggiungi**.
1. È possibile aggiungere un altro account selezionando di nuovo l'opzione **+Aggiungi account** oppure tornare alla home page selezionando il pulsante **Amministrazione**.  
1. Se si fa clic per visualizzare la scheda Account, l'account appena aggiunto verrà visualizzato con stato "In sospeso". Lo stato diventerà "Attivo" dopo che il proprietario dell'account avrà eseguito l'accesso a EA Portal per la prima volta.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Proprietari di account nuovi o esistenti per creare nuove sottoscrizioni

**Passaggio 1: Accedere all'account**
1. In Azure EA Portal selezionare la scheda **Gestisci** e passare a **Sottoscrizione** nel menu in alto.
1. Verificare di aver eseguito l'accesso come proprietario dell'account.
1. Fare clic su **+Aggiungi sottoscrizione**.
1. Fare clic su **Acquista**.

La prima volta che si aggiunge una sottoscrizione a un account, è necessario specificare le informazioni di contatto. Quando si aggiungono sottoscrizioni successive, le informazioni di contatto vengono inserite automaticamente.

La prima volta che si aggiunge una sottoscrizione all'account, viene chiesto di accettare il Contratto di Sottoscrizione Microsoft Online e un piano tariffario. Queste sezioni NON sono applicabili ai clienti del Contratto Enterprise, ma sono attualmente necessarie per il provisioning della sottoscrizione. L'emendamento della registrazione del Contratto Microsoft Azure Enterprise prevale sulle informazioni riportate in queste sezioni e la relazione contrattuale non cambia. Selezionare la casella che indica di accettare le condizioni.

**Passaggio 2: Aggiornare il nome della sottoscrizione**

Tutte le nuove sottoscrizioni vengono aggiunte con il nome predefinito "Microsoft Azure Enterprise". È importante aggiornare il nome della sottoscrizione per distinguerlo dalle altre sottoscrizioni all'interno della registrazione Enterprise e assicurarsi che sia riconoscibile sui report a livello aziendale.

Fare clic su **Sottoscrizioni**, selezionare la sottoscrizione creata e quindi fare clic su **Modifica i dettagli della sottoscrizione**.

Aggiornare il nome della sottoscrizione e l'amministratore del servizio, quindi fare clic sul segno di spunta. Il nome della sottoscrizione verrà visualizzato nei report e come nome del progetto associato alla sottoscrizione nel portale di sviluppo.
Per propagare le nuove sottoscrizioni nell'elenco possono essere necessarie fino a 24 ore.

Solo i proprietari di account possono visualizzare e gestire le sottoscrizioni.

### <a name="troubleshooting"></a>risoluzione dei problemi

**Proprietario dell'account con stato In sospeso**

Quando vengono aggiunti nuovi proprietari di account a una registrazione per la prima volta, il relativo stato risulta "In sospeso". Non appena riceve il messaggio di posta elettronica di benvenuto per l'attivazione, il proprietario può accedere per attivare il proprio account. Per effetto di questa attivazione, lo stato dell'account passerà da "In sospeso" ad "Attivo".

**Addebiti per utilizzo dopo l'acquisto di SKU di piano**

Questo scenario si verifica quando il cliente ha distribuito servizi con il numero di registrazione errato o ha selezionato i servizi non corretti.

Per verificare che la distribuzione venga eseguita in base alla registrazione corretta, è possibile controllare le informazioni sulle unità incluse tramite l'elenco dei prezzi. Accedere come amministratore dell'organizzazione, fare clic su **Report** nel riquadro di spostamento a sinistra e quindi selezionare la scheda **Elenco prezzi**. Fare clic sull'icona di download nell'angolo in alto a destra, trovare i numeri di parte degli SKU di piano corrispondenti impostando un filtro sulla colonna "Quantità inclusa" e quindi selezionare i valori maggiori di "0".

Verificare che il piano OMS sia visualizzato nell'elenco dei prezzi tra le unità incluse. Se non sono presenti unità incluse per il piano OMS nella registrazione, il piano OMS potrebbe trovarsi in un'altra registrazione. Contattare il supporto di Azure Enterprise Portal all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Se le unità incluse per i servizi nell'elenco dei prezzi non corrispondono a quelle distribuite, ad esempio Operational Insights Premium Data Analyzed rispetto a Operational Insights Standard Data Analyzed, significa che potrebbero essere stati distribuiti servizi che non sono coperti dal piano. Contattare il supporto di Azure Enterprise Portal all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) per ricevere assistenza.

**Servizi SKU di piano di cui è stato effettuato il provisioning in una registrazione non valida**

Se si hanno più registrazioni e i servizi sono stati distribuiti con un numero di registrazione errato, che non include un piano OMS, contattare il supporto di Azure Enterprise Portal all'indirizzo [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure EA Portal, vedere [Introduzione ad Azure EA Portal](billing-ea-portal-get-started.md).
- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
