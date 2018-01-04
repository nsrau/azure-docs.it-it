---
title: Panoramica del Controllo degli accessi in base al ruolo per le risorse di Azure PIM | Microsoft Docs
description: "Panoramica della funzionalità Controllo degli accessi in base al ruolo in PIM, incluse la terminologia e le notifiche"
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: 19715f800e7d8d40336d8e9fa3bf8073795dce5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="pim-for-azure-resources-preview"></a>PIM per Risorse di Azure (anteprima)

Con Azure Active Directory Privileged Identity Management (PIM) è ora possibile gestire, controllare e monitorare l'accesso a Risorse di Azure (anteprima) nell'organizzazione, incluse le sottoscrizioni, i gruppi di risorse e addirittura le macchine virtuali. Qualsiasi risorsa nel portale di Azure che sfrutta i vantaggi della funzionalità Controllo degli accessi in base al ruolo può usare tutte le straordinarie funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD PIM e alcune nuove funzionalità eccellenti che saranno presto disponibili nei ruoli di Azure AD. 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>PIM per Risorse di Azure (anteprima) aiuta gli amministratori delle risorse

- Verificare gli utenti e i gruppi a cui sono assegnati ruoli per le risorse di Azure amministrate
- Abilitare l'accesso su richiesta "Just-In-Time" per gestire le risorse come sottoscrizioni, gruppi di risorse e altro ancora
- Determinare la scadenza dell'accesso alle risorse assegnate a utenti/gruppi automaticamente con le nuove impostazioni di assegnazione con associazione temporale
- Assegnare l'accesso temporaneo alle risorse per attività rapide o pianificazioni su richiesta
- Imporre Multi-Factor Authentication per l'accesso alle risorse in qualsiasi ruolo predefinito o personalizzato 
- Ottenere report sull'accesso alle risorse e sulle attività correlate alle risorse durante la sessione attiva di un utente
- Ottenere avvisi quando a nuovi utenti o gruppi viene assegnato l'accesso alle risorse e quando vengono attivate assegnazioni idonee

Azure AD PIM consente di gestire i ruoli predefiniti delle risorse di Azure, oltre ai ruoli personalizzati del Controllo degli accessi in base al ruolo, tra cui:

- Proprietario
- Amministratore accessi utente
- Collaboratore
- Amministrazione della protezione
- Gestore della sicurezza e altro ancora

>[!NOTE]
Gli utenti o i membri di un gruppo assegnati ai ruoli Proprietario o Amministratore Accesso utenti e gli amministratori globali che abilitano la gestione delle sottoscrizioni in Azure AD sono amministratori di risorse. Questi amministratori possono assegnare ruoli, configurare le impostazioni dei ruoli e verificare l'accesso usando PIM per Risorse di Azure. Visualizzare l'elenco dei [ruoli predefiniti per le risorse di Azure](../role-based-access-built-in-roles.md).

## <a name="tasks"></a>Attività

PIM offre un comodo accesso per l'attivazione di ruoli, la visualizzazione delle attivazioni/richieste in sospeso, delle approvazioni in sospeso (per i [ruoli della directory di Azure AD](azure-ad-pim-approval-workflow.md)) e delle verifiche in sospeso in attesa della risposta dell'utente dalla sezione Attività nel menu di spostamento a sinistra.

Quando si accede a una delle voci del menu Attività dal punto di ingresso Panoramica, la visualizzazione risultante contiene i risultati per i ruoli della directory di Azure AD e per i ruoli delle risorse di Azure (anteprima). 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Ruoli personali contiene un elenco delle assegnazioni a ruoli attivi e idonei dell'utente per i ruoli della directory di Azure AD e per i ruoli di Risorse di Azure (anteprima).

## <a name="activate-roles"></a>Attivare i ruoli

L'attivazione dei ruoli per Risorse di Azure (anteprima) introduce una nuova esperienza che consente ai membri dei ruoli idonei di pianificare l'attivazione per una data/ora futura e selezionare una durata di attivazione specifica entro il valore massimo, configurato dagli amministratori. Altre informazioni sull'[attivazione dei ruoli di Azure AD sono disponibili qui](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Dal menu Attivazioni immettere la data e l'ora di inizio per attivare il ruolo. Ridurre facoltativamente la durata dell'attivazione, ovvero il periodo di tempo per cui il ruolo rimane attivo, e immettere una giustificazione, se necessario. Fare clic su Attiva.

