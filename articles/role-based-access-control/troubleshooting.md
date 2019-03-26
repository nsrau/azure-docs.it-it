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
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407590"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Risolvere i problemi del controllo degli accessi in base al ruolo per le risorse di Azure

Questo articolo contiene le risposte alle domande comuni sul controllo degli accessi in base al ruolo per le risorse di Azure, per poter sapere che cosa accade quando si usano i ruoli nel portale di Azure e risolvere i problemi di accesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemi con le assegnazioni di ruolo di Controllo degli accessi in base al ruolo

- Se non si riesce ad aggiungere un'assegnazione di ruolo nel portale di Azure sul **controllo di accesso (IAM)** perché la **Add** > **aggiungere un'assegnazione di ruolo** opzione è disabilitata o perché viene visualizzato l'errore relativo alle autorizzazioni "il client con id oggetto non è autorizzato a eseguire l'azione", verificare che si è connessi attualmente con un account utente che viene assegnato un ruolo con il `Microsoft.Authorization/roleAssignments/write` l'autorizzazione, ad esempio [proprietario](built-in-roles.md#owner) oppure [amministratore accesso utenti](built-in-roles.md#user-access-administrator) nell'ambito in cui si sta tentando di assegnare il ruolo.
- Se viene visualizzato il messaggio di errore "non più assegnazioni di ruolo possono essere create (codice: RoleAssignmentLimitExceeded) "quando si tenta di assegnare un ruolo, provare a ridurre il numero di assegnazioni di ruolo assegnando ruoli invece a gruppi. Azure supporta fino a **2000** assegnazioni di ruolo per sottoscrizione.

## <a name="problems-with-custom-roles"></a>Problemi con i ruoli personalizzati

- Se sono necessari passaggi per creare un ruolo personalizzato, vedere le esercitazioni di ruolo personalizzate utilizzando [Azure PowerShell](tutorial-custom-role-powershell.md) oppure [Azure CLI](tutorial-custom-role-cli.md).
- Se è in grado di aggiornare un ruolo personalizzato esistente, verificare che si è connessi attualmente con un account utente che viene assegnato un ruolo con il `Microsoft.Authorization/roleDefinition/write` l'autorizzazione, ad esempio [proprietario](built-in-roles.md#owner) o [amministratore accesso utenti](built-in-roles.md#user-access-administrator).
- Se non si riesce a eliminare un ruolo personalizzato e ottenere il messaggio di errore "sono presenti assegnazioni di ruolo che fanno riferimento a ruolo (codice: RoleDefinitionHasAssignments) ", significa che sono presenti assegnazioni di ruolo Usa ancora il ruolo personalizzato. Rimuovere le assegnazioni di ruolo e provare nuovamente a eliminare il ruolo personalizzato.
- Se viene visualizzato il messaggio di errore "limite di definizioni ruolo superato. Non è possibile creare più definizioni di ruolo (codice: RoleDefinitionLimitExceeded) "quando si prova a creare un nuovo ruolo personalizzato, eliminare tutti i ruoli personalizzati che non vengono usati. Azure supporta fino a **2000** ruoli personalizzati in un tenant.
- Se si verifica un errore simile a "il client è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleDefinitions/write' sull'ambito '/ sottoscrizioni / {subscriptionid}', ma la sottoscrizione collegata non è stata trovata" quando si tenta di aggiornare un ruolo personalizzato, controllare Se uno o più [ambiti assegnabili](role-definitions.md#assignablescopes) sono stati eliminati nel tenant. Se l'ambito è stato eliminato, quindi creare un ticket di supporto come non c'è alcuna soluzione self-service disponibile in questo momento.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Ripristinare il Controllo degli accessi in base al ruolo quando le sottoscrizioni vengono spostate tra i tenant

- Se sono necessari passaggi per il trasferimento di una sottoscrizione a un Azure AD diverso tenant, vedere [trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md).
- Se si trasferisce una sottoscrizione a un Azure AD diverso del tenant, tutte le assegnazioni di ruolo vengono eliminate definitivamente dal tenant di Azure AD di origine e non vengono migrate al tenant di Azure AD di destinazione. È necessario creare nuovamente le assegnazioni di ruolo nel tenant di destinazione.
- Se sei un Azure AD amministratore globale e si autorizzati ad accedere a una sottoscrizione dopo lo spostamento tra i tenant, usare il **Access management per risorse di Azure** attiva/disattiva temporaneamente [elevare l'accesso](elevate-access-global-admin.md) per ottenere l'accesso alla sottoscrizione.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemi con gli amministratori del servizio o i coamministratori

- Se si verificano problemi con l'amministratore del servizio o coamministratore, vedere [gli amministratori delle sottoscrizioni di Azure aggiungere o modificare](../billing/billing-add-change-azure-subscription-administrator.md) e [ruoli amministratore sottoscrizione classico, i ruoli RBAC di Azure e Azure AD i ruoli di amministratore](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Accesso negato o errori di autorizzazione

- Se viene visualizzato l'errore relativo alle autorizzazioni "il client con id oggetto non è autorizzato a eseguire azioni sull'ambito (codice: AuthorizationFailed) "quando si tenta di creare una risorsa, verificare che si è connessi attualmente con un account utente che viene assegnato un ruolo che dispone dell'autorizzazione di scrittura per la risorsa nell'ambito selezionato. Ad esempio, per gestire le macchine virtuali in un gruppo di risorse, è necessario per il [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) ruolo nel gruppo di risorse (o ambito padre). Per un elenco delle autorizzazioni per ogni ruolo predefinito, vedere [ruoli predefiniti per le risorse di Azure](built-in-roles.md).
- Se viene visualizzato l'errore relativo alle autorizzazioni "Si è autorizzati a creare una richiesta di supporto" quando si prova a creare o aggiornare un ticket di supporto, controllare che si è connessi attualmente con un account utente che viene assegnato un ruolo che dispone di `Microsoft.Support/supportTickets/write` autorizzazione, ad esempio [Collaboratore richiesta di supporto](built-in-roles.md#support-request-contributor).

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

