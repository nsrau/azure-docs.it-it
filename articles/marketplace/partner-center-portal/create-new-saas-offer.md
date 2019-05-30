---
title: Creare una nuova offerta SaaS nel Marketplace commerciale
description: Come creare un nuovo Software come servizio (SaaS) offerta per la visualizzazione in elenco o di vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) tramite il portale Marketplace commerciale su Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9750aa924ba4b6e4e5f3a51e5b34531d3fab0a6e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243335"
---
# <a name="create-a-new-saas-offer"></a>Creare una nuova offerta SaaS

Per avviare la creazione di Software come un servizio (SaaS) offre, assicurarsi che si primo [creare un account del centro per i Partner](./create-account.md) e aprire il [dashboard commerciale Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la **offre** della scheda selezionata. 

![Dashboard del Marketplace nel centro per i Partner commerciali](./media/commercial-marketplace-offers.png)

Selezionare il + **creare una nuova...** pulsante e quindi selezionare il **Software come un servizio** voce di menu. 

Se si seleziona uno degli altri tipi di offerta, si verrà reindirizzati a versioni precedenti [portale Cloud Partner](https://cloudpartner.azure.com/).  Solo le offerte SaaS sono disponibili nel portale commerciale Marketplace nel centro per i Partner in questo momento. 

![Creare finestra offerta nel centro per i Partner](./media/new-offer.png)


Il **nuova offerta** verrà visualizzata la finestra di dialogo. ![Finestra di dialogo nuova offerta](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>Nome e ID offerta

- **Offer ID** (ID offerta): Creare un identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del marketplace e i modelli di Azure Resource Manager (se applicabile). ID offerta deve essere minuscola, caratteri alfanumerici (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). Questo è limitato a 50 caratteri e non può essere aggiornato dopo aver selezionato creare.  
Esempio: test-offerta di-1
<br>Risultante nell'URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Nome offerta**: Il nome ufficiale dell'offerta dell'applicazione SaaS, coerenza per le pubblicazioni, annunci e siti web.  Questo nome può essere si.  Nome non può contenere spazi vuoti, emoji (a meno che non sono il simbolo del marchio o copyright) dell'offerta e deve essere limitato a 50 caratteri.
<br>Esempio: Offerta di test 1&#8482;

Selezionare **Create**.  Un' **panoramica dell'offerta** pagina viene creata per questa offerta.  

![Panoramica di offerta nel centro per i Partner](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>Panoramica dell'offerta

Il **panoramica dell'offerta** pagina include: 

- Il **lo stato di pubblicazione** Visualizza una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta e quanto tempo ogni passaggio necessario per completare. Icone di passaggio pubblicazione incompleto risulterà disattivate. 

- Il **panoramica dell'offerta** menu contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco delle operazioni verrà cambiare a seconda della selezione effettuata per l'offerta.  
    - Se l'offerta è una bozza – Elimina bozza 
    - Se l'offerta è live – Stop vendere offerta 
    - Se l'offerta è disponibile in anteprima-Go-live 
    - Se è stata completata la pubblicazione sign out-Annulla pubblicazione

## <a name="offer-setup"></a>Programma di installazione di offerta

Il **offrono il programma di installazione** scheda richiede le informazioni seguenti. Selezionare **salvare** dopo aver completato questi campi.

- **Vuoi vendere tramite Microsoft?** (Sì/No)
    - **Sì**, vuoi vendere la tua offerta tramite il Microsoft, presso Microsoft che ospita le transazioni di marketplace dell'utente; o 
    - **No**, si vuole semplicemente elencare la tua offerta tramite i Marketplace, l'elaborazione di eventuali transazioni monetarie indipendentemente da Microsoft.    

### <a name="sell-through-microsoft"></a>Vendita tramite Microsoft

Vendita tramite il Microsoft offre una migliore individuazione dei clienti e acquisizione, consente a Microsoft per le transazioni di marketplace di host per tuo conto e sfrutta le funzionalità disponibili a livello globale e-commerce di Microsoft.

#### <a name="saas-offer-requirements"></a>Requisiti di offerta SaaS

Per visualizzare un elenco del Software come un servizio (SaaS) offerte del Marketplace nel centro per i Partner commerciali, devono essere soddisfatti i criteri seguenti:

- L'offerta deve essere compatibile con client di Azure. (Spesso le app SaaS anch sono esse ospitate in Azure per ottimizzare le prestazioni e compatibilità, ma questo non è un requisito.) 
- È necessario utilizzare l'offerta [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) per la gestione delle identità e autenticazione.
- È necessario utilizzare l'offerta [API SaaS evasione](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-fulfillment-api-v2) per l'integrazione con Azure Marketplace.

#### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura
Per le offerte SaaS, come server di pubblicazione, tenere in considerazione per spese di utilizzo dell'infrastruttura di Azure e i costi di licenza software come un elemento di costo singolo. Questo costo è rappresentato come una tariffa mensile fissa al cliente. Utilizzo dell'infrastruttura di Azure gestito e fatturata all'utente, il partner, direttamente. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. 

Software di costi di licenza vengono presentati come una tariffa di sottoscrizione mensile, ricorrente basata su sito tariffa fissa e non vengono fatturate a consumo o in base al consumo.

|**Costo della licenza**|**100 dollari al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Cliente verrà addebitato il costo da Microsoft|$100.00 al mese (server di pubblicazione tenere in considerazione per eventuali costi di infrastruttura sostenuti o pass-through nella tariffa di licenza)|

- In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
- I partner che sono qualificati per il **ridotto tariffa del servizio Marketplace** visualizzeranno una tariffa ridotta su SaaS offre da maggio 2019 fino a giugno 2020. In questo scenario Microsoft addebita $100.00 la licenza del software e paga out $90,00 al server di pubblicazione.

> [!NOTE]
> **Ridotto tariffa del servizio Marketplace**: Per alcuni SaaS offre che è stata pubblicata in Marketplace commerciale, Microsoft consente di ridurre la tariffa del servizio Marketplace dal 20%, come descritto nel contratto per la pubblicazione Microsoft, al 10%. Affinché l'offerta qualificare, almeno una delle tue offerte deve essere stata definita da Microsoft come IP CO-Selling pronta o CO-Selling IP la priorità.  Idoneità deve essere soddisfatti almeno cinque (5) giorni prima della fine di ogni mese di calendario per ricevere questa tariffa del servizio Marketplace ridotto per il mese.  La tariffa del servizio Marketplace ridotte non è applicabile per le macchine virtuali, le app gestite o qualsiasi altro prodotto reso disponibile tramite Marketplace commerciale.  La tariffa del servizio Marketplace ridotto sarà disponibile solo per le offerte completo per gli addebiti delle licenze raccolti da Microsoft tra il 1 maggio 2019 e il 30 giugno 2020.  Trascorso questo periodo, la tariffa del servizio Marketplace restituirà al relativo al normale. 

|**Microsoft addebita**|**100 dollari al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS completo, Microsoft paga il 90% del costo della licenza*|$ 80,00 al mese <br>*$* 90,00 al mese *|


#### <a name="csp-program-opt-in"></a>CSP programma acconsenti esplicitamente
Il [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programma consente di offerte di software raggiungere milioni di clienti Microsoft qualificati con il minimo investimento marketing e vendita.

- **Canali: Pubblicare un'offerta disponibile nel programma CSP** (casella di controllo)

Bacino di utenza rendere l'offerta disponibile nel programma CSP consente ai provider di soluzioni Cloud vendere un prodotto come parte di una soluzione in dotazione ai propri clienti. 

### <a name="list-through-microsoft"></a>Elenco attraverso Microsoft

Promuovere la propria azienda con Microsoft tramite la creazione di un'inserzione di marketplace. Se si sceglie di elencare solo l'offerta e non transact tramite mezzi di Microsoft che Microsoft non fanno parte direttamente nelle transazioni di licenza software. Non sono previste commissioni di transazione associato e il server di pubblicazione trattiene il 100% di qualsiasi software raccolti dal cliente spese di licenza. Tuttavia, è responsabile del supporto di tutti gli aspetti della transazione di licenza software, inclusi ma non solo per il server di pubblicazione: ordine di evasione degli ordini, misurazione, fatturazione, la fatturazione, pagamento e raccolta. 

- **La modalità potenziali clienti per interagire con questa offerta di elenco?**

##### <a name="get-it-now-free"></a>Scarica adesso (gratuito)
Elencare gratuita dell'offerta ai clienti, fornendo un URL valido (che inizia con http o https) in cui è possibile accedere all'app.  Ad esempio: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>Versione di prova gratuita
Elenco dell'offerta ai clienti per un periodo di valutazione gratuito, fornendo un URL valido (che inizia con http o https) in cui è possibile accedere all'app.  Ad esempio: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>Contact me (Contattami)
Raccogliere le informazioni di contatto dei clienti tramite la connessione di sistema Customer Relationship Management (CRM). Il cliente verrà richiesto per l'autorizzazione per condividere le proprie informazioni. Questi dettagli cliente, con il nome dell'offerta, ID e origine marketplace in cui sono disponibili l'offerta, verranno inviati al sistema CRM che è stato configurato. Per altre informazioni sulla configurazione proprio sistema CRM, vedere [gestione dei lead Connect](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Marketplace di esempio inserzione dell'offerta

![Inserzione di marketplace esempio con le note](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Un test drive è un ottimo modo per presentare l'offerta ai clienti potenziali di concedere loro l'opzione ' provare prima dell'acquisto ', determinando un aumento di conversione e la generazione di lead altamente qualificati. [Altre informazioni sui test Drive.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Abilitare una test drive** (casella di controllo) 

Abilita unità di test, dovrà configurare un ambiente demo per i clienti a provare l'offerta per un periodo di tempo fisso. 

### <a name="type-of-test-drive"></a>Tipo di test unità

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usare solo le risorse di Azure.
- **[Dynamics 365 per Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft ospita e gestisce il servizio test (tra cui provisioning e distribuzione) per una Business Central sistema enterprise resource planning (Finanza, operazioni, supply chain, CRM e così via.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft ospita e gestisce il servizio test (tra cui provisioning e distribuzione) per un sistema di coinvolgimento dei clienti (sales, service, servizio di progetto, assistenza sul campo e così via).  
- **[Dynamics 365 per le operazioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft ospita e gestisce il servizio test (tra cui provisioning e distribuzione) per un Finance and Operations sistema enterprise resource planning (Finanza, operazioni, produzione, supply chain e così via). 
- **[App per la logica](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : Un modello di distribuzione che include tutte le architetture delle soluzioni complesse. I prodotti personalizzati devono usare questo tipo di Test Drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Un collegamento incorporato a un dashboard personalizzato. Prodotti che desiderano dimostrare che un'interattivi di Power BI visual devono usare questo tipo di Test Drive. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse di unità di test aggiuntivi
- [Test unità tecniche consigliate](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Il test Drive di procedure consigliate di Marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testare una cercapersone Panoramica di unità](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Connettere la gestione dei lead

Connettersi con i clienti direttamente tramite l'inserzione dell'offerta nel Marketplace di e all'associazione sistema Customer Relationship Management (CRM) in modo da poter ricevere informazioni di contatto cliente immediatamente dopo che un cliente esprime interesse o distribuisce un il prodotto.

- **Scegliere una destinazione del lead** (menu di scelta rapida): Fornire i dettagli della connessione al sistema CRM in cui si desidera contattarci per inviare i lead. 

Centro per i partner supporta i seguenti sistemi CRM per la gestione dei lead. Selezionare il collegamento per le istruzioni di installazione.

- Azure Blob offrono posta elettronica di contatto, nome del contenitore e stringa di connessione di account di archiviazione. 
- [Tabelle di Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – fornire posta elettronica di contatto e la stringa di connessione di account di archiviazione. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) : fornire l'indirizzo e-mail di contatto, l'URL e modalità di autenticazione (Office 365 o Azure Active Directory).
- [HTTPS Endpoint](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – fornire posta elettronica di contatto e l'URL dell'endpoint HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) : fornire l'indirizzo e-mail di contatto, l'ID modulo, ID Munchkin dell'account e ID del server.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -fornire posta elettronica di contatto e ID organizzazione. 

#### <a name="additional-lead-management-resources"></a>Risorse di gestione aggiuntivi di lead
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori di configurazione comuni lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Lead una cercapersone Panoramica di gestione](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ricordarsi di **salvare** prima di passare alla sezione successiva.

## <a name="properties"></a>Properties
Il **proprietà** scheda viene chiesto di definire le categorie e settori utilizzati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. 

Selezionare **salvare** dopo aver completato questi campi. 

### <a name="category"></a>Category
Selezionare un minimo di uno (1) e un massimo di tre (3) le categorie utilizzate per l'offerta di raggruppamento nelle aree di ricerca marketplace appropriato. Sottolineare come l'offerta supporta queste categorie nella descrizione dell'offerta. 

### <a name="industry"></a>Settore
Selezionare fino a due (2) i settori utilizzati per raggruppare l'offerta nelle aree di ricerca marketplace appropriato. Se l'offerta non è specifico di un settore, non selezionare uno. Sottolineare come l'offerta supporta i settori selezionati nella descrizione dell'offerta. 

### <a name="app-version"></a>Versione dell'app
Si tratta di un campo facoltativo usato per identificare il numero di versione dell'offerta nel marketplace AppSource. 

### <a name="standard-contract"></a>Contratto standard

- **Usare il contratto Standard?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legali per i fornitori di software, Microsoft offre un modello di contratto Standard per facilitare una transazione in marketplace. 

Piuttosto che alla creazione personalizzate termini e condizioni, gli editori di Marketplace di Azure è possono scegliere di offrire il proprio software nell'ambito del contratto Standard, che i clienti devono solo esaminare ed accettare una sola volta. 

Il contratto Standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se le condizioni di licenza sono diverse dal contratto di Standard, è possibile scegliere di immettere i proprio legali d'uso di seguito. È anche possibile immettere fino a 10.000 caratteri del testo in questo campo. Se le condizioni d'uso è necessaria una descrizione più dettagliata, immettere un singolo collegamento all'URL in questo campo in cui sono presenti le condizioni di licenza supplementari. Per i clienti verranno visualizzati come un collegamento attivo.

Per ottenere la versione di prova dell'app, i clienti devono accettare tali condizioni. 

Ricordarsi di **salvare** prima di passare alla sezione successiva.

## <a name="offer-listing"></a>Inserzione dell'offerta

L'inserzione scheda consente di visualizzare il linguaggi e mercati, in cui l'offerta è disponibile, attualmente inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, questa pagina consente di visualizzare lo stato dell'elenco specifiche della lingua e la data/ora che è stato aggiunto. È necessario definire i dettagli di marketplace (offrire nome, descrizione, i termini di ricerca e così via) per ogni lingua / sul mercato.

### <a name="offer-listings"></a>Presentazione delle offerte

Fornire i dettagli da visualizzare nel marketplace, incluse le descrizioni dell'offerta e risorse di marketing.

- **Nome** (obbligatorio): Il nome definito in questo caso verrà visualizzato come titolo dell'offerta nel marketplace(s) scelto. Il nome viene prepopolato base precedente **nuova offerta** voce.  Ciò potrebbe essere si.  Questo non deve contenere spazi vuoti, emoji (a meno che non siano simboli di copyright e marchi) e devono essere limitati a 50 caratteri.
- **Riepilogo** (obbligatorio): Fornire una breve descrizione dell'offerta da utilizzare nei risultati della ricerca di inserzioni marketplace. Fino a 100 caratteri del testo possono essere immessi in questo campo.
- **Descrizione** (obbligatorio): Fornire una descrizione dell'offerta da visualizzare nella panoramica inserzioni marketplace. Si consideri ad esempio una proposta di valore, i vantaggi principali, tutte le associazioni di categoria o al settore, opportunità di acquisto in-app, le richieste divulgazioni e un collegamento per altre informazioni.
Fino a 3000 caratteri del testo possono essere immessi in questo campo. Per altri suggerimenti, vedere l'articolo [scrivere una descrizione di app eccezionali](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Cercare nelle parole chiave**: Immettere un massimo di tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nel marketplace(s).
- **Introduzione a istruzioni** (obbligatorio): Viene descritto come configurare e iniziare a usare l'app per i clienti potenziali.  Questa Guida introduttiva può contenere collegamenti alla documentazione online più dettagliato. Fino a 3000 caratteri del testo possono essere immessi in questo campo. 

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): Collegamento all'informativa sulla privacy della propria organizzazione. Si è responsabili per garantire la che conformità a normative e leggi sulla privacy dell'app e per fornire un'informativa sulla privacy valido
- **Materiali di Marketing programma CSP** (facoltativo): È necessario fornire un collegamento al materiale di marketing, se si sceglie di estendere l'offerta per la [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) programma. CSP consente di estendere l'offerta per un'ampia gamma di clienti qualificati abilitando i partner CSP ad aggregare, sul mercato e rivendere l'offerta. I rivenditori dovranno accedere ai materiali di marketing dell'offerta. Per altre informazioni, vedere [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Collegamenti utili** (facoltativo): I documenti online supplementari facoltativi sulla tua app o servizi correlati elencati, fornendo una **Title** e **URL**. Aggiungere altri collegamenti utili facendo **+ Aggiungi un URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: Per ogni contatto cliente, fornire un dipendente **Name** , **numero di telefono**, e **posta elettronica** indirizzo.  (Questi *non* visualizzato pubblicamente). Oggetto **URL del supporto** è necessaria anche per il **contatto del supporto tecnico** gruppo.  (Questa informazione *verranno* visualizzato pubblicamente).

**Il contatto di supporto** (obbligatorio): Per domande sul supporto generale.

**Engineering contact** (obbligatorio): Per domande di natura tecnica.

**Canale contact Manager** (obbligatorio): Per domande rivenditore riguardanti il programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): Aggiungere i documenti di marketing correlati per l'offerta, in formato PDF, che fornisce un minimo di uno (1) e massimo di tre (3) documenti per ogni offerta.
- **Le immagini** (facoltativo): Esistono più punti in cui appaia immagini del logo dell'offerta in tutto il marketplace(s), che richiedono le seguenti dimensioni: Small: 48 x 48 pixel _(obbligatorio),_ Media: 90 x 90 pixel, di grandi dimensioni: 216x216 pixel _(obbligatorio),_ Wide: 255 x 115 pixel e alta: 815 x 290 pixel. Tutte le immagini devono essere. Formato PNG.
- **Gli screenshot** (obbligatorio): Aggiungere schermate che illustrano l'offerta. Un massimo di cinque (5) schermate può essere aggiunti e deve essere ridimensionato a 1280 x 720 pixel. Tutte le immagini devono essere. Formato PNG.
- **Video su** (facoltativo): Aggiungere collegamenti ai video che illustra l'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. È necessario anche immettere un'immagine di anteprima del video, ridimensionarli in base a 1280 x 720 pixel in formato PNG. È possibile visualizzare un massimo di quattro video per ogni offerta.

Ricordarsi di **salvare** prima di passare alla sezione successiva.

#### <a name="additional-marketplace-listing-resources"></a>Risorse di inserzione di marketplace aggiuntive

- [Le procedure consigliate per marketplace offrono elenchi](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Anteprima

Il **Preview** scheda consente di definire un limitato **anteprima Audience** per il rilascio dell'offerta prima della pubblicazione dell'offerta in tempo reale al gruppo di destinatari più ampio di marketplace.

> [!IMPORTANT]
> È necessario selezionare **lanciate** prima dell'offerta verrà pubblicato in tempo reale per il gruppo di destinatari marketplace pubblico dopo aver controllato l'offerta in anteprima.

- **Definire un gruppo di destinatari di anteprima: Aggiungere un singolo messaggio di account AAD/MSA per ogni riga, insieme a una descrizione facoltativa.**

Aggiungere manualmente gli indirizzi di posta elettronica fino a dieci (10) o venti (20) se caricando un file CSV, per Account Microsoft esistente (MSA) o gli account di Azure Active Directory (AAD) per consentire di convalidare l'offerta prima della pubblicazione in tempo reale. Aggiungendo questi account, si definisce un gruppo di destinatari che sarà consentito l'accesso all'anteprima per l'offerta prima della pubblicazione per il marketplace(s). Se l'offerta è già attivo, è ancora può definire un gruppo di destinatari di anteprima per i test di eventuali modifiche o aggiornamenti per l'offerta.

> [!NOTE]
> I destinatari di anteprima è diverso da un gruppo di destinatari privato. Un gruppo di destinatari di anteprima è consentito l'accesso alla tua proposta _preventiva_ da pubblicare in tempo reale nei Marketplace. È anche possibile scegliere creare un piano e renderla disponibile solo per un gruppo di destinatari privato. Nel **pianificare listato** scheda, è possibile definire un gruppo di destinatari privata con il **si tratta di un piano privato** casella di controllo. È quindi possibile definire un gruppo di destinatari privata di fino a 20.000 clienti che usano gli ID Tenant di Azure.

## <a name="technical-configuration"></a>Configurazione tecnica

Il **configurazione tecnica** scheda definisce i dettagli tecnici (percorso dell'URL webhook, ID tenant e ID app) utilizzati per la connessione per l'offerta. Questa connessione consente di eseguire il provisioning dell'offerta come una risorsa nella sottoscrizione di Azure del cliente se scelgono di acquisirlo.

- **URL della pagina di destinazione** (obbligatorio): Definire il sito URL a cui i clienti saranno indirizzate a visualizzata dopo aver acquisito l'offerta dal marketplace. Questo URL sarà anche l'endpoint che riceverà le API di connessione che agevolano l'attività commerciale con Microsoft.

- **Webhook di connessione** (obbligatorio): Per tutti gli eventi asincroni che Microsoft deve inviare all'utente per conto del cliente (ad esempio, Sottoscrizione di Azure ha verificato non valida), viene richiesto di fornire un webhook di connessione. Se si ha già un sistema di webhook posto, la configurazione più semplice è disporre di un'App per la logica di Endpoint HTTP che sarà in ascolto per tutti gli eventi da inviare ad esso e quindi gestirle adeguatamente (ad esempio https:\//prod-1westus.logic.azure.com:443/work). Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenant di Azure AD** (obbligatorio): Nel portale di Azure, è necessario che si [creare un'app di Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo che possa essere convalidato la connessione tra i due servizi si trova dietro una comunicazioni autenticate. Per trovare le [ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), passare ad Azure Active Directory e selezionare **delle proprietà**, quindi cercare il **ID Directory** numero indicato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID app Azure AD** (obbligatorio): È necessario anche il [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) e una chiave di autenticazione. Per ottenere questi valori, passare ad Azure Active Directory e selezionare **registrazioni per l'App**, quindi cercare il **ID applicazione** numero indicato (ad esempio 50c464d3-4930-494c-963c-1e951d15360e). Per trovare la chiave di autenticazione, consultare **le impostazioni** e selezionare **chiavi**. È necessario fornire una descrizione e durata e verrà quindi specificare un valore numerico.

 Si noti che l'ID applicazione di Azure è associata all'ID editore, assicurarsi che lo stesso ID applicazione è utilizzato in tutte le offerte.

## <a name="plan-overview"></a>Panoramica del piano

Il **Panoramica del piano** scheda consente di fornire un'ampia gamma di opzioni del piano all'interno dell'offerta stessa. Questi piani (talvolta detto SKU) potrebbero essere diversi in termini di livelli di servizio, la monetizzazione o versione. È necessario configurare almeno un piano per vendere l'offerta nel marketplace.

Una volta creato, si noterà i nomi di piano, gli ID, modelli, la disponibilità (pubblico o privato), di determinazione prezzi corrente la pubblicazione di stato e le azioni disponibili.

**Le azioni** disponibile nel **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

- Se lo stato del piano **bozza** – Elimina bozza
- Se lo stato del piano **Live** – Stop vendere piano o gruppo di destinatari privata di sincronizzazione

**Crea nuovo piano** (la quantità minima di un piano per gli utenti che consente di vendere tramite Microsoft)

- **ID piano:** In questa offerta, creare un ID di piani univoci per ogni piano. Questo ID sarà visibile ai clienti i modelli di Azure Resource Manager e l'URL del prodotto (se applicabile). Usare solo caratteri alfanumerici minuscoli, trattini o caratteri di sottolineatura. È consentito un massimo di 50 caratteri per questo ID del piano. Si noti che l'ID non può essere modificato dopo la selezione di creare.
- **Nome del piano:** I clienti visualizzano questo nome quando si decide che intende selezionare all'interno dell'offerta. In questa offerta, creare un nome dell'offerta univoca per ogni piano. Il nome del piano viene utilizzato per distinguere i piani di software che possono essere parte dell'offerta stessa (ad esempio Nome offerta: Windows Server. piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Elenco di piano

Il **pianificare listato** scheda Visualizza le lingue (e mercati) in cui è disponibile, il piano attualmente inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, questa pagina consente di visualizzare lo stato dell'elenco specifiche della lingua e la data/ora che è stato aggiunto. È necessario definire i dettagli di marketplace (offrire nome, descrizione, i termini di ricerca e così via) per ogni lingua / sul mercato.

#### <a name="plan-listing-details"></a>Elenco dettagli piano

Selezionando uno dei linguaggi piano verranno visualizzate le **pianificare listato** informazioni, tra cui **nome** e **descrizione.**

- **Nome**: Pre-popolato in base la versione di anteprima **nuovo piano** voce e verrà visualizzato come titolo del piano dell'offerta"Software" visualizzato nel marketplace.
- **Descrizione:** Questa descrizione è un'opportunità per spiegare ciò che rende unico piano software e eventuali differenze da altri piani software all'interno dell'offerta. Può contenere fino a 500 caratteri.

Selezionare **salvare** dopo aver completato questi campi.

#### <a name="plan-pricing-and-availability"></a>Piano di determinazione dei prezzi e disponibilità

Il **prezzi e disponibilità** scheda consente di configurare i mercati che il piano sarà disponibile, il modello desiderato la monetizzazione, prezzo e termine di fatturazione. Inoltre, è possibile indicare se si desidera rendere visibile il piano a tutti gli utenti o solo per clienti specifici (un gruppo di destinatari privata).

#### <a name="markets"></a>Mercati

- **Modifica mercati** (facoltativo)

Tutti i piani devono essere disponibili almeno un mercato. Selezionare la casella di controllo per qualsiasi posizione sul mercato in cui si desidera rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione di "Tax Remitted" paesi, in cui Microsoft rinvii sales e l'uso di imposte per tuo conto, sono inclusi. 


Se già stato impostato i prezzi per il piano in Italia dollari (USD) e aggiungere un'altra posizione sul mercato, il prezzo per il nuovo mercato verrà calcolato in base ai tassi di cambio correnti. È sempre consigliabile esaminare il prezzo per ogni mercato prima della pubblicazione. È possibile esaminare prezzi usando il collegamento "L'esportazione dei prezzi (xlsx)" dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

- **Modello tariffario**: Tariffa fissa o postazione basati su

**Tariffa fissa:** Abilitare l'accesso per l'offerta con un prezzo di tariffa fissa singolo prezzo mensile o annuale. Ciò è talvolta detta basato su sito sui prezzi.

**Postazioni di base:** Abilitare l'accesso per l'offerta con il prezzo in base al numero di utenti l'accesso all'offerta o che occupa *postazioni*. Questo modello basati sulle postazioni consente di impostare il valore minimo e massimo numero di postazioni consentito in base al prezzo. In questo modo, i punti di prezzo diverso possono essere configurati in base al numero di utenti tramite la configurazione di più piani.  Questi campi sono facoltativi. Se lasciato vuoto, il numero di postazioni viene interpretato in quanto non hanno un limite massimo (minimo 1) e con un massimo di tutti i campi di sistema può supportare. Questi campi possono essere modificati come parte di un aggiornamento al piano.

Dopo la pubblicazione, la scelta del modello tariffario fatturazione non può essere modificata. Inoltre, tutti i piani all'offerta stessa devono condividere lo stesso modello di determinazione dei prezzi.

- **Periodo di fatturazione**: Mensile o annuale

Selezionare la frequenza con cui i clienti devono pagare il prezzo elencato. È necessario specificare almeno una frequenza mensile o annuale di prezzo, o entrambe le opzioni possono essere rese disponibili ai clienti.

- **Prezzo**: USD al mese o USD all'anno

Set dei prezzi nella valuta locale (USD = dollari statunitensi) vengono convertite nella valuta locale di tutti i mercati con i tassi di cambio disponibili durante l'installazione. Convalidare i prezzi prima della pubblicazione esportando i prezzi del foglio di calcolo e verifica il prezzo nei singoli mercati. Se si desidera impostare i prezzi personalizzati in un mercato singoli, modificare e importare il foglio di calcolo dei prezzi. Si è responsabile della convalida questo piano tariffario e proprietari queste impostazioni.
**È prima necessario salvare le modifiche ai prezzi per abilitare l'esportazione dei dati sui prezzi.*

Consultare i prezzi con attenzione prima della pubblicazione, poiché sono presenti alcune limitazioni su ciò che è possibile modificare dopo la pubblicazione di un piano:

- Dopo aver pubblicato un piano, il modello di determinazione prezzi non può essere modificato.
- Dopo aver pubblicato un termine di fatturazione per un piano, non può essere rimosso in un secondo momento.
- Dopo aver pubblicato un prezzo di un mercato nel piano, non può essere modificato in un secondo momento.

### <a name="plan-audience"></a>Piano di destinatari

È possibile configurare ogni piano per essere visibile a tutti gli utenti o solo un gruppo di destinatari specifico di propria scelta. È possibile assegnare l'appartenenza al gruppo di destinatari limitato usando l'ID tenant di Azure AD.

#### <a name="privacy"></a>Privacy

- **Si tratta di un piano privato** (casella di controllo facoltativo)

Selezionare questa casella per rendere il piano privato e visibile solo per i destinatari con restrizioni di propria scelta. Una volta pubblicata come un piano privato, è possibile aggiornare i destinatari o scegliere di rendere disponibile il piano a tutti gli utenti. Dopo aver pubblicato un piano come visibile a tutti gli utenti, devono rimanere visibile a tutti. (Il piano non può essere configurato come un piano privato anche in questo caso).

- **Gruppo di destinatari limitato (ID Tenant)**

Assegnare i destinatari che avranno accesso a questo piano privato. Viene assegnato l'accesso utilizzando l'ID tenant con l'opzione per includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 tenant ID o ID primi 20.000 clienti tenant se importando un file di foglio di calcolo con estensione csv.

Un tenant è una rappresentazione di un'organizzazione, con un ID sotto forma di GUID (Globally Unique Identifier, un numero intero a 128 bit usato per identificare le risorse). È un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft, come l'iscrizione ad Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Per controllare il tenant, accedere al portale di Azure con l'account da usare per gestire l'applicazione. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account. Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio. Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID. È inoltre possibile cercare ID tenant dell'organizzazione tramite un URL al nome di dominio: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

Mentre le offerte SaaS usano ID tenant per definire un gruppo di destinatari privato, altri tipi di offerta possono usare gli ID sottoscrizione di Azure (che sono rappresentati anche come GUID).

> [!NOTE]
> Destinatari privato (o gruppo di destinatari limitato) è diverso da un gruppo di destinatari di anteprima. Nel **[anteprima](#preview)** scheda, è possibile definire un gruppo di destinatari di anteprima. Un gruppo di destinatari di anteprima è consentito l'accesso alla tua proposta *preventiva* all'offerta da pubblicare in tempo reale nel marketplace. Mentre la designazione di destinatari privati si applica solo a un piano specifico, i destinatari di anteprima consente di visualizzare tutti i piani (privato o No), ma solo durante il periodo di anteprima limitata, mentre il piano viene testato e convalidato.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Esempio di elenco dei piani all'interno di un'offerta del marketplace

![Piano del marketplace esempio listato con Lotus notes](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

Il **il Test drive** scheda consente di configurare una dimostrazione (o "prova") che consentirà ai clienti di provare l'offerta prima del commit per acquistarlo. Altre informazioni nell'articolo [What ' s Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non è più si desidera fornire una test drive per l'offerta, tornare alla **[offrono il programma di installazione](#offer-setup)** pagina e deselezionare **abilitazione test unità**.

### <a name="technical-configuration"></a>Configurazione tecnica
I seguenti tipi di test drive sono disponibili, ognuno con i propri requisiti tecnici di configurazione.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (configurazione tecnici non necessaria)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Versione di test configurazione tecnico per Azure Resource Manager

Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che rientrano in questo scenario usare solo le risorse di Azure. Altre informazioni sulla configurazione di un' [test drive di Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Aree** (obbligatorio): Attualmente esistono 26 aree geografiche supportate da Azure in cui il test drive può essere rese disponibile. In genere, è opportuno rendere disponibile il test drive nelle aree in cui si prevede il maggior numero di clienti, in modo che è possibile selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che la sottoscrizione è autorizzata a distribuire tutte le risorse necessarie in ogni area che viene scelta.

- **Instances**: Selezionare il tipo (a caldo o freddo) e numero delle istanze disponibili, che verranno moltiplicate per il numero di aree in cui l'offerta è disponibile.

**Hot**: Questo tipo di istanza è distribuito ed è in attesa di accesso per ogni area selezionata. I clienti possono accedere istantaneamente *frequente* istanze di un test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile avere almeno uno *frequente* istanze, come la maggior parte dei clienti non desiderano attendere per le distribuzioni complete, causando una consegna nell'utilizzo del cliente se nessun *frequente* istanza è disponibile.

**Ad accesso sporadico**: Questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite eventualmente per area. Le istanze a freddo richiedono l'intero modello di gestione risorse di Test unità per la distribuzione quando un cliente richiede il test drive, pertanto *ad accesso sporadico* istanze sono molto più lente rispetto a caricare *frequente* istanze. Lo svantaggio è che solo necessario pagare per la durata del test drive, perché *non* sempre in esecuzione nella sottoscrizione di Azure come con un *frequente* istanza.

- **Modello di Azure Resource Manager di test unità**: Caricare il file ZIP che contiene il modello Azure Resource Manager.  Altre informazioni sulla creazione di un modello Azure Resource Manager nell'articolo della Guida introduttiva [crea e Distribuisci modelli di Azure Resource Manager usando il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durata del test drive** (obbligatorio): Immettere l'intervallo di tempo che resterà attivo, in numero di ore di Test Drive. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può solo scommettere set da un numero intero di ore (ad esempio ore "2", "1.5" non è valido).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Versione di test configurazione tecnica per Dynamics 365

Microsoft può rimuovere la complessità della configurazione di un test drive da ospitare e gestire il provisioning del servizio e la distribuzione usando questo tipo di test unità. La configurazione per questo tipo di test di hosted è la stessa indipendentemente dal fatto che il test drive è destinato a un gruppo di destinatari Business Central, il coinvolgimento dei clienti o le operazioni.

- **Numero massimo di unità test simultanei** (obbligatorio): Impostare il numero massimo di clienti che è possibile usare il test drive in una sola volta. Ogni utente simultanee utilizzerà una licenza Dynamics 365, mentre il test drive è attivo, pertanto è necessario assicurarsi di avere licenze sufficienti supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata del test drive** (obbligatorio): Immettere l'intervallo di tempo che resterà attivo del Test Drive, definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non è più utilizzare una delle licenze. È consigliabile un valore di 2 a 24 ore a seconda della complessità dell'offerta. Questa durata può solo scommettere set da un numero intero di ore (ad esempio ore "2", "1.5" non è valido).  L'utente può richiedere una nuova sessione se vengono eseguite all'esterno di tempo e si desidera accedere di nuovo il test drive.

- **URL dell'istanza** (obbligatorio): URL cui il cliente avrà inizio il test drive. In genere l'URL dell'istanza di Dynamics 365 che eseguono l'app con i dati di esempio installati (ad esempio https://testdrive.crm.dynamics.com).

- **URL dell'API Web dell'istanza** (obbligatorio): Recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account di Microsoft 365 e passare a **le impostazioni** \&gt; **Personalizzazione** \&gt; **Risorse per sviluppatori** \&gt; **API Web (URL radice del servizio) dell'istanza**, copiare l'URL qui (ad esempio https://testdrive.crm.dynamics.com/api/data/v9.0).

- **Nome del ruolo** (obbligatorio): Specificare il nome di ruolo di sicurezza che è stato definito in test drive di Dynamics 365 personalizzato. Questo verrà assegnato all'utente durante il test drive (ad esempio, test-passaggio-ruolo).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnici per la versione di test di app per la logica

I prodotti personalizzati devono usare questo tipo di test unità del modello di distribuzione che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di App per la logica test Drive, visitare [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) su GitHub.

- **Area** (elenco a discesa a selezione singola, obbligatorio): Attualmente esistono 26 aree geografiche supportate da Azure in cui il test drive può essere rese disponibile. Le risorse per app per la logica verranno distribuite nell'area selezionata. Se l'App per la logica ha tutte le risorse personalizzate archiviate in un'area specifica, verificare che tale area è selezionata qua. Il modo migliore per farlo è completamente la distribuzione di App per la logica in locale nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- **Numero massimo di unità test simultanei** (obbligatorio): Impostare il numero massimo di clienti che è possibile usare il test drive in una sola volta. Questi dischi sono già stati distribuiti, consentendo ai clienti di accedervi immediatamente senza tempi di attesa per una distribuzione di test.

- **Durata del test drive** (obbligatorio): Immettere l'intervallo di tempo che resterà attivo, in numero di ore di Test Drive. Il test drive termina automaticamente dopo questo periodo di tempo termina.

- **Nome del gruppo di risorse di Azure** (obbligatorio): Immettere il [gruppo di risorse Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) nome in cui è salvato il test drive di App per la logica.

- **Nome dell'app per la logica di Azure** (obbligatorio): Immettere il nome dell'app per la logica che assegna il test drive per l'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- **Nome dell'app per la logica per effettuare il deprovisioning** (obbligatorio): Immettere il nome dell'app per la logica che effettua il deprovisioning del test drive dopo che il cliente è terminato. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessario per i test Drive di Power BI

Prodotti che desiderano dimostrare che un'interattivi di Power BI visual può usare un collegamento incorporato per condividere un dashboard personalizzato come il test drive, necessaria alcuna ulteriore configurazione tecnica. Altre informazioni sulla configurazione[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) modello app.

### <a name="deployment-subscription-details"></a>Dettagli della sottoscrizione distribuzione

Per distribuire i Test Drive per tuo conto, creare e fornire una sottoscrizione di Azure separati, univoci. (Non necessario per i test Drive di Power BI).

- **ID sottoscrizione di Azure** (obbligatorio per le app di Azure Resource Manager e per la logica): Immettere l'ID sottoscrizione per concedere l'accesso ai servizi account Azure per la creazione di report e di fatturazione dell'utilizzo delle risorse. È consigliabile considerare [creazione di una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per i test Drive se non è già stata. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando al **sottoscrizioni** scheda del menu a sinistra. Selezionando la scheda verrà visualizzato l'ID sottoscrizione (ad esempio, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenant di Azure AD** (obbligatorio): Immettere la Azure Active Directory (AD) [tenant ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu di sinistra, selezionare **delle proprietà** , quindi cercare il **ID Directory** numero indicato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare ID tenant dell'organizzazione usando l'URL del nome di dominio in: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com).

- **Nome del tenant Azure AD** (obbligatorio per Dynamics 365): Immettere il nome di Azure Active Directory (AD). Per trovare il nome specificato, accedi per il [portale di Azure](https://portal.azure.com/), nell'angolo superiore destro conterrà il nome del tenant nel nome dell'account.

- **ID app Azure AD** (obbligatorio): Immettere la Azure Active Directory (AD) [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu di sinistra, selezionare **registrazioni per l'App**, quindi cercare il **ID applicazione** numero elencati (ad esempio 50c464d3-4930-494c-963c-1e951d15360e).

- **Chiave dell'app Azure AD** (obbligatorio): Immettere la Azure Active Directory (AD) [chiave dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu di sinistra, selezionare **registrazioni per l'App** , quindi selezionare **impostazioni**  >  **Chiavi**.

Ricordarsi di **salvare** prima di passare alla sezione successiva.

### <a name="test-drive-listings-optional"></a>Elenchi di unità di test (facoltativo)

Il **elenchi di Test Drive** opzione disponibile nel **Test drive** scheda Visualizza le lingue (e mercati) in cui è disponibile, il test drive attualmente inglese (Stati Uniti) è l'unica posizione disponibile . Inoltre, questa pagina consente di visualizzare lo stato dell'elenco specifiche della lingua e la data/ora che è stato aggiunto. È necessario definire i dettagli dei test unità (descrizione, manuale dell'utente, video, e così via) per ogni lingua/sul mercato.

- **Descrizione** (obbligatorio): Descrivere il test drive, ciò che verrà illustrato più avanti, gli obiettivi per l'utente per sperimentare le funzionalità da esplorare e tutte le informazioni rilevanti per consentire all'utente di determinare se acquisire l'offerta. Fino a 3000 caratteri del testo possono essere immessi in questo campo. 

- **Accedere alle informazioni** (obbligatorio per le unità di test di Azure Resource Manager e per la logica): Viene illustrato ciò che un cliente ha bisogno di conoscere per poter accedere e usare il test drive. Illustra uno scenario per l'utilizzo dell'offerta ed esattamente ciò che il cliente deve sapere per accedere alle funzionalità in tutto il test drive. Fino a 10.000 caratteri del testo possono essere immessi in questo campo.

- **Manuale dell'utente** (obbligatorio): Procedura guidata dell'esperienza di test drive. Il manuale dell'utente deve comprendere esattamente cosa si intende il cliente per ottenere informazioni da verificano il test drive e fungono da riferimento per eventuali domande che potrebbero avere. Il file deve essere in formato PDF e chiamarsi (massimo di 255 caratteri) dopo il caricamento.

- **Video: Aggiungere video** (facoltativo): I video possono essere caricati in YouTube o Vimeo e a cui fa riferimento qui un'immagine di anteprima e collegamento (533, 324 pixel) in modo che un cliente può visualizzare una procedura dettagliata delle informazioni per aiutarli a comprendere meglio il test drive, incluse le procedure usare correttamente le funzionalità del offrono e comprendere gli scenari che evidenziano i relativi vantaggi.
  - **Nome** (obbligatorio)
  - **URL (YouTube o Vimeo solo)** (obbligatorio)
  - **Anteprima (533 x 324px)** : File di immagine deve essere nel formato PNG.

Selezionare **salvare** dopo aver completato questi campi.

## <a name="publish"></a>Pubblica

#### <a name="submit-offer-to-preview"></a>Invio dell'offerta in anteprima

Dopo aver completato tutte le sezioni necessarie dell'offerta, selezionare **pubblicare** nell'angolo in alto a destra del portale. Sarà reindirizzati per il **revisione e pubblicare** pagina. 

Se questa è la prima volta che la pubblicazione di questa offerta, è possibile:

- Visualizzare lo stato di completamento per ogni sezione dell'offerta.
    - *Non è stato avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o richiede informazioni da fornire. Torna indietro per le sezioni e aggiornarlo.
    - *Completa* : significa che la sezione è stata completata, è stato fornito tutti i dati necessari e non siano presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato di completamento prima di inviare l'offerta.
- Vengono fornite istruzioni test al team di certificazione per assicurarsi che l'app viene verificata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.
- Invio dell'offerta per la pubblicazione selezionando **Submit**. Ti invieremo un'e-mail per essere informati quando una versione di anteprima dell'offerta è disponibile per la revisione e approvazione. È necessario tornare al centro per i Partner e selezionare **Go-live** dell'offerta pubblicare l'offerta al pubblico (o se offerta una privata, per il pubblico a privato).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](./update-existing-offer.md)
