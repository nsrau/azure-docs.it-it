---
title: Passaggi successivi per la gestione dell&quot;accesso mediante i gruppi | Documentazione Microsoft
description: Procedura avanzata per la gestione dei gruppi di sicurezza e l&quot;uso di questi gruppi per gestire l&quot;accesso a una risorsa.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c4920107b5d05716021bcef80d3c52c25f6eade
ms.contentlocale: it-it
ms.lasthandoff: 12/28/2016


---
<a id="managing-owners-for-a-group" class="xliff"></a>

# Gestione dei proprietari di un gruppo
Quando il proprietario delle risorse ha assegnato l'accesso a una risorsa a un gruppo di Azure AD, l'appartenenza al gruppo viene gestita dal proprietario del gruppo. Il proprietario della risorsa delega in modo efficace l'autorizzazione ad assegnare agli utenti la loro risorsa al proprietario del gruppo.

<a id="assigning-group-ownership" class="xliff"></a>

## Assegnare la proprietà del gruppo
**Per Aggiungere un proprietario a un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com)selezionare **Active Directory**e aprire la directory dell'organizzazione.
2. Selezionare la scheda **Gruppi** e aprire il gruppo a cui si desiderano aggiungere proprietari.
3. Selezionare **Aggiungi proprietari**.
4. Nella pagina **Aggiungi proprietari** selezionare l'utente che si desidera aggiungere come proprietario del gruppo e verificare che questo nome venga aggiunto al riquadro **Selezionato**.

**Per rimuovere un proprietario da un gruppo**

1. Nel [portale di Azure classico](https://manage.windowsazure.com)selezionare **Active Directory**e aprire la directory dell'organizzazione.
2. Selezionare la scheda **Gruppi** e aprire il gruppo da cui si desidera rimuovere un proprietario.
3. Selezionare la scheda **Proprietari** .
4. Selezionare il proprietario da rimuovere dal gruppo e quindi scegliere **Rimuovi**.

<a id="additional-information" class="xliff"></a>

## Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Informazioni su Azure Active Directory](active-directory-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)


