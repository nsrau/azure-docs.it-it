---
title: Offerte SaaS Azure Active Directory e transazionali nel Marketplace commerciale
description: Informazioni sul funzionamento di Azure Active Directory con offerte SaaS transazionali nel Marketplace commerciale Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 0d8e2ee684bc08ec23e052229d50b7e9d62c0ecb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328325"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Offerte SaaS Azure AD e transazionali nel Marketplace commerciale

Il servizio di gestione delle identità e degli accessi basato sul cloud Microsoft, [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure ad) consente agli utenti di accedere alle risorse interne ed esterne. Nel Marketplace commerciale Microsoft, Azure AD rende le soluzioni SaaS transazionali più semplici e sicure per tutti, inclusi autori, acquirenti e utenti. Con Azure AD, gli editori possono automatizzare il provisioning degli utenti nelle app SaaS (Software as a Service) e gli acquirenti stessi possono gestire questi utenti di cui è stato effettuato il provisioning. 

Inoltre, [Azure AD Single Sign-on](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO) fornisce sicurezza e praticità quando gli utenti possono accedere alle app in Azure ad. Il coinvolgimento e le esperienze ottimizzate più rapide ispirano anche gli acquirenti e la confidenza dell'utente dalla prima interazione con l'app SaaS dell'editore. Ciò offre un'impressione positiva che consente di compilare la visibilità e incoraggiare la ripetizione delle attività aziendali.

Seguendo le istruzioni riportate in questo articolo, si contribuirà a certificare l'offerta SaaS nel Marketplace commerciale. Per altri dettagli sulla certificazione, leggi i criteri dettagliati per la [certificazione del Marketplace commerciale](https://aka.ms/commercial-marketplace-certification-policies#100-general), inclusi quelli [specifici per Saas](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Prima di iniziare

Quando si [crea l'offerta SaaS](./partner-center-portal/create-new-saas-offer.md) nel centro per i partner, è possibile scegliere tra una serie di chiamate specifiche alle opzioni di azione che verranno visualizzate nell'elenco delle offerte. La scelta determina il modo in cui l'offerta viene sottoposta a transazione nel Marketplace commerciale. Le offerte vendute tramite Microsoft sono denominate offerte transazionali. Il cliente viene fatturato per conto di tutte le offerte transazionali. Se si sceglie di vendere tramite Microsoft e ci sono transazioni per conto dell'utente (opzione **Sì** ), si è scelto di creare un'offerta transazionale e questo articolo è adatto all'utente. È consigliabile leggerlo completamente.

Se si sceglie di elencare l'offerta solo se il Marketplace commerciale ed elaborare le transazioni in modo indipendente (l'opzione **No** ), sono disponibili tre opzioni per il modo in cui i clienti potenziali potranno accedere all'offerta: ottenere ora (gratuita), versione di valutazione gratuita e contatti. Se si seleziona **Get it Now (gratuito)** o **versione di valutazione gratuita**, questo articolo non è adatto. Per altre informazioni, vedere [creare la pagina di destinazione per l'offerta SaaS gratuita o di valutazione nel Marketplace commerciale](./azure-ad-free-or-trial-landing-page.md) . Se si seleziona **contattami**, non sono presenti responsabilità dirette del server di pubblicazione. Continuare a creare l'offerta nel centro per i partner.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Funzionamento del Azure AD con il Marketplace commerciale per le offerte SaaS

Azure AD consente l'acquisto, l'evasione e la gestione di soluzioni di Marketplace commerciali senza problemi. Nella figura 1 viene illustrato il modo in cui Publisher, buyer e utente interagiscono per acquistare e attivare una sottoscrizione. Viene inoltre illustrato in che modo i clienti utilizzano e gestiscono le applicazioni SaaS che ottengono dal Marketplace commerciale. Ai fini di questa illustrazione, l'acquirente è l'utente dell'applicazione SaaS che avvia un acquisto dal Marketplace commerciale.

Come illustrato nella figura 1, quando un acquirente seleziona l'offerta, avvia una catena di flussi di lavoro che include l'acquisto, la sottoscrizione e la gestione degli utenti. All'interno di questa catena, l'autore è responsabile di determinati requisiti, con la fornitura di supporto da parte di Microsoft a punti chiave.

***Figura 1: uso di Azure AD per le offerte SaaS nel Marketplace commerciale***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Vengono illustrati i passaggi per la gestione degli acquisti, le sottoscrizioni e il processo di gestione degli utenti facoltativi.":::

Le sezioni seguenti forniscono informazioni dettagliate sui requisiti per ogni passaggio del processo.

## <a name="process-steps-for-purchase-management"></a>Passaggi del processo per la gestione degli acquisti

Questa figura illustra i quattro passaggi del processo per la gestione degli acquisti.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Vengono illustrati i quattro passaggi del processo per la gestione degli acquisti.":::

Questa tabella fornisce informazioni dettagliate per i passaggi del processo di gestione degli acquisti.

