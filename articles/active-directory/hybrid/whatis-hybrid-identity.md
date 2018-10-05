---
title: Connettere Active Directory ad Azure Active Directory. | Microsoft Docs
description: Azure AD Connect integra le directory locali con Azure Active Directory. Consente di fornire un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD.
keywords: introduzione ad Azure AD Connect, panoramica di Azure AD Connect, che cos'è Azure AD Connect, installare active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181779"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Soluzioni ibride di gestione delle identità e soluzioni di gestione delle identità di Microsoft
Attualmente le aziende e le società stanno diventando sempre più una combinazione di applicazioni locali e cloud.  Avere applicazioni e utenti che richiedono l'accesso a queste applicazioni, in locale e nel cloud, è diventato uno scenario complesso.

Le soluzioni di gestione delle identità di Microsoft abbracciano funzionalità locali e basate sul cloud, creando una singola identità utente per l’autenticazione e l’autorizzazione per tutte le risorse, indipendentemente dalla loro ubicazione. Tutto questo viene chiamato Identità ibrida.

## <a name="what-is-azure-ad-connect"></a>Cos'è Azure AD Connect?

Azure AD Connect è lo strumento di Microsoft progettato per soddisfare e raggiungere gli obiettivi relativi alla soluzione ibrida di gestione delle identità.  Consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD.  Offre le funzionalità seguenti:
    
