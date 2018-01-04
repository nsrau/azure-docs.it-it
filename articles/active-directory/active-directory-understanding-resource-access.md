---
title: Informazioni sull'accesso alle risorse in Azure | Microsoft Docs
description: Questo argomento illustra i concetti relativi all'uso di amministratori della sottoscrizione per controllare l'accesso alle risorse nel portale di Azure completo.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.custom: it-pro;
ms.openlocfilehash: ad6658aaed55801ac8f6a39a721fb7469892303d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="understanding-resource-access-in-azure"></a>Informazioni sull'accesso alle risorse in Azure

La fatturazione costituisce il punto di partenza per il controllo di accesso in Azure. Il proprietario di un account Azure, a cui si accede tramite il [Centro account di Azure](https://account.windowsazure.com/subscriptions), è l'amministratore account. Le sottoscrizioni sono contenitori per la fatturazione, ma vengono usate anche come limiti per la sicurezza: ogni sottoscrizione ha un Amministratore del servizio (SA) che può aggiungere, rimuovere e modificare le risorse di Azure nella sottoscrizione tramite il [portale di Azure](https://portal.azure.com/). L'Amministratore del servizio predefinito di una nuova sottoscrizione è l'Amministratore dell'account, ma quest'ultimo può modificare l'Amministratore del servizio nel Centro account di Azure.

<br><br>![Account Azure][1]

Le sottoscrizioni dispongono anche di un'associazione a una directory. Una directory definisce un set di utenti. Può trattarsi di utenti di lavoro o dell'istituto di istruzione che ha creato la directory o possono essere utenti guest esterno. Le sottoscrizioni sono accessibili da un subset di tali utenti di directory che sono stati assegnati come servizio amministratore (SA) o coamministratore (CA); l'unica eccezione è che, per motivi di compatibilità, gli account Microsoft (precedentemente Windows Live ID) possono essere assegnati come SA o CA senza essere presenti nella directory.

<br><br>![Controllo di accesso in Azure][2]

Funzionalità disponibili nel portale di Azure consentono associazioni di sicurezza che hanno eseguito l'accesso utilizzando un Account Microsoft di modificare la directory a cui è associata una sottoscrizione. Questa operazione ha implicazioni sul controllo di accesso di tale sottoscrizione.

<br><br>![Flusso di accesso utente semplice][3]

Nel caso più semplice un'organizzazione, ad esempio Contoso, imporrà la fatturazione e il controllo di accesso nello stesso set di sottoscrizioni. Questo significa che la directory è associata alle sottoscrizioni di proprietà di un singolo account Azure. Una volta eseguito l'accesso al portale di Azure, gli utenti visualizzeranno due raccolte di risorse (in arancione nella figura precedente):

* Directory in cui è presente l'account utente (originato o aggiunto come entità esterna). Si noti che la directory usata per l'accesso non è rilevante ai fini di questo calcolo, di conseguenza verranno sempre visualizzate tutte le directory, indipendentemente da quella usata per l'accesso.
* Risorse che fanno parte delle sottoscrizioni associate alla directory usata per l'accesso E a cui l'utente può accedere (perché è amministratore dei servizi o coamministratore).

<br><br>![Utente con più sottoscrizioni e directory][4]

Gli utenti con le sottoscrizioni in più directory hanno la possibilità di cambiare il contesto corrente del portale di Azure tramite il filtro di sottoscrizione. Questo comporta un accesso separato a una directory diversa, operazione che viene eseguita automaticamente tramite l'accesso Single Sign-On.

Operazioni quali lo spostamento di risorse tra sottoscrizioni possono risultare più difficoltose a causa della visualizzazione a directory singola delle sottoscrizioni. Per eseguire il trasferimento di risorse, potrebbe essere necessario usare prima il comando **Modifica directory** nella pagina Sottoscrizioni di **Impostazioni** per associare le sottoscrizioni alla stessa directory.

## <a name="next-steps"></a>Fasi successive
* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png
