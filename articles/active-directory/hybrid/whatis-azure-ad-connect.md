---
title: Informazioni su Azure AD Connect e Connect Health | Microsoft Docs
description: Vengono descritti gli strumenti usati per sincronizzare e monitorare l'ambiente locale con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c984faf22b8e4cd499a5588f45d7072610f33544
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767926"
---
# <a name="what-is-azure-ad-connect"></a>Cos'è Azure AD Connect?

Azure AD Connect è lo strumento di Microsoft progettato per soddisfare e raggiungere gli obiettivi relativi alla soluzione ibrida di gestione delle identità.  Offre le funzionalità seguenti:
    
- [Sincronizzazione dell'hash delle password](whatis-phs.md): metodo di accesso che consente di sincronizzare un hash di una password utente AD locale con Azure AD.
- [Autenticazione pass-through](how-to-connect-pta.md): metodo di accesso che consente agli utenti di usare la stessa password in locale e nel cloud, ma non richiede l'infrastruttura aggiuntiva di un ambiente federato.
- [Integrazione della federazione](how-to-connect-fed-whatis.md): la federazione è una parte facoltativa di Azure AD Connect e può essere usata per configurare un ambiente ibrido usando un'infrastruttura AD FS locale. Fornisce anche funzionalità di gestione di AD FS, ad esempio rinnovo dei certificati e distribuzioni aggiuntive di server AD FS.
- [Sincronizzazione](how-to-connect-sync-whatis.md): è responsabile della creazione di utenti, gruppi e altri oggetti.  Deve anche garantire che le informazioni sulle identità per utenti e gruppi locali corrispondano a quelle nel cloud.  Questa sincronizzazione include anche gli hash delle password.
-   [Monitoraggio dell'integrità](whatis-hybrid-identity-health.md): Azure AD Connect Health può offrire un monitoraggio affidabile e una posizione centralizzata nel portale di Azure per visualizzare questa attività. 


![Cos'è Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Cos'è Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health offre un monitoraggio affidabile dell'infrastruttura di identità locale. Consente infatti di mantenere una connessione affidabile a Office 365 e Microsoft Online Services.  Per garantire questa affidabilità, vengono usate funzionalità di monitoraggio per i componenti chiave delle identità, rendendo inoltre facilmente accessibili i punti dati chiave relativi a questi componenti.

Queste informazioni vengono visualizzate nel [portale di Azure AD Connect Health](https://aka.ms/aadconnecthealth). Usare il portale di Azure AD Connect Health per visualizzare avvisi, informazioni sul monitoraggio delle prestazioni, analisi sull'utilizzo e altre informazioni. Azure AD Connect Health abilita la singola sezione dell'integrità per i componenti chiave per l'identità, in un'unica posizione.

![Cos’è Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Perché usare Azure AD Connect?
L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Utenti e organizzazioni possono sfruttare i vantaggi seguenti:

* Gli utenti possono usare un'unica identità per accedere alle applicazioni locali e ai servizi cloud, come ad esempio Office 365.
* Un unico strumento che offre un'esperienza di distribuzione semplificata per la sincronizzazione e l'accesso.
* Offre le funzionalità più recenti per gli scenari in uso. Azure AD Connect sostituisce le versioni precedenti degli strumenti di integrazione delle identità, ad esempio DirSync e Azure AD Sync. Per altre informazioni, vedere [confronto degli strumenti di integrazione delle directory Identity ibrido](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Perché usare Azure AD Connect Health
Con Azure AD gli utenti sono più produttivi perché è disponibile un'identità comune per accedere alle risorse cloud e locali. Garantire l'affidabilità dell'ambiente, in modo tale che gli utenti possano accedere a queste risorse, costituisce una vera e propria sfida.  Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale, garantendo l'affidabilità di questo ambiente. È semplice come installare un agente in ogni server di identità locale in uso.

Azure AD Connect Health per AD FS supporta AD FS 2.0 su Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Consente anche il monitoraggio dei server proxy AD FS o di applicazione Web che offrono il supporto per l'autenticazione per l'accesso alla rete Extranet. Con un'installazione semplice e rapida dell'agente per l'integrità, Azure AD Connect Health per AD FS offre un set di funzionalità chiave.

Vantaggi chiave e procedure consigliate:

|Vantaggi principali|Procedure consigliate|
|-----|-----|
|Sicurezza aumentata|[Tendenze del blocco Extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Report sugli accessi non riusciti](how-to-connect-health-adfs-risky-ip.md)</br>[Conformità alla privacy](reference-connect-health-user-privacy.md)|
|Avvisi su [tutti i problemi critici del file system distribuito di Azure](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Configurazione e disponibilità del server</br>[Prestazioni e connettività](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Manutenzione regolare|
|Facile da distribuire e gestire|[Installazione dell'agente rapida](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Aggiornamento automatico alla versione più recente dell'agente</br>Dati disponibili nel portale entro pochi minuti|
[Metriche di utilizzo](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs) avanzate|Utilizzo delle applicazioni principali</br>Percorsi di rete e connessione TCP</br>Richieste di token per ogni server|
|Miglioramento dell'esperienza utente|Modalità dashboard dal portale di Azure</br>[Avvisi tramite messaggi di posta elettronica](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisiti di licenza per l'uso di Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Requisiti di licenza per l'utilizzo di Azure AD Connect Health
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
