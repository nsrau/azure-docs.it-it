---
title: "Identificare se un utente specifico è in grado di accedere a un'applicazione | Microsoft Docs"
description: "Come identificare se un utente fondamentale è in grado di accedere a un'applicazione configurata per il provisioning utenti con Azure AD"
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
ms.openlocfilehash: 42c305ad48e6994b1d2c729b849acf665e575807
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Identificare se un utente specifico è in grado di accedere a un'applicazione
Quando si usa il provisioning utenti automatico con un'applicazione, Azure AD esegue automaticamente il provisioning e aggiorna gli account utente in un'app in base ad alcuni elementi, ad esempio l'[assegnazione di utenti e gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal), a intervalli regolari pianificati, in genere ogni 10 minuti.

## <a name="how-long-does-it-take"></a>Quanto tempo occorre?

Il tempo necessario per eseguire il provisioning di un utente varia essenzialmente a seconda che sia stata eseguita o meno una sincronizzazione iniziale "completa".

La prima sincronizzazione tra Azure AD e un'app può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti inclusi nell'ambito del provisioning. 

Le sincronizzazioni successive a quella iniziale risultano più veloci (entro 10 minuti), poiché il servizio di provisioning archivia i limiti che rappresentano lo stato di entrambi i sistemi dopo la sincronizzazione iniziale, migliorando le prestazioni delle sincronizzazioni successive.

## <a name="how-to-check-the-status-of-a-user"></a>Come controllare lo stato di un utente

Per visualizzare lo stato di provisioning per un utente selezionato, vedere i log di controllo in Azure AD.

I log di controllo di provisioning sono accessibili nella scheda **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt; Log di controllo** del Portale di Azure. Filtrare i log nella categoria **Provisioning account** per visualizzare solo gli eventi di provisioning per l'app specifica. È possibile cercare gli utenti in base all'ID di abbinamento configurato nel mapping degli attributi. 

Ad esempio, se è stato configurato il nome dell'entità utente o l'indirizzo di posta elettronica come attributo di abbinamento sul lato Azure AD e l'utente non sottoposto a provisioning presenta un valore "audrey@contoso.com", cercare "audrey@contoso.com" nei log di controllo ed esaminare le voci restituite.

I log di controllo di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, ad esempio:

* Esecuzione in Azure AD di query sugli utenti assegnati che rientrano nell'ambito del provisioning
* Esecuzione nell'app di destinazione di query sull'esistenza di tali utenti
* Confronto degli oggetti utente tra i sistemi
* Aggiunta, aggiornamento o disabilitazione dell'account utente nel sistema di destinazione in base al confronto

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
