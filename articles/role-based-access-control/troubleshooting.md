---
title: Risolvere i problemi relativi al controllo degli accessi in base al ruolo in Azure | Microsoft Docs
description: Risolvere i problemi relativi al controllo degli accessi in base al ruolo di Azure.
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
ms.date: 07/23/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d1a0e46fe348bbc60a4d02a4727a9bb27cb26742
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223297"
---
# <a name="troubleshoot-rbac-in-azure"></a>Risolvere i problemi relativi al controllo degli accessi in base al ruolo in Azure

Questo articolo contiene le risposte alle domande comuni sul controllo degli accessi in base al ruolo, per poter sapere che cosa accade quando si usano i ruoli nel portale di Azure e risolvere i problemi di accesso.

## <a name="web-app-features-that-require-write-access"></a>Funzionalità dell'app Web che richiedono l'accesso in scrittura

Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Per le funzionalità di gestione seguenti è necessario avere accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario. Tali funzionalità non saranno disponibili in uno scenario di sola lettura.

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

* Visualizzazione del piano tariffario dell'app Web, che può essere Free o Standard  
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

## <a name="rbac-changes-are-not-being-detected"></a>Le modifiche del controllo degli accessi in base al ruolo non vengono rilevate

In alcuni casi, Azure Resource Manager memorizza nella cache le configurazioni e i dati per migliorare le prestazioni. Durante la creazione o l'eliminazione delle assegnazioni di ruolo, per l'applicazione delle modifiche possono essere necessari fino a 30 minuti. Se si usa il portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile forzare l'aggiornamento delle modifiche alle assegnazioni di ruolo effettuando la disconnessione e quindi di nuovo l'accesso. Se si apportano modifiche alle assegnazioni di ruolo con chiamate all'API REST, è possibile forzare l'aggiornamento semplicemente aggiornando il token di accesso.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md)
* [Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo](change-history-report.md)

