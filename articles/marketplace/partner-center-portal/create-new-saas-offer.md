---
title: Creare una nuova offerta SaaS per il marketplace commerciale Microsoft
description: Come creare una nuova offerta Software as a Service (SaaS) per l'elenco o la vendita in Microsoft AppSource, Azure Marketplace o tramite il programma Cloud Solution Provider (CSP) utilizzando il programma Microsoft Commercial Marketplace in Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 45559978f710549088d75d0f0706b798ebad3821
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314079"
---
# <a name="create-a-new-saas-offer"></a>Creare una nuova offerta SaaS

Per iniziare a creare offerte Software as a Service (SaaS), assicurarsi di creare prima [un account del Centro per](./create-account.md) i partner e aprire il dashboard Commercial [Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)con la scheda **Panoramica** selezionata.

![Dashboard Marketplace commerciale nel Centro per i partner](./media/new-offer-overview.png)

>[!Note]
> Una volta pubblicata un'offerta, le modifiche apportate all'offerta effettuata nel Centro per i partner verranno aggiornate nel sistema e archiviate solo dopo la ripubblicazione. Assicurarsi di inviare l'offerta per la pubblicazione dopo aver apportato le modifiche.

Seleziona l'opzione Nuova **offerta...** , quindi selezionare la voce di menu **Software come servizio.**

