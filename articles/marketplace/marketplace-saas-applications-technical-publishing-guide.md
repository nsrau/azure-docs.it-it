---
title: Guida tecnica alla pubblicazione di applicazioni SaaS in Azure Marketplace
description: Guida dettagliata ed elenchi di controllo per la pubblicazione di applicazioni SaaS in Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809472"
---
# <a name="saas-applications-technical-publishing-guide"></a>Guida tecnica alla pubblicazione di applicazioni SaaS

La Guida tecnica alla pubblicazione di applicazioni SaaS in Azure Marketplace è stata pensata per aiutare gli editori potenziali e quelli già operativi a presentare i propri servizi e applicazioni in Azure Marketplace sfruttando l'offerta esistente di applicazioni SaaS. 

Per illustrare più chiaramente come pubblicare un'offerta SaaS, questa guida è suddivisa nelle sezioni seguenti:
* Panoramica dell'offerta
* Requisiti aziendali
* Requisiti tecnici
* Processo di pubblicazione
* Uso di Azure Active Directory per abilitare l'accesso alle versioni di valutazione
* Certificazione dell'integrazione di Azure AD per Marketplace

## <a name="offer-overview"></a>Panoramica dell'offerta  

Le applicazioni SaaS sono disponibili in entrambe le vetrine di Azure. La tabella seguente illustra le opzioni attualmente disponibili:

| Vetrina | Elenco | Versione di valutazione/Transazione |  
| --- | --- | --- |  
| AppSource | Sì (Contatta) | Sì (Power BI/Dynamics) |
| Azure Marketplace | No  | Sì (app SaaS) |   

**Inserzione**: questa opzione di pubblicazione consiste in un'offerta di tipo Contatta e viene usata quando non è possibile una partecipazione a livello di valutazione o di transazione. Il vantaggio di questo approccio è dato dall'opportunità offerta agli editori di mettere subito in commercio una soluzione per iniziare a ricevere clienti potenziali che possono trasformarsi in ottimi affari per la crescita della loro azienda.  
**Versione di valutazione/Transazione**: il cliente ha la possibilità di acquistare direttamente o richiedere una versione di valutazione della soluzione. Un'esperienza di valutazione aumenta il livello di coinvolgimento offerto ai clienti e consente loro di esaminare la soluzione prima di acquistarla. Offrendo un'esperienza di valutazione, si hanno maggiori opportunità di promozione nelle vetrine e con molta probabilità si riesce a raggiungere un numero più ampio di clienti potenziali grazie a questa occasione di coinvolgimento. Le versioni di valutazione devono includere il supporto gratuito almeno per la durata del periodo di valutazione.  

| Offerta di app SaaS | Requisiti aziendali | Requisiti tecnici |  
| --- | --- | --- |  
| **Contatta** | Sì | No  |  
| **PowerBI/Dynamics** | Sì | Sì (integrazione di Azure AD) |  
| **App SaaS**| Sì | Sì (integrazione di Azure AD) |     

