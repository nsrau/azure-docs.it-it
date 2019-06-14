---
title: Eseguire il binding a un dominio gestito di Azure Active Directory Domain Services tramite l'accesso LDAP sicuro (LDAPS) | Microsoft Docs
description: Eseguire il binding a un dominio gestito di Azure Active Directory Domain Services tramite l'accesso LDAP sicuro (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 47c9553b4191fe6dbae8d92d75dfae83f191a063
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234862"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Eseguire il binding a un dominio gestito di Azure Active Directory Domain Services tramite l'accesso LDAP sicuro (LDAPS)

## <a name="before-you-begin"></a>Prima di iniziare
Completare l'[Attività 4: Configurare il DNS per l'accesso al dominio gestito da Internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Attività 5: Eseguire il binding al dominio gestito mediante LDAP usando LDP.exe
È possibile usare lo strumento LDP.exe, incluso nel pacchetto di Strumenti di amministrazione remota del server, per eseguire il binding e la ricerca su LDAP.

Innanzitutto, aprire LDP e connettersi al dominio gestito. Fare clic su **Connessione**, quindi fare clic su **Connetti...**  nel menu. Specificare il nome di dominio DNS del dominio gestito. Specificare la porta da usare per le connessioni. Per le connessioni LDAP, usare la porta 389. Per le connessioni LDAPS, usare la porta 636. Fare clic sul pulsante **OK** per connettersi al dominio gestito.

Eseguire quindi il binding al dominio gestito. Fare clic su **Connessione**, quindi fare clic su **Associazione...** nel menu. Fornire le credenziali di un account utente appartenente al gruppo "AAD DC Administrators".

Selezionare **Visualizza**, quindi selezionare **Albero** nel menu. Lasciare vuoto il campo Nome distinto di base e fare clic su OK. Passare al contenitore in cui si desidera eseguire la ricerca, fare clic su di esso con il pulsante destro, quindi selezionare Cerca.

> [!TIP]
> - Utenti e gruppi sincronizzati da Azure AD vengono archiviati nell'unità organizzativa **AADDC Users** (Utenti AADDC). Il percorso di ricerca per questa unità organizzativa è simile a ```OU=AADDC Users,DC=CONTOSO100,DC=COM```.
> - Gli account computer per tutti i computer aggiunti al dominio gestito sono archiviati nell'unità organizzativa **AADDC Computers**. Il percorso di ricerca per questa unità organizzativa è simile a ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```.
>
>

Altre informazioni - [Nozioni di base sulle query LDAP](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Attività 6: Bloccare l'accesso LDAP sicuro al dominio gestito su Internet
> [!NOTE]
> Ignorare questa attività di configurazione, se non è stato abilitato l'accesso LDAPS al dominio gestito su Internet.
>
>

Prima di iniziare questa attività, completare la procedura descritta nell'[Attività 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Quando si abilita l'accesso LDAPS al dominio gestito su Internet, viene creata una minaccia della sicurezza. Il dominio gestito è raggiungibile da Internet presso la porta usata per l'accesso LDAP sicuro, ovvero la porta 636. È possibile scegliere di limitare l'accesso al dominio gestito a determinati indirizzi IP noti. Creare un gruppo di sicurezza di rete e associarlo alla subnet in cui è stato abilitato Azure Active Directory Domain Services.

Il gruppo di sicurezza di rete di esempio, nella tabella seguente, blocca l'accesso LDAP sicuro su Internet. Le regole nel gruppo di sicurezza di rete consentono l'accesso LDAP sicuro in ingresso sulla porta TCP 636 solo da un set specificato di indirizzi IP. La regola predefinita "DenyAll" si applica a tutto il traffico in ingresso da Internet. La regola del gruppo di sicurezza di rete per consentire l'accesso LDAPS su Internet da indirizzi IP specificati ha una priorità superiore rispetto alla regola DenyAll del gruppo di sicurezza di rete.

![Gruppo di sicurezza di rete di esempio per proteggere l'accesso LDAPS su Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Altre informazioni** - [Gruppi di sicurezza di rete](../virtual-network/security-overview.md)


## <a name="related-content"></a>Contenuti correlati
* [Servizi di dominio Azure AD: introduzione](create-instance.md)
* [Gestire un dominio di Azure AD Domain Services](manage-domain.md)
* [Nozioni di base sulle query LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Gestire i criteri di gruppo per Azure Active Directory Domain Services](manage-group-policy.md)
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md)
* [Creare un gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Passaggio successivo
[Risolvere i problemi relativi all'accesso LDAP sicuro per un dominio gestito](tshoot-ldaps.md)
