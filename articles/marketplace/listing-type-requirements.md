---
title: Requisiti in base al tipo di inserzione| Azure
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
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 07a62dfa2d7e1c71daf547c5aa7c8c7d15830bfd
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309317"
---
# <a name="requirements-by-listing-type"></a>Requisiti in base al tipo di inserzione  
I requisiti tecnici e quelli relativi al contenuto di marketing variano in base alla vetrina, al tipo di offerta e al tipo di inserzione. Rivedere le specifiche seguenti per verificare la conformità.  
1. Requisiti della vetrina:  
    *   [AppSource](#storefront-requirements-appSource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Requisiti del tipo di inserzione e di offerta:  
    *   Per altre informazioni sui tipi di inserzione e di offerta, visitare la pagina che illustra come determinare il tipo di inserzione per la propria soluzione all'indirizzo [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Requisiti della vetrina: AppSource  
Nella tabella seguente vengono descritti i prerequisiti per la pubblicazione in AppSource.  
| Requisito | Dettagli | Obbligatori o consigliati |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | L'app deve consentire l'accesso Single Sign-On federato di Azure Active Directory (SSO federato di Azure AD) con il consenso abilitato.<ul> <li>Per altre informazioni sull'abilitazione dell'SSO federato di Azure AD, visitare pagina Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory all'indirizzo [docs.microsoft.com/azure/active-directory/ Attiva-directory--custom-App saas](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Obbligatoria |   
| ***Integrazione con servizi Microsoft Cloud*** | L'app deve essere integrabile con altri servizi Microsoft Cloud come Microsoft Power BI, Cortana Intelligence o i servizi di Microsoft Azure.<ul> <li>Un esempio di un servizio cloud di Microsoft è Internet delle cose.</li> </ul> | Consigliato |  
| ***Destinatari*** | L'applicazione deve essere destinata a utenti line-of-business e a titolari di aziende. | Obbligatoria | 
| ***App SaaS (Software as a Service) per aziende*** | L'applicazione deve soddisfare i seguenti requisiti.<ul> <li>SaaS line-of-business</li> <li>Focalizzata sui processi aziendali</li> <li>Destinata a clienti aziendali</li> <li>Consentire agli utenti di usare le proprie credenziali di lavoro (nome utente e password) per accedere</li> </ul> | Obbligatoria |  
| ***Periodo di valutazione gratuita ed esperienza di valutazione*** | L'app deve includere una delle seguenti opzioni affinché un cliente possa usare l'app gratuitamente per un periodo di tempo limitato.<ul> <li>Fornire un metodo `try` in modo che i clienti possano avviare una versione di valutazione dell'app in AppSource</li> <li>Fornire un'opzione `request trial` in AppSource, in modo che i clienti possano richiedere una versione di valutazione dell'app</li> </ul>La versione di valutazione gratuita fornita deve offrire al cliente un periodo di tempo preimpostato per provare l'app senza costi aggiuntivi. | Obbligatoria |  
| ***Soluzione pronta all'uso e facilmente configurabile*** | L'app deve essere semplice e rapida da configurare e installare senza richiedere personalizzazioni. | Obbligatoria |  
| ***Gestione dei clienti potenziali*** | È necessario abilitare il CRM per accettare i dati dei clienti potenziali prima di riceverli dalla vetrina.<ul> <li>Esempi di CRM sono Marketo, Microsoft Dynamics o Salesforce</li> </ul> | Obbligatoria |  
| ***Informativa sulla privacy e condizioni per l'utilizzo*** | L'app deve fornire un collegamento alla pagina dell'informativa sulla privacy con un URL pubblico e le condizioni per l'utilizzo devono essere fornite in formato testo al momento della pubblicazione. | Obbligatoria |  
| ***Supporto*** | L'app deve fornire un collegamento alla pagina dell'assistenza clienti con un URL pubblico. Se l'app è una versione di valutazione, è necessario fornire il supporto senza alcun costo aggiuntivo durante il periodo di valutazione. | Obbligatoria |  

## <a name="storefront-requirements-azure-marketplace"></a>Requisiti delle vetrine: Azure Marketplace  
Di seguito sono elencati i prerequisiti per i tipi di inserzione in Azure Marketplace.  
| Requisito | Dettagli | Tipo di inserzione |  
|:--- |:--- |:--- |  
| ***Criteri di partecipazione*** | L'app deve seguire criteri di partecipazione ad Azure Marketplace.<ul> <li>Per altre informazioni sui criteri di partecipazione vedere la pagina Criteri di Partecipazione a Microsoft Azure Marketplace all'indirizzo [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />transazione<br />versione di valutazione |  
| ***Integrazione con Microsoft*** | Le offerte dovrebbero usare o estendere i tipi di servizi di Microsoft Azure come calcolo, rete o archiviazione e allinearsi a una categoria esistente di Azure Marketplace, ad esempio database, sicurezza o rete.<ul> <li>Per ulteriori informazioni sulle offerte di Marketplace, visitare la pagina delle app di Marketplace all'indirizzo [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />transazione<br />versione di valutazione |  
| ***Destinatari*** | Le offerte devono essere destinate a professionisti IT, sviluppatori cloud o altri clienti con specifici ruoli tecnici. | list<br />transazione<br />versione di valutazione |  
| ***Gestione dei clienti potenziali*** | È necessario configurare il proprio sistema CRM (Marketo, Microsoft Dynamics o Salesforce) in modo da accettare i dati relativi ai clienti potenziali prima di riceverli dalla vetrina. | list<br />transazione<br />versione di valutazione |  
| ***Informativa sulla privacy e condizioni per l'utilizzo*** | L'app deve fornire un collegamento alla pagina dell'informativa sulla privacy con un URL pubblico e le condizioni per l'utilizzo devono essere fornite in formato testo al momento della pubblicazione. | list<br />transazione<br />versione di valutazione |  
| ***Supporto*** | L'offerta deve fornire un collegamento alla pagina dell'assistenza clienti con un URL pubblico. Se l'offerta è una versione di valutazione, è necessario fornire il supporto senza alcun costo aggiuntivo durante il periodo di valutazione. | transazione<br />versione di valutazione |    

## <a name="non-transact-listings"></a>Inserzioni senza transazioni  
In questa sezione vengono descritte tutte le offerte che non usano il tipo di inserzione Transazione. 

### <a name="list"></a>Elenco  
Il tipo di inserzione Elenco include i seguenti tipi di offerta nelle vetrine in Marketplace.  

| Tipo di offerta | Vetrina | Dettagli |  
|:---        |:---        |:---     |  
| Servizi di consulenza | AppSource | [Requisiti: AppSource: Elenco: Servizi di consulenza](#requirements-appsource-list-consulting-services) |  
| Servizi di consulenza | Azure Marketplace | [Requisiti: Azure Marketplace: Elenco: Servizi di consulenza](#requirements-azure-marketplace-list-consulting-services) |  
| Contattami | AppSource | [](#) |  
| Contattami | Azure Marketplace | [Requisiti: AppSource: Elenco: Contattami](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Requisiti: AppSource: Elenco: Servizio di consulenza  

| Requisiti | Dettagli |  
|:--- |:--- |  
| Caratteristiche dell'offerta del servizio | Il servizio di consulenza deve soddisfare i criteri seguenti.<ul> <li>Offerta con un ambito, una durata e un prezzo prestabiliti (o gratuita).</li> <li>Orientato principalmente alla pre-vendita.</li> <li>Limitato a un singolo cliente.</li> <li>Effettuato presso la sede.</li> </ul> |  
| Requisiti dei partner per i servizi di consulenza | Vengono soddisfatti i criteri nell'area pertinente al servizio.<table><tr><th>Area soluzione</th><th>Criteri</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Avere la competenza Silver o Gold di Cloud Customer Relationship Management.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Enterprise edition</td><td>Avere la competenza Silver o Gold di Enterprise Resource Planning e un fatturato generato dalle operazioni cloud degli ultimi 12 mesi di $25.000 o più.</td></tr><tr><td>Dynamics 365 for Finance and Operations, Business edition</td><td>Aver operato come Provider di servizi Cloud (CSP) o come Digital POR (Partner of Record) per uno o più clienti.</td></tr><tr><td>Power BI</td><td>Soddisfare i criteri di Partner di soluzioni.</td></tr><tr><td>PowerApps</td><td>Avere pubblicato una presentazione di soluzioni partner.</td></tr></table><ul> <li>Per altre informazioni sulla gestione delle relazioni con i clienti, visitare la pagina Cloud Customer Relationship Management all'indirizzo [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Per altre informazioni sulla pianificazione delle risorse, visitare la pagina Enterprise Resource Planning all'indirizzo [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Per altre informazioni sui provider CSP, visitare la pagina Cloud Services Provider all'indirizzo [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Per altre informazioni su DPOR, visitare la pagina Digital POR (Partner of Record) e associazione del partner all'indirizzo [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Per altre informazioni sui criteri dei partner di soluzione, consultare la documentazione sulla panoramica e gli incentivi dei partner di soluzione disponibile all'indirizzo [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Per altre informazioni sulla presentazione dei partner, visitare la pagina sulla presentazione dei partner pagina all'indirizzo [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Requisiti: AppSource: Marketplace: Elenco: Servizi di consulenza  

| Requisiti | Dettagli |  
|:--- |:--- |  
| Caratteristiche dell'offerta del servizio | Il servizio di consulenza deve soddisfare i criteri seguenti.<ul> <li>Offerta con un ambito, una durata e un prezzo prestabiliti (o gratuita).</li> <li>Orientato principalmente alla pre-vendita.</li> <li>Limitato a un singolo cliente.</li> <li>Effettuato presso la sede.</li> </ul> |  
| Requisiti dei partner per i servizi di consulenza | È necessario avere la competenza Silver o Gold in una delle competenze seguenti nell'area pertinente al servizio. <table><tr><th>Area soluzione</th><th>Competenza</th></tr><td>Piattaforma e infrastruttura cloud</td><td>Piattaforma cloud<br />Data center</td><tr><td>Sviluppo di applicazioni e ISV</td><td>Sviluppo di applicazioni<br />Integrazione delle applicazioni<br />DevOps</td></tr><tr><td>Gestione e analisi dei dati</td><td>Analisi dei dati<br />Piattaforma dati</td></tr></table><ul> <li>Per altre informazioni sulle competenze, visitare la pagina Partner con competenze all'indirizzo [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Per altre informazioni sulle inserzioni, visitare la pagina Servizi di consulenza in Azure Marketplace all'indirizzo [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Versione di valutazione  

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
| Periodo di valutazione gratuita ed esperienza di valutazione | Il cliente può provare l'app gratuitamente per un periodo di tempo limitato.<br /><br />Il cliente non deve pagare costi di licenza o di sottoscrizione per l'offerta. I clienti non devono pagare il prodotto o il servizio proprietario di Microsoft sottostante. Tutte le opzioni di valutazione sono distribuite nella sottoscrizione di Azure. L'utente ha il controllo esclusivo dell'ottimizzazione dei costi e della gestione.<br /><br />È possibile scegliere una versione di valutazione gratuita, una demo interattiva o un test drive. Indipendentemente dall'opzione selezionata, per la prova gratuita è necessario concedere al cliente un periodo prestabilito di tempo per provare l'offerta senza costi aggiuntivi.<ul> <li>Per iniziare a creare un test drive, inviare un messaggio di posta elettronica a [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com).</li> </ul>Nota: le versioni di valutazione SaaS di Azure Marketplace SaaS devono consentire agli utenti di usare le credenziali di lavoro personali per accedere.<ul> <li>Per altre informazioni, visitare la sezione relativa alle esperienze di valutazione di AppSource all'indirizzo [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Soluzione pronta all'uso e facilmente configurabile | L'app deve essere semplice e rapida da configurare e installare. |  
| Disponibilità/Tempo di attività | L'app o la piattaforma SaaS deve avere un tempo di attività pari ad almeno il 99,9%. |  
| Azure Active Directory | L'offerta deve consentire l'accesso Single Sign-On (SSO) federato di Azure Active Directory (accesso SSO federato di Azure AD) con il consenso abilitato. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Requisiti: Azure Marketplace: Versione di valutazione: Versione di valutazione gratuita/Versione di valutazione di SaaS  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente al cliente di provare il prodotto prima di acquistarlo con un metodo automatico per la conversione all'uso a pagamento. oltre a offrire un modello di verifica per i clienti e opportunità di collaborazione con i team di vendita Microsoft. | La soluzione offerta è di tipo macchina virtuale o modello di soluzione<br /><br />oppure è una soluzione SaaS e si offre un prodotto SaaS multi-tenant,<br /><br />non si ha un'esperienza consolidata per garantire ai clienti l'operatività immediata<br /><br />ed è configurato un tenant singolo ma si aggiungono clienti come utenti guest. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Requisiti: Azure Marketplace: Versione di valutazione: Demo interattiva  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente ai clienti di osservare come funziona una soluzione senza doversi preoccupare dell'installazione. | La soluzione richiede un'installazione complessa che sarebbe difficile completare nel periodo di prova. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Requisiti: Azure Marketplace: Versione di valutazione: Test drive  

| Vantaggi | Requisito |  
|:--- |:--- |  
| Consente ai clienti di valutare il prodotto prima dell'acquisto.<br /><br />Offre un'esperienza guidata della soluzione usando impostazioni preconfigurate.<br /><br />Di seguito sono illustrati altri vantaggi quando si usa un test drive.<ul> <li>Il 27% delle ricerche eseguite dagli utenti nel marketplace è filtrato in modo da visualizzare solo offerte con test drive.</li> <li>Le offerte con test drive generano un numero di clienti potenziali superiore del 38% rispetto alle offerte che ne sono prive.</li> <li>Il 36% delle acquisizioni di nuovi clienti nel marketplace proviene dai clienti che hanno valutato una test drive.</li> <li>Le test drive consentono ai venditori Microsoft sul campo di comprendere meglio le funzionalità di un prodotto per offrire maggiori opportunità di co-selling.</li> </ul> | La soluzione offerta è di tipo macchina virtuale o modello di soluzione oppure è un'app SaaS con tenant singolo o è una soluzione di cui è complicato effettuare il provisioning. <br /><br />Non prevede un metodo per convertire la versione di valutazione in offerta a pagamento. |  

---

## <a name="transact-specific-listings"></a>Inserzioni specifiche per transazioni

### <a name="transact"></a>Transazione  

| Tipo di offerta | Vetrina | Dettagli |   
|:---        |:---        | :--- |  
| App di Azure: App gestita | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App di Azure: App gestita](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| App di Azure: Modello di soluzione | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App di Azure: Modello di soluzione](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Contenitori | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: Contenitore](#requirements-azure-marketplace-transact-container) |  
| App SaaS  | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: App SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Macchina virtuale | Azure Marketplace | [Requisiti: Azure Marketplace: Transazione: Macchina virtuale](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Requisiti: Azure Marketplace: Transazione: Contenitore  

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Supporta il modello di fatturazione BYOL o di fatturazione gratuita. |  
| Immagine basata su Docker | Le immagini dei contenitori devono essere basate sul formato di immagine di Docker e devono essere estratte da Registri contenitori di Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Requisiti: Azure Marketplace: Transazione: App SaaS  

| Requisito | Dettagli |  
|:--- |:--- |  
| Fatturazione e misurazione | Per l'offerta è prevista una tariffa fissa mensile. Non sono attualmente supportati prezzi basati sull'utilizzo e opzioni per il calcolo dell'*utilizzo effettivo*. |  
| Annullamento | L'offerta può essere annullata dal cliente in qualsiasi momento. |  
| Pagina di destinazione delle transazioni | Ospitare una pagina di destinazione delle transazioni di Azure con co-branding. La pagina di destinazione consente ai clienti di creare e gestire l'account di servizio SaaS. |  
| API della sottoscrizione SaaS | Fornire un servizio che interagisce con la sottoscrizione SaaS per creare, aggiornare ed eliminare un account utente e un piano di servizio. Tutte le modifiche critiche all'API devono essere supportate entro 24 ore. Le modifiche non critiche all'API verranno aggiornate periodicamente. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Requisiti: Azure Marketplace: Transazione: Macchina virtuale  

| Requisito | Dettagli |  
|:--- |:--- | 
| Fatturazione e misurazione | La macchina virtuale deve supportare la fatturazione mensile BYOL o con pagamento in base al consumo. |  
| Disco rigido virtuale (VHD) compatibile con Azure | Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, visitare la sezione Creare un VHD compatibile con Azure (basato su Linux) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, visitare la sezione Creare un VHD compatibile con Azure (basato su Windows) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Passaggi successivi
*   Visitare la pagina [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).  
 
---  
