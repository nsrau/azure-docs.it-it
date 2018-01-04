---
title: Domande frequenti su Gestione costi di Azure | Microsoft Docs
description: Risposte ad alcune domande comuni su Gestione costi di Azure.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/14/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: f62e5a224c2fb33714a80bc47b98238208b787e5
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Domande frequenti su Gestione costi di Azure

Questo articolo affronta alcune domande comuni su Gestione costi di Azure (chiamata anche Cloudyn). Eventuali domande su Gestione costi possono essere formulate nel forum [FAQs for Azure Cost Management by Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn) (Domande frequenti su Gestione costi di Azure di Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Come è possibile risolvere i problemi di configurazione indiretta dell'organizzazione?

Quando si usa per la prima volta il portale di Cloudyn, vengono visualizzati i messaggi seguenti se si ha un contratto Enterprise o Provider di soluzioni cloud (CSP):

- "The specified API key is not a top level enrollment key" (La chiave API specificata non è una chiave di registrazione principale), nella procedura guidata di **configurazione di Gestione costi di Azure**.
- "Direct Enrollment – No" (Registrazione diretta - No), nel portale per i contratti Enterprise.
- "No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account" (Non sono stati trovati dati di utilizzo per gli ultimi 30 giorni. Contattare il distributore per verificare che per l'account Azure sia stato abilitato il markup), nel portale di Cloudyn.

I messaggi precedenti indicano che è stato acquistato un contratto Enterprise di Azure tramite un rivenditore o un CSP. Per poter visualizzare i dati in Cloudyn, è necessario che il rivenditore o il CSP abiliti il _markup_ per l'account Azure.

Ecco come risolvere i problemi:

1. Il rivenditore deve abilitare il _markup_ per l'account. Per le istruzioni, vedere la [guida introduttiva per i clienti indiretti](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Generare la chiave del contratto Enterprise di Azure da usare con Cloudyn. Per le istruzioni, vedere [Adding Your Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) (Aggiunta del contratto Enterprise di Azure) o [How to Find Your EA Enrollment ID and API Key](https://youtu.be/u_phLs_udig) (Come trovare l'ID registrazione e la chiave API del contratto Enterprise).

Gestione costi può essere abilitato solo da un amministratore del servizio Azure. Le autorizzazioni di coamministratore non sono sufficienti.

Per poter generare la chiave API del contratto Enterprise di Azure per la configurazione di Cloudyn, abilitare prima di tutto l'API di fatturazione di Azure seguendo queste istruzioni:

- [Panoramica delle API di creazione di report per i clienti Enterprise](../billing/billing-enterprise-api.md)
- Sezione **Enabling data access to the API** (Abilitazione dell'accesso ai dati nell'API) in [Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (API di creazione report di Microsoft Azure Enterprise Portal)


Potrebbe anche essere necessario concedere ad amministratori di reparto, proprietari di account e amministratori dell'organizzazione le autorizzazioni per _visualizzare gli addebiti_ con l'API di fatturazione.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Perché non vedo indicazioni di ottimizzazione?

Informazioni di raccomandazione sono disponibile solo per gli account che vengono attivati. Non si vedranno le informazioni di raccomandazione **Optimizer** report categorie per gli account che sono *disattivati*, tra cui:

- Gestione di ottimizzazione
- Ottimizzazione di ridimensionamento
- Inefficienze

Se è possibile visualizzare i dati di Query Optimizer raccomandazione, molto probabilmente, è necessario account che sono disattivati. Per attivare un account, è necessario registrarlo con le credenziali di Azure.

Per attivare un account:

1.  Nel portale di Cloudyn, fare clic su **Impostazioni** in alto a destra e selezionare **Cloud Accounts** (Account cloud).
2.  Nella scheda account di Microsoft Azure, cercare gli account che hanno un **disattivati** sottoscrizione.
3.  A destra di un account disattivato, fare clic su di **modifica** simbolo simile a una matita.
4.  Tenant di ID e l'ID di velocità viene rilevato automaticamente. Fare clic su **Avanti**.
5.  Si viene reindirizzati al portale di Azure. Accedi al portale e autorizzare Cloudyn dell'agente di raccolta per accedere ai dati di Azure.
6.  Successivamente, si viene reindirizzati alla pagina di gestione di account Cloudyn e la sottoscrizione viene aggiornata con **active** lo stato dell'Account. Viene illustrato un simbolo di segno di spunta verde.
7.  Se non viene visualizzato un simbolo di segno di spunta verde per uno o più sottoscrizioni, significa che non si dispone delle autorizzazioni per creare un'applicazione lettore (il CloudynCollector) per la sottoscrizione. Un utente con autorizzazioni più elevate per la sottoscrizione deve ripetere i passaggi 3 e 4.  

Dopo aver completato i passaggi precedenti, è possibile visualizzare i suggerimenti di Query Optimizer entro due giorni. Tuttavia, può richiedere fino a cinque giorni prima che i dati di ottimizzazione complete sono disponibili.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Come si abilitano gli utenti sospesi o bloccati?

Se si riceve un avviso che richiede di consentire l'accesso a un utente, è necessario attivarne l'account.

Per attivare l'account utente:

1. Accedere a Cloudyn con l'account utente amministratore di Azure utilizzato per configurare Cloudyn. In alternativa accedere con un account utente a cui è stato concesso l'accesso amministrativo.

2. Selezionare il simbolo a forma di ingranaggio in alto a destra e selezionare **User Management** (Gestione utenti).

3. Trovare l'utente e quindi selezionare il simbolo a forma di matita e modificare l'utente.

4. In **User status** (Stato utente) modificare lo stato da **Suspended** (Sospeso) ad **Active** (Attivo).

Gli account utente Cloudyn si connettono tramite Single Sign-On da Azure. Se un utente digita in modo non corretto la propria password, potrebbe essere bloccato da Cloudyn pur potendo ancora accedere ad Azure.

La modifica dell'indirizzo di posta elettronica in Cloudyn rispetto all'indirizzo predefinito in Azure può causare il blocco dell'account e la visualizzazione dell'errore "status initiallySuspended" (stato initiallySuspended). Se l'account utente viene bloccato, contattare un altro amministratore per reimpostarlo.

È consigliabile creare almeno due account amministratore di Cloudyn nel caso in cui uno venga bloccato.

Se non si riesce ad accedere al portale di Cloudyn, verificare di usare l'URL di Gestione costi di Azure corretto per l'accesso a Cloudyn. Utilizzare [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evitare di usare l'URL diretto di Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Come si attivano account non attivati con le credenziali di Azure?

Non appena Cloudyn individua account Azure, i dati sui costi vengono immediatamente resi disponibili in report basati sui costi. Affinché Cloudyn fornisca dati di utilizzo e sulle prestazioni, tuttavia, è necessario registrare le credenziali di Azure per gli account. Per le istruzioni, vedere [Add Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager) (Aggiungere Azure Resource Manager).

Per aggiungere le credenziali di Azure per un account, nel portale di Cloudyn selezionare il simbolo di modifica a destra del nome dell'account, non della sottoscrizione.

Finché le credenziali di Azure non vengono aggiunte a Cloudyn, l'account risulta _non attivato_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Come si aggiungono più account ed entità a una sottoscrizione esistente?

Le entità aggiuntive vengono usate per includere altri contratti Enterprise in una sottoscrizione di Cloudyn. Per una descrizione dell'aggiunta di altre entità, vedere i link seguenti:

- Articolo [Adding an Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) (Aggiunta di un'entità)
- Video [Defining your hierarchy with Cost Entities](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) (Definizione della gerarchia con entità di costo)

Per CSP:

Per aggiungere altri account CSP a un'entità, selezionare **MSP Access** (Accesso MSP) invece di **Enterprise** durante la creazione della nuova entità. Se l'account è registrato come contratto Enterprise e si vogliono aggiungere credenziali CSP, potrebbe essere necessaria la modifica delle impostazioni dell'account da parte del personale di supporto di Cloudyn. Se si ha una sottoscrizione di Azure a pagamento, è possibile creare una nuova richiesta di supporto nel portale di Azure. Selezionare **Guida e supporto** e quindi **Nuova richiesta di supporto**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Simboli di valuta nei report Cloudyn

Potrebbero essere presenti più account Azure che usano valute diverse. I report dei costi in Cloudyn tuttavia non visualizzano più di un tipo di valuta per report.

Se si hanno più sottoscrizioni che usano valute diverse, un'entità padre e le valute delle entità figlio vengono visualizzate con il simbolo **$**. La procedura consigliata consiste nell'evitare di usare valute diverse nella stessa gerarchia di entità. In altre parole, tutte le sottoscrizioni organizzate in una struttura di entità devono usare la stessa valuta.

Cloudyn rileva automaticamente la valuta della sottoscrizione Enterprise Agreement e la visualizza correttamente nei report.  Cloudyn tuttavia visualizza il simbolo **$** solo per gli account Azure con accesso Web diretto e CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quali sono le tempistiche di aggiornamento dei dati di Cloudyn?

Cloudyn offre le tempistiche di aggiornamento dei dati riportate di seguito.

- **Iniziale**: la visualizzazione dei dati sui costi in Cloudyn dopo la configurazione può richiedere fino a 24 ore. Possono inoltre trascorrere fino a 10 giorni prima che Cloudyn raccolga dati sufficienti per visualizzare consigli sul ridimensionamento.
- **Giornaliero**: dal giorno 10 fino alla fine di ogni mese, Cloudyn visualizzerà i dati aggiornati del giorno precedente dopo le ore UTC+3 circa del giorno successivo.
- **Mensile**: dal giorno 1 al 10 di ogni mese, Cloudyn potrebbe visualizzare solo i dati fino alla fine del mese precedente.

Cloudyn elabora i dati per il giorno precedente quando sono tutti disponibili. I dati del giorno precedente sono in genere disponibili in Cloudyn entro le ore UTC+3 circa di ogni giorno. L'elaborazione di alcuni dati, come i tag, può richiedere altre 24 ore.

I dati per il mese in corso non sono disponibili per la raccolta all'inizio di ogni mese. Durante tale periodo, i provider di servizi finalizzano la fatturazione per il mese precedente, i cui dati vengono visualizzati in Cloudyn da 5 a 10 giorni dopo l'inizio di ogni mese. In tale intervallo di tempo, potrebbero essere visualizzati solo i costi ammortizzati del mese precedente e non i dati giornalieri di fatturazione o utilizzo. Quando diventano disponibili, i dati vengono elaborati da Cloudyn retroattivamente. Al termine dell'elaborazione tutti i dati mensili vengono visualizzati tra il 5 e il 10 di ogni mese.

Se si verifica un ritardo nell'invio dei dati da Azure a Cloudyn, i dati vengono comunque registrati in Azure e vengono trasferiti a Cloudyn al ripristino della connessione.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Come può un CSP diretto configurare l'accesso a Cloudyn per partner o clienti CSP indiretti?

Per le istruzioni, vedere [Configurare l'accesso CSP indiretto in Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Perché viene visualizzato l'elemento del menu Optimizer (Ottimizzatore)?

Dopo aver aggiunto l'accesso di Azure Resource Manager e aver raccolto i dati, viene visualizzata l'opzione **Optimizer** (Ottimizzatore). Per attivare l'accesso da Azure Resource Manager, vedere [Come si attivano account non attivati con le credenziali di Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cost-managementcloudyn-agent-based"></a>Si basa sull'agente di Gestione costi di Azure/Cloudyn?

di serie Gli agenti non vengono usati. I dati di metrica della macchina virtuale di Azure per le macchine virtuali vengono raccolti dall'API di Microsoft Insights. Se si desidera raccogliere dati di metrica da macchine virtuali di Azure, queste dovranno avere abilitate le impostazioni di diagnostica.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>I report di Cloudyn mostrano più di un tenant di Active Directory per ogni report?

Sì. È possibile [creare un'entità di account cloud corrispondente](tutorial-user-access.md#create-entities) per ogni tenant di Active Directory di cui si dispone. È quindi possibile visualizzare tutti i dati del tenant di Azure AD e altri provider di piattaforma cloud inclusi Amazon Web Services e Google Cloud Platform.