Se la data e l'ora di inizio non vengono modificate, il ruolo verrà attivato entro pochi secondi. Nella pagina Ruoli personali verrà visualizzato un messaggio di avvio relativo a un ruolo accodato per l'attivazione. Fare clic sul pulsante Aggiorna per chiudere il messaggio.

![](media/azure-pim-resource-rbac/my-roles.png)

Se l'attivazione è pianificata per una data e un'ora future, la richiesta in sospeso verrà visualizzata nella scheda Richieste in sospeso nel menu di spostamento a sinistra. Se l'attivazione del ruolo non è più necessaria, l'utente può annullare la richiesta facendo clic sul pulsante Annulla disponibile nel lato destro della pagina.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Individuare e gestire le risorse di Azure

Per trovare e gestire ruoli per una risorsa di Azure, selezionare Risorse di Azure (anteprima) nella scheda Gestisci nel menu di spostamento a sinistra. Usare i filtri o la barra di ricerca nella parte superiore della pagina per trovare una risorsa.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Dashboard delle risorse

Il dashboard Visualizzazione amministratore include quattro componenti principali. Una rappresentazione grafica delle attivazioni dei ruoli delle risorse negli ultimi sette giorni. Come ambito per questi dati viene specificata la risorsa selezionata e vengono visualizzate attivazioni per i ruoli più comuni (Proprietario, Collaboratore, Amministratore Accesso utenti) e tutti i ruoli combinati.

Alla destra del grafico relativo alle attivazioni sono disponibili due grafici che mostrano la distribuzione delle assegnazioni di ruolo in base al tipo di assegnazione, per utenti e gruppi. La selezione di una sezione del grafico cambia il valore in una percentuale o viceversa.

![](media/azure-pim-resource-rbac/admin-view.png)

Sotto i grafici viene visualizzato il numero di utenti e gruppi con nuove assegnazioni di ruolo negli ultimi 30 giorni (a sinistra) e viene mostrato un elenco di ruoli ordinati in base a numero totale di assegnazioni (decrescente).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Gestire le assegnazioni di ruoli

Gli amministratori possono gestire le assegnazioni di ruolo selezionando Ruoli o Membri dal menu di spostamento a sinistra. La selezione di Ruoli consente agli amministratori di specificare un ruolo specifico come ambito per le rispettive attività di gestione, mentre l'opzione Membri mostra tutte le assegnazioni di ruolo per utenti e gruppi per la risorsa.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Se è presente un'attivazione di ruolo in sospeso, un banner di notifica viene visualizzato nella parte superiore della pagina quando si visualizza l'appartenenza.

## <a name="assign-roles"></a>Assegnare ruoli

Per assegnare un utente o un gruppo a un ruolo, selezionare il ruolo, se si usa la visualizzazione Ruoli, oppure fare clic su Aggiungi sulla barra delle azioni, se si usa la visualizzazione Membri.

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Se si aggiunge un utente o un gruppo dalla scheda Membri, sarà necessario selezionare un ruolo dal menu Aggiungi prima di poter selezionare un utente o un gruppo.

![](media/azure-pim-resource-rbac/select-role.png)

Scegliere un utente o un gruppo dalla directory.

![](media/azure-pim-resource-rbac/choose.png)

Scegliere il tipo di assegnazione appropriato dal menu a discesa. 

**Assegnazione Just-In-Time:** fornisce all'utente o ai membri del gruppo un accesso idoneo ma non permanente al ruolo per un periodo di tempo specificato o illimitato, se configurato nelle impostazioni del ruolo. 

**Assegnazione diretta:** non richiede all'utente o ai membri del gruppo di attivare l'assegnazione di ruolo ed è definito accesso permanente. Microsoft consiglia di usare l'assegnazione diretta per l'uso a breve termine, ad esempio per turni con reperibilità o attività che dipendono da un orario specifico, ovvero nei casi in cui l'accesso non sarà richiesto al termine dell'attività.

![](media/azure-pim-resource-rbac/membership-settings.png)

Una casella di controllo sotto l'elenco a discesa del tipo di assegnazione consente di specificare se l'assegnazione deve essere permanente, ovvero permanentemente idonea per l'attivazione dell'assegnazione Just-In-Time o permanentemente attiva per l'assegnazione diretta. Per indicare una durata specifica per l'assegnazione, deselezionare la casella di controllo e modificare i campi relativi a data e ora di inizio e/o fine.

