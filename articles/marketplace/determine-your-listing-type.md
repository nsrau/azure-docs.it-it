---
title: Determinare il tipo di inserzione in Microsoft Azure Marketplace | Azure
description: Questo articolo illustra i criteri di idoneità e i requisiti di pubblicazione per i partner che vogliono comprendere come pubblicare le app in Microsoft Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 1c7fd3ac1a4cf62dd8f909acc26615ea5f96f32c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309099"
---
# <a name="determine-the-listing-type-for-your-solution"></a>Determinare il tipo di inserzione per la propria soluzione  
Le vetrine supportano molte opzioni di pubblicazione: tipi di inserzioni e tipi di offerte. Scegliere il tipo di offerta che meglio rappresenta i dettagli della soluzione e del servizio. Tutte le opzioni di pubblicazione offrono l'accesso alla condivisione di clienti potenziali.   

| Vetrina | Tipo di inserzione | Tipo di app | Linee guida tecniche |  
|:--- |:--- |:--- |:--- |  
| AppSource | [Elenco](#list) | [Servizio di consulenza](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
| Azure Marketplace | [Elenco](#list) | [Servizio di consulenza](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| Azure Marketplace | [Elenco](#list) | [Contattami](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| Azure Marketplace | [Versione di valutazione](#trial) | [Versione di valutazione gratuita/Versione di valutazione SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| AppSource |  | Cortana Intelligence | [docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
| AppSource |  |Dynamics 365 for Customer Engagement | [docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
| AppSource |  | Dynamics 365 per Finanza e operazioni | [docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
| Azure Marketplace | [Versione di valutazione](#trial) | [Demo interattiva](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| Azure Marketplace | [Versione di valutazione](#trial) | [Test drive](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| AppSource |  | Office 365 | [docs.microsoft.com/office/dev/store/submit-to-the-office-store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
| AppSource |  | Power BI | [docs.microsoft.com/power-bi/developer/office-store](https://docs.microsoft.com/power-bi/developer/office-store) | | Azure Marketplace | [Transazione](#transact) | [App di Azure: App gestite](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/azure/managed-applications/overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| Azure Marketplace | [Transazione](#transact) | [App di Azure: Modello di soluzione](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| Azure Marketplace | [Transazione](#transact) | [Contenitore](#requirements-azure-marketplace-transact-container) |  [](#) |  
| Azure Marketplace | [Transazione](#transact) | [App SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| Azure Marketplace | [Transazione](#transact) | [Macchina virtuale](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |   

## <a name="azure-certified-program"></a>Programma Azure Certified  
Tutte le macchine virtuali (VM) pubblicate in Azure Marketplace sono testate per il programma Azure Certified.  
*   Per altre informazioni, visitare la pagina Microsoft Azure Certified all'indirizzo [azure.microsoft.com/marketplace/programs/certified](https://azure.microsoft.com/marketplace/programs/certified). 

Il programma di certificazione offre quanto segue.  
*   La macchina virtuale è compatibile con la piattaforma Azure e con il modello di vendita del Marketplace.  
*   La macchina virtuale è testata per la conformità ai requisiti di sicurezza dell'immagine online, inclusi virus e malware.  
*   La macchina virtuale include il badging a livello di offerta. Il badging come soluzione convalidata migliora la promozione per i clienti aziendali di Microsoft.   

---   

## <a name="appsource-publishing-by-product"></a>Pubblicazione AppSource per prodotto  
La tabella seguente fornisce altre informazioni sui requisiti specifici per le app AppSource che estendono Office Dynamics e Power BI.  

| Tipo di inserzione | Tipo di app | Linee guida tecniche |  
|:--- |:--- |:--- |  
| [Elenco](#list) | [Servizio di consulenza](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
|  | Cortana Intelligence | [docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
|  |Dynamics 365 for Customer Engagement | [docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
|  | Dynamics 365 per Finanza e operazioni | [docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
|  | Office 365 | [docs.microsoft.com/office/dev/store/submit-to-the-office-store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
|  | Power BI | [docs.microsoft.com/power-bi/developer/office-store](https://docs.microsoft.com/power-bi/developer/office-store) |  

---   

## <a name="azure-marketplace-publishing-by-product"></a>Pubblicazione in Azure Marketplace per prodotto  

![](./media/marketplace-publishers-guide/workflow-azure-marketplace.png)  

La tabella seguente offre altre informazioni sui requisiti specifici per le offerte di Azure Marketplace.  

| Tipo di inserzione | Tipo di offerta |  Linee guida tecniche |  
|:--- |:--- |:--- |  
| [Elenco](#list) | [Servizio di consulenza](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| [Elenco](#list) | [Contattami](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| [Versione di valutazione](#trial) | [Versione di valutazione gratuita/Versione di valutazione SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| [Versione di valutazione](#trial) | [Demo interattiva](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| [Versione di valutazione](#trial) | [Test drive](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| [Transazione](#transact) | [App di Azure: App gestite](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/azure/managed-applications/overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| [Transazione](#transact) | [App di Azure: Modello di soluzione](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| [Transazione](#transact) | [Contenitore](#requirements-azure-marketplace-transact-container) |  [](#) |  
| [Transazione](#transact) | [App SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| [Transazione](#transact) | [Macchina virtuale](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |  

---   

## <a name="non-transact-listings"></a>Inserzioni senza transazioni  
Questa sezione vengono descritte tutte le offerte che non usano il tipo di inserzione Transazione. 

### <a name="list"></a>Elenco  
Il tipo di inserzione Elenco include i seguenti tipi di offerta nelle vetrine in Marketplace.  

| Tipo di offerta | Vetrina | Dettagli |  
|:---        |:---        |:---     |  
| Servizi di consulenza | AppSource | [Requisiti: AppSource: Elenco: Servizi di consulenza](#requirements-appsource-list-consulting-service) |  
| Servizi di consulenza | Azure Marketplace | [Requisiti: Azure Marketplace: Elenco: Servizi di consulenza](#requirements-azure-marketplace-list-consulting-service) |  
| Contattami | AppSource | [](#) |  
| Contattami | Azure Marketplace | [Requisiti: AppSource: Elenco: Contattami](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisiti: AppSource: Elenco: Servizio di consulenza  
Quando l'offerta è costituita principalmente da servizi professionali, ad esempio valutazioni, implementazioni o workshop, usare il tipo di offerta Servizi di consulenza.  

| Requisiti | Dettagli |  
|:--- |:--- |  
| Caratteristiche dell'offerta del servizio | Il servizio di consulenza deve soddisfare i criteri seguenti.<ul> <li>Offerta con un ambito, una durata e un prezzo prestabiliti (o gratuita).</li> <li>Orientato principalmente alla pre-vendita.</li> <li>Limitato a un singolo cliente.</li> <li>Effettuato presso la sede.</li> </ul> |  
| Requisiti dei partner per i servizi di consulenza | Vengono soddisfatti i criteri nell'area pertinente al servizio.<table><tr><th>Area soluzione</th><th>Criteri</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Avere la competenza Silver o Gold di Cloud Customer Relationship Management.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise edition</td><td>Avere la competenza Silver o Gold di Enterprise Resource Planning e un fatturato generato dalle operazioni cloud degli ultimi 12 mesi di $25.000 o più.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business edition</td><td>Aver operato come Provider di servizi Cloud (CSP) o come Digital POR (Partner of Record) per uno o più clienti.</td></tr><tr><td>Power BI</td><td>Soddisfare i criteri di Partner di soluzioni.</td></tr><tr><td>PowerApps</td><td>Avere pubblicato una presentazione di soluzioni partner.</td></tr></table><ul> <li>Per altre informazioni sulla gestione delle relazioni con i clienti, visitare la pagina Cloud Customer Relationship Management all'indirizzo [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Per altre informazioni sulla pianificazione delle risorse, visitare la pagina Enterprise Resource Planning all'indirizzo [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Per altre informazioni sui provider CSP, visitare la pagina Cloud Services Provider all'indirizzo [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Per altre informazioni su DPOR, visitare la pagina Digital POR (Partner of Record) e associazione del partner all'indirizzo [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Per altre informazioni sui criteri dei partner di soluzione, consultare la documentazione sulla panoramica e gli incentivi dei partner di soluzione disponibile all'indirizzo [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Per altre informazioni sulla presentazione dei partner, visitare la pagina sulla presentazione dei partner pagina all'indirizzo [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisiti: AppSource: Marketplace: Elenco: Servizi di consulenza  

| Requisiti | Dettagli |  
|:--- |:--- |  
| Caratteristiche dell'offerta del servizio | Il servizio di consulenza deve soddisfare i criteri seguenti.<ul> <li>Offerta con un ambito, una durata e un prezzo prestabiliti (o gratuita).</li> <li>Orientato principalmente alla pre-vendita.</li> <li>Limitato a un singolo cliente.</li> <li>Effettuato presso la sede.</li> </ul> |  
| Requisiti dei partner per i servizi di consulenza | È necessario avere la competenza Silver o Gold in una delle competenze seguenti nell'area pertinente al servizio. <table><tr><th>Area soluzione</th><th>Competenza</th></tr><td>Piattaforma e infrastruttura cloud</td><td>Piattaforma cloud<br />Data center</td><tr><td>Sviluppo di applicazioni e ISV</td><td>Sviluppo di applicazioni<br />Integrazione delle applicazioni<br />DevOps</td></tr><tr><td>Gestione e analisi dei dati</td><td>Analisi dei dati<br />Piattaforma dati</td></tr></table><ul> <li>Per altre informazioni sulle competenze, visitare la pagina Partner con competenze all'indirizzo [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Per altre informazioni sulle inserzioni, visitare la pagina Servizi di consulenza in Azure Marketplace all'indirizzo [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

#### <a name="requirements-azure-marketplace-list-contact-me"></a>Requisiti: Azure Marketplace: Elenco: Contattami  
Usare il tipo di offerta Contattami quando l'uso del tipo di inserzione di valutazione o del tipo di inserzione con transazione non è fattibile. Il vantaggio del tipo di offerta Contattami è che è possibile ricevere clienti potenziali con una soluzione commerciale. I clienti potenziali devono essere seguiti nelle offerte di base per dare impulso alla propria azienda. Lo svantaggio consiste invece nel coinvolgimento limitato del cliente rispetto ad altri tipi di offerta.  

>[!IMPORTANT]
>Il coinvolgimento dei clienti è migliore con le offerte di tipo Valutazione e Transazione. Il valore del tipo di inserzione Contattami è il cliente potenziale che si riceve. Se si sceglie il tipo di inserzione Contattami, è necessario verificare che i clienti potenziali destinatari siano configurati e si è pronti per ampliare al massimo il loro numero.  

---   

### <a name="trial"></a>Versione di valutazione  
Un'esperienza di valutazione aumenta il livello di impegno fornito ai clienti. Un livello di impegno maggiore provoca una maggiore esposizione della soluzione. Un'offerta di versione di valutazione offre la possibilità ai clienti di esplorare la soluzione prima di acquistarla. Offrendo un'esperienza di valutazione, si hanno maggiori opportunità di promozione nelle vetrine e con molta probabilità si riesce a raggiungere un numero più ampio di clienti potenziali grazie a questa occasione di coinvolgimento.  

L'app o l'offerta che usa il tipo di inserzione Versione di valutazione viene distribuita nell'ambiente di prova, nella sottoscrizione di Azure o in entrambi. Altri tipi di inserzioni saranno distribuiti all'ambiente o nella sottoscrizione di Azure del cliente. L'app o l'offerta deve essere gestita dal cliente senza richieste aggiuntive di acquisto. L'app o l'offerta dovrebbe essere minima, con una configurazione aggiuntiva per completare un caso d'uso. L'applicazione o l'offerta devono includere il supporto gratuito almeno per la durata del periodo di prova. Per ottenere risultati ottimali, i clienti devono essere seguiti e monitorati lungo un percorso di valutazione ponderato. Si invita a usare i clienti potenziali forniti in marketplace e dalla propria competenza in-app. I clienti potenziali devono essere usati per monitorare e gestire i clienti.  

Il tipo di inserzione Versione di valutazione include i tipi di offerta seguenti nelle vetrine in marketplace.  

| Tipo di offerta | Vetrina | Dettagli |  
|:---        |:---        |:---     |  
| Versione di valutazione gratuita/SaaS | AppSource | [Requisiti del tipo di inserzione: Versione di valutazione](#listing-type-requirements-trial) |  
| Versione di valutazione gratuita/SaaS | Azure Marketplace | [Requisiti: Azure Marketplace: versione di valutazione: Versione di valutazione gratuita/Versione di valutazione di SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Demo interattiva | AppSource | [Requisiti del tipo di inserzione: Versione di valutazione](#listing-type-requirements-trial) |  
| Demo interattiva | Azure Marketplace | [Requisiti: Azure Marketplace: Versione di valutazione: Demo interattiva](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test drive | AppSource | [Requisiti del tipo di inserzione: Versione di valutazione](#listing-type-requirements-trial) |  
| Test drive | Azure Marketplace | [Requisiti: Azure Marketplace: Versione di valutazione: Test drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Requisiti: Azure Marketplace: Versione di valutazione  

| Requisito | Dettagli |  
|:--- |:--- |  
| Periodo di valutazione gratuita ed esperienza di valutazione | Il cliente può provare l'app gratuitamente per un periodo di tempo limitato.<br /><br />Il cliente non deve pagare costi di licenza o di sottoscrizione per l'offerta. I clienti non devono pagare il prodotto o il servizio proprietario di Microsoft sottostante. Tutte le opzioni di valutazione sono distribuite nella sottoscrizione di Azure. L'utente ha il controllo esclusivo dell'ottimizzazione dei costi e della gestione.<br /><br />È possibile scegliere una versione di valutazione gratuita, una demo interattiva o un test drive. Indipendentemente dall'opzione selezionata, per la prova gratuita è necessario concedere al cliente un periodo prestabilito di tempo per provare l'offerta senza costi aggiuntivi.<ul> <li>Per iniziare a creare un test drive, inviare un messaggio di posta elettronica a [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Nota: tutte le offerte di prova SaaS in Azure Marketplace devono consentire al cliente di usare le credenziali di lavoro per eseguire l'accesso.<ul> <li>Per altre informazioni, visitare la sezione relativa alle esperienze di valutazione di AppSource all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Soluzione pronta all'uso e facilmente configurabile | L'app deve essere semplice e rapida da configurare e installare. |  
| Disponibilità/Tempo di attività | L'app o la piattaforma SaaS deve avere un tempo di attività pari ad almeno il 99,9%. |  
| Azure Active Directory | L'offerta deve consentire l'accesso Single Sign-On (SSO) federato di Azure Active Directory (accesso SSO federato di Azure AD) con il consenso abilitato. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisiti: Azure Marketplace: Versione di valutazione: Versione di valutazione gratuita/Versione di valutazione di SaaS  
Fornire una soluzione o l'app usando una versione di valutazione basata su SaaS (software-as-a-service) che è possibile provare gratuitamente. Il tipo di offerta di valutazione SaaS ha il vantaggio di attirare clienti potenziali di qualità tra i clienti interessati, in modo da dare impulso alla propria azienda. Le offerte di prova gratuita possono essere presentate come un account di prova con durata o uso limitato e dovrebbero includere un invito che consenta di accelerare la conversione all'uso della soluzione a pagamento.  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente al cliente di provare il prodotto prima di acquistarlo con un metodo automatico per la conversione all'uso a pagamento. oltre a offrire un modello di verifica per i clienti e opportunità di collaborazione con i team di vendita Microsoft. | La soluzione è macchina virtuale o applicazioni di Azure: tipo di offerta Modello di soluzione.<br /><br />La soluzione è un'offerta SaaS e viene offerto un prodotto SaaS multi-tenant.<br /><br />Si ha un'esperienza consolidata per garantire ai clienti l'operatività immediata.<br /><br />Si dispone di un tenant singolo ma si aggiungono clienti come guest. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisiti: Azure Marketplace: Versione di valutazione: Demo interattiva  
Fornire ai clienti un'esperienza guidata della soluzione usando una demo interattiva. Il vantaggio dell'offerta della demo interattiva è dato dalla possibilità di fornire un'esperienza di valutazione di una soluzione complessa senza dover eseguire complicate operazioni di provisioning. La demo interattiva offre al cliente la possibilità di osservare la soluzione. La demo interattiva fornisce i clienti potenziali che devono seguire offerte di base per dare impulso alla propria azienda.  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente ai clienti di osservare come funziona una soluzione senza doversi preoccupare dell'installazione. | La soluzione richiede un'installazione complessa che sarebbe difficile completare nel periodo di prova. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisiti: Azure Marketplace: Versione di valutazione: Test drive  
Vengono distribuite una o più macchine virtuali tramite le applicazioni IaaS (infrastructure-as-a-service) o SaaS. Il vantaggio del tipo di offerta Test drive è il provisioning automatico di una macchina virtuale o dell'intera soluzione con una presentazione guidata ospitata da un partner. Un test drive fornisce al cliente una valutazione senza alcun costo aggiuntivo. Non è necessario che il cliente sia già un utente di Azure per contribuire a generare nuovi clienti potenziali di qualità.  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente ai clienti di valutare il prodotto prima dell'acquisto.<br /><br />Offre un'esperienza guidata della soluzione usando impostazioni preconfigurate.<br /><br />Di seguito sono illustrati altri vantaggi quando si usa un test drive.<ul> <li>Il 27% delle ricerche eseguite dagli utenti nel marketplace è filtrato in modo da visualizzare solo offerte con test drive.</li> <li>Le offerte con test drive generano un numero di clienti potenziali superiore del 38% rispetto alle offerte che ne sono prive.</li> <li>Il 36% delle acquisizioni di nuovi clienti nel marketplace proviene dai clienti che hanno usato un test drive.</li> <li>Le test drive consentono ai venditori Microsoft sul campo di comprendere meglio le funzionalità di un prodotto per offrire maggiori opportunità di co-selling.</li> </ul> | La soluzione è una macchina virtuale, app Azure: Modello di soluzione, app SaaS con tenant singolo o è una soluzione di cui è complicato effettuare il provisioning. <br /><br />Non prevede un metodo per convertire la versione di valutazione in offerta a pagamento. |  

---

## <a name="transact-specific-listings"></a>Inserzioni specifiche per transazioni
Questa sezione descrive tutte le offerte che usano il tipo di inserzione Transazione.

### <a name="transact"></a>Transazione  
Il tipo di inserzione Transazione include i tipi di offerta seguenti nelle vetrine in marketplace.  

| Tipo di offerta | Vetrina | Dettagli |   
|:---        |:---        | :--- |  
| App di Azure: App gestita | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App di Azure: App gestita](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| App di Azure: Modello di soluzione | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App di Azure: Modello di soluzione](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Contenitori | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: Contenitore](#requirements-azure-marketplace-transact-container) |  
| App SaaS  | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Macchina virtuale | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: Macchina virtuale](#requirements-azure-marketplace-transact-virtual-machine) |  

#### <a name="requirements-azure-marketplace-transact-azure-apps-managed-app"></a>Requisiti: Azure Marketplace: Transazione: App di Azure: App gestita  
Usare l'app di Azure: tipo di offerta di app gestita quando sono necessarie le condizioni seguenti.  
*   Si distribuisce una soluzione basata su sottoscrizione per i clienti che usano una macchina virtuale o un'intera soluzione basata su IaaS.  
*   L'utente o il cliente richiedono che la soluzione venga gestita da un partner.  

    >[!NOTE]
    >Ad esempio, un partner potrebbe essere un integratore di sistema o provider di servizi gestiti (MSP).  
  
*   Per un elenco di domande frequenti, visitare la pagina delle domande frequenti su Marketplace all'indirizzo [azure.microsoft.com/marketplace/faq](https://azure.microsoft.com/marketplace/faq).  

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

#### <a name="requirements-azure-marketplace-transact-azure-apps-solution-template"></a>Requisiti: Azure Marketplace: Transazione: App di Azure: Modello di soluzione  
Usare app Azure: l'offerta Modello di soluzione quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una semplice macchina virtuale. È possibile automatizzare il provisioning di una o più macchine virtuali con le app di Azure: Modelli di soluzione. È anche possibile eseguire il provisioning di risorse di rete e di archiviazione. App di Azure: l'offerta Modello di soluzione offre i vantaggi di automazione per singole macchine virtuali e le soluzioni complete basate su IaaS.  

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisiti: Azure Marketplace: Transazione: Contenitore  
Usare il tipo di offerta Contenitore quando la soluzione è un'immagine del contenitore Docker di cui è stato effettuato il provisioning come un servizio contenitore di Azure basato su Kubernetes.

>[!NOTE]
>Ad esempio, un servizio contenitore di Azure basato su Kubernetes come Azure Kubernetes Service o Istanze di contenitore di Azure, la scelta dei clienti di Azure per un runtime del contenitore basato su Kubernetes.  

Microsoft supporta attualmente i modelli di licenza BYOL (Bring Your Own License) e gratuiti. Altre opzioni commerciali sono previste per i prossimi mesi, tra cui opzioni di sottoscrizione e opzioni di fatturazione in base al consumo.  

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita. |  
| Immagine creata da Dockerfile | Le immagini del contenitore devono essere basate sulla specifica per immagini Docker e devono essere create da Dockerfile.<ul> <li>Per altre informazioni sulla creazione delle immagini Docker, consultare la sezione Usage (Utilizzo) all'indirizzo [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting nel record di controllo di accesso | Le immagini del contenitore devono essere ospitate in un repository del Registro contenitori di Azure.<ul> <li>Per altre informazioni sull'uso del Registro contenitori di Azure, visitare la pagina Avvio rapido: Creare un registro contenitori con il portale di Azure all'indirizzo [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tag di immagine | Le immagini del contenitore devono includere almeno 1 tag (numero massimo di tag: 16).<ul> <li>Per altre informazioni sui tag di immagine, visitare la pagina docker tag all'indirizzo [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisiti: Azure Marketplace: Transazione: App SaaS  
Usare il tipo di offerta di app SaaS per consentire ai clienti di acquistare la soluzione tecnica basata su SaaS come una sottoscrizione. Per l'app SaaS è necessario soddisfare i requisiti seguenti.  
*   Prezzo e costi del servizio con tariffe fisse mensili.  
*   Fornire un metodo per aggiornare o annullare il servizio in qualsiasi momento.  

Microsoft gestisce la transazione commerciale. Microsoft invia gli addebiti al cliente per conto dell'utente. Per usare un'app SaaS sotto forma di sottoscrizione, è necessario abilitare la propria API del servizio di gestione della sottoscrizione. L'API del servizio di gestione della sottoscrizione deve comunicare direttamente con le API di Azure Resource Manager. L'API del servizio di gestione della sottoscrizione deve supportare il provisioning, l'aggiornamento e l'annullamento del servizio.  

| Requisito | Dettagli |  
|:--- |:--- |  
|Fatturazione e misurazione | Per l'offerta è prevista una tariffa fissa mensile. Non sono attualmente supportati prezzi basati sull'utilizzo e funzionalità per il calcolo dell'utilizzo effettivo. |  
|Annullamento | L'offerta può essere annullata dal cliente in qualsiasi momento. |  
|Pagina di destinazione delle transazioni | L'editore ospita una pagina di destinazione delle transazioni di Azure con co-branding in cui gli utenti possono creare e gestire il proprio account per il servizio SaaS. |   
| API della sottoscrizione | L'editore espone un servizio che può interagire con la sottoscrizione SaaS per creare, aggiornare ed eliminare un account utente e un piano di servizio. Le modifiche critiche all'API devono essere supportate entro 24 ore. Le modifiche non critiche all'API verranno rilasciate periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisiti: Azure Marketplace: Transazione: Macchina virtuale  
Usare il tipo di offerta della macchina virtuale quando si distribuisce un'appliance virtuale associata al cliente. Le macchine virtuali sono completamente abilitate per la commercializzazione tramite i modelli di licenza con pagamento in base al consumo o Bring Your Own License (BYOL). Microsoft gestisce la transazione commerciale e addebita il costo al cliente per conto dell'utente. Si ottiene il vantaggio di usare la relazione di pagamento preferita tra il cliente e Microsoft, compresi eventuali contratti Enterprise.  

>[!NOTE]
>A questo punto, gli impegni monetari associati a un contratto Enterprise possono essere usati per l'utilizzo di Azure della macchina virtuale, ma non per i costi di licenza del software.  

>[!NOTE]
>È possibile limitare l'individuazione e la distribuzione della VM a un set specifico di clienti inserendo l'immagine e i prezzi in un'inserzione privata. Le offerte private consentono agli utenti di creare offerte esclusive per i clienti principali e di proporre software e condizioni di utilizzo personalizzati. Tali condizioni personalizzate consentono di porre in evidenza numerosi scenari, con offerte contenenti condizioni e prezzi particolari, nonché l'accesso anticipato a versioni software limitate. Le offerte private consentono di offrire a un gruppo ristretto di clienti prezzi o prodotti specifici, creando un nuovo SKU con i dettagli selezionati.  
*   Per altre informazioni sulle offerte private, visitare la pagina relativa alle offerte private in Azure Marketplace all'indirizzo [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Dettagli |  
|:--- |:--- | 
| Fatturazione e misurazione | La macchina virtuale deve supportare la fatturazione mensile BYOL o con pagamento in base al consumo. |  
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, visitare la sezione Creare un VHD compatibile con Azure (basato su Linux) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, visitare la sezione Creare un VHD compatibile con Azure (basato su Windows) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Passaggi successivi
*   Visitare la pagina [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  
