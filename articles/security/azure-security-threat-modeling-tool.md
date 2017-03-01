---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: pagina principale per Microsoft Threat Modeling Tool, che contiene soluzioni di prevenzione per le minacce generate maggiormente esposte
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
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool 
Microsoft Threat Modeling Tool è un elemento principale di Microsoft Security Development Lifecycle (SDL). Consente ai progettisti di software di identificare e ridurre tempestivamente i potenziali problemi di sicurezza, quando sono relativamente semplici e convenienti da risolvere. Di conseguenza, riduce notevolmente i costi totali di sviluppo. Inoltre, lo strumento è progettato pensando agli esperti non relativi alla sicurezza, poiché semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo istruzioni chiare sulla creazione e sull'analisi dei modelli di rischio. 

Lo strumento consente a tutti gli utenti di:
* Parlare della progettazione della protezione dei sistemi
* Analizzare le progettazioni per individuare potenziali problemi di sicurezza usando una metodologia comprovata
* Suggerire e gestire soluzioni di prevenzione per problemi di protezione

Ecco alcune funzionalità degli strumenti e innovazioni, solo per citarne alcune:
* **Automazione:** linee guida e feedback sulla definizione di un modello
* **STRIDE per ogni elemento:** analisi guidata di minacce e soluzioni di prevenzione
* **Creazione di report:** attività di protezione e test in fase di verifica
* **Metodologia univoca:** consente agli utenti di visualizzare meglio e conoscere le minacce
* **Progettato per gli sviluppatori e incentrato sul software:** molti approcci sono incentrati su risorse o utenti malintenzionati. Noi ci siamo concentrati sul software. Facciamo leva sulle attività con cui tutti gli sviluppatori di software e i progettisti hanno familiarità, ad esempio il disegno di immagini per l'architettura software
* **Concentrazione sull'analisi della progettazione:** il termine "modellazione delle minacce" può fare riferimento a un requisito o una tecnica di analisi della progettazione. In alcuni casi, si riferisce a una combinazione complessa dei due. L'approccio Microsoft SDL alla modellazione delle minacce è una tecnica di analisi di progettazione focalizzata

## <a name="threats"></a>Minacce

Threat Modeling Tool consente di rispondere a domande specifiche, ad esempio quelle indicate di seguito:

* Come può un utente malintenzionato modificare i dati di autenticazione?
* Se un utente malintenzionato può leggere i dati del profilo utente, quali sono le conseguenze?
* Cosa accade se viene negato l'accesso al database del profilo utente?

Per aiutare l'utente a formulare questo tipo di domande indirizzate, Microsoft usa il modello STRIDE, che classifica i diversi tipi di minacce e semplifica le conversazioni di protezione globale.

| Categoria | Descrizione |
| -------- | ----------- |
| Spoofing | Implica l'accesso in modo illegale e l'uso delle informazioni di autenticazione di un altro utente, ad esempio nome utente e password |
| Manomissione | Comporta la modifica non autorizzata dei dati. Alcuni esempi sono le modifiche non autorizzate apportate ai dati persistenti, ad esempio quelli contenuti in un database, e la modifica dei dati trasmessi tra due computer in una rete aperta, ad esempio Internet |
| Ripudio | Associato agli utenti che negano l'esecuzione di un'azione senza che altri possano provare il contrario, ad esempio, un utente esegue un'operazione non valida in un sistema in cui non è presente la possibilità di tenere traccia delle operazioni non consentite. Il non ripudio si riferisce alla capacità di un sistema di far fronte a rischi di ripudio. Ad esempio, un utente che acquista un articolo potrebbe dover firmare al momento della ricezione. Il fornitore può quindi usare la firma della ricevuta come prova che l'utente ha ricevuto il pacco |
| Divulgazione di informazioni | Comporta l'esposizione di informazioni a individui che non hanno il permesso di accedere al sistema, ad esempio, la capacità degli utenti di leggere un file al quale non hanno ricevuto il permesso di accesso o la capacità di un intruso di leggere dati in transito tra due computer |
| Denial of Service | Gli attacchi Denial of service (DoS) negano il servizio agli utenti validi, per esempio rendendo un server Web temporaneamente non disponibile o inutilizzabile. È necessario proteggersi da determinati tipi di minacce DoS semplicemente per migliorare l'affidabilità e la disponibilità del sistema |
| Elevazione dei privilegi | Un utente senza privilegi acquisisce accesso privilegiato e ha pertanto un accesso sufficiente per compromettere o distruggere l'intero sistema. L'elevazione dei pericoli di privilegio include quelle situazioni in cui un utente malintenzionato ha violato tutte le difese di sistema e diventa parte del sistema affidabile, effettivamente una situazione pericolosa |

