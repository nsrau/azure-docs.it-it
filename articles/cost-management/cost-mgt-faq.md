---
title: Domande frequenti su Gestione costi di Azure | Microsoft Docs
description: Risposte ad alcune domande comuni su Gestione costi di Azure.
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/23/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 693f0541376f54d1f702b285775ebae6c5fcbc0c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="frequently-asked-questions-for-azure-cost-management"></a>Domande frequenti su Gestione costi di Azure


Questo articolo affronta alcune domande comuni su Gestione costi di Azure. Eventuali domande su Gestione costi possono essere formulate nel forum [FAQs for Azure Cost Management by Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-azure-cost-management-by-cloudyn?forum=Cloudyn) (Domande frequenti su Gestione costi di Azure di Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-set-up-errors"></a>Come è possibile risolvere gli errori di configurazione indiretta dell'organizzazione?

Agli utenti CSP o con contratto Enterprise che usano per la prima volta il portale di Cloudyn potrebbero essere visualizzati gli errori seguenti:

- **The specified API key is not a top level enrollment key** (La chiave API specificata non è una chiave di registrazione principale), nella procedura guidata di configurazione di Gestione costi di Azure.
- **Direct Enrollment – No** (Registrazione diretta - No), nel portale per i contratti Enterprise.
- **No usage data was found for the last 30 days. Please contact your distributor to make sure markup was enabled for your Azure account** (Non sono stati trovati dati di utilizzo per gli ultimi 30 giorni. Contattare il distributore per verificare che per l'account Azure sia stato abilitato il markup), nel portale di Cloudyn.

Gli errori precedenti indicano che è stato acquistato un contratto Enterprise di Azure tramite un rivenditore o un CSP. Per poter visualizzare i dati in Cloudyn, è necessario che il rivenditore o il CSP abiliti il _markup_ per l'account Azure.

Ecco come risolvere gli errori:

1. Il rivenditore deve abilitare il _markup_ per l'account. Vedere le istruzioni riportate nella [guida introduttiva per i clienti indiretti](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Generare la chiave del contratto Enterprise di Azure da usare con Cloudyn. Vedere le istruzioni riportate in [Adding Your Azure EA](https://support.cloudyn.com/hc/en-us/articles/210429585-Adding-Your-AZURE-EA) (Aggiunta del contratto Enterprise di Azure) o [How to Find Your EA Enrollment ID and API Key](https://youtu.be/u_phLs_udig) (Come trovare l'ID registrazione e la chiave API del contratto Enterprise).

Gestione costi può essere abilitato solo da un amministratore del servizio Azure. Le autorizzazioni di coamministratore non sono sufficienti.

Abilitare prima di tutto l'API di fatturazione di Azure per poter generare la chiave API del contratto Enterprise di Azure per la configurazione di Cloudyn, seguendo queste istruzioni:

- [Panoramica delle API di creazione di report per i clienti Enterprise](../billing/billing-enterprise-api.md)
- Sezione _Enabling data access to the API_ (Abilitazione dell'accesso ai dati nell'API) in [Microsoft Azure Enterprise Portal Reporting API](https://ea.azure.com/helpdocs/reportingAPI) (API di creazione report di Microsoft Azure Enterprise Portal)


Potrebbe anche essere necessario concedere ad amministratori di reparto, proprietari di account e amministratori dell'organizzazione le autorizzazioni per _visualizzare gli addebiti_ con l'API di fatturazione.

## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Come si abilitano gli utenti sospesi o bloccati?

Se si riceve un avviso che richiede di consentire l'accesso a un utente, è necessario attivarne l'account.

Per attivare l'account utente:

1. Accedere a Cloudyn con l'account utente amministratore di Azure usato per configurare Cloudyn. In alternativa, accedere con un account utente a cui è stato concesso l'accesso amministrativo.
2. Fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **User Management** (Gestione utenti).
3. Trovare l'utente e quindi fare clic sul simbolo a forma di matita e modificare l'utente.
4. In **User status** (Stato utente) modificare lo stato da **Suspended** (Sospeso) ad **Active** (Attivo).

Gli account utente Cloudyn si connettono tramite Single Sign-On da Azure. Se un utente digita in modo non corretto la propria password, potrebbe essere bloccato da Cloudyn pur potendo ancora accedere ad Azure.

La modifica dell'indirizzo di posta elettronica in Cloudyn rispetto all'indirizzo predefinito in Azure può causare il blocco dell'account e la visualizzazione dell'errore _status initiallySuspended_ (stato initiallySuspended). Se l'account utente viene bloccato, contattare un altro amministratore per reimpostarlo.

È consigliabile creare almeno due account amministratore di Cloudyn nel caso in cui uno venga bloccato.

Se non si riesce ad accedere al portale di Cloudyn, verificare di usare l'URL di Gestione costi di Azure corretto per l'accesso a Cloudyn. Usare uno degli URL seguenti:

- https://azure.cloudyn.com
- https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade

Evitare di usare l'URL diretto di Cloudyn https://app.cloudyn.com.

## <a name="how-to-activate-unactivated-accounts-with-azure-credentials"></a>Come si attivano account non attivati con le credenziali di Azure?

Non appena Cloudyn individua account Azure, i dati sui costi vengono immediatamente resi disponibili in report basati sui costi. Affinché Cloudyn fornisca dati di utilizzo e sulle prestazioni, tuttavia, è necessario registrare le credenziali di Azure per gli account. Seguire le istruzioni riportate in [Adding Azure Resource Manager](https://support.cloudyn.com/hc/en-us/articles/212784085-Adding-Azure-Resource-Manager) (Aggiunta di Azure Resource Manager).

Per aggiungere le credenziali di Azure per un account, nel portale di Cloudyn fare clic sul simbolo di modifica a destra del nome dell'account, non della sottoscrizione.

Finché le credenziali di Azure non vengono aggiunte a Cloudyn, l'account risulta _non attivato_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Come si aggiungono più account ed entità a una sottoscrizione esistente?

Per una descrizione dell'aggiunta di altre entità, vedere i link seguenti. Le entità aggiuntive vengono usate per includere altri contratti Enterprise in una sottoscrizione di Cloudyn.

- Articolo [Adding an Entity](https://support.cloudyn.com/hc/en-us/articles/212016145-Adding-an-Entity) (Aggiunta di un'entità)
- Video [Defining your hierarchy with Cost Entities](https://support.cloudyn.com/hc/en-us/articles/115005142529-Video-Defining-your-hierarchy-with-Cost-Entities) (Definizione della gerarchia con entità di costo)

Per CSP:

Per aggiungere altri account CSP a un'entità, selezionare **MSP Access** (Accesso MSP) invece di **Enterprise** durante la creazione della nuova entità. Se l'account è registrato come contratto Enterprise e si vogliono aggiungere credenziali CSP, potrebbe essere necessaria la modifica delle impostazioni dell'account da parte del personale di supporto di Cloudyn. Se si ha una sottoscrizione di Azure a pagamento, è possibile creare una nuova richiesta di supporto nel portale di Azure. Fare clic su **Guida e supporto** e quindi su **Nuova richiesta di supporto**.

## <a name="how-do-i-change-the-currency-symbol-used-in-cloudyn"></a>Come si modifica il simbolo di valuta usato in Cloudyn?

Quando tutti gli account Azure in una singola entità usano la stessa valuta, la valuta in uso viene rilevata automaticamente. Il simbolo di valuta viene tuttavia visualizzato erroneamente come **$** per le valute seguenti:

- GBP = sterlina del Regno Unito
- EUR = euro
- INR = rupia indiana
- NOK = corona norvegese

Anche se il simbolo di valuta potrebbe essere visualizzato come **$**, corrispondente al dollaro USA, i valori di costo vengono mostrati nella valuta corretta. Se tutti gli account nella stessa entità usano euro, ad esempio, i _valori_ visualizzati in Cloudyn sono in euro anche se viene erroneamente visualizzato il simbolo **$**.

Per i clienti con contratti Enterprise di Azure, il personale di supporto di Cloudyn può modificare il simbolo di valuta $ visualizzato nei report dei costi. È possibile creare una nuova richiesta di supporto nel portale di Azure. Fare clic su **Guida e supporto** e quindi su **Nuova richiesta di supporto**.

I clienti CSP non possono modificare il simbolo di valuta. Cloudyn supporta solo tariffari in dollari USA. Cloudyn sta valutando la possibilità di supportare tariffari in altre valute.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quali sono le tempistiche di aggiornamento dei dati di Cloudyn?

Cloudyn offre le tempistiche di aggiornamento dei dati riportate di seguito.

- Iniziale: la visualizzazione dei dati sui costi in Cloudyn dopo la configurazione può richiedere fino a 24 ore. Possono inoltre trascorrere fino a 10 giorni prima che Cloudyn raccolga dati sufficienti per visualizzare consigli sul ridimensionamento.
- Giornaliero: dal 10 alla fine di ogni mese, Cloudyn visualizzerà i dati aggiornati del giorno precedente dopo le ore UTC+3 circa del giorno successivo.
- Mensile: dall'1 al 10 di ogni mese, Cloudyn potrebbe visualizzare solo i dati fino alla fine del mese precedente.

Cloudyn elabora i dati per il giorno precedente quando sono tutti disponibili. I dati del giorno precedente sono in genere disponibili in Cloudyn entro le ore UTC+3 circa di ogni giorno. L'elaborazione di alcuni dati, come i tag, può richiedere altre 24 ore.

I dati per il mese in corso non sono disponibili per la raccolta all'inizio di ogni mese. Durante tale periodo, i provider di servizi finalizzano la fatturazione per il mese precedente, i cui dati vengono visualizzati in Cloudyn da cinque a 10 giorni dopo l'inizio di ogni mese. In tale intervallo di tempo, potrebbero essere visualizzati solo i costi ammortizzati del mese precedente e non i dati giornalieri di fatturazione o utilizzo. Quando diventano disponibili, i dati vengono elaborati da Cloudyn retroattivamente. Al termine dell'elaborazione, tutti i dati mensili vengono visualizzati tra il 5 e il 10 di ogni mese.

Se si verifica un ritardo nell'invio dei dati da Azure a Cloudyn, i dati vengono comunque registrati in Azure e vengono trasferiti a Cloudyn al ripristino della connessione.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Come può un CSP diretto configurare l'accesso a Cloudyn per partner o clienti CSP indiretti?

Per istruzioni, vedere [Configurare l'accesso CSP indiretto in Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).
