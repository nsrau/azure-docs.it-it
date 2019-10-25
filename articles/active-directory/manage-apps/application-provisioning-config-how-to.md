---
title: Come configurare il provisioning degli utenti in un'applicazione della raccolta di Azure AD | Microsoft Docs
description: Come configurare rapidamente il provisioning e il deprovisioning degli account utente per applicazioni già elencate nella raccolta di applicazioni AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bcc53b97b1187314404cfe075f6593f437e7bf4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789056"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Come configurare il provisioning degli utenti in un'applicazione della raccolta di Azure AD

Il *provisioning degli account utente* è l'atto di creazione, aggiornamento e/o la disabilitazione di record di account utente nell'archivio di profili utente locale di un'applicazione. La maggior parte delle applicazioni cloud e SaaS archivia il ruolo utenti e le autorizzazioni nell'archivio dei profili utente locale dell'utente e la presenza di tale record utente nell'archivio locale dell'utente è *necessaria* per Single Sign-on e l'accesso al lavoro.

Nel portale di Azure, la scheda **Provisioning** nel riquadro di spostamento a sinistra di un'applicazione Enterprise mostra le modalità di provisioning supportate per l'applicazione. Può essere uno dei due valori:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurazione di un'applicazione per il Provisioning manuale

Il provisioning *manuale* significa che gli account utente devono essere creati manualmente usando i metodi forniti dall'app. Potrebbe essere necessario accedere a un portale amministrativo dell'applicazione e aggiungere gli utenti usando un'interfaccia utente basata sul Web. Oppure caricare un foglio di calcolo con i dettagli degli account utente, usando un meccanismo fornito dall'applicazione. Consultare la documentazione fornita dall'app o contattare lo sviluppatore dell'app per determinare quali meccanismi sono disponibili.

Se *Manual* è l'unica modalità visualizzata per una determinata applicazione, significa che non è ancora disponibile un connettore di provisioning automatico Azure ad per l'app. O significa che l'app non supporta i prerequisiti per l'API di gestione degli utenti di Microsoft, che viene usata per creare un connettore di provisioning automatico.

Se si intende richiedere supporto per il provisioning automatico per una determinata applicazione, è possibile inviare una richiesta usando la pagina delle [richieste relative all'applicazione Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurazione di un'applicazione per il Provisioning automatico

*Automatico* significa che è stato sviluppato un connettore di provisioning Azure AD per l'applicazione. Per altre informazioni sul servizio di provisioning automatico e sul relativo funzionamento, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Per altre informazioni su come eseguire il provisioning di utenti e gruppi specifici per un'app, vedere [Anteprima: gestione del provisioning degli account utenti per app aziendali nel nuovo portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

I passaggi effettivi necessari per abilitare e configurare il provisioning automatico variano in base all'applicazione.

> [!NOTE]
> È consigliabile iniziare cercando l'esercitazione per la configurazione del provisioning per l'applicazione e seguire i passaggi per configurare l'applicazione e Azure AD per creare la connessione di provisioning. 

Le esercitazioni per le applicazioni si trovano nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Ciò include l'impostazione della "proprietà corrispondente" utilizzata per identificare in modo univoco e associare utenti/gruppi tra i due sistemi. Per ulteriori informazioni sui mapping degli attributi, vedere il collegamento nei *passaggi successivi* .

## <a name="next-steps"></a>Passaggi successivi
[Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

