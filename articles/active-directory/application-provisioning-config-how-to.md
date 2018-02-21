---
title: Come configurare il provisioning degli utenti in un'applicazione della raccolta di Azure AD | Microsoft Docs
description: "Come configurare rapidamente il provisioning e il deprovisioning degli account utente per applicazioni già elencate nella raccolta di applicazioni AD Azure"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a41c09cc5a13c8326e22e315801b81c86d125c0e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Come configurare il provisioning degli utenti in un'applicazione della raccolta di Azure AD

Il *provisioning degli account utente* è l'atto di creazione, aggiornamento e/o la disabilitazione di record di account utente nell'archivio di profili utente locale di un'applicazione. La maggior parte delle applicazioni cloud e SaaS archiviano i ruoli e le autorizzazioni utente in un proprio archivio di profili utente locale e la presenza di tali record utente nell'archivio locale è *necessaria* per il funzionamento degli accessi e del Single Sign-on.

Nel portale di Azure, la scheda **Provisioning** nel riquadro di spostamento a sinistra di un'applicazione Enterprise mostra le modalità di provisioning supportate per l'applicazione. Può essere uno dei due valori:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurazione di un'applicazione per il Provisioning manuale

Con il provisioning *manuale* gli account utente devono essere creati manualmente usando i metodi forniti dall'applicazione. Potrebbe essere necessario accedere a un portale amministrativo dell'applicazione e aggiungere gli utenti usando un'interfaccia utente basata sul Web. Oppure caricare un foglio di calcolo con i dettagli degli account utente, usando un meccanismo fornito dall'applicazione. Consultare la documentazione o contattare lo sviluppatore dell'applicazione per determinare i meccanismi disponibili.

Se Manuale è l'unica modalità disponibile per una determinata applicazione, significa che non è stato creato alcun connettore di provisioning automatico AD Azure per l'applicazione. O significa che l'applicazione non supporta l'API prerequisita di gestione degli utenti in base a cui creare un connettore di provisioning automatico.

Se si intende richiedere supporto per il provisioning automatico per una determinata applicazione, è possibile inviare una richiesta all'indirizzo <http://aka.ms/aadapprequest>.

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurazione di un'applicazione per il Provisioning automatico

*Automatico* significa che è stato sviluppato un connettore di provisioning Azure AD per l'applicazione. Per altre informazioni sul servizio di provisioning automatico e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Per altre informazioni su come eseguire il provisioning di utenti e gruppi specifici per un'app, vedere [Anteprima: gestione del provisioning degli account utenti per app aziendali nel nuovo portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

I passaggi necessari per abilitare e configurare il provisioning automatico variano a seconda dell'applicazione.

>[!NOTE]
>È consigliabile iniziare cercando l'esercitazione per la configurazione del provisioning per l'applicazione e seguire i passaggi per configurare l'applicazione e Azure AD per creare la connessione di provisioning. 
>
>

Le esercitazioni per le applicazioni si trovano nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Rientra in questo ambito anche l'impostazione della "proprietà di abbinamento" che può essere usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi. Per altre informazioni su questo processo importante.

## <a name="next-steps"></a>Passaggi successivi
[Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

