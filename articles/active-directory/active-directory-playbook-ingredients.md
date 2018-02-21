---
title: Ingredienti del playbook dei modelli di verifica di Azure Active Directory | Microsoft Docs
description: "Esplorare e implementare rapidamente gli scenari di Gestione delle identità e degli accessi"
services: active-directory
keywords: azure active directory, studio, modello di verifica, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: ff4a8601b619837d835ec25c26b1f7e69b46ae85
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-ingredients"></a>Ingredienti del playbook dei modelli di verifica di Azure Active Directory 

## <a name="theme"></a>Tema
Azure AD offre soluzioni per la gestione di identità e accessi in più aree dell'organizzazione. Gli scenari vengono classificati in base alle aree seguenti: 

* [Molte app, una sola identità](active-directory-playbook-implementation.md#theme---lots-of-apps-one-identity) 
* [Aumentare la sicurezza](active-directory-playbook-implementation.md#theme---increase-your-security) 
* [Scalabilità self-service](active-directory-playbook-implementation.md#theme---scale-with-self-service) 

La definizione di un tema ai fini dell’impostazione di un modello di verifica aiuta a mettere in evidenza gli sforzi necessari per raggiungere gli obiettivi aziendali, che rappresentano spesso il primo motivo di interesse nella creazione di un modello di verifica. 

## <a name="environment"></a>Environment

È importante determinare i dettagli dell'ambiente in cui verrà distribuito il modello di verifica che idealmente, una volta completato, dovrebbe costituire la base su cui costruire l’ambiente. L'ambiente di destinazione è fondamentale ed è consigliabile trovare il giusto equilibrio tra l’obiettivo di renderlo il più reale possibile e il numero totale di vincoli o considerazioni aggiuntive. Gli ambienti tipici per i modelli di verifica sono:
* **Produzione:** gli scenari verranno implementati nell'ambiente live e nei servizi Microsoft Cloud già distribuiti (Active Directory di produzione, Office 365, tenant di Azure AD/soluzione SSO). 
* **Test accettazione utente / ambiente di sviluppo:** è disponibile un’infrastruttura di test (AD parallelo e potenziale tenant di Azure AD/soluzione SSO) con dati di test analoghi alla produzione. In genere, l'ambiente di test viene condiviso tra più progetti dell'organizzazione.

Nella maggior parte dei casi, gli scenari in questa guida sono additivi in natura. Pertanto, possono essere distribuiti nel tenant di produzione senza impatto sugli utenti esterni al modello di verifica. In questo documento, verranno esclusi gli scenari che avrebbero un impatto globale a livello di tenant. In questi casi, è consigliabile prendere in considerazione un ambiente non di produzione. 


## <a name="target-users"></a>Utenti di destinazione

È importante determinare il set di destinazione degli utenti che faranno pratica nell’ambito di questi scenari, in particolar modo quando l'ambiente è di produzione o di test. Le categorie di utenti di destinazione per i modelli di verifica sono:
* **Utenti pilota:** utenti reali nell'ambiente che utilizzeranno la soluzione con l'account usato per le normali attività quotidiane
* **Utenti test:** account di prova creati nell'ambiente 

La maggior parte degli scenari contemplati in questa guida può essere usata dagli utenti pilota. In questo documento verranno escluse se necessario le considerazioni in merito agli utenti di destinazione.


[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]