## <a name="mitigations"></a>Soluzioni di prevenzione

Le soluzioni di prevenzione di Threat Modeling Tool sono suddivise in base al contesto di sicurezza dell'applicazione Web, costituito dai seguenti elementi:

| Categoria | Descrizione |
| -------- | ----------- |
| [Controllo e registrazione](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | Chi ha fatto cosa e quando? Controllo e registrazione fanno riferimento al modo in cui l'applicazione registra gli eventi di sicurezza |
| [Autenticazione](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | Chi si sta autenticando? L'autenticazione è il processo che permette a un'entità di dimostrare l'identità di un'altra entità, in genere mediante l'uso di credenziali, come un nome utente e una password |
| [Autorizzazione](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | Cosa fare L'autorizzazione è il modo in cui l'applicazione fornisce controlli sull'accesso per risorse e operazioni | 
| [Sicurezza delle comunicazioni](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | Con si sta parlando? La protezione delle comunicazioni garantisce che tutte le comunicazioni siano eseguite nel modo più sicuro possibile |
| [Gestione della configurazione](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | A nome di chi è eseguita l'applicazione? A quale database si connette? Come viene amministrata l'applicazione? Come vengono protette queste impostazioni? La gestione della configurazione fa riferimento al modo in cui l'applicazione gestisce questi problemi operativi | 
| [Crittografia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | Come si mantengono i segreti (la riservatezza)? Come si mettono a prova di manomissione i dati o le librerie (integrità)? Come vengono creati i valori di inizializzazione casuali che devono essere crittograficamente sicuri? La crittografia fa riferimento al modo in cui l'applicazione garantisce riservatezza e integrità | 
| [Gestione delle eccezioni](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | Quando una chiamata del metodo nell'applicazione ha esito negativo, che cosa fa l'applicazione? Quanto viene rivelato? Si restituiscono informazioni di errore descrittive agli utenti finali? Si passano informazioni importanti sull'eccezione al chiamante? L'applicazione ha esito negativo correttamente? |
| [Convalida dell'input](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | Come è possibile sapere che l'input che l'applicazione riceve è valido e sicuro? La convalida dell'input fa riferimento al modo in cui l'applicazione filtra, elimina o respinge gli input prima di un'ulteriore elaborazione. Considerare la limitazione dell'input tramite punti di ingresso e la codifica dell'output tramite punti di uscita. Ci si fida dei dati dalle fonti come i database e la condivisione di file? |
| [Dati sensibili](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | In che modo l'applicazione gestisce dati sensibili? I dati sensibili si riferiscono al modo in cui l'applicazione gestisce tutti i dati che devono essere protetti in memoria, nella rete, o in archivi permanenti | 
| [Gestione delle sessioni](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | In che modo l'applicazione gestisce e protegge le sessioni utente? Una sessione fa riferimento a una serie di interazioni correlate tra un utente e l'applicazione Web | 

Ciò consente di identificare: 

* Il punto in cui vengono eseguiti gli errori più comuni
* Dove si trovano i miglioramenti più utilizzabili

Di conseguenza, queste categorie vengono usate per focalizzarsi e definire le priorità del lavoro legato alla sicurezza, in modo che se si sa che i problemi di sicurezza più prevalenti si verificano nelle categorie di convalida, autenticazione e autorizzazione input, è possibile iniziare da questo punto. Fare clic [qui](https://www.google.com/patents/US7818788) per altre informazioni

## <a name="next-steps"></a>Passaggi successivi
Consultare questi collegamenti utili per iniziare oggi stesso:
* [Collegamento di download](https://www.microsoft.com/download/details.aspx?id=49168)
* [Per iniziare](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [Formazione di base](https://www.microsoft.com/download/details.aspx?id=16420)

Perché non vedere anche quello che hanno fatto alcuni esperti di Threat Modeling Tool:
* [Gestione delle minacce](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Blog sulla protezione di Simone Curzi](https://simoneonsecurity.com/)