| Passaggio del processo | Azione Editore | Consigliato o obbligatorio per gli editori |
| ------------ | ------------- | ------------- |
| 1. il compratore accede al Marketplace commerciale con l'identità ID di Azure e seleziona un'offerta SaaS. | Non è richiesta alcuna azione del server di pubblicazione. | Non applicabile |
| 2. dopo l'acquisto, l'acquirente seleziona **Configura account** in Azure Marketplace o **Configura ora** in AppSource, che indirizza l'acquirente alla pagina di destinazione dell'editore per questa offerta. L'acquirente deve essere in grado di accedere all'applicazione SaaS dell'editore con Azure AD SSO e deve essere richiesto solo il consenso minimo che non richiede l'approvazione Azure AD amministratore. | Progettare una [pagina di destinazione](azure-ad-transactable-saas-landing-page.md) per l'offerta in modo che riceva un utente con l'identità del Azure ad o della account Microsoft (MSA) e faciliti il provisioning o la configurazione aggiuntiva necessaria. | Richiesto |
| 3. il server di pubblicazione richiede i dettagli di acquisto dall'API di evasione SaaS. | Utilizzando un [token di accesso](./partner-center-portal/pc-saas-registration.md) generato dall'ID applicazione della pagina di destinazione, [chiamare l'endpoint di risoluzione](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) per recuperare le specifiche relative all'acquisto. | Richiesto |
| 4. tramite Azure AD e l'API Microsoft Graph, l'editore raccoglie i dettagli aziendali e dell'utente necessari per effettuare il provisioning dell'acquirente nell'applicazione SaaS dell'editore.  | Scomporre il token utente Azure AD per trovare il nome e il messaggio di posta elettronica oppure [chiamare l'API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) e utilizzare le autorizzazioni delegate per [recuperare le informazioni](https://docs.microsoft.com/graph/api/user-get) sull'utente che ha eseguito l'accesso. | Richiesto |
||||

## <a name="process-steps-for-subscription-management"></a>Passaggi del processo per la gestione delle sottoscrizioni

Nella figura sono illustrati i due passaggi del processo per la gestione delle sottoscrizioni.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Vengono illustrati i due passaggi del processo per la gestione delle sottoscrizioni.":::

In questa tabella vengono descritti i dettagli relativi ai passaggi del processo di gestione delle sottoscrizioni.

| Passaggio del processo | Azione Editore | Consigliato o obbligatorio per gli editori |
| ------------ | ------------- | ------------- |
| 5. l'editore gestisce la sottoscrizione all'applicazione SaaS tramite l'API di evasione SaaS. | Gestire le modifiche alla sottoscrizione e altre attività di gestione tramite le [API di evasione Saas](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Per questo passaggio è necessario un token di accesso come descritto in processo passaggio 3. | Richiesto |
| 6. quando si usa il prezzo a consumo, l'editore genera eventi di utilizzo per l'API del servizio di misurazione. | Se l'app SaaS offre la fatturazione basata sull'utilizzo, effettuare notifiche di utilizzo tramite le [API del servizio di misurazione del Marketplace](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Per questo passaggio è necessario un token di accesso, come descritto nel passaggio 3. | Obbligatorio per la misurazione |
||||

## <a name="process-steps-for-user-management"></a>Passaggi del processo per la gestione degli utenti

Nella figura sono illustrati i tre passaggi del processo per la gestione degli utenti.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Vengono illustrati i tre passaggi facoltativi del processo per la gestione degli utenti.":::

I passaggi da 7 a 9 sono i passaggi del processo di gestione degli utenti facoltativi. Forniscono vantaggi aggiuntivi per gli editori che supportano Azure AD Single Sign-On (SSO). In questa tabella vengono descritti i dettagli relativi ai passaggi del processo di gestione degli utenti.

| Passaggio del processo | Azione Editore | Consigliato o obbligatorio per gli editori |
| ------------ | ------------- | ------------- |
| 7. Azure AD amministratori dell'azienda dell'acquirente possono facoltativamente gestire l'accesso per utenti e gruppi tramite Azure AD. | Non è necessaria alcuna azione del server di pubblicazione per abilitare questa impostazione se Azure AD SSO è configurato per gli utenti (passaggio 9). | Non applicabile |
| 8. il servizio di provisioning Azure AD comunica le modifiche tra Azure AD e l'applicazione SaaS dell'editore. | [Implementare un endpoint scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) per ricevere gli aggiornamenti da Azure ad durante l'aggiunta e la rimozione degli utenti. | Implementazione consigliata |
| 9. dopo che l'app è stata autorizzata e ne è stato effettuato il provisioning, gli utenti della società dell'acquirente possono usare Azure AD SSO per accedere all'applicazione SaaS dell'editore. | [Usare Azure ad SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) per consentire agli utenti di eseguire l'accesso una sola volta con un account per l'applicazione SaaS dell'editore. | Implementazione consigliata |
||||

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta SaaS nel Marketplace commerciale](./partner-center-portal/create-new-saas-offer.md)
- [Creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale](./azure-ad-transactable-saas-landing-page.md)
