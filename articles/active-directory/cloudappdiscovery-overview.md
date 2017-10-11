---
title: Miglioramenti di Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'individuazione e la gestione delle applicazioni con Cloud App Discovery, quali sono i vantaggi e il relativo funzionamento.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: curtand
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: 59af2a5de5936d15456058aaeacfc334b9b34d4c
ms.contentlocale: it-it
ms.lasthandoff: 09/29/2017

---
# <a name="cloud-app-discovery-enhancements-in-azure-active-directory"></a>Miglioramenti di Cloud App Discovery in Azure Active Directory 
Oltre l'80% dei dipendenti ammette di usare app SaaS non approvate per scopi di lavoro. Cloud App Discovery in Azure AD è stato ora integrato con funzionalità di gestione dei dati e analisi di Microsoft Cloud App Security per consentire l'identificazione di più app cloud non gestite più facilmente. Questa nuova integrazione richiede passaggi di configurazione diversi rispetto a quelli necessari in passato.

## <a name="what-can-i-do-now-with-the-improvements-to-cloud-app-discovery-in-azure-ad"></a>Nuovi vantaggi offerti dai miglioramenti apportati a Cloud App Discovery in Azure AD

* **Maggiore visibilità dell'utilizzo delle app cloud** Cloud App Discovery in Azure AD è ora in grado di individuare **oltre 15.000 app** da tutto il traffico di rete dell'organizzazione e da qualsiasi dispositivo. 
* **Nessun agente necessario** Questa nuova versione di Cloud App Discovery non richiede l'installazione di agenti nei dispositivi degli utenti. L'individuazione avviene invece in base ai file di log importati dai firewall e dai proxy. È possibile individuare le app da tutto il traffico di rete dell'organizzazione, indipendentemente dal dispositivo o dal sistema operativo.
* **Analisi e avvisi continui** Cloud App Discovery in Azure AD fornisce ora un'analisi dettagliata e continua dei rischi, insieme ad avvisi quando vi sono nuove app in uso. È ora possibile ottenere informazioni approfondite sull'utilizzo delle app cloud nell'organizzazione, ad esempio sul traffico in ingresso e in uscita e sui principali utenti per le app individuate.

Chi non ha Active Directory Premium può fare riferimento a [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) per altre informazioni.

Usare questo collegamento per esplorare la [nuova esperienza di Cloud App Discovery in Azure AD](https://portal.cloudappsecurity.com).

## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per configurare la versione di Cloud App Discovery in Azure AD.

* [Introduzione all'uso di Cloud App Discovery](cloudappdiscovery-get-started.md)
* [Creare report di snapshot](cloudappdiscovery-set-up-snapshots.md)
* [Configurare la creazione di report continua](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usare un parser di log personalizzato](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
