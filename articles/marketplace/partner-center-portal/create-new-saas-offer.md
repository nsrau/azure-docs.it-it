---
title: Creare una nuova offerta SaaS nel Marketplace commerciale
description: Come creare una nuova offerta SaaS (software as a Service) per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cea763416f36abd80b1d22b4414cc2454bc30c66
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868734"
---
# <a name="create-a-new-saas-offer"></a>Creare una nuova offerta SaaS

Per iniziare a creare offerte SaaS (software as a Service), prima di tutto è necessario [creare un account del centro](./create-account.md) per i partner e aprire il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), con la scheda **Panoramica** selezionata.

![Dashboard del Marketplace commerciale nel centro per i partner](./media/new-offer-overview.png)

Selezionare l' **offerta + nuova...** , quindi selezionare la voce **di menu software come servizio** . 

Se si seleziona uno degli altri tipi di offerta, si verrà reindirizzati al [portale cloud partner](https://cloudpartner.azure.com/)precedente.  Al momento, solo le offerte SaaS sono disponibili nel portale del Marketplace commerciale nel centro per i partner. 

![Crea finestra offerta nel centro per i partner](./media/new-offer-click.png)

Verrà visualizzata la finestra di dialogo **nuova offerta** . 

![Finestra di dialogo nuova offerta](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>ID offerta e alias

- **Offer ID** (ID offerta): Identificatore univoco per ogni offerta nell'account. Questo ID sarà visibile ai clienti nell'indirizzo URL per l'offerta del Marketplace e i modelli Azure Resource Manager (se applicabile). L'ID offerta deve essere minuscolo, alfanumerico (inclusi trattini e caratteri di sottolineatura, ma senza spazi vuoti). Questa operazione è limitata a 50 caratteri e non può essere modificata dopo aver selezionato *Crea*.  
Esempio: test-offer-1
<br>Risultato dell'URL:`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias offerta**: Nome usato per fare riferimento all'offerta nel portale del centro per i partner. Questo nome non verrà usato nel Marketplace ed è diverso dal nome dell' *offerta* e da altri valori che verranno visualizzati ai clienti. Questo valore non può essere modificato dopo aver selezionato *Crea*.

<br>Esempio: Offerta di test 1&#8482;

Selezionare **Create**.  Viene creata una pagina di **Panoramica dell'offerta** per questa offerta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Panoramica dell'offerta

La pagina **Panoramica dell'offerta** include: 

- Lo **stato di pubblicazione** consente di visualizzare una rappresentazione visiva dei passaggi necessari per pubblicare l'offerta e il tempo necessario per il completamento di ogni passaggio. Le icone delle fasi di pubblicazione incomplete saranno visualizzate in grigio. 

- Il menu **Panoramica dell'offerta** contiene un elenco di collegamenti per l'esecuzione di operazioni su questa offerta. Questo elenco di operazioni verrà modificato in base alla selezione effettuata per l'offerta.  
    - Se l'offerta è una bozza di eliminazione 
    - Se l'offerta è Live-stop sell offer 
    - Se l'offerta è in anteprima – Go-Live 
    - Se la disconnessione del server di pubblicazione non è stata completata, Annulla pubblicazione

## <a name="offer-setup"></a>Installazione dell'offerta

La scheda **installazione offerta** richiede le informazioni seguenti. Selezionare **Salva** dopo aver completato questi campi.

- **Si vuole vendere tramite Microsoft?** (Sì/no)
    - **Sì**, vuoi vendere la tua offerta tramite Microsoft, con Microsoft che ospita le transazioni del Marketplace per conto tuo. o 
    - **No**, preferisci semplicemente elencare l'offerta attraverso i Marketplace, elaborando le transazioni monetarie in modo indipendente da Microsoft.    

### <a name="sell-through-microsoft"></a>Vendita tramite Microsoft

La vendita tramite Microsoft offre una migliore individuazione e acquisizione dei clienti, consente a Microsoft di ospitare le transazioni del Marketplace per conto dell'utente e sfrutta le funzionalità di Commerce disponibili a livello globale di Microsoft.

#### <a name="saas-offer-requirements"></a>Requisiti dell'offerta SaaS

Per elencare le offerte SaaS (software as a Service) con Marketplace commerciale nel centro per i partner, è necessario soddisfare i criteri seguenti:

- L'offerta deve usare [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) per la gestione delle identità e l'autenticazione.
- L'offerta deve usare [API di evasione Saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) per l'integrazione con Azure Marketplace.
- Per requisiti più estesi, vedere la [Guida alla pubblicazione dell'offerta SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>Costi dell'infrastruttura di fatturazione SaaS in Azure
Se l'offerta SaaS è ospitata in Azure, l'utente, come autore, deve tenere in considerazione le tariffe di utilizzo dell'infrastruttura di Azure e le tariffe di licenza software come un singolo costo. Questo costo è rappresentato come una tariffa mensile fissa per il cliente. L'utilizzo dell'infrastruttura di Azure viene gestito e fatturato direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. 

I costi di licenza software vengono presentati come una tariffa mensile di sottoscrizione basata su sito ricorrente e non sono a consumo o a consumo.

|**Costo della licenza**|**$100 al mese**|
|:---|:---|
|Costo dell'utilizzo di Azure (D1/1-Core)|Costi addebitati direttamente all'editore, non al cliente|
|Il cliente viene fatturato da Microsoft|$100,00 al mese (l'editore deve tenere conto di eventuali costi dell'infrastruttura sostenuti o pass-through nel costo della licenza)|

- In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
- Ai partner qualificati per la **Tariffa ridotta del servizio Marketplace** verrà visualizzato un importo ridotto delle transazioni sulle offerte SaaS, dal 2019 maggio fino al giugno 2020. In questo scenario Microsoft fattura $100,00 per la licenza software e paga $90,00 all'editore.

> [!NOTE]
> **Costo del servizio Marketplace ridotto**: Per alcune offerte SaaS pubblicate sul Marketplace commerciale, Microsoft ridurrà il costo del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%. Per qualificare l'offerta, è necessario che almeno una delle offerte sia stata designata da Microsoft come indirizzo di co-selling IP pronto o co-selling IP in ordine di priorità.  L'idoneità deve essere soddisfatta per almeno cinque (5) giorni lavorativi prima della fine di ogni mese di calendario per poter ricevere questa tariffa per il servizio Marketplace ridotta per il mese.  La tariffa ridotta del servizio Marketplace non si applica alle macchine virtuali, alle app gestite o ad altri prodotti resi disponibili tramite il Marketplace commerciale.  Il costo del servizio Marketplace ridotto sarà disponibile solo per le offerte qualificate per i costi di licenza raccolti da Microsoft tra il 1 ° maggio 2019 e il 30 giugno 2020.  Al termine di questo periodo di tempo, la tariffa del servizio Marketplace tornerà al valore normale. 

|**Microsoft addebita**|**$100 al mese**|
|:---|:---|
|Microsoft paga l'80% del costo della licenza <br>**Per le app SaaS qualificate, Microsoft paga il 90% del costo della licenza*|$ 80,00 al mese <br>*$* 90,00 al mese *|


#### <a name="csp-program-opt-in"></a>Consenso esplicito al programma CSP
Il programma [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) consente alle offerte software di raggiungere milioni di clienti Microsoft qualificati con un investimento minimo di marketing e vendite.

- **Canali Rendere disponibile l'offerta nel programma** CSP (casella di controllo)

La scelta di rendere disponibile l'offerta nel programma CSP consente ai provider di soluzioni cloud di vendere il prodotto come parte di una soluzione in bundle ai clienti. 

### <a name="list-through-microsoft"></a>Elenca tramite Microsoft

Promuovere la propria azienda con Microsoft creando un elenco di Marketplace. Se si seleziona per elencare solo l'offerta e non la transazione tramite Microsoft, Microsoft non partecipa direttamente alle transazioni di licenza software. Non viene addebitata alcuna tariffa per le transazioni e l'editore mantiene il 100% dei costi di licenza software raccolti dal cliente. Tuttavia, il server di pubblicazione è responsabile del supporto di tutti gli aspetti della transazione di licenza software, inclusi, a titolo esemplificativo, evasione degli ordini, misurazione, fatturazione, fatturazione, pagamento e raccolta. 

- **In che modo si desidera che i clienti possano interagire con questa offerta di inserzione?**

##### <a name="get-it-now-free"></a>Ottieni ora (gratuito)
Elenca gratuitamente l'offerta ai clienti fornendo un URL valido (a partire da http o HTTPS) dove possono accedere all'app.  Ad esempio: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Versione di valutazione gratuita (elenco)
Elencare l'offerta ai clienti con un collegamento a una versione di valutazione gratuita fornendo un URL valido (a partire da http o HTTPS) dove possono accedere all'app.  Ad esempio: `https://contoso.com/trial/saas-app`. Le versioni di valutazione gratuite dell'elenco di offerte vengono create, gestite e configurate dal servizio e non hanno sottoscrizioni gestite da Microsoft.

##### <a name="contact-me"></a>Contact me (Contattami)
Raccogliere le informazioni di contatto del cliente connettendosi al sistema CRM (Customer Relationship Management). Al cliente verrà richiesta l'autorizzazione a condividere le informazioni. Queste informazioni sui clienti, insieme al nome dell'offerta, all'ID e all'origine del Marketplace in cui è stata trovata l'offerta, verranno inviate al sistema CRM configurato. Per ulteriori informazioni sulla configurazione del CRM, vedere [Connect Lead Management](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Elenco di offerte del Marketplace di esempio

![Elenco delle offerte di Marketplace di esempio con note](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Abilitare un test drive

Un test drive è un ottimo modo per presentare l'offerta ai potenziali clienti offrendo loro la possibilità di provare prima di acquistare, ottenendo una maggiore conversione e la generazione di lead altamente qualificati. [Altre informazioni sulle unità di test.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Abilitare una test drive** casella 

Abilitando test drive, verrà richiesto di configurare un ambiente dimostrativo per consentire ai clienti di provare l'offerta per un periodo di tempo fisso. 

### <a name="type-of-test-drive"></a>Tipo di test drive

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure.
- **[Dynamics 365 for business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali centrale (finanza, operazioni, supply chain, CRM e così via).  
- **[Dynamics 365 per il coinvolgimento dei clienti](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di engagement dei clienti (vendite, servizio, servizio del progetto, servizio campo e così via).  
- **[Dynamics 365 per le operazioni](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft ospita e gestisce il servizio test drive (inclusi il provisioning e la distribuzione) per un sistema di pianificazione delle risorse aziendali finanziarie e operative (finanza, operazioni, produzione, supply chain e così via). 
- **[App](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** per la logica: Un modello di distribuzione che comprende tutte le architetture di soluzioni complesse. Tutti i prodotti personalizzati devono usare questo tipo di test drive.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : Un collegamento incorporato a un dashboard personalizzato. I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo devono usare questo tipo di test drive. È sufficiente caricare l'URL di Power BI incorporato qui.

#### <a name="additional-test-drive-resources"></a>Risorse test drive aggiuntive
- [Procedure consigliate tecniche di test drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Procedure consigliate di marketing di test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Panoramica di test drive un cercapersone](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Gestione dei lead di connessione

È possibile connettersi direttamente con i clienti elencando l'offerta nei Marketplace e associando il sistema CRM (Customer Relationship Management) in modo da ricevere le informazioni di contatto del cliente immediatamente dopo che un cliente esprime l'interesse o distribuisce il prodotto.

- **Scegliere una destinazione principale** (menu a discesa): Fornire i dettagli della connessione al sistema CRM in cui si desidera inviare i lead dei clienti. 

Il centro per i partner supporta i sistemi CRM seguenti per la gestione dei lead. Selezionare il collegamento per le istruzioni di installazione.

- BLOB di Azure: fornire posta elettronica di contatto, nome del contenitore e stringa di connessione dell'account di archiviazione. 
- [Tabella di Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) : specificare la stringa di connessione dell'account di archiviazione e di contatto. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) : fornire l'indirizzo di posta elettronica di contatto, l'URL e la modalità di autenticazione (Office 365 o Azure Active Directory).
- [Endpoint HTTPS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) : fornire la posta elettronica di contatto e l'URL dell'endpoint HTTPS. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) : fornire posta elettronica di contatto, ID modulo, ID account Munchkin e ID server.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) : fornire un indirizzo di posta elettronica di contatto e un ID organizzazione. 

#### <a name="additional-lead-management-resources"></a>Risorse aggiuntive per la gestione dei lead
- [Domande frequenti sulla gestione dei lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Errori comuni di configurazione del lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Panoramica della gestione dei lead un cercapersone](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

## <a name="properties"></a>Properties
La scheda **Proprietà** richiede di definire le categorie e i settori usati per raggruppare l'offerta nei Marketplace, i contratti legali che supportano l'offerta e la versione dell'app. 

Selezionare **Salva** dopo aver completato questi campi. 

### <a name="category"></a>Category
Selezionare almeno uno (1) e un massimo di tre (3) categorie utilizzate per raggruppare l'offerta nelle aree di ricerca del Marketplace appropriate. Per scoprire come l'offerta supporta queste categorie nella descrizione dell'offerta. 

### <a name="industry"></a>Settore
Selezionare un massimo di due (2) settori usati per raggruppare l'offerta nelle aree di ricerca del Marketplace appropriate. Se l'offerta non è specifica di un settore, non selezionarne una. Per scoprire come l'offerta supporta le industrie selezionate nella descrizione dell'offerta, 

### <a name="app-version"></a>Versione dell'app
Si tratta di un campo facoltativo usato in AppSource Marketplace per identificare il numero di versione dell'offerta. 

### <a name="standard-contract"></a>Contratto standard

- **Usare il contratto standard?**

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità giuridica per i fornitori di software, Microsoft offre un modello di contratto standard per facilitare una transazione nel Marketplace. 

Anziché creare termini e condizioni personalizzati, gli editori di Azure Marketplace possono scegliere di offrire il proprio software al di sotto del contratto standard, che i clienti devono solo controllare e accettare una sola volta. 

Il contratto standard è disponibile qui: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se le condizioni di licenza sono diverse da quelle del contratto standard, è possibile scegliere di immettere le condizioni legali per l'utilizzo qui. In questo campo è anche possibile immettere fino a 10.000 caratteri di testo. Se le condizioni per l'utilizzo richiedono una descrizione più lunga, immettere un solo collegamento URL in questo campo in cui è possibile trovare le condizioni di licenza aggiuntive. Verrà visualizzato come collegamento attivo ai clienti.

Per ottenere la versione di prova dell'app, i clienti devono accettare tali condizioni. 

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

## <a name="offer-listing"></a>Elenco offerte

Nella scheda elenco offerte vengono visualizzate le lingue (e i mercati) in cui è disponibile l'offerta, attualmente in lingua inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. Sarà necessario definire i dettagli del Marketplace (nome dell'offerta, descrizione, termini di ricerca e così via) per ogni lingua/mercato.

> [!NOTE]
> Il contenuto dell'elenco di offerte, ad esempio Descrizione dell'offerta, documenti, schermate, condizioni per l'utilizzo e informativa sulla privacy, non deve essere in inglese finché la descrizione dell'offerta inizia con la frase "questa applicazione è disponibile solo in [lingua non inglese]". È anche accettabile fornire un URL di *collegamento utile* per offrire contenuto in una lingua diversa da quella usata nell'offerta di visualizzazione del contenuto.

### <a name="offer-listings"></a>Presentazione delle offerte

Fornire i dettagli da visualizzare nel Marketplace, incluse le descrizioni dell'offerta e le risorse di marketing.

- **Nome** (obbligatorio): Il nome definito qui verrà visualizzato come titolo dell'inserzione dell'offerta nei Marketplace scelti. Il nome viene prepopolato in base alla **nuova** voce dell'offerta precedente.  Questo può essere registrato.  Questo non deve contenere spazi vuoti, emoji (a meno che non siano i marchi e i simboli di copyright) e deve essere limitato a 50 caratteri.
- **Riepilogo** di (obbligatorio): Fornire una breve descrizione dell'offerta da usare nei risultati della ricerca nell'elenco dei Marketplace. In questo campo è possibile immettere fino a 100 caratteri di testo.
- **Descrizione** di (obbligatorio): Fornire una descrizione dell'offerta da visualizzare nella panoramica degli annunci del Marketplace. Si consiglia di includere una proposta di valore, i vantaggi principali, le associazioni di categoria o di settore, le opportunità di acquisto in-app, le divulgazioni richieste e un collegamento per ottenere ulteriori informazioni.
In questo campo è possibile immettere fino a 3.000 caratteri di testo. Per altri suggerimenti, vedere l'articolo [scrivere un'ottima descrizione dell'app](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Parole chiave di ricerca**: Immettere fino a tre parole chiave di ricerca che i clienti possono usare per trovare l'offerta nel Marketplace.
- **Istruzioni introduttive** (obbligatorio): Spiega come configurare e iniziare a usare la tua app per i potenziali clienti.  Questa Guida introduttiva può contenere collegamenti a documentazione online più dettagliata. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

#### <a name="links"></a>Collegamenti

- **Informativa sulla privacy** (obbligatorio): Collegamento all'informativa sulla privacy dell'organizzazione. L'utente è responsabile di garantire che l'applicazione sia conforme alle leggi e alle normative sulla privacy e per fornire un'informativa sulla privacy valida
- **Materiali di marketing del programma CSP** (facoltativo): È necessario fornire un collegamento ai materiali di marketing se si sceglie di estendere l'offerta al programma [Cloud Solution Provider (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . CSP estende l'offerta a una gamma più ampia di clienti qualificati, permettendo ai partner CSP di aggregare, commercializzare e rivendere l'offerta. Questi rivenditori dovranno avere accesso ai materiali per il marketing dell'offerta. Per ulteriori informazioni, vedere la pagina relativa ai [Servizi go-to-Market](https://partner.microsoft.com/reach-customers/gtm).
- **Collegamenti utili** (facoltativo): Documenti online supplementari facoltativi sull'app o sui servizi correlati elencati fornendo un **titolo** e un **URL**. Aggiungere altri collegamenti utili facendo clic su **+ Aggiungi URL**.

#### <a name="contact-information"></a>Informazioni contatto

- **Contatti**: Per ogni contatto del cliente, fornire un **nome** di dipendente, un **numero di telefono**e un indirizzo di **posta elettronica** .  (Questi *non verranno* visualizzati pubblicamente). Per il gruppo di **contatti di supporto** è necessario anche un **URL di supporto** .  (Queste informazioni *verranno* visualizzate pubblicamente).

**Contatto supporto tecnico** (obbligatorio): Per domande di supporto generale.

**Contatto tecnico** (obbligatorio): Per domande tecniche.

**Contatto di gestione canali** (obbligatorio): Per domande sul rivenditore correlate al programma CSP.

#### <a name="files-and-images"></a>File e immagini

- **Documenti** (obbligatorio): Aggiungere i documenti di marketing correlati per l'offerta, in formato PDF, specificando almeno uno (1) e un massimo di tre (3) documenti per ogni offerta.
- **Immagini** di (facoltativo): Sono disponibili più posizioni in cui le immagini del logo dell'offerta possono apparire in tutti i Marketplace, richiedendo le dimensioni seguenti: Small: 48 x 48 pixel _(obbligatorio),_ medio: 90 x 90 pixel, grande: 216 x 216 pixel _(obbligatorio),_ Wide: 255 x 115 pixel e Hero: 815 x 290 pixel. Tutte le immagini devono essere presenti in. Formato PNG.
- **Schermate** (obbligatorio): Aggiungere screenshot che dimostrano l'offerta. È possibile aggiungere un massimo di cinque (5) schermate e dimensionarle a 1280 x 720 pixel. Tutte le immagini devono essere presenti in. Formato PNG.
- **Video** di (facoltativo): Aggiungere collegamenti a video che dimostrano l'offerta. È possibile usare collegamenti a video di YouTube e/o Vimeo che vengono visualizzati insieme all'offerta ai clienti. Sarà inoltre necessario immettere un'immagine di anteprima del video, ridimensionata a 1280 x 720 pixel in formato PNG. È possibile visualizzare un massimo di quattro video per ogni offerta.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

#### <a name="additional-marketplace-listing-resources"></a>Risorse aggiuntive per l'elenco del Marketplace

- [Procedure consigliate per gli elenchi di offerte del Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Anteprima

La scheda **Anteprima** consente di definire un pubblico di **Anteprima** limitato per il rilascio dell'offerta prima di pubblicare l'offerta in tempo reale per i destinatari più ampi del Marketplace.

> [!IMPORTANT]
> È necessario selezionare **Go Live** prima che l'offerta venga pubblicata in tempo reale per il pubblico del Marketplace dopo aver verificato l'offerta in anteprima.

- **Definire un gruppo di destinatari di anteprima: Aggiungere un solo indirizzo di posta elettronica dell'account AAD/MSA per riga, insieme a una descrizione facoltativa.**

È possibile aggiungere fino a dieci (10) indirizzi di posta elettronica manualmente o 20 (20) se si carica un file CSV, per gli account Microsoft (MSA) o Azure Active Directory (AAD) esistenti per convalidare l'offerta prima della pubblicazione in tempo reale. Aggiungendo questi account, si definisce un gruppo di destinatari a cui verrà consentito l'accesso in anteprima all'offerta prima che venga pubblicata nei Marketplace. Se l'offerta è già attiva, è comunque possibile definire un pubblico di anteprima per testare eventuali modifiche o aggiornamenti all'offerta.

> [!NOTE]
> Il pubblico di anteprima è diverso da un pubblico privato. Un pubblico di anteprima è autorizzato ad accedere all'offerta _prima_ di essere pubblicata Live nei Marketplace. È anche possibile scegliere di creare un piano e renderlo disponibile solo per i destinatari privati. Nella scheda **elenco piani** è possibile definire un pubblico privato con la casella di controllo **questo è un piano privato** . È quindi possibile definire un pubblico privato di un massimo di 20.000 clienti usando gli ID tenant di Azure.

## <a name="technical-configuration"></a>Configurazione tecnica

La scheda **configurazione tecnica** definisce i dettagli tecnici (percorso URL, webhook, ID tenant e ID app) usati per connettersi all'offerta. Questa connessione consente di effettuare il provisioning dell'offerta per il cliente finale se sceglie di acquisirla. I diagrammi che descrivono l'utilizzo dei campi raccolti sono disponibili nella documentazione per le [API di evasione Saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL della pagina di destinazione** (obbligatorio): Definire l'URL del sito su cui i clienti potranno atterrare dopo aver acquisito l'offerta dal Marketplace. Questo URL sarà l'endpoint che riceve un token quando un cliente viene indirizzato alla pagina. Questo token può essere scambiato per i dettagli del provisioning usando Risolvi nelle API di evasione. Questi dettagli e altri utenti raccolti possono essere usati come parte di una pagina Web interattiva del cliente integrata nell'esperienza per completare la registrazione e attivare l'acquisto.

- **Webhook di connessione** (obbligatorio): Per tutti gli eventi asincroni che Microsoft deve inviare all'utente per conto del cliente (ad esempio, La sottoscrizione SaaS non è più valida), è necessario fornire un webhook di connessione. Se non si dispone già di un sistema di Webhook, la configurazione più semplice consiste nel disporre di un'app per la logica endpoint HTTP che resterà in ascolto di tutti gli eventi da inviare e quindi di gestirli in modo\/appropriato (ad esempio https:/prod-1westus.Logic.Azure.com:443/Work). Per altre informazioni, vedere [Chiamare, attivare o annidare i flussi di lavoro con endpoint HTTP in app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID tenant Azure ad** (obbligatorio): All'interno portale di Azure è necessario [creare un'app Azure Active Directory (ad)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) in modo che sia possibile convalidare la connessione tra i due servizi dietro una comunicazione autenticata. Per trovare l' [ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), passare alla Azure Active Directory e selezionare **Proprietà**, quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

- **ID app Azure ad** (obbligatorio): Sono necessari anche l' [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e una chiave di autenticazione. Per ottenere tali valori, passare alla Azure Active Directory e selezionare **registrazioni app**, quindi cercare il numero di **ID dell'applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). Per trovare la chiave di autenticazione, passare a **Impostazioni** e selezionare **chiavi**. Sarà necessario fornire una descrizione e una durata, quindi verrà fornito un valore numerico.

 Si noti che l'ID applicazione di Azure è associato all'ID editore, quindi assicurarsi che lo stesso ID applicazione venga usato in tutte le offerte.

## <a name="plan-overview"></a>Panoramica del piano

La scheda **panoramica piano** consente di fornire diverse opzioni per i piani all'interno della stessa offerta. Questi piani (detti anche SKU) potrebbero differire in termini di versione, monetizzazione o livelli di servizio. È necessario configurare almeno un piano per vendere l'offerta nel Marketplace.

Al termine della creazione, vengono visualizzati i nomi dei piani, gli ID, i modelli tariffari, la disponibilità (pubblica o privata), lo stato di pubblicazione corrente ed eventuali azioni disponibili.

Le **azioni** disponibili nella **Panoramica del piano** variano a seconda dello stato corrente del piano e possono includere:

- Se lo stato del piano è **bozza** – Elimina bozza
- Se lo stato del piano è **Live** -stop sell plan o Sync private audience

**Crea nuovo piano** (almeno un piano per coloro che scelgono di vendere tramite Microsoft)

- **ID piano:** Creare un ID piano univoco per ogni piano di questa offerta. Questo ID sarà visibile ai clienti nell'URL del prodotto e Azure Resource Manager modelli (se applicabile). Usare solo caratteri minuscoli, alfanumerici, trattini o caratteri di sottolineatura. Per questo ID piano sono consentiti al massimo 50 caratteri. Si noti che l'ID non può essere modificato dopo aver selezionato Crea.
- **Nome del piano:** Il nome verrà visualizzato dai clienti quando si decide quale piano selezionare nell'offerta. Creare un nome di offerta univoco per ogni piano nell'offerta. Il nome del piano viene usato per distinguere i piani software che possono far parte della stessa offerta, ad esempio Nome offerta: Windows Server; piani Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Elenco di piani

La scheda **elenco piani** Visualizza le lingue e i mercati in cui è disponibile il piano, attualmente in lingua inglese (Stati Uniti) è l'unica località disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. Sarà necessario definire i dettagli del Marketplace (nome dell'offerta, descrizione, termini di ricerca e così via) per ogni lingua/mercato.

#### <a name="plan-listing-details"></a>Dettagli elenco piano

Se si seleziona una delle lingue del piano, vengono visualizzate le informazioni relative all' **elenco dei piani** , inclusi **nome** e **Descrizione.**

- **Nome**: Pre-popolata in base alla **nuova** voce del piano anteprima e verrà visualizzata come titolo del "piano software" dell'offerta visualizzato nel Marketplace.
- **Descrizione:** Questa descrizione è un'opportunità per spiegare cosa rende univoco questo piano software e qualsiasi differenza rispetto ad altri piani software all'interno dell'offerta. Può contenere un massimo di 500 caratteri.

Selezionare **Salva** dopo aver completato questi campi.

#### <a name="plan-pricing-and-availability"></a>Pianificare prezzi e disponibilità

La scheda **prezzi e disponibilità** consente di configurare i mercati in cui sarà disponibile il piano, il modello di monetizzazione, il prezzo e il termine di fatturazione desiderati. Inoltre, è possibile indicare se rendere il piano visibile a tutti o solo a specifici clienti (destinatari privati).

##### <a name="enabling-free-trials"></a>Abilitazione di versioni di valutazione gratuite

Le offerte SaaS tramite il Marketplace commerciale ti permettono di offrire una versione di valutazione gratuita di un mese per la vendita tramite Microsoft. Per tutti i modelli di fatturazione e i termini eccetto i piani a consumo, sono supportate le versioni di valutazione gratuite. Questa opzione consente ai clienti di avere una barriera limitata all'ingresso per un mese di accesso gratuito.  Se si sceglie di abilitare una versione di valutazione gratuita per i piani all'interno dell'offerta, il cliente non sarà in grado di eseguire la conversione a una sottoscrizione a pagamento prima della fine del periodo iniziale di un mese.  Durante questo periodo, i clienti che acquistano l'offerta possono provare i piani supportati con la versione di valutazione gratuita abilitata e convertirli tra loro.  La conversione a una sottoscrizione a pagamento viene eseguita automaticamente alla fine del periodo di validità.

>[!Note]
>Se il cliente sceglie di eseguire la conversione in un piano senza versioni di valutazione gratuite, la conversione verrà eseguita, ma la versione di valutazione gratuita andrà persa immediatamente.  Inoltre, una volta che un cliente inizia a pagare per un piano, non potrà più ottenere una versione di valutazione gratuita sulla stessa sottoscrizione, anche se si converte in uno SKU che supporta versioni di valutazione gratuite.

La possibilità di configurare una versione di valutazione gratuita è disponibile per ogni piano nell'offerta. È sufficiente passare a prezzi e disponibilità per ogni offerta e selezionare la casella per consentire una versione di valutazione di un mese.

![Casella di controllo della versione di valutazione gratuita di un mese](./media/free-trial-enable.png)

Per ottenere informazioni sulle sottoscrizioni dei clienti che attualmente partecipano a una versione di valutazione gratuita `isFreeTrial`, usare la nuova proprietà API, che verrà contrassegnata come true o false. Per ulteriori informazioni, vedere l' [API di sottoscrizione Saas Get](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) .

>[!Note]
>Le versioni di valutazione gratuite non sono supportate per i piani che sfruttano il servizio di misurazione del Marketplace.

#### <a name="markets"></a>Mercati

- **Modificare i mercati** opzionale

Ogni piano deve essere disponibile in almeno un mercato. Selezionare la casella di controllo per qualsiasi località di mercato in cui si desidera rendere disponibile questo piano. Una casella di ricerca e un pulsante per la selezione dei paesi in cui vengono riportate le imposte, in cui Microsoft si impegnano le vendite e le imposte per conto dell'utente, vengono fornite informazioni utili. 


Se sono già stati impostati i prezzi per il piano in Stati Uniti dollari (USD) e si aggiunge un altro percorso di mercato, il prezzo del nuovo mercato verrà calcolato in base ai tassi di cambio correnti. Prima di pubblicare il prezzo di ogni mercato, è sempre necessario esaminarlo. I prezzi possono essere esaminati tramite il collegamento "Esporta prezzi (xlsx)" dopo aver salvato le modifiche.

#### <a name="pricing"></a>Prezzi

- **Modello tariffario**: Frequenza piatta o basata su postazione

**Frequenza flat:** Consente di accedere all'offerta con un prezzo tariffario singolo mensile o annuale. Questa operazione viene a volte definita prezzi basati sul sito. Con questo modello di determinazione dei prezzi, è possibile definire facoltativamente piani a consumo che usano l'API del servizio di misurazione del Marketplace per addebitare i clienti in base alle unità non standard.  Per altre informazioni sulla fatturazione a consumo, vedere [fatturazione a consumo con il servizio di misurazione del Marketplace](./saas-metered-billing.md).

**Basato su postazione:** Consentire l'accesso all'offerta con il prezzo in base al numero di utenti che accedono all'offerta o occupano le *postazioni*. Questo modello basato sulla postazione consente di impostare il numero minimo e massimo di postazioni consentite in base al prezzo. In questo modo, è possibile configurare punti di prezzo diversi in base al numero di utenti configurando più piani.  Questi campi sono facoltativi. Se lasciato vuoto, il numero di postazioni verrà interpretato in modo da non avere un limite (min 1 e max of a quanti ne può supportare il sistema). Questi campi possono essere modificati come parte di un aggiornamento del piano.

Dopo la pubblicazione, non è possibile modificare la scelta del modello di determinazione prezzi per la fatturazione. Inoltre, tutti i piani per la stessa offerta devono condividere lo stesso modello di determinazione prezzi.

- **Termine fatturazione**: Mensile o annuale

Selezionare la frequenza con cui i clienti devono pagare il prezzo elencato. È necessario fornire almeno un prezzo mensile o annuale oppure entrambe le opzioni possono essere rese disponibili ai clienti.

- **Prezzo**: USD al mese o USD all'anno

I prezzi impostati nella valuta locale (USD = Stati Uniti dollaro) vengono convertiti nella valuta locale di tutti i mercati selezionati usando i tassi di cambio correnti disponibili durante l'installazione. Convalidare questi prezzi prima della pubblicazione esportando il foglio di calcolo dei prezzi e esaminando il prezzo in ogni mercato. Se si desidera impostare prezzi personalizzati in un singolo mercato, modificare e importare il foglio di calcolo dei prezzi. L'utente è responsabile della convalida dei prezzi ed è proprietario di queste impostazioni.
**È innanzitutto necessario salvare le modifiche ai prezzi per consentire l'esportazione dei dati dei prezzi.*

Esaminare attentamente i prezzi prima della pubblicazione, in quanto esistono alcune restrizioni su cosa può cambiare dopo la pubblicazione di un piano:

- Una volta pubblicato un piano, il modello di determinazione dei prezzi non può essere modificato.
- Una volta pubblicato un periodo di fatturazione per un piano, non sarà possibile rimuoverlo in un secondo momento.
- Una volta pubblicato il prezzo per un mercato nel piano, non è possibile modificarlo in un secondo momento.

### <a name="plan-audience"></a>Pianificare i destinatari

È possibile configurare ogni piano in modo che sia visibile a tutti o solo a un determinato gruppo di destinatari. È possibile assegnare l'appartenenza a questi destinatari limitati usando Azure AD ID tenant.

#### <a name="privacy"></a>Privacy

- Si **tratta di un piano privato** (Casella di controllo facoltativo)

Selezionare questa casella per rendere il piano privato e visibile solo ai destinatari di propria scelta. Una volta pubblicato come piano privato, è possibile aggiornare il gruppo di destinatari o scegliere di rendere disponibile il piano a tutti. Una volta che un piano è stato pubblicato come visibile a tutti, deve rimanere visibile a tutti gli utenti. Il piano non può essere configurato nuovamente come piano privato.

- **Destinatari con restrizioni (ID tenant)**

Assegnare i destinatari che avranno accesso a questo piano privato. L'accesso viene assegnato usando gli ID tenant con l'opzione per includere una descrizione di ogni ID tenant assegnato. È possibile aggiungere un massimo di 10 ID tenant oppure 20.000 ID tenant dei clienti se si importa un file di foglio di calcolo con estensione CSV.

Un tenant è una rappresentazione di un'organizzazione, con un ID rappresentato come GUID (identificatore univoco globale, un numero intero a 128 bit usato per identificare le risorse). È un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft, come l'iscrizione ad Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD. Per controllare il tenant, accedere al portale di Azure con l'account che si vuole usare per gestire l'applicazione. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account. Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio. Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID. È anche possibile cercare l'ID tenant dell'organizzazione usando un URL del nome di dominio in [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.

Sebbene le offerte SaaS usino gli ID tenant per definire un pubblico privato, altri tipi di offerta possono usare gli ID sottoscrizione di Azure (che sono anche rappresentati come GUID).

> [!NOTE]
> I destinatari privati (o destinatari limitati) differiscono da un pubblico di anteprima. Nella scheda **[Anteprima](#preview)** è possibile definire un gruppo di destinatari di anteprima. Un pubblico di anteprima è autorizzato ad accedere all'offerta *prima* che l'offerta venga pubblicata dal Marketplace. Sebbene la designazione dei destinatari privati venga applicata solo a un piano specifico, i destinatari di anteprima possono visualizzare tutti i piani (privati o meno), ma solo durante il periodo di anteprima limitato durante il test e la convalida del piano.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Elenco di esempio di piani all'interno di un'offerta del Marketplace

![Elenco dei piani di Marketplace di esempio con note](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

La scheda **test drive** consente di configurare una dimostrazione (o "test drive") che consentirà ai clienti di provare l'offerta prima di eseguire il commit per acquistarla. Per altre informazioni, vedere l'articolo relativo a [test drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se non si desidera più fornire un test drive per l'offerta, tornare alla pagina **[impostazione offerta](#offer-setup)** e deselezionare **Abilita Test Drive**.

### <a name="technical-configuration"></a>Configurazione tecnica
Sono disponibili i tipi di unità di test seguenti, ognuno con requisiti di configurazione tecnici specifici.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [App per la logica](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (Configurazione tecnica non necessaria)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configurazione tecnica per Azure Resource Manager test drive

Un modello di distribuzione che contiene tutte le risorse di Azure che costituiscono la soluzione. I prodotti che soddisfano questo scenario usano solo le risorse di Azure. Altre informazioni sulla configurazione di un [Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Aree geografiche** (obbligatorio): Attualmente sono disponibili 26 aree geografiche supportate da Azure in cui è possibile rendere disponibili le test drive. In genere, è consigliabile rendere le test drive disponibili nelle aree in cui si prevede il maggior numero di clienti, in modo che possano selezionare l'area più vicina per ottenere prestazioni ottimali. È necessario assicurarsi che alla sottoscrizione sia consentita la distribuzione di tutte le risorse necessarie in ognuna delle aree selezionate.

- **Instances**: Selezionare il tipo (Hot o Cold) e il numero di istanze disponibili, che verranno moltiplicate per il numero di aree in cui è disponibile l'offerta.

A **caldo**: Questo tipo di istanza viene distribuito e in attesa di accesso per area selezionata. I clienti possono accedere immediatamente alle istanze a *caldo* di una test drive, anziché dover attendere una distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. Si consiglia di disporre di almeno *un'istanza di* accesso frequente, in quanto la maggior parte dei clienti non desidera attendere le distribuzioni complete, causando un calo nell'utilizzo del cliente se non è disponibile un'istanza a *caldo* .

A **freddo**: Questo tipo di istanza rappresenta il numero totale di istanze che possono essere distribuite per ogni area. Per le istanze a freddo è necessario che l'intero test drive Gestione risorse modello venga distribuito quando un cliente richiede l'test drive, in modo che le istanze a *freddo* risultino molto più lente del caricamento rispetto alle istanze a *caldo* . Il compromesso è dovuto al fatto che è necessario pagare solo per la durata del test drive, *non* è sempre in esecuzione nella sottoscrizione di Azure come con *un'istanza* attiva.

- **Modello di Azure Resource Manager Test Drive**: Caricare il file zip contenente il modello di Azure Resource Manager.  Per altre informazioni sulla creazione di un modello di Azure Resource Manager, vedere l'articolo introduttivo [creare e distribuire modelli di Azure Resource Manager tramite il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Durata test drive** (obbligatorio): Immettere il periodo di tempo durante il quale il test drive resterà attivo, in ore. Il test drive termina automaticamente alla fine di questo periodo di tempo. Questa durata può scommettere solo per un numero intero di ore, ad esempio "2" ore, "1,5" non valido.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configurazione tecnica per Dynamics 365 test drive

Microsoft può rimuovere la complessità della configurazione di un test drive ospitando e gestendo il provisioning e la distribuzione del servizio usando questo tipo di test drive. La configurazione di questo tipo di test drive ospitata è la stessa, indipendentemente dal fatto che la test drive sia destinata a un gruppo di lavoro centrale, Customer Engagement o Operations.

- **Numero massimo di unità di test simultanee** (obbligatorio): Impostare il numero massimo di clienti che possono utilizzare la test drive alla volta. Ogni utente simultaneo utilizzerà una licenza Dynamics 365 mentre la test drive è attiva, pertanto sarà necessario assicurarsi che siano disponibili sufficienti licenze per supportare il limite massimo impostato. Valore consigliato da 3 a 5.

- **Durata test drive** (obbligatorio): Immettere il periodo di tempo per cui il test drive resterà attivo definendo il numero di ore. Dopo questo numero di ore, la sessione terminerà e non utilizzerà più una delle licenze. È consigliabile un valore di 2-24 ore a seconda della complessità dell'offerta. Questa durata può scommettere solo per un numero intero di ore, ad esempio "2" ore, "1,5" non valido.  L'utente può richiedere una nuova sessione se esaurisce il tempo e desidera accedere nuovamente al test drive.

- **URL istanza** (obbligatorio): URL in cui il cliente inizierà la test drive. In genere l'URL dell'istanza di Dynamics 365 che esegue l'app con i dati di esempio https://testdrive.crm.dynamics.com) installati, ad esempio.

- **URL dell'API Web dell'istanza** (obbligatorio): Recuperare l'URL dell'API Web per l'istanza di Dynamics 365 accedendo all'account Microsoft 365 e passando alle **Impostazioni** \&gt; **Personalizzazione** di \&gt; **Risorse per sviluppatori** \&gt; **API Web dell'istanza (URL radice del servizio)** , copiare l'URL trovato qui, https://testdrive.crm.dynamics.com/api/data/v9.0) ad esempio.

- **Nome ruolo** (obbligatorio): Specificare il nome del ruolo di sicurezza definito nel test drive Dynamics 365 personalizzato. Questa operazione verrà assegnata all'utente durante il test drive, ad esempio test-drive-Role.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configurazione tecnica per test drive app per la logica

Tutti i prodotti personalizzati devono usare questo tipo di modello di distribuzione test drive che include un'ampia gamma di architetture di soluzioni complesse. Per altre informazioni sulla configurazione di unità di test per l'app per la logica, vedere [operazioni](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [coinvolgimento dei clienti](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) su GitHub.

- **Area geografica** (elenco a discesa obbligatorio, a selezione singola): Attualmente sono disponibili 26 aree geografiche supportate da Azure in cui è possibile rendere disponibili le test drive. Le risorse per l'app per la logica verranno distribuite nell'area selezionata. Se l'app per la logica contiene risorse personalizzate archiviate in un'area specifica, assicurarsi che l'area sia selezionata qui. Il modo migliore per eseguire questa operazione consiste nel distribuire completamente l'app per la logica localmente nella sottoscrizione di Azure nel portale e verificare che funzioni correttamente prima di effettuare questa selezione.

- **Numero massimo di unità di test simultanee** (obbligatorio): Impostare il numero massimo di clienti che possono utilizzare la test drive alla volta. Queste unità di test sono già distribuite, consentendo ai clienti di accedervi immediatamente senza attendere una distribuzione.

- **Durata test drive** (obbligatorio): Immettere il periodo di tempo durante il quale il test drive resterà attivo, in ore. Il test drive termina automaticamente al termine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** (obbligatorio): Immettere il nome del [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) in cui viene salvata l'app per la logica test drive.

- **Nome dell'app** per la logica di Azure (obbligatorio): Immettere il nome dell'app per la logica che assegna la test drive all'utente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

- Deprovisioning app per la **logica nome** (obbligatorio): Immettere il nome dell'app per la logica che effettua il deprovisioning del test drive al termine del cliente. Questa app per la logica deve essere salvata nel gruppo di risorse di Azure precedente.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configurazione tecnica non necessaria per Power BI test drive

I prodotti che vogliono dimostrare un oggetto visivo Power BI interattivo possono usare un collegamento incorporato per condividere un dashboard personalizzato come test drive, non sono necessarie altre configurazioni tecniche. Altre informazioni sulla configurazione di[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) app modello.

### <a name="deployment-subscription-details"></a>Dettagli sottoscrizione della distribuzione

Per distribuire il test drive per conto dell'utente, creare e fornire una sottoscrizione di Azure separata e univoca. (Non necessario per Power BI test drive).

- **ID sottoscrizione di Azure** (obbligatorio per Azure Resource Manager e app per la logica): Immettere l'ID sottoscrizione per concedere l'accesso ai servizi dell'account di Azure per la fatturazione e la creazione di report sull'utilizzo delle risorse. È consigliabile [creare una sottoscrizione di Azure separata](https://docs.microsoft.com/azure/billing/billing-create-subscription) da usare per le unità di test se non ne è già presente uno. È possibile trovare l'ID sottoscrizione di Azure accedendo al [portale di Azure](https://portal.azure.com/) e passando alla scheda Sottoscrizioni  del menu a sinistra. Selezionando la scheda, viene visualizzato l'ID sottoscrizione, ad esempio "a83645ac-1234-5AB6-6789-1h234g764ghty".

- **ID tenant Azure ad** (obbligatorio): Immettere l' [ID tenant](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **Proprietà** , quindi cercare il numero **ID directory** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e). È anche possibile cercare l'ID tenant dell'organizzazione usando l'URL del nome di dominio in [https://www.whatismytenantid.com](https://www.whatismytenantid.com):.

- **Nome del tenant Azure ad** (obbligatorio per la 365 dinamica): Immettere il nome del Azure Active Directory (AD). Per trovare questo nome, accedere al [portale di Azure](https://portal.azure.com/), nell'angolo in alto a destra il nome del tenant verrà elencato sotto il nome dell'account.

- **ID app Azure ad** (obbligatorio): Immettere l' [ID applicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)di Azure Active Directory (ad). Per trovare questo ID, accedere al [portale di Azure](https://portal.azure.com/), selezionare la scheda Active Directory nel menu a sinistra, selezionare **registrazioni app**, quindi cercare il numero **ID applicazione** elencato (ad esempio 50c464d3-4930-494c-963C-1e951d15360e).

- Chiave **privata client dell'app Azure ad** (obbligatorio): Immettere il [segreto client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)dell'applicazione Azure ad. Per trovare questo valore, accedere al [portale di Azure](https://portal.azure.com/). Selezionare la scheda **Azure Active Directory** nel menu a sinistra, selezionare **registrazioni app**, quindi selezionare l'app test drive. Selezionare quindi **certificati e segreti**, fare clic **su nuovo segreto client**, immettere una descrizione, selezionare **mai** sotto **scadenza**, quindi scegliere **Aggiungi**. Assicurarsi di copiare il valore. Non uscire dalla pagina prima di eseguire questa operazione, altrimenti non sarà possibile accedere al valore.

Ricordarsi di **salvare** prima di procedere alla sezione successiva.

### <a name="test-drive-listings-optional"></a>Elenchi di test drive (facoltativo)

L'opzione per gli **elenchi di test drive** disponibile nella scheda **test drive** Visualizza le lingue (e i mercati) in cui è disponibile la test drive, attualmente in inglese (Stati Uniti) è l'unica posizione disponibile. Inoltre, in questa pagina viene visualizzato lo stato dell'elenco specifico della lingua e della data/ora in cui è stato aggiunto. È necessario definire i dettagli del test drive (descrizione, manuale dell'utente, video e così via) per ogni lingua/mercato.

- **Descrizione** di (obbligatorio): Descrivere le test drive, gli obiettivi che verranno illustrati, gli obiettivi dell'utente per sperimentare, le funzionalità da esplorare e tutte le informazioni rilevanti per aiutare l'utente a determinare se acquisire l'offerta. In questo campo è possibile immettere fino a 3.000 caratteri di testo. 

- **Informazioni di accesso** (obbligatorio per Azure Resource Manager e per le unità di test della logica): Spiega cosa è necessario sapere al cliente per accedere a questo test drive e usarlo. Esaminare uno scenario per l'uso dell'offerta e esattamente ciò che il cliente deve conoscere per accedere alle funzionalità nell'test drive. In questo campo è possibile immettere fino a 10.000 caratteri di testo.

- **Manuale dell'utente** (obbligatorio): Procedura dettagliata approfondita dell'esperienza test drive. Il manuale dell'utente dovrebbe coprire esattamente ciò che si vuole che il cliente possa ottenere dall'esperienza del test drive e fungere da riferimento per eventuali domande. Il file deve essere in formato PDF ed essere denominato (255 caratteri al massimo) dopo il caricamento.

- **Video Aggiungi video** (facoltativo): È possibile caricare video in YouTube o Vimeo e farvi riferimento con un collegamento e un'immagine di anteprima (533 x 324 pixel), in modo che un cliente possa visualizzare una descrizione dettagliata delle informazioni per comprendere meglio le test drive, incluso come usare correttamente le funzionalità del offrire e comprendere gli scenari in cui vengono evidenziati i propri vantaggi.
  - **Nome** necessaria
  - **URL (solo YouTube o Vimeo)** necessaria
  - **Anteprima (533 x 324px)** : Il file di immagine deve essere in formato PNG.

Selezionare **Salva** dopo aver completato questi campi.

## <a name="publish"></a>Pubblica

#### <a name="submit-offer-to-preview"></a>Invia l'offerta all'anteprima

Una volta completate tutte le sezioni obbligatorie dell'offerta, selezionare **pubblica** nell'angolo superiore destro del portale. Si verrà reindirizzati alla pagina di **revisione e pubblicazione** . 

Se è la prima volta che si pubblica questa offerta, è possibile:

- Vedere lo stato di completamento di ogni sezione dell'offerta.
    - *Non avviato* : indica che la sezione non è stata toccata e deve essere completata.
    - *Incompleto* : indica che la sezione contiene errori che devono essere corretti o che sono necessarie altre informazioni. Tornare alla sezione o aggiornarla.
    - *Completato* : indica che la sezione è completa, sono stati forniti tutti i dati necessari e non sono presenti errori. Tutte le sezioni dell'offerta devono essere in uno stato completo prima di poter inviare l'offerta.
- Fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente, oltre a eventuali note supplementari utili per comprendere l'app.
- Inviare l'offerta per la pubblicazione selezionando **Invia**. Verrà inviato un messaggio di posta elettronica per informare l'utente quando sarà disponibile una versione di anteprima dell'offerta da rivedere e approvare. È necessario tornare al centro per i partner e selezionare **Go-Live** per l'offerta per pubblicare l'offerta al pubblico (o se un'offerta privata, per i destinatari privati).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare un'offerta esistente nel Marketplace commerciale](./update-existing-offer.md)
