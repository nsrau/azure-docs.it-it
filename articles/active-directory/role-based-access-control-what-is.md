---
title: 'Gestire accessi e autorizzazioni con il controllo degli accessi in base al ruolo: Controllo degli accessi in base al ruolo di Azure | Microsoft Docs'
description: Introduzione alla gestione degli accessi con il Controllo degli accessi in base al ruolo di Azure nel portale di Azure. Usare le assegnazioni di ruolo per assegnare autorizzazioni nella directory.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 06daecfe8cd61cbb3ab9d2307acb07e30270d921
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Introduzione al controllo degli accessi in base al ruolo nel portale di Azure
Le aziende orientate sulla sicurezza devono concedere ai propri dipendenti la quantità esatta di autorizzazioni di cui necessitano. un numero eccessivo di autorizzazioni può esporre un account agli attacchi. Un numero di autorizzazioni insufficiente ostacola l'efficienza del lavoro dei dipendenti. Il Controllo degli accessi in base al ruolo di Azure (RBAC) aiuta a risolvere questo problema offrendo la gestione specifica degli accessi per Azure.

Usando il Controllo degli accessi in base al ruolo, è possibile separare i compiti all'interno del team e concedere agli utenti solo la quantità di accesso di cui hanno bisogno per svolgere il proprio lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni. Ad esempio, usare il Controllo degli accessi in base al ruolo per consentire a un dipendente di gestire le macchine virtuali in una sottoscrizione, mentre un altro utente può gestire i database SQL della stessa sottoscrizione.

## <a name="basics-of-access-management-in-azure"></a>Nozioni fondamentali della gestione degli accessi in Azure
Ogni sottoscrizione di Azure è associata a una directory di Azure Active Directory (AD). Gli utenti, i gruppi e le applicazioni da tale directory possono gestire le risorse nella sottoscrizione di Azure. Assegnare questi diritti di accesso tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

Concedere l'accesso assegnando i ruoli Controllo degli accessi in base al ruolo appropriati a utenti, gruppi e applicazioni in un ambito specifico. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa. Un ruolo assegnato a un ambito padre concede anche l'accesso agli elementi figlio contenuti al suo interno. Ad esempio, un utente con accesso a un gruppo di risorse può gestire tutte le risorse che contiene, come siti Web, macchine virtuali e subnet.

![Diagramma della relazione tra gli elementi di Azure Active Directory](./media/role-based-access-control-what-is/rbac_aad.png)

Il ruolo RBAC assegnato determina quali risorse l'utente, il gruppo o l'applicazione può gestire in tale ambito.

## <a name="built-in-roles"></a>Ruoli predefiniti
Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa:

* **Proprietario** ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.
* **Collaboratore** può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti.
* **Lettore** può visualizzare le risorse di Azure esistenti.

Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire macchine virtuali, ma non concede l'accesso alla rete virtuale o alla subnet a cui la macchina virtuale si connette. 

[Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md) elenca i ruoli disponibili in Azure. Specifica le operazioni e l'ambito che ogni ruolo predefinito concede agli utenti. Per definire ruoli personalizzati per un maggiore controllo, vedere come creare [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Gerarchia delle risorse ed ereditarietà dell'accesso
* Ogni **sottoscrizione** in Azure appartiene a una sola directory.
* Ogni **gruppo di risorse** appartiene a una sola sottoscrizione.
* Ogni **risorsa** appartiene a un solo gruppo di risorse.

L'accesso che si concede all'ambito padre viene ereditato dall'ambito figlio. Ad esempio:

* Si assegna il ruolo Lettore a un gruppo di Azure AD nell'ambito della sottoscrizione. I membri di tale gruppo possono visualizzare ogni gruppo di risorse e ogni risorsa nella sottoscrizione.
* Si assegna il ruolo Collaboratore a un'applicazione nell'ambito del gruppo di risorse. Può gestire risorse di tutti i tipi in tale gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Controllo degli accessi in base al ruolo di Azure e Amministratore sottoscrizione classico
I ruoli Amministratore sottoscrizione classico e Coamministratore hanno l'accesso completo alla sottoscrizione di Azure. Possono gestire le risorse con il [portale di Azure](https://portal.azure.com) e le API di Azure Resource Manager o il [portale di Azure classico](https://manage.windowsazure.com) e il modello di distribuzione classica di Azure. Nel modello RBAC, agli amministratori classici viene assegnato il ruolo di Proprietario nell'ambito della sottoscrizione.

Solo il portale di Azure e le nuove API di Azure Resource Manager supportano il Controllo degli accessi in base al ruolo di Azure. Gli utenti e le applicazioni a cui vengono assegnati ruoli Controllo degli accessi in base al ruolo non possono usare il portale di gestione classico e il modello di distribuzione classica di Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorizzazioni per le operazioni di gestione e per le operazioni sui dati
Il Controllo degli accessi in base al ruolo di Azure supporta solo operazioni di gestione delle risorse di Azure nel portale di Azure e nelle API di Azure Resource Manager. Non tutte le operazioni a livello di dati svolte sulle risorse di Azure possono essere autorizzate tramite RBAC. È possibile ad esempio autorizzare un utente a gestire gli account di archiviazione, ma non i BLOB e le tabelle all'interno di un account di archiviazione. Analogamente, può essere gestito un database SQL, ma non le tabelle in esso contenute.

## <a name="next-steps"></a>Passaggi successivi
* Introduzione al [Controllo degli accessi in base al ruolo di Azure nel portale di Azure](role-based-access-control-configure.md).
* Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md)
* Definire i [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md)

