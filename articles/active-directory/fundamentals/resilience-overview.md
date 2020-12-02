---
title: Creazione di una gestione delle identità e degli accessi resiliente con Azure Active Directory
description: Una guida per architetti, amministratori IT e sviluppatori sulla creazione di resilienza per l'intralcio dei sistemi di identità.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454348"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Creazione di resilienza nella gestione delle identità e dell'accesso con Azure Active Directory

Gestione delle identità e degli accessi (IAM) è un Framework di processi, criteri e tecnologie che facilitano la gestione delle identità e degli accessi. Include i numerosi componenti che supportano l'autenticazione e l'autorizzazione di utenti e altri account nel sistema.

La resilienza IAM è la capacità di resistere alle problematiche dei componenti di sistema e di recuperare con un effetto minimo sull'azienda, sugli utenti, sui clienti e sulle operazioni. Riduzione delle dipendenze, della complessità e dei singoli punti di errore, garantendo al tempo stesso la gestione completa degli errori aumenterà la resilienza.

L'interferenza può provenire da qualsiasi componente dei sistemi IAM. Per creare un sistema IAM resiliente, si presuppone che si verifichino interruzioni e che ne venga pianificato l'esecuzione. 

Quando si pianifica la resilienza della soluzione IAM, considerare gli elementi seguenti: 

* Le applicazioni che si basano sul sistema IAM.

* Le infrastrutture pubbliche usate dalle chiamate di autenticazione, incluse le società di telecomunicazioni, i provider di servizi Internet e i provider di chiavi pubbliche.

* Il cloud e i provider di identità locali.

* Altri servizi che si basano su IAM e le API che li collegano.

* Tutti gli altri componenti locali del sistema.

Qualunque sia l'origine, il riconoscimento e la pianificazione delle contingenze è importante. Tuttavia, l'aggiunta di altri sistemi di identità e le relative dipendenze e complessità risultanti possono ridurre la resilienza anziché aumentarla.

Per aumentare la resilienza nei sistemi, vedere gli articoli seguenti:

* [Resilienza di compilazione nell'infrastruttura IAM](resilience-in-infrastructure.md)

* [Compilazione di resilienza IAM nelle applicazioni](resilience-app-development-overview.md)

* [Resilienza della build nei sistemi di gestione delle identità e degli accessi dei clienti (CIAM)](resilience-b2c.md)
