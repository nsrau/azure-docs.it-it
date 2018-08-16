---
title: 'Azure Active Directory Identity Protection: come sbloccare gli utenti | Microsoft Docs'
description: Informazioni su come sbloccare gli utenti bloccati da un criterio di Azure Active Directory Identity Protection.
services: active-directory
keywords: Azure Active Directory Identity Protection, sbloccare gli utenti
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1bfc70680ecef2ee4fe162f81aac71430c773740
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005079"
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory Identity Protection: come sbloccare gli utenti
Azure Active Directory Identity Protection consente di configurare criteri per bloccare gli utenti se vengono rispettate le condizioni configurate. Un utente bloccato contatta in genere l'help desk per essere sbloccato. Questo articolo illustra la procedura da eseguire per sbloccare un utente bloccato.

## <a name="determine-the-reason-for-blocking"></a>Determinare il motivo del blocco
Il primo passaggio per sbloccare un utente consiste nel determinare il tipo di criterio che ha bloccato l'utente, perché i passaggi successivi dipendono da questa informazione.
Azure Active Directory Identity Protection consente di bloccare un utente mediante un criterio di rischio di accesso o un criterio di rischio.

È possibile ottenere il tipo di criterio che ha bloccato un utente basandosi sull'intestazione della finestra di dialogo visualizzata all'utente durante il tentativo di accesso:

| Criterio | Finestra di dialogo utente |
| --- | --- |
| Rischio di accesso |![Accesso bloccato](./media/howto-unblock-user/02.png) |
| Rischio utente |![Account bloccato](./media/howto-unblock-user/104.png) |

Un utente bloccato da:

* Un criterio di rischio di accesso è noto anche come accesso sospetto
* Un criterio di rischio utente è noto anche come account a rischio

## <a name="unblocking-suspicious-sign-ins"></a>Sblocco di accessi sospetti
Per sbloccare un accesso sospetto, sono disponibili le opzioni seguenti:

1. **Accesso da una località o un dispositivo familiare**: un motivo comune del blocco di accessi sospetti è costituito da tentativi di accesso da località o dispositivi non familiari. Gli utenti possono determinare rapidamente se questo è il motivo del blocco provando ad accedere da una località o un dispositivo familiare.
2. **Esclusione dal criterio** : se si ritiene che la configurazione corrente del criterio di accesso provochi problemi agli utenti, è possibile escludere gli utenti dal criterio. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Sblocco di account a rischio
Per sbloccare un account a rischio, sono disponibili le opzioni seguenti:

1. **Reimpostazione della password** : è possibile reimpostare la password dell'utente. Per altre informazioni, vedere [Reimpostazione manuale della password di protezione](overview.md#manual-secure-password-reset).
2. **Eliminazione di tutti gli eventi di rischio** : il criterio di rischio utente blocca un utente se è stato raggiunto il livello di rischio utente configurato per il blocco dell'accesso. È possibile ridurre il livello di rischio di un utente chiudendo manualmente gli eventi di rischio segnalati. Per altre informazioni, vedere [Chiusura manuale degli eventi di rischio](overview.md#closing-risk-events-manually).
3. **Esclusione dal criterio** : se si ritiene che la configurazione corrente del criterio di accesso provochi problemi agli utenti, è possibile escludere gli utenti dal criterio. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Disabilitazione del criterio** : se si ritiene che la configurazione del criterio provochi problemi per tutti gli utenti, è possibile disabilitare il criterio. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Passaggi successivi
 
Per altre informazioni su Azure AD Identity Protection, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
