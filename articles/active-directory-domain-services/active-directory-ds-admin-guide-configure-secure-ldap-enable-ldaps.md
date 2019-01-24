---
title: Abilitare l'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Abilitare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: e2831d6e6d95ba3d1162336ab8756ae40da99f94
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847068"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Abilitare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure Active Directory Domain Services

## <a name="before-you-begin"></a>Prima di iniziare
Completare l'[Attività 2: Esportare il certificato LDAP sicuro in un file PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Attività 3: Abilitare l'accesso LDAP sicuro per il dominio gestito usando il portale di Azure
Per abilitare l'accesso LDAP sicuro, seguire questa procedura di configurazione:

1. Passare al **[portale di Azure](https://portal.azure.com)**.

2. Cercare "servizi di dominio" nella casella di ricerca **Cerca risorse**. Selezionare **Azure AD Domain Services** dai risultati della ricerca. Il dominio gestito viene elencato nella pagina **Azure AD Domain Services**.

    ![Trovare il dominio gestito di cui viene effettuato il provisioning](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Fare clic sul nome del dominio gestito (ad esempio, "contoso100.com") per visualizzare altre informazioni sul dominio.

    ![Domain Services - Stato del provisioning](./media/getting-started/domain-services-provisioning-state.png)

3. Fare clic su **LDAP sicuro** nel riquadro di spostamento.

    ![Domain Services - Pagina LDAP sicuro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito è disabilitato. Impostare **LDAP sicuro** su **Abilita**.

    ![Abilitare LDAP sicuro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito su Internet è disabilitato. Impostare **Abilita accesso LDAP sicuro tramite Internet** su **Abilita**, se necessario.

    > [!WARNING]
    > Quando si abilita l'accesso LDAP sicuro tramite Internet, il dominio è soggetto ad attacchi di forza bruta sulle password tramite Internet. È consigliabile pertanto configurare un gruppo di sicurezza di rete per bloccare l'accesso agli intervalli di indirizzi IP di origine necessari. Vedere le istruzioni in [Blocco dell'accesso LDAPS al dominio gestito su Internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Fare clic sull'icona della cartella accanto a **File PFX con certificato LDAP sicuro**. Specificare il percorso del file PFX con il certificato per l'accesso LDAP sicuro al dominio gestito.

7. Specificare la **password per decrittografare il file PFX**. Indicare la stessa password usata per l'esportazione del certificato nel file PFX.

8. Al termine, fare clic sul pulsante **Salva**.

9. Viene visualizzata una notifica che informa che è in corso la configurazione dell'accesso LDAP sicuro per il dominio gestito. Finché questa operazione non viene completata, non è possibile modificare altre impostazioni per il dominio.

    ![Configurazione dell'accesso LDAP sicuro per il dominio gestito](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Per abilitare l'accesso LDAP sicuro per il dominio gestito saranno necessari circa 10-15 minuti. Se il certificato LDAP sicuro fornito non soddisfa i criteri necessari, l'accesso LDAP sicuro non viene abilitato per la directory e viene visualizzato un errore. Ad esempio, il nome di dominio non è corretto, il certificato è già scaduto o scadrà presto. In questo caso, riprovare con un certificato valido.
>
>

## <a name="next-step"></a>Passaggio successivo
[Attività 4: Configurare il DNS per l'accesso al dominio gestito da Internet](active-directory-ds-ldaps-configure-dns.md)
