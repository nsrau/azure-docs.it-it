---
title: Guida alla pubblicazione di Cortana Intelligence in AppSource | Microsoft Docs
description: Di seguito sono elencati tutti i passaggi che i partner Microsoft devono seguire per pubblicare la propria soluzione Cortana Intelligence in AppSource.
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams;v-bruham;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9f867641b77c8148c1d6cbf2913da9c1f5ce5b71
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

--- 
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Guida alla pubblicazione di Cortana Intelligence in AppSource

## <a name="overview"></a>Panoramica
AppSource è la posizione centrale in cui i Business Decision Maker (BDM) possono individuare e testare facilmente soluzioni e app aziendali create dai partner e valutate da Microsoft. Per informazioni sul funzionamento di AppSource, vedere [questo video](https://youtu.be/hpq_Y9LuIB8). 

I partner Microsoft possono trarre notevoli vantaggi dalla pubblicazione in AppSource nei casi seguenti:
- È stata creata una soluzione/app intelligente con [Cortana Intelligence Suite](https://azure.microsoft.com/en-us/suites/cortana-intelligence-suite/?cdn=disable).
- La soluzione/app risolve un problema aziendale specifico.
- Sono stati creati moduli o proprietà intellettuale che i clienti possono riusare piuttosto rapidamente in modo prevedibile.

Dare un'occhiata alle [soluzioni Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1) che sono già pubblicate in AppSource. 

In questo articolo verranno illustrati i passaggi per ottenere una soluzione Cortana Intelligence di un partner pubblicata in AppSource

## <a name="getting-started"></a>Introduzione
1. Nel documento [Partner Community Benefits Guide](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF), consultare la pagina 9 per essere pubblicati come partner Advanced Analytics.
1. Compilare il modulo per [inviare l'app](https://appsource.microsoft.com/en-us/partners/list-an-app).

    Alla domanda *"Choose the products that your app is built for*" (Scegliere i prodotti per cui è stata creata l'app), selezionare la casella di controllo **Other** (Altro) e inserire "Cortana Intelligence" nel controllo di modifica.
1. Affinché un'app Cortana Intelligence possa essere caricata in AppSource, è necessario ottenere la certificazione dal team Partner Solution Technology di Cortana Intelligence. Per avviare il processo, condividere i dettagli sull'app compilando il modulo di sondaggio alla pagina [Cortana Intelligence solution evaluation for AppSource publishing](https://aka.ms/cisappsrceval) (Valutazione della soluzione Cortana Intelligence per la pubblicazione in AppSource). Questo sito è protetto da password e contiene istruzioni su come ottenerne una.

## <a name="solution-evaluation-criteria"></a>Criteri di valutazione della soluzione
Ecco l'elenco dei criteri che l'app deve soddisfare
1. Le app devono risolvere il problema di un caso d'uso aziendale specifico all'interno di una determinata area funzionale in modo ripetibile con configurazioni minime per proposte di valore predefinite implementabili entro un breve periodo.
1. La soluzione deve usare almeno uno dei componenti seguenti:

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Analisi di flusso
    - Servizi cognitivi
    - Bot Framework
    - Analysis Services
    - Microsoft R Server (Standalone)
    - Servizi R in SQL 2016 o HDInsight Premium
1. La soluzione deve generare almeno $ 1.000 al mese per cliente mediante DPOR/CSP.
1. La soluzione deve usare l'accesso Single Sign-On federato di Azure Active Directory (SSO federato di AAD) con consenso abilitato per l'autenticazione utente e controlli di accesso alle risorse. È necessario dimostrare al team di valutazione che la soluzione è abilitata per Single Sign-On federato di AAD affinché l'app possa essere caricata in AppSource.

     Per comprendere cosa significa che una soluzione sia abilitata per Single Sign-On federato di AAD, passare al minuto 02:35 del video [AppSource trial experience walk through](https://aka.ms/trialexperienceforwebapps) (Procedura dettagliata per l'esperienza di prova AppSource). Se l'app non è ancora abilitata per Single Sign-On federato di AAD, di seguito sono riportati alcuni documenti a riguardo
   1. [Accesso con un clic](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/).
   1. [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application).
     
1. Uso di Power BI nella soluzione: requisito facoltativo ma consigliato poiché è stato dimostrato che genera un numero di lead più elevato.

## <a name="devcenter-account-setup"></a>Configurazione dell'account Dev Center
Si tratta del processo di registrazione dell'azienda come entità di pubblicazione con Microsoft. Se è già presente un'entità di pubblicazione registrata con un account Dev Center esistente, condividere l'ID di posta elettronica associato all'account. Dopo che l'applicazione è stata approvata per la pubblicazione, si otterrà l'accesso alla documentazione completa sul [profilo di editore Cloud Portal Manage](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)

In caso contrario, di seguito sono riportati i passaggi principali per configurare un account Dev Center.
1. Creare un account Microsoft [qui](https://signup.live.com/signup.aspx).
1. Passare alla [pagina di registrazione](http://go.microsoft.com/fwlink/?LinkId=615100) di Microsoft Dev Center e fare clic su "Registrati".
1. Quando viene richiesto di eseguire il pagamento, usare il codice fornito. Se non si dispone di un codice, contattare [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Selezionare il [paese/area geografica](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) in cui vive o lavora l'utente. **Non sarà possibile modificare questa impostazione in un secondo momento.**
1. Selezionare il [tipo di account sviluppatore](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees). Per AppSource, selezionare **Account aziendale**. Per un account aziendale, assicurarsi di consultare queste [linee guida](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Immettere le informazioni di contatto da usare per l'account sviluppatore.
Per la procedura dettagliata di configurazione di un account Dev Center, vedere le istruzioni [qui](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Fornire informazioni per i venditori Microsoft
Una delle proposte di valore principali di AppSource per i partner è rappresentata dalla possibilità di collaborare con i venditori Microsoft Sellers per esporre le app dei partner ai potenziali clienti.

Compilare le [informazioni sulla soluzione partner per venditori Microsoft](https://aka.ms/aapartnerappinfo) e inviarle a [ appsourcecissupport@microsoft.com ](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Si tratta di un passaggio obbligatorio per ottenere l'approvazione per la pubblicazione di un'app Cortana Intelligence.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Creare una procedura dettagliata interessante per i clienti in AppSource
In primo luogo, osservare [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) (Ottimizzazione dell'inventario in Neal Analytics) in AppSource. Ogni voce di app in AppSource presenta un titolo, un riepilogo (max 100 caratteri), una descrizione (max 1.300 caratteri), immagini, video (facoltativi), documenti PDF e un punto di ingresso per la valutazione. I partner devono usare tutti questi elementi per creare un'esperienza coinvolgente per i clienti.

Devono concepire i contenuti da inserire in AppSource come una sorta di orchestrazione di vendita end-to-end. I clienti leggono titolo e descrizione per farsi un'idea della proposta di valore e quindi iniziano a scorrere immagini e video per conoscere il contenuto della soluzione. I case study aiutano i potenziali clienti a scoprire i vantaggi offerti dalla soluzione ai clienti esistenti. 

Tutti questi elementi dovrebbero suscitare l'interesse dei clienti e spingerli ad approfondire il discorso. Si pensi a questi come a una breve presentazione che un buon rappresentante di vendite esporrebbe ai nuovi clienti. Il formato consigliato della descrizione consiste nel suddividere il testo in sottosezioni in base alle proposte di valore, ognuna con una sottointestazione evidenziata. 

I visitatori in genere danno un'occhiata al campo di riepilogo dell'offerta e alle sottointestazioni per avere un'idea dell'app e capire in un attimo perché potrebbe fare al caso loro. È dunque importante catturare l'attenzione degli utenti e offrire loro un motivo per continuare a leggere le specifiche.

Ecco cosa hanno fatto questi partner.
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview) (Ottimizzazione dell'inventario in Neal Analytics)
- [Plexure Retail Personalization](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

La mossa finale per la vendita è quella di mostrare una demo dell'app o della soluzione per evidenziare la proposta di valore. È esattamente questo lo scopo di un'esperienza di valutazione per il cliente in AppSource. Di seguito sono indicati i punti essenziali di una demo valida:
- Riepilogare la proposta di valore dell'app in breve ed elencare gli utenti tipo nell'azienda del cliente che interagiranno con la soluzione
- Raccontare una testimonianza e impostare il contesto di un cliente che deve risolvere alcuni problemi
- Spiegare in che modo la situazione può degenerare e coinvolgere il cliente (prima) e che cosa accadrebbe se la soluzione è già in uso. Questo punto può essere illustrato tramite dashboard PowerBI e così via.
- Riepilogare in che modo si concretizza la soluzione usando specifici algoritmi di Machine Learning e così via.

Il contenuto aggiunto in AppSource deve essere di qualità elevata e raggruppato in modo tale da consentire quanto segue:
- Il rappresentante di vendita di un partner deve usarlo per le orchestrazioni di vendita. Il fatto che il team addetto alle vendite lo usi è un buon segno per aspettarsi che anche i rappresentanti di vendita Microsoft si comportino allo stesso modo. In questo modo il punto di contatto del cliente potrà trasmettere in modo coerente la stessa testimonianza ai colleghi del team e ai superiori per ottenere budget e approvazioni prima di poter eseguire un'offerta di acquisto.
- Un cliente che visita il sito in modo organico può scorrere il contenuto in autonomia e sentirsi interessato a rispondere alle comunicazioni del partner per procedere alle fasi successive.

Per questo motivo i partner devono concepire i contenuti da inserire in AppSource come una sorta di orchestrazione di vendita end-to-end. In base al tipo di testimonianza e alle funzioni da visualizzare nell'esperienza di valutazione, è possibile scegliere il tipo di offerta.

## <a name="publish-your-app-on-the-publishing-portal"></a>Pubblicare l'app nel portale di pubblicazione
Dopo aver valutato i passaggi precedenti per l'applicazione, si otterrà l'accesso al portale di pubblicazione e verrà visualizzata la pagina [How to publish a Cortana Intelligence offer via Cloud Partner Portal](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) (Come pubblicare un'offerta Cortana Intelligence tramite il portale per Cloud Partner) per informazioni dettagliate sui passaggi successivi.

Per informazioni su qualsiasi campo, inviare un messaggio all'indirizzo di posta elettronica <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Dopo aver pubblicato l'app in AppSource, come procedere?
Congratulazioni per aver pubblicato l'app.
Il livello di ricavi che verranno ottenuti pubblicando l'app in AppSource dipende essenzialmente dalla capacità di influenzare i destinatari. Vedere [Growth-Hacking your Cortana Intelligence app on AppSource](http://aka.ms/aagrowthhackguide) (Growth Hacking di un'app Cortana Intelligence in AppSource) per maggiori dettagli su come ottimizzare i ricavi.

In caso di domande o suggerimenti, contattare Microsoft all'indirizzo <appsourcecissupport@microsoft.com>.


