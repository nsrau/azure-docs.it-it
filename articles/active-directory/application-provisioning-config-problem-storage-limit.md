---
title: Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD | Microsoft Docs
description: "Come risolvere i problemi comuni riscontrati durante la configurazione del provisioning utenti per un'applicazione già elencata nella raccolta di Azure AD"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: asmalser
ms.openlocfilehash: 6617345c8923b1fc8081b01ddfe8b4bedf10b6ea
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory 

Quando si usa il portale di Azure per configurare il [provisioning utenti automatico](active-directory-saas-app-provisioning.md) per un'applicazione aziendale, può verificarsi la situazione descritta di seguito.

* Le **credenziali dell'amministratore** immesse per l'applicazione sono valide e il pulsante **Test connessione** funziona correttamente. Non risulta tuttavia possibile salvare le credenziali e nel portale di Azure viene restituito un messaggio di errore generico.

Se per la stessa applicazione viene configurato anche l'accesso Single Sign-On basato su SAML, la causa più probabile di questo errore è il superamento del limite di archiviazione interno per applicazione di Azure AD per i certificati e le credenziali.

Azure AD dispone attualmente di una capacità di archiviazione massima pari a un kilobyte per tutti i certificati, token segreti, credenziali e dati di configurazione correlati associati a una singola istanza di un'applicazione (definita anche record entità servizio in Azure AD).

Quando si configura l'accesso Single Sign-On basato su SAML, il certificato usato per firmare i token SAML viene archiviato qui e spesso usa oltre il 50% dello spazio.

Qualsiasi token segreto, URI, indirizzo di posta elettronica di notifica, nome utente e password immessa durante la configurazione del provisioning degli utenti può provocare il superamento del limite archiviazione.

## <a name="how-to-work-around-this-issue"></a>Risoluzione del problema 

Attualmente sono disponibili due modi per risolvere questo problema:

1. **Usare due istanze dell'applicazione nella raccolta, una per l'accesso Single Sign-On e una per il provisioning degli utenti**: prendendo l'applicazione [LinkedIn Elevate](active-directory-saas-linkedinelevate-tutorial.md) come esempio, è possibile aggiungere LinkedIn Elevate dalla raccolta e configurare tale istanza per l'accesso Single Sign-On. Per il provisioning, aggiungere un'altra istanza di LinkedIn Elevate dalla raccolta di app di Azure AD e denominare tale istanza "LinkedIn Elevate (Provisioning)". Per la seconda istanza configurare il [provisioning](active-directory-saas-linkedinelevate-provisioning-tutorial.md), ma non l'accesso Single Sign-On. Quando si usa questa soluzione alternativa, gli stessi utenti e gruppi devono essere [assegnati](active-directory-coreapps-assign-user-azure-portal.md) a entrambe le applicazioni. 

2. **Ridurre la quantità di dati di configurazione archiviati**: tutti i dati immessi nella sezione [Credenziali amministratore](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) della scheda Provisioning vengono archiviati nella stessa posizione del certificato SAML. Anche se potrebbe non essere possibile ridurre la lunghezza di tutti i dati, è possibile rimuovere alcuni campi di configurazione facoltativi, ad esempio **Notifica di posta elettronica**.

## <a name="next-steps"></a>Passaggi successivi
[Configurare il provisioning e il deprovisioning utenti in applicazioni SaaS](active-directory-saas-app-provisioning.md)
