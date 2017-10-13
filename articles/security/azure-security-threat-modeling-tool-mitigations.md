---
title: 'Migrazione: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: Pagina di mitigazione per Microsoft Threat Modeling Tool che riporta possibili soluzioni di prevenzione per le minacce generate maggiormente esposte.
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigazioni di Microsoft Threat Modeling Tool

Microsoft Threat Modeling Tool è un elemento principale di Microsoft Security Development Lifecycle (SDL). Consente ai progettisti di software di identificare e ridurre tempestivamente i potenziali problemi di sicurezza, quando sono relativamente semplici e convenienti da risolvere. Di conseguenza, riduce notevolmente i costi totali di sviluppo. Inoltre, lo strumento è progettato pensando agli esperti non relativi alla sicurezza, poiché semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo istruzioni chiare sulla creazione e sull'analisi dei modelli di rischio.

Visitare **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** oggi stesso per iniziare!

## <a name="mitigation-categories"></a>Categorie di mitigazione

Le soluzioni di prevenzione di Threat Modeling Tool sono suddivise in base al contesto di sicurezza dell'applicazione Web, costituito dai seguenti elementi:

| Categoria | Descrizione |
| -------- | ----------- |
| **[Controllo e registrazione](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Chi ha fatto cosa e quando? Controllo e registrazione fanno riferimento al modo in cui l'applicazione registra gli eventi di sicurezza |
| **[Autenticazione](./azure-security-threat-modeling-tool-authentication.md)** | Chi si sta autenticando? L'autenticazione è il processo che permette a un'entità di dimostrare l'identità di un'altra entità, in genere mediante l'uso di credenziali, come un nome utente e una password |
| **[Autorizzazione](./azure-security-threat-modeling-tool-authorization.md)** | Cosa fare L'autorizzazione è il modo in cui l'applicazione fornisce controlli sull'accesso per risorse e operazioni |
| **[Sicurezza delle comunicazioni](./azure-security-threat-modeling-tool-communication-security.md)** | Con si sta parlando? La protezione delle comunicazioni garantisce che tutte le comunicazioni siano eseguite nel modo più sicuro possibile |
| **[Gestione della configurazione](./azure-security-threat-modeling-tool-configuration-management.md)** | A nome di chi è eseguita l'applicazione? A quale database si connette? Come viene amministrata l'applicazione? Come vengono protette queste impostazioni? La gestione della configurazione fa riferimento al modo in cui l'applicazione gestisce questi problemi operativi |
| **[Crittografia](./azure-security-threat-modeling-tool-cryptography.md)** | Come si mantengono i segreti (la riservatezza)? Come si mettono a prova di manomissione i dati o le librerie (integrità)? Come vengono creati i valori di inizializzazione casuali che devono essere crittograficamente sicuri? La crittografia fa riferimento al modo in cui l'applicazione garantisce riservatezza e integrità |
| **[Gestione delle eccezioni](./azure-security-threat-modeling-tool-exception-management.md)** | Quando una chiamata del metodo nell'applicazione ha esito negativo, che cosa fa l'applicazione? Quanto viene rivelato? Si restituiscono informazioni di errore descrittive agli utenti finali? Si passano informazioni importanti sull'eccezione al chiamante? L'applicazione ha esito negativo correttamente? |
| **[Convalida dell'input](./azure-security-threat-modeling-tool-input-validation.md)** | Come è possibile sapere che l'input che l'applicazione riceve è valido e sicuro? La convalida dell'input fa riferimento al modo in cui l'applicazione filtra, elimina o respinge gli input prima di un'ulteriore elaborazione. Considerare la limitazione dell'input tramite punti di ingresso e la codifica dell'output tramite punti di uscita. Ci si fida dei dati dalle fonti come i database e la condivisione di file? |
| **[Dati sensibili](./azure-security-threat-modeling-tool-sensitive-data.md)** | In che modo l'applicazione gestisce dati sensibili? I dati sensibili si riferiscono al modo in cui l'applicazione gestisce tutti i dati che devono essere protetti in memoria, nella rete, o in archivi permanenti |
| **[Gestione delle sessioni](./azure-security-threat-modeling-tool-session-management.md)** | In che modo l'applicazione gestisce e protegge le sessioni utente? Una sessione fa riferimento a una serie di interazioni correlate tra un utente e l'applicazione Web |

Ciò consente di identificare:

* Il punto in cui vengono eseguiti gli errori più comuni
* Dove si trovano i miglioramenti più utilizzabili

Di conseguenza, queste categorie vengono usate per focalizzarsi e definire le priorità del lavoro legato alla sicurezza, in modo che se si sa che i problemi di sicurezza più prevalenti si verificano nelle categorie di convalida, autenticazione e autorizzazione input, è possibile iniziare da questo punto. Per altre informazioni, visitare **[questo collegamento al brevetto](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle categorie di minacce utilizzate dallo strumento per generare possibili minacce di progettazione, visitare **[Minacce di Threat Modeling Tool](./azure-security-threat-modeling-tool-threats.md)**.