- [Sincronizzazione](how-to-connect-sync-whatis.md): questo componente è responsabile della creazione di utenti, gruppi e altri oggetti. Deve anche garantire che le informazioni sulle identità per utenti e gruppi locali corrispondano a quelle nel cloud.  È responsabile della sincronizzazione degli hash delle password con Azure AD.
-   [AD FS e integrazione della federazione](how-to-connect-fed-whatis.md): la federazione è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido usando un'infrastruttura AD FS locale. Fornisce anche funzionalità di gestione di AD FS, ad esempio rinnovo dei certificati e distribuzioni aggiuntive di server AD FS.
-   [Autenticazione pass-through](how-to-connect-pta.md): un altro componente facoltativo che consente agli utenti di usare la stessa password in locale e nel cloud, ma non richiede l'infrastruttura aggiuntiva di un ambiente federato
-   [Monitoraggio dell'integrità](whatis-hybrid-identity-health.md): Azure AD Connect Health può offrire un monitoraggio affidabile e una posizione centralizzata nel portale di Azure per visualizzare questa attività. 


![Cos'è Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Cos'è Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione. Consente di mantenere una connessione affidabile a Office 365 e Microsoft Online Services offrendo capacità di monitoraggio per i componenti chiave relativi all'identità, ad esempio i server Active Directory Federation Services (AD FS), i server Azure AD Connect (noto anche come motore di sincronizzazione), i controller di dominio di Active Directory e così via. Rende anche facilmente accessibili i punti dati chiave relativi a questi componenti, per poter recuperare dati sull'utilizzo e altre informazioni importanti e utili per prendere decisioni informate.

Queste informazioni vengono visualizzate nel [portale di Azure AD Connect Health](https://aka.ms/aadconnecthealth). Nel portale di Azure AD Connect Health è possibile visualizzare avvisi, informazioni sul monitoraggio delle prestazioni, analisi sull'utilizzo e altre informazioni. Azure AD Connect Health abilita la singola sezione dell'integrità per i componenti chiave per l'identità, in un'unica posizione.

![Cos’è Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Per quanto aumentino le funzionalità in Azure AD Connect Health, il portale offre un unico dashboard nella sezione dell'identità. L'ambiente risultante è ancora più affidabile, integro e integrato e offre agli utenti maggiori possibilità di eseguire le operazioni necessarie.


## <a name="why-use-azure-ad-connect"></a>Perché usare Azure AD Connect?
L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Utenti e organizzazioni possono sfruttare i vantaggi seguenti:

* Gli utenti possono usare un'unica identità per accedere alle applicazioni locali e ai servizi cloud, come ad esempio Office 365.
* Un unico strumento che offre un'esperienza di distribuzione semplificata per la sincronizzazione e l'accesso.
* Offre le funzionalità più recenti per gli scenari in uso. Azure AD Connect sostituisce le versioni precedenti degli strumenti di integrazione delle identità, ad esempio DirSync e Azure AD Sync. Per altre informazioni, vedere [Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Perché usare Azure AD Connect Health
Quando si integrano le directory locali con Azure AD, gli utenti sono più produttivi perché è disponibile un'identità comune per accedere alle risorse cloud e locali. Tuttavia, questa integrazione comporta la difficoltà di garantire che questo ambiente sia integro, per consentire agli utenti di accedere in modo affidabile alle risorse locali e cloud da qualsiasi dispositivo. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di identità locale usata per accedere a Office 365 o ad altre applicazioni Azure AD. È semplice come installare un agente in ogni server di identità locale in uso.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health per AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health per AD FS supporta AD FS 2.0 su Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Consente anche il monitoraggio dei server proxy AD FS o di applicazione Web che offrono il supporto per l'autenticazione per l'accesso alla rete Extranet. Con un'installazione semplice e rapida dell'agente per l'integrità, Azure AD Connect Health per AD FS offre un set di funzionalità chiave.

#### <a name="key-benefits-and-best-practices"></a>Vantaggi chiave e procedure consigliate

- *Sicurezza avanzata*
  - [Tendenze del blocco Extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Report sugli accessi non riusciti](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Conformità alla privacy](reference-connect-health-user-privacy.md)    
- *Avvisi su tutti i [problemi del sistema ADFS critici](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Configurazione e disponibilità del server 
    - [Prestazioni e connettività](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Manutenzione regolare    
- *Semplicità di distribuzione e gestione*
  - [Installazione dell'agente](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) rapida 
  - Aggiornamento automatico alla versione più recente dell'agente 
  - Dati disponibili nel portale entro pochi minuti    
- *[Metriche di utilizzo](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs) avanzate* 
  - Utilizzo delle applicazioni principali
  - Percorsi di rete e connessione TCP
  - Richieste di token per ogni server    
- *Miglioramento dell'esperienza utente* 
  - Modalità dashboard dal portale di Azure
  - [Avvisi tramite messaggi di posta elettronica](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Funzionalità principali

*   Monitoraggio tramite avvisi per segnalare quando i server AD FS e proxy AD FS non sono integri.
*   Notifiche tramite posta elettronica per gli avvisi critici
*   Tendenze nei dati delle prestazioni, utili per la pianificazione delle capacità di AD FS.
*   Analisi di utilizzo per gli accessi ad AD FS con pivot (app, utenti, percorso di rete e così via)
*   Report per AD FS, relativi ad esempio ai primi 50 utenti che hanno tentativi di nome utente/password non riusciti e l'ultimo indirizzo IP.
*   Report relativi agli indirizzi IP rischiosi per accessi ad AD FS non riusciti

Per altre informazioni, vedere [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
Azure AD Connect Health per la sincronizzazione monitora e fornisce informazioni sulle sincronizzazioni che si verificano tra Active Directory locale e Azure AD. Azure AD Connect Health per la sincronizzazione fornisce il set di funzionalità chiave seguente:

* Monitoraggio degli avvisi per sapere quando un server di Azure AD Connect, noto anche come motore di sincronizzazione, non è integro.
* Notifiche tramite posta elettronica per gli avvisi critici
* Informazioni operative approfondite sulla sincronizzazione, che includono grafici della latenza per le operazioni di sincronizzazione e tendenze relative a varie operazioni, come aggiunta, aggiornamento o eliminazione
* Riepilogo rapido delle informazioni sulle proprietà di sincronizzazione e sull'ultima esportazione riuscita in Azure AD
* Report sugli errori di sincronizzazione a livello di oggetto \(non è necessario Azure AD Premium\).

Per altre informazioni, vedere [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health per Active Directory Domain Services](how-to-connect-health-adds.md)
Azure AD Connect Health per Active Directory Domain Services consente il monitoraggio dei controller di dominio installati in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Con un'installazione dell'agente per l'integrità è possibile monitorare l'ambiente locale di Active Directory Domain Services dal cloud. Azure AD Connect Health per Servizi di dominio Active Directory offre il seguente insieme di funzionalità chiave:

* Avvisi di monitoraggio per rilevare quando i controller di dominio non sono integri e notifiche di posta elettronica per avvisi critici
* Dashboard dei controller di dominio, che permette di visualizzare rapidamente l'integrità e lo stato operativo dei controller di dominio
* Dashboard Stato replica con le informazioni di replica più recenti e collegamenti a guide per la risoluzione dei problemi quando vengono rilevati errori
* Accesso rapido ovunque a grafici di dati dei contatori delle prestazioni più diffusi, per obiettivi di risoluzione dei problemi e monitoraggio

Per altre informazioni, vedere [Uso di Azure AD Connect Health con Active Directory Domain Services](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Passaggi successivi


- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md)|
- [Autenticazione pass-through](how-to-connect-pta.md)
- [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Servizio di sincronizzazione Azure AD Connect](how-to-connect-sync-whatis.md)
- [Cronologia delle versioni](reference-connect-version-history.md)
- [Confronto degli strumenti di integrazione directory](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Domande frequenti su Azure AD Connect](reference-connect-faq.md)