Per altre informazioni sulle vetrine di Marketplace e per una descrizione delle opzioni di pubblicazione,vedere [Guida alla pubblicazione per Azure Marketplace](https://aka.ms/sellerguide) e [Opzioni di pubblicazione](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Requisiti aziendali
I requisiti aziendali dell'offerta SaaS possono essere soddisfatti in parallelo con i requisiti tecnici. La maggior parte delle informazioni e dei requisiti aziendali viene raccolta durante la creazione dell'offerta SaaS nel portale Cloud Partner. Di seguito sono riportati i requisiti aziendali: 
* Accettare i criteri di partecipazione
* Implementare l'integrazione con Microsoft 
* Identificare i destinatari dell'offerta
* Definire e determinare la modalità di gestione dei clienti potenziali
* Configurare l'informativa sulla privacy e le condizioni per l'utilizzo
* Definire i contatti del supporto tecnico  

Altre informazioni sono disponibili nell'argomento [Prerequisiti per la pubblicazione nel Marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing).

## <a name="technical-requirements"></a>Requisiti tecnici

I requisiti tecnici per le applicazioni SaaS sono semplici. Gli editori devono semplicemente garantire l'integrazione con Azure Active Directory (Azure AD) per la pubblicazione. L'integrazione di Azure AD con le applicazioni è documentata in modo dettagliato e Microsoft mette a disposizione una vasta gamma di risorse e SDK a tale scopo.  

Per iniziare, è consigliabile disporre di una sottoscrizione dedicata per la pubblicazione in Azure Marketplace, in modo da isolare questo tipo di attività da altre iniziative. Al termine dell'operazione, è possibile iniziare a distribuire l'applicazione SaaS nella sottoscrizione corrente per avviare il lavoro di sviluppo.  

La documentazione, gli esempi e le linee guida migliori per Azure Active Directory sono disponibili nei siti seguenti: 

* [Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integrazione con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Roadmap per Azure - Sicurezza e identità](https://azure.microsoft.com/roadmap/?category=security-identity)

Per le esercitazioni video, vedere i collegamenti seguenti:

* [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticazione di Azure Active Directory con Vittorio Bertocci)

* [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 1 di 2)

* [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Briefing tecnico sulle identità di Azure Active Directory - Parte 2 di 2)

* [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Compilazione di app con Microsoft Azure Active Directory)

* [Video di Microsoft Azure incentrati su Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Per contenuti di formazione gratuiti per Azure Active Directory, vedere il collegamento seguente:  
* [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Azure Active Directory fornisce inoltre un sito in cui verificare la disponibilità di aggiornamenti del servizio.   
* [Aggiornamenti del servizio Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Per il supporto, è possibile usare le risorse seguenti:
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Processo di pubblicazione

Il processo di pubblicazione SaaS include passaggi a livello tecnico e aziendale.  La maggior parte delle operazioni per lo sviluppo e l'integrazione di Azure Active Directory può essere eseguita in parallelo con il lavoro necessario per soddisfare i requisiti aziendali dell'offerta. La maggior parte dei requisiti aziendali fa parte della configurazione dell'offerta di app SaaS nel portale Cloud Partner.  
Il diagramma seguente illustra i passaggi principali per la pubblicazione dell'offerta Versione di valutazione/Transazione:  

![Procedura per la pubblicazione SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

La tabella seguente descrive i passaggi di pubblicazione principali:  

| Passaggio di pubblicazione | DESCRIZIONE |   
| --- | --- |  
| **Creare l'applicazione SaaS** | Accedere al portale Cloud Partner, selezionare **New** (Nuovo) e quindi selezionare l'offerta relativa alle **app SaaS**. |  
| **Creare l'integrazione con Azure AD** | Seguire i requisiti tecnici descritti nella sezione precedente per integrare l'offerta SaaS con Azure AD. |  
| **Definire le impostazioni dell'offerta**| Immettere tutte le informazioni iniziali sull'offerta SaaS, ovvero l'ID e il nome dell'offerta che si vuole usare. |     
| **Impostare le informazioni tecniche** | Immettere le informazioni tecniche relative all'offerta. Per le applicazioni SaaS, è necessario specificare l'URI della soluzione e il tipo di pulsante di acquisizione dell'offerta (Gratuita, Versione di valutazione o Contatta). |  
| **Test drive (facoltativo)** | Si tratta di un tipo facoltativo di valutazione gratuita, necessario soprattutto per altri tipi di offerte di Marketplace. Consente di ottenere la versione di valutazione distribuita nelle sottoscrizioni dell'editore anziché in una sottoscrizione di cliente finale. |  
| **Definire i materiali per la vetrina delle offerte**| In questa sezione l'editore collega e carica i logo, i materiali di marketing e i documenti legali e configura il sistema di gestione dei clienti potenziali. |
| **Impostare i contatti dell'offerta** | Immettere sia i contatti di progettazione sia le informazioni di contatto del supporto per l'offerta SaaS. |  
| **Verificare l'integrazione dell'app SaaS con Azure AD** | Prima di inviare l'app SaaS per la pubblicazione, è necessario verificare che l'app sia integrata con Azure AD. |  
| **Pubblicare l'offerta**| Dopo aver completato l'offerta e le risorse tecniche, è possibile procedere all'invio dell'offerta. Verrà così avviato il processo di pubblicazione, in cui il modello della soluzione viene testato, convalidato, certificato e approvato per la pubblicazione. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Uso di Azure Active Directory per abilitare l'accesso alle versioni di valutazione  

Microsoft usa Azure AD per autenticare tutti gli utenti di Marketplace. Pertanto, quando un utente autenticato fa clic su un'inserzione relativa a una versione di valutazione in Marketplace e viene reindirizzato all'ambiente di valutazione, l'editore può consentire all'utente di accedere direttamente a tale ambiente senza richiedere un altro passaggio di accesso. Il token che l'app riceve da Azure AD in fase di autenticazione include informazioni rilevanti sull'utente che l'editore può usare per creare un account utente nell'app, in modo da automatizzare l'esperienza di provisioning e aumentare così le probabilità che la versione di valutazione venga convertita in offerta a pagamento. Per altre informazioni sul token, vedere [Token di esempio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

L'uso di Azure AD per abilitare l'autenticazione con un solo clic all'app o alla versione di valutazione offre i vantaggi seguenti:  
* Consente di ottimizzare l'esperienza di accesso dei clienti da Marketplace alla versione di valutazione.  
* Mantiene l'aspetto di un'esperienza integrata nel prodotto, anche quando l'utente viene reindirizzato da Marketplace al dominio o all'ambiente di valutazione dell'editore.  
* Riduce la probabilità di abbandono al momento del reindirizzamento poiché non è necessario eseguire un altro passaggio per l'accesso.  
* Facilita la distribuzione per l'ampia popolazione di utenti di Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificazione dell'integrazione di Azure AD per Marketplace  

È possibile certificare l'integrazione di Azure AD in diversi modi, a seconda che l'applicazione sia single-tenant o multi-tenant e che sia già previsto o meno il supporto per l'accesso Single Sign-On (SSO) federato di Azure AD.  

**Per le applicazioni multi-tenant:**  

Se Azure AD è già supportato, eseguire le operazioni seguenti:
1.  Registrare l'applicazione nel portale di Azure
2.  Abilitare la funzionalità per il supporto multi-tenancy in Azure AD per ottenere un'esperienza di valutazione con un solo clic. Altre informazioni specifiche sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se non si ha familiarità con l'accesso SSO federato di Azure AD, eseguire queste operazioni: 
1.  Registrare l'applicazione nel portale di Azure
2.  Sviluppare SSO con Azure AD usando [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) o [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Abilitare la funzionalità per il supporto multi-tenancy in AAD per ottenere un'esperienza di valutazione con un solo clic. Altre informazioni specifiche sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Per un'applicazione single-tenant, usare una delle opzioni seguenti:**  
* Aggiungere utenti alla directory come utenti guest tramite [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Effettuare manualmente il provisioning delle versioni di valutazione per i clienti tramite l'opzione Contatta
* Sviluppare una test drive per ogni cliente
* Creare un'app demo di esempio multi-tenant con SSO