Se si seleziona un altro tipo di offerta, è possibile che si venga reindirizzati al [portale Cloud Partner](https://cloudpartner.azure.com/)meno recente. Al momento, nel portale di Commercial Marketplace del Centro per i partner sono disponibili solo le offerte SaaS e Dynamics 365.

![Creare una finestra dell'offerta nel Centro per i partner](./media/new-offer-click.png)

Viene visualizzata la finestra di dialogo **Nuova offerta.**

![Finestra di dialogo Nuova offerta](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>ID e alias dell'offerta

- **ID offerta**: Identificatore univoco per ogni offerta nel tuo account. Questo ID sarà visibile ai clienti nell'indirizzo URL dell'offerta del marketplace e nei modelli di Azure Resource Manager (se applicabile). L'ID offerta deve essere minuscolo, alfanumerico (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). **L'ID offerta** è limitato a 50 caratteri e non può essere modificato dopo aver selezionato *Crea*.  
Esempio: test-offer-1
<br>Risultato nell'URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias dell'offerta:** nome utilizzato per fare riferimento all'offerta all'interno del portale del Centro per i partner. Questo nome non verrà utilizzato nel marketplace ed è diverso dal *nome dell'offerta* e da altri valori che verranno mostrati ai clienti. Questo valore non può essere modificato dopo aver selezionato *Crea*.

<br>Esempio: Offerta di test 1&#8482;

Selezionare **Create** (Crea).  Viene creata una pagina **Panoramica offerta** per questa offerta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** include:

- Lo **stato Pubblicazione** visualizza una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta e della durata del completamento di ogni passaggio. Le icone dei passaggi di pubblicazione incompleti saranno disattivate.

- Il menu **Panoramica offerta** contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco di operazioni cambierà in base alla selezione effettuata per l'offerta.  
    - Se l'offerta è una bozza - Elimina bozza
    - Se l'offerta è in diretta - Interrompi offerta di vendita
    - Se l'offerta è in anteprima - Go-live
    - Se non hai completato la disconnessione del publisher - Annulla pubblicazione

## <a name="offer-setup"></a>Configurazione dell'offerta

La scheda **Impostazione offerta** richiede le seguenti informazioni. Selezionare **Salva** dopo aver completato questi campi.

- **Vuoi vendere tramite Microsoft?** (Sì/No)
    - **Sì,** desideri vendere la tua offerta tramite Microsoft, con Microsoft che ospita transazioni sul mercato per tuo conto; O 
    - **No,** si preferisce elencare solo la vostra offerta attraverso i mercati, l'elaborazione di tutte le transazioni monetarie indipendentemente da Microsoft.

### <a name="sell-through-microsoft"></a>Vendita tramite Microsoft

La vendita tramite Microsoft offre una migliore individuazione e acquisizione dei clienti, consente a Microsoft di ospitare transazioni di marketplace per conto dell'utente e sfrutta le funzionalità di commercio disponibili a livello globale di Microsoft.

#### <a name="saas-offer-requirements"></a>Requisiti dell'offerta SaaS

Per elencare le offerte Software as a Service (SaaS) con Commercial Marketplace nel Centro per i partner, è necessario soddisfare i seguenti criteri:

- L'offerta deve usare [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) per la gestione delle identità e l'autenticazione.
- L'offerta deve usare [le API di evasione SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per l'integrazione con Azure Marketplace.
- Per requisiti più ampi, vedere la Guida alla [pubblicazione dell'offerta SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Prezzi e opzioni di fatturazione saaS

Con le soluzioni SaaS in esecuzione nella sottoscrizione di Azure dell'editore, le tariffe di licenza pagate dai clienti includono il costo dell'infrastruttura in cui viene distribuito il software. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente all'utente, il partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori devono raggruppare i costi di utilizzo dell'infrastruttura di Azure nei prezzi delle licenze software. 

SaaS offre supporto per la fatturazione mensile o annuale in base a una tariffa fissa, per utente o spese di consumo utilizzando il servizio di fatturazione a consumo. Il mercato commerciale di Microsoft opera su un modello di agenzia, in base al quale gli editori impostano i prezzi, Microsoft fattura i clienti e Microsoft paga le entrate all'editore trattenendo una commissione di agenzia.

La tabella seguente mostra un esempio di ripartizione dei costi e dei pagamenti per illustrare il modello di agenzia.

|**Costo della licenza**|**100 USD al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Importo addebitato da Microsoft al cliente|100,00 USD al mese (l'editore deve tenere conto dei costi dell'infrastruttura sostenuti o pass-through nella tariffa di licenza)|

|**Microsoft addebita**|**100 USD al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS qualificate, Microsoft paga il 90% del costo della licenza*|$ 80,00 al mese <br>*$* 90,00 al mese|

- In questo esempio, Microsoft fattura 100,00 USD al cliente per la licenza software e paga 80,00 USD per l'editore.
- I partner che si sono qualificati per la Tassa di **servizio del Marketplace ridotto** vedranno una commissione di transazione ridotta sulle offerte SaaS da maggio 2019 a giugno 2020. In questo scenario, Microsoft fattura 100,00 USD per la licenza software e paga 90,00 USD all'editore.

> [!NOTE]
> **Riduzione della tariffa**del servizio Marketplace : Per alcune offerte SaaS pubblicate sul Marketplace commerciale, Microsoft ridurrà la commissione per il servizio Marketplace dal 20% (come descritto nel Contratto Microsoft Publisher) al 10%. Affinché l'offerta sia idonea, almeno una delle offerte deve essere stata designata da Microsoft come pronta per il co-selling IP o con priorità per la co-vendita IP.  L'idoneità deve essere soddisfatta almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per ricevere questa tariffa di servizio Marketplace ridotta per il mese.  La tariffa del servizio Mercatino ridotto non si applica alle macchine virtuali, alle app gestite o a qualsiasi altro prodotto reso disponibile tramite il nostro Commercial Marketplace.  La tariffa per il servizio Marketplace ridotto sarà disponibile solo per le offerte qualificate per le richieste di licenza raccolte da Microsoft tra il 1 maggio 2019 e il 30 giugno 2020.  Dopo questo periodo, la commissione di servizio Marketplace tornerà al suo importo normale.

### <a name="list-through-microsoft"></a>Elenco tramite Microsoft

Promuovi la tua attività con Microsoft creando una presentazione sul marketplace. Se si sceglie di elencare solo l'offerta e non effettuare transazioni tramite Microsoft, Microsoft non partecipa direttamente alle transazioni di licenza software. Non vi è alcuna commissione di transazione associata e l'editore mantiene il 100% delle eventuali spese di licenza software riscosse dal cliente. Tuttavia, l'editore è responsabile del supporto di tutti gli aspetti della transazione di licenza software, incluso, ma non solo: evasione dell'ordine, misurazione, fatturazione, fatturazione, pagamento e ritiro.

- **Come vuoi che i potenziali clienti interagiscano con questa offerta?**

#### <a name="get-it-now-free"></a>Scaricalo ora (gratuito)

Elencare l'offerta ai clienti gratuitamente fornendo un URL valido (a partire da *http* o *https)* in cui è possibile ottenere una versione di valutazione tramite [l'autenticazione con un clic tramite Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Ad esempio: `https://contoso.com/saas-app`

#### <a name="free-trial-listing"></a>Versione di prova gratuita (elenco)

Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da *http* o *https*), in cui è possibile ottenere una versione di valutazione [tramite l'autenticazione con un clic tramite Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)  Ad esempio: `https://contoso.com/trial/saas-app`. L'offerta che elenca le versioni di valutazione gratuite viene creata, gestita e configurata dal servizio e non dispone di sottoscrizioni gestite da Microsoft.Offer listing free trials are created, managed, and configured by your service and do not have subscriptions managed by Microsoft.

> [!NOTE]
> I token che l'applicazione riceverà tramite il collegamento di valutazione possono essere usati solo per ottenere informazioni utente tramite Azure AD per automatizzare la creazione di account nell'app. Gli account Microsoft (MSA) non sono supportati per l'autenticazione tramite questo token.

#### <a name="contact-me"></a>Contact me (Contattami)

Raccogliere le informazioni di contatto del cliente collegando il sistema CRM (Customer Relationship Management). Al cliente verrà chiesto il permesso di condividere le proprie informazioni. Questi dettagli del cliente, insieme al nome dell'offerta, all'ID e all'origine del marketplace in cui hanno trovato l'offerta, verranno inviati al sistema CRM configurato. Per ulteriori informazioni sulla configurazione di CRM, vedere [Connect lead management](#connect-lead-management).

## <a name="example-marketplace-offer-listing"></a>Elenco di offerte di esempio del marketplace

![Elenco di offerte di marketplace di esempio con note](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Un test drive è un ottimo modo per mostrare la tua offerta ai potenziali clienti dando loro la possibilità di "provare prima di acquistare", con conseguente aumento della conversione e la generazione di lead altamente qualificati. Per ulteriori informazioni, consulta [Consentire ai clienti di testare l'offerta.](./test-drive.md)

- **Abilitare un test drive** (casella di controllo)

Abilitando il test drive, verrà chiesto di configurare un ambiente dimostrativo in cui i clienti possono provare l'offerta per un periodo di tempo fisso. 

### <a name="test-drive-resources"></a>Risorse del test drive

- [Test Drive best practice](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)- di marketing[Test Drive best practice tecniche](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Panoramica di Test Drive (download PDF)](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Gestione dei lead di Connect

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Ulteriori risorse per la gestione dei lead
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead Un cercapersone](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ricordarsi di **salvare** prima di passare alla sezione successiva.

## <a name="properties"></a>Proprietà

La scheda **Proprietà** ti chiede di definire le categorie e i settori utilizzati per raggruppare la tua offerta sui marketplace, i contratti legali che supportano la tua offerta e la versione dell'app.

Selezionare **Salva** dopo aver completato questi campi.

### <a name="category"></a>Category

Selezionare un minimo di uno (1) e un massimo di tre (3) categorie utilizzate per raggruppare l'offerta nelle aree di ricerca del marketplace appropriate. Scopri come la tua offerta supporta queste categorie nella descrizione dell'offerta.

### <a name="industry"></a>Settore

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Versione dell'app

Questo campo è facoltativo e viene utilizzato nel marketplace AppSource per identificare il numero di versione dell'offerta.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contratto standard per il mercato commerciale Microsoft

Microsoft fornisce un modello Di contratto standard.

- **Utilizzare il contratto standard per il marketplace commerciale Microsoft?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legale per i fornitori di software, Microsoft offre un contratto standard per il marketplace commerciale Microsoft per facilitare le transazioni nel marketplace. Invece di creare termini e condizioni personalizzati, gli editori del mercato commerciale possono scegliere di offrire il proprio software ai sensi del Contratto Standard, che i clienti devono solo controllare e accettare una volta. Il Contratto Standard può https://go.microsoft.com/fwlink/?linkid=2041178essere trovato qui: .

Puoi scegliere di utilizzare il Contratto Standard invece di fornire termini e condizioni personalizzati selezionando la casella di controllo "Usa il contratto standard per il mercato commerciale".

![Utilizzo della casella di controllo Contratto standard](./media/use-standard-contract.png)

> [!NOTE]
> Dopo aver pubblicato un'offerta utilizzando il contratto Standard per il mercato commerciale Microsoft, non è possibile utilizzare i termini e le condizioni personalizzati. È uno scenario "o". Offrite la vostra soluzione in base al Contratto Standard **o** ai vostri termini e condizioni. Se si desidera modificare i termini del Contratto Standard, è possibile farlo tramite Le modifiche del contratto standard.

#### <a name="standard-contract-amendments"></a>Modifiche del contratto standard

Le modifiche del contratto standard consentono agli editori di selezionare i termini del contratto standard per semplicità e personalizzare i termini per il loro prodotto o azienda. I clienti devono rivedere le modifiche al contratto solo se hanno già esaminato e accettato il contratto standard Microsoft.

Sono disponibili due tipi di emendamenti per gli editori del mercato commerciale:

- Emendamenti universali: questi emendamenti sono applicati universalmente al contratto standard per tutti i clienti. Gli emendamenti universali vengono mostrati a tutti i clienti dell'offerta nel flusso di acquisto. I clienti devono accettare i termini del Contratto Standard e la modifica prima di poter utilizzare l'offerta.
- Modifiche personalizzate: queste modifiche sono modifiche speciali al contratto standard destinate a clienti specifici solo tramite ID tenant di Azure.Custom Amendments: These amendments are special amendments to the Standard Contract that are targeted to specific customers only via Azure tenant IDs. I publisher possono scegliere il tenant di destinazione. Solo ai clienti del tenant verranno presentati i termini di modifica personalizzati nel flusso di acquisto dell'offerta.  I clienti devono accettare i termini del Contratto Standard e gli emendamenti prima di poter utilizzare l'offerta.

>[!NOTE]
> Questi due tipi di emendamenti si sovrappongono l'uno sull'altro. I clienti mirati con modifiche personalizzate otterranno anche la modifica universale al contratto standard durante l'acquisto.

Termini di **modifica universale per il contratto standard per il mercato commerciale di Microsoft**: Immettere i termini di modifica universale in questa casella. È possibile fornire un unico emendamento universale per ogni offerta. È possibile immettere un numero illimitato di caratteri in questa casella. Questi termini vengono visualizzati ai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

Termini di **modifica personalizzati per il contratto standard per il mercato commerciale di Microsoft**: Iniziare selezionando Aggiungi termini di modifica **personalizzata**. Puoi fornire fino a 10 termini di modifica personalizzati per offerta.

- **Termini di modifica personalizzata**: immettere i termini della modifica personalizzata nella casella Termini di modifica personalizzati. È possibile immettere un numero illimitato di caratteri in questa casella. Solo i clienti degli ID tenant specificati per questi termini personalizzati verranno presentati con i termini di modifica personalizzati nel flusso di acquisto dell'offerta nel portale di Azure.Only customers from the tenant IDs you specify for these custom terms will be presented the custom amendment terms in the offer's purchase flow in the Azure portal.  
- **ID tenant** (obbligatorio): ogni modifica personalizzata può essere destinata a un massimo di 20 ID tenant. Se si aggiunge una modifica personalizzata, è necessario fornire almeno un ID tenant. L'ID tenant identifica il cliente in Azure.The tenant ID identifies your customer in Azure. È possibile chiedere questo ID al cliente e può trovarlo passando a portal.azure.com > Azure Active Directory > Properties.You can ask your customer for this ID and they can find it by navigating to portal.azure.com > Azure Active Directory > Properties. Il valore dell'ID directory è l'ID tenant (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). È inoltre possibile cercare l'ID tenant dell'organizzazione del cliente utilizzando l'URL del nome di dominio in [Che cos'è l'ID tenant di Microsoft Azure e Office 365?](https://www.whatismytenantid.com).
- **Descrizione** (facoltativo): fornisci facoltativamente una descrizione descrittiva dell'ID tenant che ti aiuti a identificare il cliente a cui ti rivolgi con la modifica.

#### <a name="terms-and-conditions"></a>Termini e condizioni

Se si desidera fornire termini e condizioni personalizzati, è possibile scegliere di immetterli nel campo Termini e condizioni. In questo campo è possibile immettere fino a 10.000 caratteri di testo. Se i termini e le condizioni richiedono una descrizione più lunga, inserisci un singolo collegamento URL in questo campo in cui è possibile trovare i termini e le condizioni. Verrà visualizzato ai clienti come collegamento attivo.

I clienti sono tenuti ad accettare questi termini prima di poter provare la tua offerta.

Ricordarsi di **salvare** prima di passare alla sezione successiva.

## <a name="offer-listing"></a>Inserzione dell'offerta

La scheda Offerta elenca le lingue (e i mercati) in cui l'offerta è disponibile, attualmente l'inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e la data/ora in cui è stato aggiunto. Dovrai definire i dettagli del mercato (nome dell'offerta, descrizione, termini di ricerca, ecc.) per ogni lingua / mercato.

> [!NOTE]
> Il contenuto dell'inserzione dell'offerta (come la descrizione dell'offerta, i documenti, le schermate, le condizioni per l'utilizzo e l'informativa sulla privacy) non è necessario per essere in inglese, purché la descrizione dell'offerta inizi con la frase "Questa applicazione è disponibile solo in [lingua non in lingua inglese]". È inoltre accettabile fornire un *URL di collegamento utile* per offrire contenuti in una lingua diversa da quella utilizzata nel contenuto della scheda Offerta.

### <a name="offer-listings"></a>Presentazione delle offerte

Fornisci i dettagli da visualizzare nel marketplace, incluse le descrizioni dell'offerta e le risorse di marketing.

- **Nome** (obbligatorio): il nome qui definito verrà visualizzato come titolo della presentazione dell'offerta sui marketplace che hai scelto. Il nome viene prepopolato in base alla voce **Nuova offerta** precedente. Il nome può essere registrato. Non può contenere emoji (a meno che non siano i simboli del marchio e del copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo** (obbligatorio): fornire una breve descrizione dell'offerta da utilizzare nei risultati di ricerca degli elenchi di marketplace. In questo campo è possibile immettere fino a 100 caratteri di testo.
- **Descrizione** (obbligatorio): fornire una descrizione dell'offerta da visualizzare nella panoramica delle inserzioni del marketplace. Prendi in considerazione l'inclusione di una proposta di valore, vantaggi chiave, qualsiasi categoria o associazione di settore, opportunità di acquisto in-app, eventuali divulgazioni obbligatorie e un link per saperne di più.
In questo campo è possibile immettere fino a 3.000 caratteri di testo, incluso il markup. Per ulteriori suggerimenti, consulta l'articolo [Scrivere una descrizione dell'app ideale.](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)
- **Parole chiave di ricerca**: Inserisci fino a tre parole chiave di ricerca che i clienti possono utilizzare per trovare la tua offerta nei marketplace.
- **Istruzioni introduttive** (obbligatorio): spiega come configurare e iniziare a usare l'app per i potenziali clienti.  Questa guida introduttiva può contenere collegamenti a una documentazione online più dettagliata. In questo campo è possibile immettere fino a 3.000 caratteri di testo.

#### <a name="description"></a>**Descrizione**

Questo campo è obbligatorio. Elementi da includere in **Descrizione**:

* Descrivere chiaramente la proposta di valore dell'offerta subito nelle prime frasi della descrizione.  
* Tenere presente che le prime frasi possono essere visualizzate nei risultati dei motori di ricerca.  
* Per vendere un prodotto, non basarsi sulle funzionalità, ma concentrarsi sul valore che può offrire.  
* Usare il più possibile la terminologia specifica del settore o formule che mettano in evidenza i vantaggi offerti.

I componenti fondamentali di una proposta di valore devono includere le informazioni seguenti:

* Descrizione del prodotto.
* Tipo di utente che ottiene vantaggi dal prodotto.
* Esigenze o dolore del cliente.

Per rendere **Description** la descrizione dell'offerta più coinvolgente, usa l'editor RTF per formattare la descrizione.

![Utilizzo dell'editor DI testo RTF](./media/text-editor2.png)

Utilizzare le istruzioni seguenti per utilizzare l'editor RTF:

- Per modificare il formato del contenuto, evidenziare il testo da formattare e selezionare uno stile di testo, come illustrato di seguito:

     ![Utilizzo dell'editor RTF per modificare il formato del testo](./media/text-editor3.png)

- Per aggiungere un elenco puntato o numerato al testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per aggiungere elenchi](./media/text-editor4.png)

- Per aggiungere o rimuovere il rientro del testo, utilizzare le opzioni seguenti:

     ![Utilizzo dell'editor RTF per il rientro](./media/text-editor5.png)

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): collegamento all'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'app sia conforme alle leggi e ai regolamenti sulla privacy e di fornire una valida informativa sulla privacy
- Materiali di marketing del **programma CSP** (facoltativo): fornire un collegamento ai materiali di marketing se si sceglie di estendere l'offerta al programma [Cloud Solution Provider (CSP).](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) CSP estende la tua offerta a una più ampia gamma di clienti qualificati consentendo ai partner CSP di raggruppare, commercializzare e rivendere la tua offerta. Questi rivenditori avranno bisogno di accedere ai materiali per commercializzare la tua offerta. Per ulteriori informazioni, vedere [Go-To-Market Services](https://partner.microsoft.com/reach-customers/gtm).
- **Link utili** (facoltativo): documenti online supplementari facoltativi relativi all'app o ai servizi correlati elencati fornendo un **Titolo** e un **URL.** Aggiungere ulteriori collegamenti utili facendo clic su **Aggiungi un URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: Per ogni contatto cliente, specificare un **nome**dipendente, un **numero di**telefono e un indirizzo di **posta elettronica.**  (Questi *non verranno* visualizzati pubblicamente). Per il gruppo Di **contatto** al supporto tecnico è necessario anche un URL di **supporto.**  (Queste informazioni *verranno* visualizzate pubblicamente).

**Contatto di supporto** (obbligatorio): per domande generali sul supporto tecnico.

**Contatto tecnico** (obbligatorio): per domande tecniche.

**Contatto Channel Manager** (obbligatorio): per le domande dei rivenditori relative al programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): aggiungi i documenti di marketing correlati per la tua offerta, in formato PDF, fornendo un minimo di uno (1) e massimo tre (3) documenti per offerta.
- **Immagini** (opzionale): Ci sono più posizioni in cui le immagini del logo dell'offerta possono essere visualizzate in tutti i marketplace, che richiedono le seguenti dimensioni - Piccolo: 48 x 48 pixel _(obbligatorio),_ Medio: 90 x 90 pixel _(obbligatorio)_, Grande: 216 x 216 pixel _(obbligatorio),_ Wide: 255 x 115 pixel, ed Eroe: 815 x 290 pixel. Tutte le immagini devono essere in . formato PNG.
- **Screenshot (obbligatorio):** aggiungi screenshot che illustrano la tua offerta. È possibile aggiungere un massimo di cinque (5) screenshot e devono essere ridimensionati a 1280 x 720 pixel. Tutte le immagini devono essere in . formato PNG.
- **Video** (facoltativo): aggiungi link a video che illustrano la tua offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. Dovrai anche inserire un'immagine in miniatura del video, dimensionata a 1280 x 720 pixel in formato PNG. Puoi visualizzare un massimo di quattro video per offerta.

Ricordarsi di **salvare** prima di passare alla sezione successiva.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per la presentazione del marketplace

- [Procedure consigliate per le inserzioni con offerte di marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Anteprima

La scheda **Anteprima** consente di definire un pubblico di **anteprima** limitato per il rilascio dell'offerta prima di pubblicare l'offerta in diretta al pubblico del marketplace più ampio.

> [!IMPORTANT]
> Dopo aver controllato l'offerta in Anteprima, seleziona **Passa allo stato Live** in modo che l'offerta possa essere pubblicata in diretta per il pubblico del marketplace.

- **Definire un pubblico di anteprima: aggiungi un singolo indirizzo e-mail di account AAD/MSA per riga, insieme a una descrizione facoltativa.**

Aggiungere fino a 10 indirizzi di posta elettronica manualmente o 20 se si carica un file CSV, per gli account Microsoft (MSA) o Azure Active Directory esistenti per facilitare la convalida dell'offerta prima della pubblicazione dal vivo. Aggiungendo questi account, si definisce un gruppo di destinatari a cui sarà consentito l'accesso in anteprima all'offerta prima che venga pubblicata nei marketplace. Se la tua offerta è già attiva, puoi comunque definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti alla tua offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima può accedere alla tua offerta _prima_ di essere pubblicato in diretta nei marketplace. Puoi anche scegliere di creare un piano e renderlo disponibile solo a un pubblico privato. Nella scheda **dell'elenco dei piani,** puoi definire un pubblico privato con la casella di controllo **Questo è un piano privato.** È quindi possibile definire un gruppo di destinatari privato composto da un massimo di 20.000 clienti usando gli ID tenant di Azure.You can then define a private audience of up to 20,000 customers using Azure Tenant IDs.

## <a name="technical-configuration"></a>Configurazione tecnica

La scheda **Configurazione tecnica** definisce i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. Questa connessione ci consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquistarla. I diagrammi che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione per le API di [evasione SaaS.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)

- **URL pagina** di destinazione (obbligatorio): definire l'URL del sito su cui i clienti verranno attivati dopo l'acquisizione dell'offerta dal marketplace. Questo URL sarà l'endpoint che riceve un token quando un cliente viene instradato alla pagina. Tale token può essere scambiato per i dettagli di provisioning usando la risoluzione nelle API di evasione. Tali dettagli e tutti gli altri che raccogli possono essere utilizzati come parte di una pagina web interattiva del cliente creata nella tua esperienza per completare la registrazione e attivare il loro acquisto.

- **Connessione webhook** (obbligatorio): per tutti gli eventi asincroni che Microsoft deve inviare all'utente per conto del cliente (ad esempio: sottoscrizione SaaS non è più valido), è necessario fornire un webhook di connessione. Se non si dispone già di un sistema webhook, la configurazione più semplice consiste nell'avere un'app per la logica dell'endpoint\/HTTP che ascolterà tutti gli eventi pubblicati e quindi gestirli in modo appropriato (ad esempio, https: /prod-1westus.logic.azure.com:443/work). Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenant di Azure AD** (obbligatorio): all'interno del portale di Azure, è necessario creare [un'app Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo da poter convalidare la connessione tra i due servizi è protetta da una comunicazione autenticata. Per trovare [l'ID tenant,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)passare ad Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID** directory elencato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID app Azure AD** (obbligatorio): sono necessari anche [l'ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e una chiave di autenticazione. Per ottenere tali valori, passare ad Azure Active Directory e selezionare **Registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio, 50c464d3-4930-494c-963c-1e951d15360e). Per trovare la chiave di autenticazione, vai a **Impostazioni** e seleziona **Chiavi**. Dovrai fornire una descrizione e una durata e ti verrà quindi fornito un valore numerico.

>[!Note]
>L'ID applicazione di Azure è associato all'ID editore, quindi assicurati che lo stesso ID applicazione sia usato in tutte le offerte.

## <a name="plan-overview"></a>Panoramica del piano

La scheda **Panoramica piano** consente di fornire un'ampia gamma di opzioni di piano all'interno della stessa offerta. Questi piani (talvolta denominati SKU) potrebbero differire in termini di versione, monetizzazione o livelli di servizio. Devi impostare almeno un piano per vendere la tua offerta sul mercato.

Una volta creati, verranno visualizzati i nomi dei piani, gli ID, i modelli di determinazione dei prezzi, la disponibilità (pubblico o privato), lo stato di pubblicazione corrente e le azioni disponibili.

**Le azioni** disponibili nella **panoramica** del piano variano a seconda dello stato corrente del piano e possono includere:

- Se lo stato del piano è **Bozza** - Elimina bozza
- Se lo stato del piano è **In diretta** - Interrompi vendita del piano o Sincronizza pubblico privato

**Creare un nuovo piano** (minimo di un piano per coloro che scelgono di vendere tramite Microsoft)

- **ID piano:** Creare un ID piano univoco per ogni piano in questa offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto e nei modelli di Azure Resource Manager (se applicabile). Utilizzare solo caratteri minuscoli, alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. L'ID non può essere modificato dopo aver selezionato create.
- **Nome piano:** I clienti vedranno questo nome quando decideranno quale piano selezionare all'interno della tua offerta. Crea un nome di offerta univoco per ogni piano in questa offerta. Il nome del piano viene utilizzato per differenziare i piani software che possono far parte della stessa offerta (ad esempio, nome offerta: Windows Server; piani: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Pianificare l'inserzione

La scheda **dell'elenco** dei piani mostra le lingue (e i mercati) in cui il piano è disponibile, attualmente l'inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e la data/ora in cui è stato aggiunto. Dovrai definire i dettagli del mercato (nome dell'offerta, descrizione, termini di ricerca, ecc.) per ogni lingua / mercato.

#### <a name="plan-listing-details"></a>Pianificare i dettagli dell'inserzione

Selezionando una delle lingue del piano verranno visualizzate le informazioni **sull'elenco** dei piani, inclusi **Nome** e **Descrizione.**

- **Nome**: Precompilato in base all'anteprima **Voce Nuovo piano** e apparirà come titolo del "Piano Software" dell'offerta visualizzato nel marketplace.
- **Descrizione:** Questa descrizione è un'opportunità per spiegare ciò che rende questo piano software unico e tutte le differenze da altri piani software all'interno della vostra offerta. Può contenere fino a 500 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

#### <a name="plan-pricing-and-availability"></a>Pianificare prezzi e disponibilità

La scheda **Prezzi e disponibilità** consente di configurare i mercati in cui questo piano sarà disponibile, il modello di monetizzazione desiderato, il prezzo e il termine di fatturazione. Inoltre, è possibile indicare se rendere il piano visibile a tutti o solo a clienti specifici (un pubblico privato).

##### <a name="enabling-free-trials"></a>Abilitazione delle versioni di prova gratuite

Le offerte SaaS tramite il mercato commerciale consentono di fornire una versione di valutazione gratuita di un mese durante la vendita tramite Microsoft. Per tutti i modelli e i termini di fatturazione, ad eccezione dei piani a consumo, sono supportate le versioni di valutazione gratuite. Questa opzione consente ai clienti di avere una barriera bassa per l'ingresso attraverso un mese di accesso gratuito.  Se si sceglie di abilitare una versione di valutazione gratuita per i piani all'interno dell'offerta, il cliente non sarà in grado di convertire in un abbonamento a pagamento prima della fine del periodo iniziale di un mese.  Durante questo periodo, i clienti che acquistano la tua offerta possono provare uno qualsiasi dei piani supportati che hanno la prova gratuita abilitata e convertire tra di loro.  La conversione in un abbonamento a pagamento viene eseguita automaticamente alla fine del periodo di validità.

>[!Note]
>Se il cliente sceglie di convertire in un piano senza prove gratuite, la conversione avverrà, ma la prova gratuita andrà persa immediatamente.  Inoltre, una volta che un cliente inizia a pagare per un piano, non può più ottenere la prova gratuita sullo stesso abbonamento di nuovo, anche se si converte in uno SKU che supporta le versioni di valutazione gratuite.

La possibilità di configurare una versione di valutazione gratuita è disponibile per ogni piano dell'offerta. Accedi a Prezzi e disponibilità per ogni offerta e seleziona la casella per consentire una versione di valutazione di un mese.

![Casella di controllo di prova gratuita di un mese](./media/free-trial-enable.png)

>[!Note]
>Una volta che l'offerta transazionale è stata pubblicata con una versione di valutazione gratuita, non può essere disabilitata per tale piano. Assicurarsi che questa impostazione sia corretta per la prima pubblicazione per evitare di dover ricreare il piano.

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente `isFreeTrial`partecipano a una versione di valutazione gratuita, utilizzare la nuova proprietà API , che verrà contrassegnata come true o false. Per altre informazioni, vedere [l'API SaaS Get Subscription](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription).

>[!Note]
>Le versioni di valutazione gratuite non sono supportate per i piani che sfruttano il servizio di misurazione del marketplace.

#### <a name="markets"></a>Mercati

- **Modifica mercati** (opzionale)

Ogni piano deve essere disponibile in almeno un mercato. Seleziona la casella di controllo per qualsiasi località di mercato in cui desideri rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi "Tassa rimessi", in cui Microsoft rimette le vendite e l'imposta sull'utente per conto dell'utente, sono inclusi.

Se hai già impostato i prezzi per il tuo piano in Dollari USA (USD) e aggiungi un'altra posizione di mercato, il prezzo per il nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Rivedere il prezzo per ogni mercato prima della pubblicazione. I prezzi possono essere rivisti utilizzando il collegamento "Prezzi all'esportazione (xlsx)" dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

- **Modello di determinazione dei prezzi**: Tariffa fissa o Basata su Seat

**Tariffa forfettaria:** Abilita l'accesso alla tua offerta con un unico prezzo forfettario mensile o annuale. Questo è a volte indicato come prezzi basati sul sito. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che utilizzano l'API del servizio di misurazione del marketplace per addebitare ai clienti in base a unità non standard.  Per ulteriori informazioni sulla fatturazione a consumo, vedere [Fatturazione a consumo mediante il servizio](./saas-metered-billing.md)di misurazione del marketplace .

**Per utente:** Abilita l'accesso alla tua offerta con il prezzo in base al numero di utenti che accedono all'offerta o occupano posti a sedere. Questo modello basato sull'utente consente di impostare il numero minimo e massimo di utenti consentiti in base al prezzo. In questo modo, è possibile configurare diversi punti di prezzo in base al numero di utenti configurando più piani.  Questi campi sono facoltativi. Se lasciato deselezionata, il numero di utenti verrà interpretato come non avere un limite (min di 1 e massimo di quanti il sistema può supportare). Questi campi possono essere modificati come parte di un aggiornamento del piano.

Una volta pubblicata, la scelta del modello di prezzo di fatturazione non può essere modificata. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione dei prezzi.

- **Termine di fatturazione**: Mensile o Annuale

Selezionare la frequenza con cui i clienti devono pagare il prezzo indicato. È necessario fornire almeno un prezzo mensile o annuale oppure è possibile mettere a disposizione dei clienti entrambe le opzioni.

- **Prezzo**: USD al mese o USD all'anno

I prezzi impostati in valuta locale (USD e dollaro degli Stati Uniti) vengono convertiti nella valuta locale di tutti i mercati selezionati utilizzando i tassi di cambio correnti disponibili durante l'impostazione. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e rivedendo il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida di questi prezzi e possiede queste impostazioni.
*\*È innanzitutto necessario salvare le modifiche dei prezzi per consentire l'esportazione dei dati sui prezzi.*

Rivedi attentamente i tuoi prezzi prima della pubblicazione, poiché ci sono alcune restrizioni su ciò che può cambiare dopo la pubblicazione di un piano:

- Una volta pubblicato un piano, il modello di determinazione dei prezzi non può essere modificato.
- Una volta pubblicato, un termine di fatturazione per un piano, non può essere rimosso in un secondo momento.
- Una volta pubblicato, un prezzo per un mercato nel tuo piano, non può essere modificato in un secondo momento.

### <a name="plan-audience"></a>Pianificare il pubblico

Hai la possibilità di configurare ogni piano per essere visibile a tutti o solo a un pubblico specifico di tua scelta. È possibile assegnare l'appartenenza a questo gruppo di destinatari con restrizioni usando gli ID tenant di Azure AD.

#### <a name="privacy"></a>Privacy

- **Questo è un piano privato** (casella di controllo opzionale)

Seleziona questa casella per rendere il tuo piano privato e visibile solo al pubblico limitato di tua scelta. Una volta pubblicato come piano privato, puoi aggiornare il pubblico o scegliere di rendere il piano disponibile a tutti. Una volta che un piano è pubblicato come visibile a tutti, deve rimanere visibile a tutti. (Il piano non può essere configurato nuovamente come piano privato).

- **Destinatari con restrizioni (ID tenant)**

Assegnare il gruppo di destinatari che avrà accesso a questo piano privato. Ad accedono viene assegnato tramite ID tenant con la possibilità di includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 ID tenant o 20.000 ID tenant clienti se si importa un file di foglio di calcolo con estensione csv.

Un tenant è una rappresentazione di un'organizzazione, con un ID rappresentato come GUID (Globally Unique Identifier, un numero intero a 128 bit utilizzato per identificare le risorse). Si tratta di un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando l'organizzazione o lo sviluppatore di app crea una relazione con Microsoft, ad esempio quando si effettua l'iscrizione ad Azure, Microsoft Intune o Microsoft 365.It's a dedicated instance of Azure AD that an organization or app developer receives when the organization or app developer creates a relationship with Microsoft, for example, when signing up for Azure, Microsoft Intune or Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Per controllare il tenant, accedere al portale di Azure con l'account che si vuole usare per gestire l'applicazione. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account. Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio. Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID. È inoltre possibile cercare l'ID tenant dell'organizzazione [https://www.whatismytenantid.com](https://www.whatismytenantid.com)utilizzando un URL del nome di dominio all'indirizzo: .

Mentre SaaS offre l'uso di ID tenant per definire un gruppo di destinatari privato, altri tipi di offerta possono usare gli ID sottoscrizione di Azure (rappresentati anche come GUID).

> [!NOTE]
> Il pubblico privato (o pubblico con restrizioni) è diverso da un pubblico di anteprima. Nella scheda **[Anteprima,](#preview)** è possibile definire un pubblico di anteprima. Un pubblico di anteprima può accedere alla tua offerta *prima* che l'offerta venga pubblicata dal vivo nel marketplace. Mentre la designazione del pubblico privato si applica solo a un piano specifico, il pubblico di anteprima può visualizzare tutti i piani (privati o meno), ma solo durante il periodo di anteprima limitato mentre il piano viene testato e convalidato.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Elenco di esempio di piani all'interno di un'offerta di marketplace

![Elenco di esempio del piano del marketplace con note](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>Pubblico rivenditore Cloud Solution Provider (CSP)

L'opzione per rendere disponibile la tua offerta nel programma CSP consente ai Cloud Solution Provider di vendere il tuo prodotto come parte di una soluzione integrata ai loro clienti. Per ulteriori informazioni, vedere [Cloud Solution Providers](https://go.microsoft.com/fwlink/?linkid=2111109).

## <a name="publish"></a>Pubblica

Dopo aver completato tutte le sezioni richieste dell'offerta, selezionare **Pubblica** nell'angolo in alto a destra del portale. Si verrà reindirizzati alla pagina **Revisione e pubblicazione.**

### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Se è la prima volta che pubblichi questa offerta, puoi:

- Vedi lo stato di completamento per ogni sezione dell'offerta.
    - *Non avviato* - significa che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* - significa che la sezione contiene errori che devono essere corretti o che richiedono ulteriori informazioni. Dovrai tornare alla sezione e aggiornarla.
    - *Completo* - significa che la sezione è completa, tutti i dati richiesti sono stati forniti e non ci sono errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Fornisci istruzioni di test al team di certificazione per assicurarti che l'app sia testata correttamente, oltre a eventuali note aggiuntive utili per comprendere la tua app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Ti invieremo un'email per comunicarti quando è disponibile una versione di anteprima dell'offerta da rivedere e approvare. Devi tornare al Centro per i partner e selezionare **Go-live** per l'offerta di pubblicare la tua offerta al pubblico (o se un'offerta privata, al pubblico privato).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel marketplace commerciale](./update-existing-offer.md)
