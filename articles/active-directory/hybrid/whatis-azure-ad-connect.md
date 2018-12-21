---
title: Informazioni su Azure AD Connect e Connect Health | Microsoft Docs
description: Vengono descritti gli strumenti usati per sincronizzare e monitorare l'ambiente locale con Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fbd6edb02dfc4080d7692e43324a5b3981091f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110328"
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
* Offre le funzionalità più recenti per gli scenari in uso. Azure AD Connect sostituisce le versioni precedenti degli strumenti di integrazione delle identità, ad esempio DirSync e Azure AD Sync. Per altre informazioni, vedere [Confronto degli strumenti di integrazione directory per la soluzione ibrida di gestione delle identità](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Perché usare Azure AD Connect Health
Con Azure AD gli utenti sono più produttivi perché è disponibile un'identità comune per accedere alle risorse cloud e locali. Garantire l'affidabilità dell'ambiente, in modo tale che gli utenti possano accedere a queste risorse, costituisce una vera e propria sfida.  Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale, garantendo l'affidabilità di questo ambiente. È semplice come installare un agente in ogni server di identità locale in uso.


## <a name="next-steps"></a>Passaggi successivi

- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
