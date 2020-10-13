---
title: Come pianificare un'offerta SaaS per Microsoft Commercial Marketplace
description: Come pianificare una nuova offerta SaaS (Software as a Service) per l'inserzione o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) usando il programma Commercial Marketplace nel centro per i partner Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 8dfc1eb35572a6b706deb47335357417bd837825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819939"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Come pianificare un'offerta SaaS per il Marketplace commerciale

Questo articolo illustra le diverse opzioni e i requisiti per la pubblicazione di un'offerta SaaS (Software as a Service) nel Marketplace commerciale Microsoft. Le offerte SaaS consentono di distribuire e concedere in licenza soluzioni software ai clienti tramite una sottoscrizione online anziché l'installazione locale nei singoli computer. Questo articolo consente di preparare l'offerta per la pubblicazione nel Marketplace commerciale con il centro per i partner.

## <a name="listing-options"></a>Opzioni di elenco

Quando si prepara la pubblicazione di una nuova offerta SaaS, è necessario decidere quale opzione di _elenco_ scegliere. Verranno stabilite le informazioni aggiuntive da fornire durante la creazione dell'offerta nel centro per i partner. Si definirà l'opzione di inserzione nella pagina  **installazione offerta** , come illustrato in [come creare un'offerta SaaS nel Marketplace commerciale](create-new-saas-offer.md).

La tabella seguente mostra le opzioni di elenco per le offerte SaaS nel Marketplace commerciale.

| Elenco di opzioni | Processo di transazione |
| ------------ | ------------- |
| Contact me (Contattami) | Il cliente contatta direttamente le informazioni dell'inserzione.``*`` |
| Versione di prova gratuita | Il cliente viene reindirizzato all'URL di destinazione tramite Azure Active Directory (Azure AD).``*`` |
| Ottieni ora (gratuito) | Il cliente viene reindirizzato all'URL di destinazione tramite Azure AD.``*`` |
| Vendita tramite Microsoft  | Le offerte vendute tramite Microsoft sono denominate offerte _transazionali_ . Un'offerta transazionale è una delle quali Microsoft semplifica lo scambio di denaro per una licenza software per conto dell'editore. Le offerte SaaS vengono fatturate usando il modello di determinazione dei prezzi scelto e gestite le transazioni dei clienti per conto dell'utente. Si noti che le tariffe di utilizzo dell'infrastruttura di Azure vengono addebitate direttamente all'utente, al partner. È necessario tenere conto dei costi dell'infrastruttura nel modello di determinazione dei prezzi. Questa procedura è illustrata più dettagliatamente nella [fatturazione Saas](#saas-billing) riportata di seguito.  |
|||

``*`` Gli editori sono responsabili del supporto di tutti gli aspetti della transazione di licenza software, tra cui l'ordine, l'evasione, la misurazione, la fatturazione, la fatturazione, il pagamento e la raccolta.

Per ulteriori informazioni su queste opzioni di elenco, vedere la pagina relativa alle [funzionalità di transacting del Marketplace commerciale](marketplace-commercial-transaction-capabilities-and-considerations.md).

Dopo la pubblicazione dell'offerta, l'opzione relativa all'elenco scelto per l'offerta viene visualizzata come pulsante nell'angolo in alto a sinistra della pagina di presentazione dell'offerta. Ad esempio, la schermata seguente mostra una pagina di presentazione dell'offerta in Azure Marketplace con i pulsanti **contattami** e **test drive** .

![Viene illustrato un elenco di offerte nel negozio online.](./media/listing-options.png)

## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici variano in base all'opzione di inserzione scelta per l'offerta.

L'opzione _Contact me_ Listing non ha requisiti tecnici. È possibile connettere un sistema CRM per gestire i lead dei clienti, descritto nella sezione [Lead clienti](#customer-leads) , più avanti in questo articolo.

Le opzioni per _ottenere ora (gratuita)_, _versione di valutazione gratuita_e _vendita tramite Microsoft_ elencano i requisiti tecnici seguenti:

- L'applicazione SaaS deve essere una soluzione multi-tenant.
- È possibile abilitare sia gli account Microsoft (MSA) sia [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) per l'autenticazione degli utenti.
- È necessario creare una pagina di destinazione. Dopo che l'utente ha acquistato l'offerta, viene indirizzata alla pagina di destinazione per facilitare il provisioning o la configurazione aggiuntiva richiesta. Per istruzioni sulla creazione della pagina di destinazione, vedere i seguenti articoli:
  - [Creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale](azure-ad-transactable-saas-landing-page.md)
  - [Crea la pagina di destinazione per l'offerta SaaS gratuita o di valutazione nel Marketplace commerciale](azure-ad-free-or-trial-landing-page.md)

Questi requisiti tecnici aggiuntivi si applicano solo all'opzione di inserzione _sell through Microsoft_ (transazionale):

- Per l'acquisto dell'utente che accede alla pagina di destinazione, è necessario Azure AD con la gestione delle identità di Single Sign-On (SSO) e l'autenticazione. Per istruzioni dettagliate, vedere [Azure ad e le offerte SaaS transazionali nel Marketplace commerciale](azure-ad-saas.md).
- È necessario usare le [API di evasione Saas](./partner-center-portal/pc-saas-fulfillment-api-v2.md) per l'integrazione con Azure Marketplace e Microsoft AppSource. È necessario esporre un servizio che può interagire con la sottoscrizione SaaS per creare, aggiornare ed eliminare un account utente e un piano di servizio. Le modifiche critiche all'API devono essere supportate entro 24 ore. Le modifiche non critiche all'API verranno rilasciate periodicamente. I diagrammi e le spiegazioni dettagliate che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione relativa alle [API](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- È necessario creare almeno un piano per l'offerta. Il piano viene valutato in base al modello di determinazione dei prezzi selezionato prima della pubblicazione: _Tariffa_ fissa o _per utente_. Altre informazioni sui [piani](#plans) sono disponibili più avanti in questo articolo.
- Il cliente può annullare l'offerta in qualsiasi momento.

### <a name="technical-information"></a>Informazioni tecniche

Se si sta creando un'offerta transazionale, è necessario raccogliere le informazioni seguenti per la pagina di **configurazione tecnica** . Se si sceglie di elaborare le transazioni in modo indipendente anziché creare un'offerta transazionale, ignorare questa sezione e passare a [test drive](#test-drives).

- **URL della pagina di destinazione**: URL del sito SaaS (ad esempio, `https://contoso.com/signup` ) a cui gli utenti verranno indirizzati dopo aver acquisito l'offerta dal Marketplace commerciale, avviando il processo di configurazione dalla sottoscrizione Saas appena creata. Questo URL riceverà un token che può essere usato per chiamare le API di evasione per ottenere i dettagli del provisioning per la pagina di registrazione interattiva.

  Questo URL viene chiamato con il parametro del token di identificazione dell'acquisto del Marketplace che identifica in modo univoco l'acquisto SaaS del cliente specifico. È necessario scambiare questo token per i dettagli della sottoscrizione SaaS corrispondenti usando l' [API Resolve](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Questi dettagli e tutti gli altri che si desidera raccogliere devono essere utilizzati come parte di una pagina Web interattiva del cliente integrata nella propria esperienza per completare la registrazione dei clienti e attivare l'acquisto. In questa pagina, l'utente deve eseguire l'iscrizione con l'autenticazione con un clic usando Azure Active Directory (Azure AD).

  Questo URL con il parametro del token di identificazione del Marketplace verrà chiamato anche quando il cliente avvierà un'esperienza SaaS gestita dall'interfaccia di amministrazione di portale di Azure o M365. È necessario gestire entrambi i flussi: quando il token viene fornito per la prima volta dopo un nuovo acquisto da parte del cliente e quando viene fornito nuovamente per un cliente esistente che gestisce la soluzione SaaS.

    La pagina di destinazione configurata deve essere in esecuzione 24/7. Questo è l'unico modo per ricevere notifiche sui nuovi acquisti delle offerte SaaS effettuate nel Marketplace commerciale o sulle richieste di configurazione per una sottoscrizione attiva di un'offerta.

- **Webhook di connessione**: per tutti gli eventi asincroni che Microsoft deve inviare all'utente (ad esempio, quando una sottoscrizione Saas è stata annullata), è necessario fornire un URL del webhook di connessione. Questo URL verrà chiamato per notificare l'evento.

  Il webhook fornito dovrebbe essere attivo e in esecuzione 24/7, in quanto questo è l'unico modo per ricevere una notifica sugli aggiornamenti delle sottoscrizioni SaaS dei clienti acquistate tramite il Marketplace commerciale.

  > [!NOTE]
  > All'interno del portale di Azure è necessario creare un'app single-tenant [Azure Active Directory (Azure ad)](../active-directory/develop/howto-create-service-principal-portal.md) per consentire l'uso di un app Azure ID per autenticare la connessione tra i due servizi. Per trovare l' [ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero ID directory elencato. Ad esempio: `50c464d3-4930-494c-963c-1e951d15360e`.

- **ID tenant Azure Active Directory**: (noto anche come ID directory). All'interno del portale di Azure è necessario [registrare un'app Azure Active Directory (ad)](../active-directory/develop/howto-create-service-principal-portal.md) in modo che sia possibile aggiungerla all'elenco di controllo di accesso (ACL) dell'API per assicurarsi di essere autorizzati a chiamarlo. Per trovare l'ID tenant per l'app Azure Active Directory (AD), passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero di **ID della directory (tenant)** elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **ID applicazione Azure Active Directory**: è necessario anche l' [ID applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Per ottenere il valore, passare al pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in Azure Active Directory. Nella colonna **nome visualizzato** selezionare l'app. Cercare quindi il numero ID dell'applicazione (client) elencato (ad esempio, `50c464d3-4930-494c-963c-1e951d15360e` ).

  Il Azure AD ID applicazione è associato all'ID editore nell'account del centro per i partner. È necessario utilizzare lo stesso ID applicazione per tutte le offerte in tale account.

  > [!NOTE]
  > Se il server di pubblicazione dispone di due o più account diversi nel centro per i partner, è necessario usare due o più ID app Azure AD diversi, ognuno per uno degli account. Ogni account partner nel centro per i partner deve usare un ID app univoco Azure AD per tutte le offerte SaaS pubblicate tramite questo account.

## <a name="test-drives"></a>Test drive
È possibile scegliere di abilitare un test drive per l'app SaaS. Le unità di test consentono ai clienti di accedere a un ambiente preconfigurato per un numero fisso di ore. È possibile abilitare le unità di test per qualsiasi opzione di pubblicazione, tuttavia questa funzionalità presenta requisiti aggiuntivi. Per ulteriori informazioni sulle unità di test, vedere [che cos'è un test drive?](what-is-test-drive.md). Per informazioni sulla configurazione di diversi tipi di unità di test, vedere [configurazione tecnica di test drive](test-drive-technical-configuration.md).

> [!TIP]
> Una test drive è diversa da una [versione di valutazione gratuita](plans-pricing.md#free-trials). È possibile offrire un test drive, una versione di valutazione gratuita o entrambi. Entrambi forniscono ai clienti la soluzione per un periodo di tempo fisso. Tuttavia, un test drive include anche una presentazione pratica e autonoma delle funzionalità chiave del prodotto e dei vantaggi illustrati in uno scenario di implementazione reale.

## <a name="customer-leads"></a>Clienti potenziali

Per raccogliere informazioni sui clienti, è necessario connettere l'offerta al sistema CRM (Customer Relationship Management). Al cliente verrà chiesta l'autorizzazione per condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e al negozio online in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Il Marketplace commerciale supporta un'ampia gamma di sistemi CRM, oltre alla possibilità di usare una tabella di Azure o di configurare un endpoint HTTPS usando Power automatici.

È possibile aggiungere o modificare una connessione CRM in qualsiasi momento durante o dopo la creazione dell'offerta. Per istruzioni dettagliate, vedere [Lead dei clienti dall'offerta del Marketplace commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Selezione di uno Store online

Quando si pubblica un'offerta SaaS, questa viene elencata in Microsoft AppSource, in Azure Marketplace o in entrambi. Ogni negozio online serve requisiti univoci per i clienti. AppSource è destinata alle soluzioni aziendali e Azure Marketplace è per le soluzioni IT. Il tipo di offerta, le funzionalità di transazione e le categorie determineranno la posizione in cui verrà pubblicata l'offerta. Le categorie e le sottocategorie sono mappate a ogni negozio online in base al tipo di soluzione. Per informazioni dettagliate sulla selezione di uno Store online, vedere [selezione di uno Store online](determine-your-listing-type.md#selecting-an-online-store).

## <a name="legal-contracts"></a>Contratti legali

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel Marketplace commerciale. Quando si offre il software nel contratto standard, i clienti devono leggere e accettare una sola volta e non è necessario creare termini e condizioni personalizzati.

Se si sceglie di utilizzare il contratto standard, si ha la possibilità di aggiungere termini di modifica universale e fino a 10 modifiche personalizzate al contratto standard. È anche possibile usare i propri termini e condizioni anziché il contratto standard. Questi dettagli vengono gestiti nella pagina delle **Proprietà** . Per informazioni dettagliate, vedere [contratto standard per Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Dopo la pubblicazione di un'offerta con il contratto standard per il Marketplace commerciale, non è possibile usare i termini e le condizioni personalizzati. Si tratta di uno scenario "or". È possibile offrire la propria soluzione in base al contratto standard o a termini e condizioni. Se si desidera modificare le condizioni del contratto standard, è possibile utilizzare le modifiche del contratto standard.

## <a name="offer-listing-details"></a>Dettagli elenco offerte

Quando si [Crea una nuova offerta SaaS](create-new-saas-offer.md) nel centro per i partner, si immetteranno testo, immagini, video facoltativi e altri dettagli nella pagina di presentazione dell' **offerta** . Si tratta delle informazioni che i clienti visualizzeranno quando scoprono l'inserzione dell'offerta nel Marketplace commerciale, come illustrato nell'esempio seguente.

:::image type="content" source="./media/example-saas-1.png" alt-text="Viene illustrato come viene visualizzata questa offerta in Microsoft AppSource.":::

**Descrizioni di chiamata**

1. Logo
2. Categorie
3. Settori
4. Indirizzo di supporto (collegamento)
5. Condizioni per l'utilizzo
6. Informativa sulla privacy
7. Nome offerta
8. Riepilogo
9. Description
10. Screenshot/video
11. Documenti

Nell'esempio seguente viene illustrato un elenco di offerte nel portale di Azure.

![Viene illustrato un elenco di offerte nel portale di Azure.](./media/example-managed-services.png)

**Descrizioni delle chiamate**

1. Titolo
1. Descrizione
1. Collegamenti utili
1. Schermate

> [!NOTE]
> Se la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]", non è necessario che il contenuto dell'elenco di offerte sia in inglese.

Per semplificare la creazione dell'offerta, è possibile preparare alcuni di questi elementi in anticipo. Se non diversamente specificato, sono necessari gli elementi seguenti.

- **Nome**: questo nome verrà visualizzato come titolo dell'inserzione dell'offerta nel Marketplace commerciale. Il nome può essere un marchio registrato. Non può contenere emoji (ad eccezione dei simboli di marchio e copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo risultati ricerca**: descrivere lo scopo o la funzione dell'offerta come una singola frase senza interruzioni di riga in caratteri di 100 o meno. Questo riepilogo viene usato nei risultati della ricerca nell'elenco dei Marketplace commerciali.
- **Descrizione**: questa descrizione verrà visualizzata nella panoramica degli elenchi di Marketplace commerciali. Si consiglia di includere una proposta di valore, i vantaggi principali, la base utente prevista, le associazioni di categoria o di settore, le opportunità di acquisto in-app, le divulgazioni richieste e un collegamento per ottenere ulteriori informazioni.
    
    Questa casella di testo contiene controlli avanzati dell'editor di testo che è possibile usare per rendere più accattivante la descrizione. È anche possibile usare i tag HTML per formattare la descrizione. È possibile immettere fino a 3.000 caratteri di testo in questa casella, incluso il markup HTML. Per altri suggerimenti, vedere [Scrivere un'ottima descrizione dell'app](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).

- **Introduzione istruzioni**: se si sceglie di vendere l'offerta tramite Microsoft (offerta transazionale), questo campo è obbligatorio. Queste sono istruzioni per aiutare i clienti a connettersi all'offerta SaaS. È possibile aggiungere fino a 3.000 caratteri di testo e collegamenti a documentazione online più dettagliata.
- **Parole chiave di ricerca** (facoltativo): fornire fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta negli archivi online. Non è necessario includere il **nome** e la **Descrizione**dell'offerta: il testo viene automaticamente incluso nella ricerca.
- **Collegamento all'informativa sulla privacy**: l'URL per l'informativa sulla privacy dell'azienda. È necessario fornire un'informativa sulla privacy valida e avere la responsabilità di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy.
- **Informazioni di contatto**: è necessario designare i contatti seguenti dall'organizzazione:
  - **Contatto del supporto tecnico**: fornire il nome, il telefono e il messaggio di posta elettronica per i partner Microsoft da usare quando i clienti aprono i biglietti. È inoltre necessario includere l'URL per il sito Web di supporto.
  - **Contatto tecnico**: fornire il nome, il telefono e il messaggio di posta elettronica che Microsoft deve usare direttamente quando si verificano problemi con l'offerta. Le informazioni di contatto non sono elencate nel Marketplace commerciale.
  - **Contatto del programma CSP** (facoltativo): specificare il nome, il telefono e il messaggio di posta elettronica se si opta per il programma CSP, in modo che i partner possano contattare l'utente per qualsiasi domanda. È anche possibile includere un URL per i materiali di marketing.
- **Collegamenti utili** (facoltativo): è possibile fornire collegamenti a diverse risorse per gli utenti dell'offerta. Ad esempio forum, domande frequenti e note sulla versione.
- **Documenti di supporto**: è possibile fornire fino a tre documenti rivolte ai clienti, ad esempio white paper, brochure, elenchi di controllo o presentazioni di PowerPoint.
- **Supporti – Logos**: fornire un file PNG per il logo di **grandi** dimensioni. Il centro per i partner lo utilizzerà per creare un logo **piccolo** e **medio** . Facoltativamente, è possibile sostituirli con immagini diverse in un secondo momento.

   - Grande (da 216 x 216 a 350 x 350 px, obbligatorio)
   - Media (90 x 90 px, facoltativo)
   - Piccolo (48 x 48 px, facoltativo)

  Questi logo vengono usati in posizioni diverse negli archivi online:

  - Il logo piccolo viene visualizzato nei risultati della ricerca di Azure Marketplace e nella pagina principale Microsoft AppSource e nei risultati della ricerca.
  - Il logo medio viene visualizzato quando si crea una nuova risorsa in Microsoft Azure.
  - Il logo di grandi dimensioni viene visualizzato nella pagina di inserzione dell'offerta in Azure Marketplace e Microsoft AppSource.

- **Supporti-schermate**: è necessario aggiungere almeno uno e un massimo di cinque screenshot con i requisiti seguenti, che mostrano il funzionamento dell'offerta:
  - 1280 x 720 pixel
  - file con estensione png
  - Deve includere una didascalia
- **Media-video** (facoltativo): è possibile aggiungere fino a quattro video con i requisiti seguenti, che dimostrano l'offerta:
  - Nome
  - URL: deve essere ospitato solo su YouTube o Vimeo.
  - Anteprima: file 1280 x 720. png

> [!Note]
> L'offerta deve soddisfare i [criteri di certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general) generale e i [criteri di software as a Service](https://docs.microsoft.com/legal/marketplace/certification-policies#1000-software-as-a-service-saas) per la pubblicazione nel Marketplace commerciale.

## <a name="preview-audience"></a>Destinatari dell'anteprima
Un pubblico di anteprima può accedere all'offerta prima di essere pubblicata Live nei negozi online per testare la funzionalità end-to-end prima di pubblicarla in tempo reale. Nella pagina **Anteprima destinatari** è possibile definire un numero limitato di destinatari di anteprima. Questa impostazione non è disponibile se si sceglie di elaborare le transazioni in modo indipendente anziché vendere l'offerta tramite Microsoft. In tal caso, è possibile ignorare questa sezione e passare ad [altre opportunità di vendita](#additional-sales-opportunities).

> [!NOTE]
> Un pubblico di anteprima è diverso da un piano privato. Un piano privato è quello reso disponibile solo per un determinato gruppo di destinatari. In questo modo è possibile negoziare un piano personalizzato con clienti specifici. Per ulteriori informazioni, vedere la sezione successiva: piani.

È possibile inviare gli inviti agli indirizzi di posta elettronica dell'account Microsoft (MSA) o Azure Active Directory (Azure AD). Aggiungere fino a 10 indirizzi di posta elettronica manualmente o importare fino a 20 con un file con estensione CSV. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

## <a name="plans"></a>Plans

Le offerte transazionali richiedono almeno un piano. Un piano definisce l'ambito e i limiti della soluzione e i prezzi associati. È possibile creare più piani per l'offerta per offrire ai clienti diverse opzioni tecniche e di prezzo. Se si sceglie di elaborare le transazioni in modo indipendente anziché creare un'offerta transazionale, la pagina **piani** non è visibile. In tal caso, ignorare questa sezione e passare ad [altre opportunità di vendita](#additional-sales-opportunities).

Vedi [piani e prezzi per le offerte del Marketplace commerciale](plans-pricing.md) per indicazioni generali sui piani, inclusi i modelli di prezzi, le versioni di valutazione gratuite e i piani privati. Le sezioni seguenti illustrano informazioni aggiuntive specifiche per le offerte SaaS.

### <a name="saas-pricing-models"></a>Modelli di determinazione dei prezzi per SaaS

Le offerte SaaS possono usare uno dei due modelli di determinazione prezzi per ogni piano, ovvero _Tariffa_ fissa o _utente_. Tutti i piani nella stessa offerta devono essere associati allo stesso modello di determinazione prezzi. Un'offerta, ad esempio, non può avere un piano di tariffa fissa e un altro piano per ogni utente.

**Tariffa** fissa: consente di accedere all'offerta con un singolo prezzo tariffario mensile o annuale. Questo approccio è a volte noto come prezzo basato sul sito. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che usano l'API del servizio di misurazione del Marketplace per addebitare ai clienti l'utilizzo non coperto dalla tariffa fissa. Per altre informazioni sulla fatturazione a consumo, vedere [fatturazione a consumo per Saas usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/saas-metered-billing.md). Usare questa opzione anche se il comportamento di utilizzo per il servizio SaaS si trova in picchi.

**Per utente** : consente di accedere all'offerta con un prezzo basato sul numero di utenti che possono accedere all'offerta o occupare le postazioni. Con questo modello basato sull'utente è possibile impostare il numero minimo e massimo di utenti supportati dal piano. È possibile creare più piani per configurare punti di prezzo diversi in base al numero di utenti. Questi campi sono facoltativi. Se viene lasciato deselezionato, il numero di utenti verrà interpretato come non avente un limite (minimo 1 e massimo di quanti ne può supportare il servizio). Questi campi possono essere modificati nell'ambito di un aggiornamento del piano.

> [!IMPORTANT]
> Dopo la pubblicazione dell'offerta, non è possibile modificare il modello di determinazione dei prezzi. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

### <a name="saas-billing"></a>Fatturazione SaaS

Per le app SaaS eseguite nella sottoscrizione di Azure (editore), l'utilizzo dell'infrastruttura viene addebitato direttamente all'utente; i clienti non visualizzano i costi di utilizzo dell'infrastruttura effettivi. È necessario aggregare i costi di utilizzo dell'infrastruttura di Azure nei prezzi delle licenze software per compensare il costo dell'infrastruttura distribuita per l'esecuzione della soluzione.

Le offerte di app SaaS vendute tramite Microsoft supportano la fatturazione mensile o annuale in base a una tariffa fissa, per utente o a costi di utilizzo usando il [servizio di fatturazione](./partner-center-portal/saas-metered-billing.md)a consumo. Il Marketplace commerciale opera su un modello di agenzia, in base al quale gli editori impostano i prezzi, i clienti di Microsoft fatturazione e i ricavi per gli editori, mantenendo al tempo stesso la tariffa dell'Agenzia.

Questo è un esempio di suddivisione dei costi e dei pagamenti per illustrare il modello di agenzia. In questo esempio Microsoft fattura $ 100,00 al cliente per la licenza software e paga $ 80,00 all'editore.

| Costo della licenza | $ 100 al mese |
| ------------ | ------------- |
| Costo dell'utilizzo di Azure (D1/1-Core) | Costi addebitati direttamente all'editore, non al cliente |
| Importo addebitato da Microsoft al cliente | $ 100,00 al mese. L'editore deve calcolare i costi di infrastruttura sostenuti o trasferirli nei costi di licenza |
| **Microsoft addebita** | **$ 100 al mese** |
| Microsoft paga l'80% del costo della licenza<br>`*` Per le app SaaS qualificate, Microsoft paga il 90% del costo della licenza| $ 80,00 al mese<br>``*`` $ 90,00 al mese |
|||

** `*` Tariffa ridotta del servizio Marketplace** : per alcune offerte SaaS pubblicate sul Marketplace commerciale, Microsoft ridurrà la tariffa del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per le offerte idonee, le offerte devono essere state designate da Microsoft come co-selling IP di Azure incentivate. L'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per ricevere la tariffa del servizio Marketplace ridotta per il mese. La tariffa ridotta per il servizio Marketplace si applica anche alle macchine virtuali incentivate di Azure con co-selling IP di Azure, alle app gestite e a qualsiasi altra offerta IaaS transazionale qualificata resa disponibile tramite il Marketplace commerciale.

## <a name="additional-sales-opportunities"></a>Opportunità di vendita aggiuntive

È possibile scegliere di acconsentire ai canali di marketing e vendita supportati da Microsoft. Quando si crea l'offerta nel centro per i partner, si vedranno due schede verso la fine del processo:

- **Rivendere tramite CSP**: usare questa opzione per consentire ai partner di Microsoft Cloud Solution Provider (CSP) di rivendere la soluzione come parte di un'offerta in bundle. Per ulteriori informazioni, vedere il [programma Cloud Solution Provider](cloud-solution-providers.md) .

- **Co-selling con Microsoft**: questa opzione consente ai team di vendita Microsoft di prendere in considerazione la soluzione di co-selling IP idonea per valutare le esigenze dei clienti. Per informazioni dettagliate su come preparare l'offerta per la valutazione, vedere [opzione di co-selling nel centro](./partner-center-portal/commercial-marketplace-co-sell.md) per i partner.

## <a name="next-steps"></a>Passaggi successivi

- [Come creare un'offerta SaaS nel Marketplace commerciale](create-new-saas-offer.md)
- [Procedure consigliate per le inserzioni di offerte](gtm-offer-listing-best-practices.md)