>[!NOTE]
È possibile che la casella di controllo non sia modificabile se un altro amministratore ha specificato la durata massima dell'assegnazione per ogni tipo di assegnazione nelle impostazioni del ruolo.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visualizzare l'attivazione e l'attività di Risorse di Azure

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile esaminare l'attività di Risorse di Azure associata a un periodo di attivazione specifico, per gli utenti idonei. Selezionare prima di tutto un utente dalla visualizzazione Membri o dall'elenco di membri in un ruolo specifico. Il risultato mostra una visualizzazione grafica delle azioni dell'utente in Risorse di Azure in base alla data e le attivazioni di ruoli recenti nello stesso periodo di tempo.

![](media/azure-pim-resource-rbac/user-details.png)

Se si seleziona un'attivazione di ruolo specifica, verranno visualizzati i dettagli relativi all'attivazione del ruolo e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Modificare le assegnazioni esistenti

Per modificare le assegnazioni esistenti dalla visualizzazione dei dettagli relativi a utenti/gruppi, selezionare Modifica le impostazioni dalla barra delle azioni nella parte superiore della pagina. Modificare il tipo di assegnazione in assegnazione Just-In-Time o assegnazione diretta.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Verificare gli utenti autorizzati ad accedere in una sottoscrizione

Per verificare le assegnazioni di ruolo nella sottoscrizione, selezionare la scheda Membri dal menu di spostamento a sinistra oppure selezionare Ruoli e scegliere un ruolo specifico per verificarne i membri. 

Selezionare Verifica dalla barra delle azioni per visualizzare le verifiche di accesso esistenti e selezionare Aggiungi per creare una nuova verifica.

![](media/azure-pim-resource-rbac/owner.png)

[Altre informazioni sulle verifiche di accesso](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Le verifiche sono attualmente supportate solo per risorse di tipo Sottoscrizione.

## <a name="configure-role-settings"></a>Configurare le impostazioni dei ruoli

La configurazione delle impostazioni dei ruoli consente di definire le impostazioni predefinite applicate alle assegnazioni nell'ambiente PIM. Per definire queste impostazioni per la risorsa specifica, selezionare la scheda Impostazioni dei ruoli dal menu di spostamento a sinistra oppure il pulsante Impostazioni dei ruoli dalla barra delle azioni in qualsiasi ruolo per visualizzare le opzioni correnti.

Se si fa clic su Modifica sulla barra delle azioni nella parte superiore della pagina, sarà possibile modificare ogni impostazione.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Le modifiche alle impostazioni vengono registrate nella pagina Impostazioni dei ruoli, inclusi l'ultimo aggiornamento di data e ora e l'amministratore che ha modificato le impostazioni.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Controllo delle risorse

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per la risorsa. È possibile filtrare le informazioni usando una data predefinita o un intervallo personalizzato.
![](media/azure-pim-resource-rbac/last-day.png) L'opzione Controllo delle risorse fornisce anche l'accesso rapido alla visualizzazione dei dettagli delle attività di un utente. In questa visualizzazione tutte le azioni "Attiva il ruolo" sono collegamenti all'attività della risorsa specifica del richiedente.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration

PIM per Risorse di Azure semplifica l'uso delle procedure consigliate JEA (Just Enough Administration) con le assegnazioni di ruolo della risorsa. Gli utenti e i membri dei gruppi con assegnazioni nelle sottoscrizioni o nei gruppi di risorse di Azure possono attivare la rispettiva assegnazione di ruolo con un ambito ridotto. 

Dalla pagina di ricerca è possibile trovare la risorsa subordinata da gestire.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Selezionare Ruoli personali dal menu di spostamento a sinistra e scegliere il ruolo appropriato da attivare. Si noti che il tipo di assegnazione è Ereditato, poiché il ruolo è stato assegnato alla sottoscrizione, invece che al gruppo di risorse, come mostrato di seguito.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ruoli predefiniti per le risorse di Azure](../role-based-access-built-in-roles.md)
- Altre informazioni sull'[attivazione dei ruoli di Azure AD sono disponibili qui](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [Flussi di lavoro di approvazione PIM](azure-ad-pim-approval-workflow.md)
