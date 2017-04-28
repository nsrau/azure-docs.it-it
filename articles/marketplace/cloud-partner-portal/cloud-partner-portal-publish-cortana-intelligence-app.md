---
title: Pubblicare un&quot;app Cortana Intelligence in AppSource | Documentazione Microsoft
description: Guida dettagliata per la pubblicazione di un&quot;app Cortana Intelligence o Microsoft R Services in AppSource
services: marketplace
documentationcenter: 
author: hiavi
manager: judym
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: abathula
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 62aa3148406068f06d1fbdaf556ff2d526c0e17b
ms.lasthandoff: 04/13/2017


---
# <a name="publish-a-cortana-intelligence-app-to-appsource"></a>Pubblicare un'app Cortana Intelligence in AppSource
Guida dettagliata per la pubblicazione di un'app Cortana Intelligence o Microsoft R Services in AppSource

## <a name="1-introduction"></a>1. Introduzione

Grazie per l'interesse nella pubblicazione di un'app Cortana Intelligence/Microsoft R Solutions in Microsoft AppSource. Microsoft è lieta di collaborare con i suoi partner dedicati (ISV/ SI) per offrire a clienti, rivenditori e consulenti per l'implementazione la sede principale in cui individuare e provare soluzioni di business e sfruttare al meglio gli investimenti. Questa guida illustra i singoli passaggi necessari per pubblicare la propria soluzione.

## <a name="2-getting-started"></a>2. Introduzione

Seguire le istruzioni

1.  Riportate nella [guida introduttiva alla presentazione con Microsoft](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/CESIPartnerJourneyOverview_1232017.pdf) per entrare a far parte dei partner di analisi avanzata.

