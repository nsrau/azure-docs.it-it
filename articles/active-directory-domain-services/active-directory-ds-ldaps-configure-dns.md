---
title: Configurare il DNS per accedere a un dominio gestito tramite LDAPS su internet | Microsoft Docs
description: Configurare il DNS per accedere a un dominio gestito di Azure AD Domain Services tramite LDAPS su Internet
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 205e41727d9e6e360c34e7ac2a99b9ceec74361b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846167"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Configurare il DNS per accedere a un dominio gestito di Azure AD Domain Services tramite LDAP sicuro (LDAPS)

## <a name="before-you-begin"></a>Prima di iniziare
Completare l'[Attività 3: Abilitare l'accesso LDAP sicuro per il dominio gestito usando il portale di Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>Attività 4: Configurare il DNS per l'accesso al dominio gestito da Internet
> [!TIP]
> **Attività facoltativa** : ignorare questa attività di configurazione se non si intende accedere al dominio gestito usando l'accesso LDAP sicuro tramite Internet.
>
>

Prima di iniziare questa attività, completare la procedura descritta nell'[Attività 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Dopo aver attivato l'accesso LDAP sicuro tramite Internet, è necessario aggiornare il server DNS in modo che i computer client possano trovare il dominio gestito. Nel campo **INDIRIZZO IP ESTERNO PER L'ACCESSO LDAPS** della scheda **Proprietà** viene visualizzato un indirizzo IP esterno.

Configurare il provider DNS esterno in modo che il nome DNS del dominio gestito, ad esempio "ldaps.contoso100.com", punti a questo indirizzo IP esterno. Creare ad esempio la voce DNS seguente:

    ldaps.contoso100.com  -> 52.165.38.113

L'operazione è terminata. La procedura è terminata ed è possibile connettersi al dominio gestito usando l'accesso LDAP sicuro tramite Internet.

> [!WARNING]
> Tenere presente che i computer client devono considerare attendibile l'autorità emittente del certificato LDAPS per potersi connettere al dominio gestito tramite LDAPS. Se si usa un'autorità di certificazione pubblicamente attendibile, non sarà necessario eseguire alcuna operazione perché queste autorità sono considerate attendibili dai computer client. Se si usa un certificato autofirmato, installare la parte pubblica del certificato autofirmato nell'archivio certificati attendibili del computer client.
>
>

## <a name="next-step"></a>Passaggio successivo
[Attività 5: Eseguire l'associazione al dominio gestito e bloccare l'accesso LDAP sicuro](active-directory-ds-ldaps-bind-lockdown.md)
