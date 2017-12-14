---
title: "Monitorare l’infrastruttura di identità locale nel cloud."
description: Si tratta della pagina di Azure AD Connect Health in cui viene descritto Azure AD Connect Health e il relativo utilizzo.
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 9919e326613ce81db32f1d72a8cb5e3668992885
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud
Azure Active Directory (Azure AD) Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione. Consente di mantenere una connessione affidabile a Office 365 e Microsoft Online Services offrendo capacità di monitoraggio per i componenti chiave relativi all'identità, ad esempio i server Active Directory Federation Services (AD FS), i server Azure AD Connect (noto anche come motore di sincronizzazione), i controller di dominio di Active Directory e così via. Rende anche facilmente accessibili i punti dati chiave relativi a questi componenti, per poter recuperare dati sull'utilizzo e altre informazioni importanti e utili per prendere decisioni informate.

Queste informazioni vengono visualizzate nel [portale di Azure AD Connect Health](https://aka.ms/aadconnecthealth). Nel portale di Azure AD Connect Health è possibile visualizzare avvisi, informazioni sul monitoraggio delle prestazioni, analisi sull'utilizzo e altre informazioni. Azure AD Connect Health abilita la singola sezione dell'integrità per i componenti chiave per l'identità, in un'unica posizione.

![Cos’è Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Per quanto aumentino le funzionalità in Azure AD Connect Health, il portale offre un unico dashboard nella sezione dell'identità. L'ambiente risultante è ancora più affidabile, integro e integrato e offre agli utenti maggiori possibilità di eseguire le operazioni necessarie.
## <a name="why-use-azure-ad-connect-health"></a>Perché usare Azure AD Connect Health
Quando si integrano le directory locali con Azure AD, gli utenti sono più produttivi perché è disponibile un'identità comune per accedere alle risorse cloud e locali. Tuttavia, questa integrazione comporta la difficoltà di garantire che questo ambiente sia integro, per consentire agli utenti di accedere in modo affidabile alle risorse locali e cloud da qualsiasi dispositivo. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di identità locale usata per accedere a Office 365 o ad altre applicazioni Azure AD. È semplice come installare un agente in ogni server di identità locale in uso.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health per AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health per AD FS supporta AD FS 2.0 su Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Consente anche il monitoraggio dei server proxy AD FS o di applicazione Web che offrono il supporto per l'autenticazione per l'accesso alla rete Extranet. Con un'installazione semplice e a basso costo dell'agente per l'integrità, Azure AD Connect Health per AD FS offre le funzionalità chiave seguenti:

* Monitoraggio tramite avvisi per segnalare quando i server AD FS e proxy AD FS non sono integri.
* Notifiche tramite posta elettronica per gli avvisi critici
* Tendenze nei dati delle prestazioni, utili per la pianificazione delle capacità di AD FS.
* Analisi di utilizzo per gli accessi di AD FS con pivot, come app, utenti, percorso di rete e così via, che permettono di comprendere come viene usato AD FS.
* Report per AD FS, relativi ad esempio ai primi 50 utenti che hanno tentativi di nome utente/password non riusciti e l'ultimo indirizzo IP.
  
Per altre informazioni, vedere [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)

Il video seguente offre una panoramica su Azure AD Connect Health per AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>

>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health per la sincronizzazione monitora e fornisce informazioni sulle sincronizzazioni che si verificano tra Active Directory locale e Azure AD. Azure AD Connect Health per la sincronizzazione fornisce il set di funzionalità chiave seguente:

* Monitoraggio degli avvisi per sapere quando un server di Azure AD Connect, noto anche come motore di sincronizzazione, non è integro.
* Notifiche tramite posta elettronica per gli avvisi critici
* Informazioni operative approfondite sulla sincronizzazione, che includono grafici della latenza per le operazioni di sincronizzazione e tendenze relative a varie operazioni, come aggiunta, aggiornamento o eliminazione
* Riepilogo rapido delle informazioni sulle proprietà di sincronizzazione e sull'ultima esportazione riuscita in Azure AD
* Report sugli errori di sincronizzazione a livello di oggetto \(non è necessario Azure AD Premium\).

Per altre informazioni, vedere [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)

Il video seguente offre una panoramica su Azure AD Connect Health per la sincronizzazione.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health per Active Directory Domain Services](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health per Active Directory Domain Services consente il monitoraggio dei controller di dominio installati in Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Con un'installazione dell'agente per l'integrità è possibile monitorare l'ambiente locale di Active Directory Domain Services dal cloud. Azure AD Connect Health per Servizi di dominio Active Directory offre il seguente insieme di funzionalità chiave:

* Avvisi di monitoraggio per rilevare quando i controller di dominio non sono integri e notifiche di posta elettronica per avvisi critici
* Dashboard dei controller di dominio, che permette di visualizzare rapidamente l'integrità e lo stato operativo dei controller di dominio
* Dashboard Stato replica con le informazioni di replica più recenti e collegamenti a guide per la risoluzione dei problemi quando vengono rilevati errori
* Accesso rapido ovunque a grafici di dati dei contatori delle prestazioni più diffusi, per obiettivi di risoluzione dei problemi e monitoraggio

Per altre informazioni, vedere [Uso di Azure AD Connect Health con Active Directory Domain Services](active-directory-aadconnect-health-adds.md)

Il video seguente offre una panoramica su Azure AD Connect Health per Active Directory Domain Services.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Introduzione ad Azure AD Connect Health
Per iniziare a usare Azure AD Connect Health sono molto semplici, seguire questa procedura:

1. [Ottenere Azure AD Premium](../active-directory-get-started-premium.md) o [avviare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Scaricare e installare gli agenti di Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) nei server di identità.
3. Visualizzare il dashboard di Azure AD Connect Health all'indirizzo [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Tenere presente che, prima di visualizzare i dati nel dashboard di Azure AD Connect Health, è necessario installare gli agenti di Azure AD Connect Health nei server di destinazione.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Scaricare e installare l'agente di Azure AD Connect Health
* Verificare che siano [soddisfatti i requisiti](active-directory-aadconnect-health-agent-install.md#requirements) per Azure AD Connect Health.
* Introduzione ad Azure AD Connect Health per AD FS
    * [Scaricare l'agente di Azure AD Connect Health per AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Vedere le istruzioni per l'installazione](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introduzione ad Azure AD Connect Health per la sincronizzazione
    * [Scaricare e installare la versione più recente di Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). L'agente di integrità per la sincronizzazione verrà installato come parte dell'installazione di Azure AD Connect (versione 1.0.9125.0 o versione successiva).
* Introduzione ad Azure AD Connect Health per Active Directory Domain Services
    * [Scaricare l'agente di Azure AD Connect Health per Active Directory Domain Services](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Vedere le istruzioni per l'installazione](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portale di Azure AD Connect Health
Il portale di Azure AD Connect Health visualizza gli avvisi, il monitoraggio delle prestazioni e l'analisi di utilizzo. L'URL https://aka.ms/aadconnecthealth permette di raggiungere il pannello principale di Azure AD Connect Health. Un pannello è paragonabile a una finestra. Nel pannello principale sono disponibili opzioni per l'**Avvio rapido**, per i servizi di Azure AD Connect Health e altre opzioni di configurazione. Vedere lo screenshot seguente e le brevi spiegazioni a seguire. Dopo la distribuzione degli agenti, il servizio integrità identifica automaticamente i servizi monitorati da Azure AD Connect Health.

> [!NOTE]
> Per informazioni sulle licenze, vedere [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md) o la [pagina Prezzi di Azure AD](https://aka.ms/aadpricing).
    
![portale di Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Avvio rapido**: quando si seleziona questa opzione, si apre il pannello **Avvio rapido**. È possibile scaricare l'agente di Azure AD Connect Health selezionando **Scaricare gli strumenti**. È anche possibile accedere alla documentazione e fornire commenti e suggerimenti.
* **Active Directory Federation Services**: questa opzione visualizza tutti i servizi di AD FS attualmente monitorati da Azure AD Connect Health. Quando si seleziona un'istanza, il pannello che si apre visualizza le informazioni su tale istanza del servizio. Queste informazioni includono una panoramica, le proprietà, gli avvisi, informazioni di monitoraggio e analisi di utilizzo. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (Sincronizzazione)**: questa opzione visualizza i server di Azure AD Connect attualmente monitorati da Azure AD Connect Health. Quando si seleziona la voce, il pannello che si apre visualizza le informazioni sui server Azure AD Connect. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services**: questa opzione visualizza tutte le foreste di Active Directory Domain Services attualmente monitorate da Azure AD Connect Health. Quando si seleziona una foresta, il pannello che si apre visualizza le informazioni su tale foresta. Queste informazioni includono una panoramica dei dati essenziali, il dashboard Controller di dominio, il dashboard Stato replica, avvisi e monitoraggio. Per altre informazioni sulle funzionalità, vedere [Uso di Azure AD Connect Health con Active Directory Domain Services](active-directory-aadconnect-health-adds.md).
* **Configura**: questa sezione include le opzioni per attivare o disattivare quanto segue:

  - Aggiornamento automatico dell'agente di Azure AD Connect Health alla versione più recente: l'agente di Azure AD Connect Health viene aggiornato automaticamente ogni volta che sono disponibili una nuove versioni. Questa opzione è abilitata per impostazione predefinita.
  - Accesso ai dati di integrità della directory di Azure AD da parte di Microsoft solo ai fini della risoluzione dei problemi: se questa opzione è abilitata, Microsoft può accedere agli stessi dati visualizzati dall'utente. Queste informazioni possono essere utili per la risoluzione dei problemi e per fornire l'assistenza necessaria. Questa opzione è disabilitata per impostazione predefinita.

## <a name="related-links"></a>Collegamenti correlati
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