2.  Riportate in AppSource compilando il modulo per la [pubblicazione di un'app](https://appsource.microsoft.com/partners/list-an-app).
    - Per la domanda *"Choose the products that your app is built for*" (Scegli i prodotti per i quali è creata l'app), selezionare la casella di controllo **Other** (Altro) e inserire "Cortana Intelligence" nel controllo di modifica.

## <a name="3-solution-evaluation-criteria"></a>3. Criteri di valutazione della soluzione

Ecco l'elenco dei criteri che l'app deve soddisfare

1.  L'app deve risolvere problemi relativi a casi d'uso aziendali specifici all'interno di una data area funzionale in modo ripetibile. Con operazioni minime di configurazione/personalizzazione, deve essere in grado di rispettare la proposta di valore predefinita entro un breve periodo di tempo.

2.  La soluzione deve usare almeno uno dei componenti di analisi seguenti

    1. HDInsight
    2. Machine Learning
    3. Data Lake Analytics
    4. Analisi di flusso
    5. Servizi cognitivi
    6. Bot Framework
    7. Microsoft R Server autonomo o servizi R in SQL 2016/HDInsight Premium

3.  La soluzione deve generare almeno \$ 1.000/mese/cliente tramite il programma Digital Partner of Record (DPOR) o Cloud Solution Provider (CSP).

4.  La soluzione deve usare l'accesso Single Sign-On federato di Azure Active Directory (SSO federato di AAD) con consenso abilitato per l'autenticazione utente e controlli di accesso alle risorse. Se l'app non è già abilitata, vedere

    1. [Attività iniziali](https://identity.microsoft.com/)
    2. [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)

5.  Uso di Power BI nella soluzione e condivisione nel file Power BI Desktop (PBIX): requisito facoltativo ma consigliato poiché è stato dimostrato che genera un numero di lead più elevato

    1. Assicurarsi che tutti i dati siano importati nel file PBIX
    2. Assenza di dati con riferimenti esterni (creiamo automaticamente il report incorporato).

## <a name="4-decide-on-the-type-of-offer-you-would-like-to-publish-on-appsource"></a>4. Stabilire il tipo di offerta che si desidera pubblicare in AppSource

AppSource consente ai partner di offrire due tipi di esperienza di valutazione agli utenti
1.  Valutazioni gestite dai clienti, in cui i clienti possono provare l'app autonomamente
    1.  Versione di prova gratuita
        1.  AppSource indirizza l'utente a un URL fornito dal partner guidandolo attraverso un processo SSO federato di AAD, offrendo un'esperienza di valutazione in un certo timebox.
        2.  Si prevede in questo caso che l'app sia un'app SaaS con supporto multi-tenancy per isolare la configurazione/i dati di un utente da un altro OPPURE che l'esperienza di valutazione riguardi soltanto un sottoinsieme di funzionalità che necessitano esclusivamente di operazioni di sola lettura.

        Esempio: vedere [AFS POP Retail Execution](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview), [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview) (questa app offre un'esperienza dettagliata con percorsi chiari per set di utenti tipo selezionati) e così via.
    2.  Test drive
        1.  La soluzione, o un suo sottoinsieme, può essere inserita in un pacchetto tramite modelli di Azure Resource Manager di cui AppSource può creare un'istanza ed eseguire la gestione nella sottoscrizione di Azure del partner entro un certo timebox, mantenendo un pool di istanze a caldo/freddo e così via.
        2.  Se si sceglie di seguire questa strada, possiamo fornire modelli e codice di esempio.
        
        Esempio: vedere Neal Analytics [Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome).

2.    Valutazioni gestite dai partner, per le quali i clienti devono compilare un modulo di informazioni di contatto in modo tale che il partner possa contattarli e offrire una demo//versione di valutazione e così via.

Per una panoramica generale, guardare il video che illustra la [procedura dettagliata dell'esperienza di valutazione in AppSource](http://aka.ms/trialexperienceforwebapps).

I dati mostrano chiaramente che le valutazioni gestite dai clienti offrono un elevato potenziale di generazione di lead rispetto a quelle guidate dai partner. Basta pensare che a nessuno piace compilare moduli 😊

Decidere quale tipo di offerta si vuole inserire in AppSource.

## <a name="5-getting-approved-to-use-azure-marketplace-publishing-portal"></a>5. Ottenere l'approvazione all'uso del portale di pubblicazione di Azure Marketplace

Prima di poter accedere al portale di pubblicazione di Azure Marketplace per avviare la pubblicazione, è attualmente necessaria l'approvazione degli ID di posta elettronica di AAD/MSA. Comunicare i dettagli seguenti all'indirizzo [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)

-   Nome visualizzato dell'editore: &lt;nome dell'azienda che appare in AppSource come editrice dell'app&gt;

-   Nome univoco dell'editore: &lt;nome univoco composto solo da caratteri minuscoli e trattino '-'&gt;

-   Sito Web: &lt;sito Web dell'azienda&gt;

-   Indirizzo di posta elettronica del proprietario: &lt;ID di posta elettronica di AAD/MSA&gt;

-   Indirizzo di posta elettronica del collaboratore 2: &lt;ID di posta elettronica di AAD/MSA&gt;

 Se necessario, aggiungere ulteriori collaboratori.

## <a name="6-get-info-about-your-companys-lead-management"></a>6. Ottenere informazioni sulla gestione dei lead della propria società

Quando i visitatori esprimono interesse in una soluzione, i lead vengono inviati direttamente nei sistemi di gestione dei lead del partner. AppSource supporta più tipi di sistemi di gestione dei lead (Dynamics CRM, Salesforce, Marketo e così via).

Per i dettagli del processo complessivo e su come trovare le giuste informazioni per configurare i lead di AppSource, vedere l'articolo relativo alla [gestione dei lead in AppSource](./cloud-partner-portal-get-customer-leads.md).    

## <a name="7-publish-your-app-on-the-publishing-portal"></a>7. Pubblicare l'app nel portale di pubblicazione

Per informazioni sui campi non illustrati in questo articolo, inviare un messaggio all'indirizzo di posta elettronica <appsourcecissupport@microsoft.com>

Seguire questa procedura

1.  Dal browser Google Chrome aprire il [portale per Cloud Partner](https://cloudpartner.azure.com)

2.  Accedere con il proprio ID di posta elettronica di AAD/MSA consentito

3.  Selezionare New Offer (Nuova offerta) -&gt;Cortana Intelligence

    ![publishaaapp1][1]
4.  Immettere i dettagli relativi alle impostazioni dell'offerta

    ![publishaaapp2][2]

    1. L'ID dell'offerta è utilizzato esclusivamente per identificare in modo univoco l'app in AppSource come parte dell'URL univoco di AppSource. Esempio: "appsource-saas-app". Sarà visibile ai clienti in posizioni quali i modelli di Azure Resource Manager e i report di fatturazione. Si noti che le informazioni relative all'ID dell'offerta e all'editore non possono essere modificate dopo il salvataggio.

    2. Per il nome dell'offerta usare il nome della soluzione. Non aggiungere il nome della società, poiché viene aggiunto automaticamente sotto il nome della soluzione

5.  Compilare la sezione delle informazioni tecniche. La maggior parte di esse è facilmente comprensibile e dispone di descrizioni comandi che forniscono altre informazioni.

    1.  I video dimostrativi devono essere registrazioni con modifiche minime delle funzionalità della soluzione effettiva e devono mettere in evidenza l'autenticazione degli utenti tramite AAD, gli aspetti principali delle funzionalità destinate agli utenti e l'integrazione con la piattaforma Cortana Intelligence.  I video dimostrativi **non** saranno pubblicamente disponibili ai clienti, ma devono essere rappresentativi del modo in cui la soluzione *si presenterebbe* a un potenziale cliente.  Di conseguenza, devono essere dotati di script e ripetibili.

        Per preparare il video dimostrativo è possibile usare qualsiasi strumento di registrazione dello schermo che consente di esportare un formato video standard (ad esempio MPEG). Ecco le istruzioni se si dispone di Skype for Business

        1.  [Avviare una riunione](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
        2. [Condividere il desktop](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
        3. [Avviare la registrazione](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
        4. Al termine della registrazione, [utilizzare Gestione registrazione per pubblicare la registrazione](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

        È necessario caricare il video registrato in un servizio che consente di generare un URL condiviso.  Ad esempio, si può usare un [collegamento guest in OneDrive/Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232). 

    2.  Per le linee guida su come caricare il diagramma dell'architettura della soluzione, vedere il documento relativo al [modello di flusso di lavoro della soluzione di analisi avanzata](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)

6.  Compilare la sezione Storefront Details (Dettagli vetrina)

    1.  Nota: nei campi relativi alla descrizione dell'offerta e alle condizioni per l'utilizzo è possibile immettere contenuto html. È possibile usare qualsiasi editor html online per modificare il contenuto html fino a ottenere l'aspetto desiderato. La descrizione dell'offerta deve essere molto chiara e illustrare sinteticamente i punti chiave che spiegano il caso aziendale risolto e il ROI che otterrebbe il cliente, con eventuale documentazione e materiale aggiuntivo a supporto di tali punti chiave.

    2.  Per il tipo di app, in base a quanto illustrato nella sezione precedente denominata "Stabilire il tipo di offerta che si desidera pubblicare in AppSource", scegliere una delle opzioni seguenti
        1.  "Free" (Gratuita) se si desidera offrire ai clienti un'esperienza completamente gratuita senza timebox.
        2.  "Trial" (Versione di valutazione) se si desidera offrire ai clienti un'esperienza tramite una versione di valutazione o un test drive con timebox.
        3.  "Request a trial" (Richiesta di una versione di valutazione) se si desidera offrire ai clienti un'esperienza di valutazione gestita dal partner.

    3.  Hide key to test your offer in staging (Nascondi chiave per testare l'offerta in fase di staging): opzione usata per creare un URL di anteprima per l'app dopo la fase di staging, senza password per maggiore semplicità. Esempio: "AppSourceHideKey"

    4.  Affinché la pubblicazione dell'offerta sia approvata, è necessario che le immagini caricate siano leggibili e di alta qualità.

    5.  Case study: fornire un case study con i dettagli seguenti

        1.  Implementazione operativa del cliente
        2. Presentazione del problema chiaramente definita per il cliente effettivo o anonimo
        3. Passaggi eseguiti per risolvere il problema
        4. Riferimento ai  componenti di analisi avanzata CIS/Microsoft che hanno contribuito a risolvere il problema.
        5.  Vantaggio netto o ROI derivante dall'adozione della soluzione
        6. *Elementi indesiderati:* video o brochure di marketing con una mera spiegazione della piattaforma complessiva senza i requisiti di cui sopra

    6.  È consigliabile inserire in AppSource un video con gli stessi requisiti del case study, in quanto genera più lead. Quando si immette l'URL di un video, può trattarsi di un video di YouTube o Vimeo. Verificare che i video siano in uno dei formati seguenti
        1. https://vimeo.com/########## oppure
        2. https://www.youtube.com/watch?v=##########

7.  Compilare la sezione dei contatti: tali dati saranno usati per l'invio di notifiche sull'utilizzo, avvisi del marketplace e così via.

8.  Pubblicare l'offerta. Il partner riceverà delle notifiche tramite posta elettronica man mano che la soluzione avanza nel processo di certificazione. È possibile visualizzare i dettagli dello stato nella scheda "Status" (Stato) dell'interfaccia utente di pubblicazione dell'offerta.

    1.  Le soluzioni entreranno prima in una fase di staging.
    2.  È possibile verificare il contenuto in fase di staging facendo clic sul collegamento di AppSource che il partner riceverà tramite posta elettronica.
    3.  Apportare le modifiche necessarie, quindi inviare di nuovo la soluzione. Quando si è soddisfatti del contenuto finale, inviare l'app in produzione.
    4.  A questo punto provvederemo a convalidare i metadati dell'app e infine ad approvare l'app per l'inserimento in AppSource o, in caso di rifiuto, a comunicarne il motivo.

[1]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp1.png
[2]: ./media/cloud-partner-portal-publish-cortana-intelligence-app/publishaaapp2.png    

