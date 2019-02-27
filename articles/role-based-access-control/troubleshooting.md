---
title: Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure | Microsoft Docs
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo per le risorse di Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 7b27c811214def7f5646f886b955d035a50c0725
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342474"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure

Questo articolo contiene le risposte alle domande comuni sul controllo degli accessi in base al ruolo per le risorse di Azure, per poter sapere che cosa accade quando si usano i ruoli nel portale di Azure e risolvere i problemi di accesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemi con le assegnazioni di ruolo di Controllo degli accessi in base al ruolo

- Se non è possibile aggiungere un'assegnazione di ruolo perché l'opzione **Aggiungi un'assegnazione di ruolo** è disabilitata o perché viene visualizzato un errore di autorizzazione, accertarsi di usare un ruolo che disponga dell'autorizzazione `Microsoft.Authorization/roleAssignments/*` nell'ambito in cui si sta tentando di assegnare il ruolo. Se non si dispone di questa autorizzazione, rivolgersi all'amministratore della sottoscrizione.
- Se si verifica un errore di autorizzazione quando si prova a creare una risorsa, accertarsi di usare un ruolo che disponga dell'autorizzazione per la creazione di risorse nell'ambito selezionato. Ad esempio, può essere necessario usare il ruolo Collaboratore. Se non si dispone di questa autorizzazione, rivolgersi all'amministratore della sottoscrizione.
- Se si verifica un errore di autorizzazione quando si prova a creare o aggiornare un ticket di supporto, accertarsi di usare un ruolo che disponga dell'autorizzazione `Microsoft.Support/*`, ad esempio quello di [Collaboratore richiesta di supporto](built-in-roles.md#support-request-contributor).
- Se si verifica un errore di superamento del numero di assegnazioni di ruolo quando si cerca di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo assegnando i ruoli a gruppi. Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione.

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Se non è possibile aggiornare un ruolo personalizzato esistente, controllare se si dispone dell'autorizzazione `Microsoft.Authorization/roleDefinition/write`.
- Se non è possibile aggiornare un ruolo personalizzato esistente, controllare se uno o più ambiti assegnabili sono stati eliminati dal tenant. La proprietà `AssignableScopes` per un ruolo personalizzato verifica [chi può creare, eliminare, aggiornare o visualizzare il ruolo personalizzato](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Se viene visualizzato un errore di superamento del limite di definizioni del ruolo quando si prova a creare un nuovo ruolo, eliminare tutti i ruoli personalizzati inutilizzati. È anche possibile provare a consolidare o riutilizzare eventuali ruoli personalizzati esistenti. Azure supporta fino a **2000** ruoli personalizzati in un tenant.
- Se non è possibile eliminare un ruolo personalizzato, verificare se sono presenti una o più assegnazioni di ruolo che usano ancora il ruolo personalizzato.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Ripristinare il Controllo degli accessi in base al ruolo quando le sottoscrizioni vengono spostate tra i tenant

- Se sono necessari passaggi per il trasferimento di una sottoscrizione a un altro tenant, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md).
- Quando si trasferisce una sottoscrizione a un altro tenant, tutte le assegnazioni di ruolo vengono eliminate definitivamente dal tenant di origine e non ne viene eseguita la migrazione al tenant di destinazione. È necessario creare nuovamente le assegnazioni di ruolo nel tenant di destinazione.
- Se si è un amministratore globale e si è perso l'accesso a una sottoscrizione, attivare **Gestione degli accessi per le risorse di Azure** per [elevare l'accesso](elevate-access-global-admin.md) temporaneamente, ottenendo così l'accesso alla sottoscrizione.

## <a name="rbac-changes-are-not-being-detected"></a>Le modifiche del controllo degli accessi in base al ruolo non vengono rilevate

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Durante la creazione o l'eliminazione delle assegnazioni di ruolo, per l'applicazione delle modifiche possono essere necessari fino a 30 minuti. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Per le funzionalità di gestione seguenti, è necessario avere accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario. Tali funzionalità non saranno disponibili in uno scenario di sola lettura.

* Comandi (quali avvio, interruzione e così via)
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log di diagnostica
* Console (prompt dei comandi)
* Distribuzioni attive e recenti, per la distribuzione continua del Git locale
* Spesa prevista
* Test Web
* Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per l'app Web.

## <a name="web-app-resources-that-require-write-access"></a>Risorse dell'app Web che richiedono l'accesso in scrittura

La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/troubleshooting/website-resource-model.png)

Quindi, se si concede l'accesso solo all'app Web, la maggior parte delle funzionalità del pannello del sito Web nel portale di Azure viene disabilitata.

Questi elementi richiedono accesso **in scrittura** al **piano di servizio App** che corrisponde al sito Web:  

* Visualizzazione del piano tariffario dell'app Web, che può essere Gratuito o Standard  
* Configurazione di scalabilità, ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica  
* Quote, ad esempio archiviazione, larghezza di banda e CPU  

Gli elementi seguenti richiedono accesso **in scrittura** all'intero **gruppo di risorse** che contiene il sito Web:  

* Certificati e associazioni SSL. I certificati SSL possono essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica.  
* Regole di avviso  
* Impostazioni di scalabilità automatica  
* Componenti di Application Insights  
* Test Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funzionalità delle macchine virtuali che richiedono l'accesso in scrittura

Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le macchine virtuali sono correlate a nomi di dominio, reti virtuali, account di archiviazione e regole di avviso.

Gli elementi seguenti richiedono accesso **in scrittura** alla **macchina virtuale**:

* Endpoint  
* Indirizzi IP  
* Dischi  
* Estensioni  

Gli elementi seguenti richiedono accesso **in scrittura** sia alla **macchina virtuale** che al **gruppo di risorse**, così come al nome di dominio a cui appartiene:  

* Set di disponibilità  
* Set con carico bilanciato  
* Regole di avviso  

Se non è possibile accedere a nessuno di questi riquadri, richiedere all'amministratore l'accesso come Collaboratore al gruppo di risorse.

## <a name="azure-functions-and-write-access"></a>Funzioni di Azure e accesso in scrittura

Alcune funzionalità di [Funzioni di Azure](../azure-functions/functions-overview.md) richiedono l'accesso in scrittura. Ad esempio un utente, a cui è stato assegnato il ruolo di lettore, non sarà in grado di visualizzare le funzioni all'interno di un'app per le funzioni. Sul portale verrà indicato **(Nessun accesso)**.

![Nessun accesso alle app per le funzioni](./media/troubleshooting/functionapps-noaccess.png)

Un lettore può fare clic sulla scheda **Funzionalità della piattaforma** e quindi su **Tutte le impostazioni** per visualizzare alcune impostazioni correlate a un'app per le funzioni (in modo simile a un'app Web), ma non può modificare nessuna impostazione.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md)
* [Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo alle risorse di Azure](change-history-report.md)

