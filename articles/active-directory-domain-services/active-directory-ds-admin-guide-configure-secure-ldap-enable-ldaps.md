---
title: Configurare l'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Servizi di dominio Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3b19f078b0d6dc3e02d951014056406fd1b099a8
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services

## <a name="before-you-begin"></a>Prima di iniziare
Assicurarsi di aver completato l'[Attività 2: Esportare il certificato LDAP sicuro in un file PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).

Scegliere se usare l'esperienza di anteprima del portale di Azure o il portale di Azure classico per completare questa attività.
> [!div class="op_single_selector"]
> * **Portale di Azure (anteprima)**: [Abilitare l'accesso LDAP sicuro tramite il portale di Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Portale di Azure classico**: [Abilitare l'accesso LDAP sicuro usando il portale di Azure classico](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview"></a>Attività 3: Abilitare l'accesso LDAP sicuro per il dominio gestito usando il portale di Azure (anteprima)
Per abilitare l'accesso LDAP sicuro, seguire questa procedura di configurazione:

1. Passare al **[portale di Azure](https://portal.azure.com)**.

2. Cercare "servizi di dominio" nella casella di ricerca **Cerca risorse**. Selezionare **Azure AD Domain Services** dai risultati della ricerca. Nel pannello **Azure AD Domain Services** è indicato il dominio gestito.

    ![Trovare il dominio gestito di cui viene effettuato il provisioning](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Fare clic sul nome del dominio gestito (ad esempio, "contoso100.com") per visualizzare altre informazioni sul dominio.

    ![Domain Services - Stato del provisioning](./media/getting-started/domain-services-provisioning-state.png)

3. Fare clic su **LDAP sicuro** nel riquadro di spostamento.

    ![Domain Services - Pannello LDAP sicuro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito è disabilitato. Impostare **LDAP sicuro** su **Abilita**.

    ![Abilitare LDAP sicuro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Per impostazione predefinita, l'accesso LDAP sicuro al dominio gestito su Internet è disabilitato. Impostare **Abilita accesso LDAP sicuro tramite Internet** su **Abilita**, se necessario. 

6. Fare clic sull'icona della cartella accanto a **File PFX con certificato LDAP sicuro**. Specificare il percorso del file PFX con il certificato per l'accesso LDAP sicuro al dominio gestito.

7. Specificare la **password per decrittografare il file PFX**. Indicare la stessa password usata per l'esportazione del certificato nel file PFX.

8. Al termine, fare clic sul pulsante **Salva** .

9. Viene visualizzata una notifica che informa che è in corso la configurazione dell'accesso LDAP sicuro per il dominio gestito. Finché questa operazione non viene completata, non è possibile modificare altre impostazioni per il dominio.

    ![Configurazione dell'accesso LDAP sicuro per il dominio gestito](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Per abilitare l'accesso LDAP sicuro per il dominio gestito saranno necessari circa 10-15 minuti. Se il certificato LDAP sicuro fornito non soddisfa i criteri necessari, l'accesso LDAP sicuro non viene abilitato per la directory e viene visualizzato un errore. Ad esempio, il nome di dominio non è corretto, il certificato è già scaduto o scadrà presto. In questo caso, riprovare con un certificato valido.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Attività 4: Configurare DNS per l'accesso al dominio gestito da Internet
> [!NOTE]
> **Attività facoltativa** : ignorare questa attività di configurazione se non si intende accedere al dominio gestito usando l'accesso LDAP sicuro tramite Internet.
>
>

Prima di iniziare questa attività, assicurarsi di aver completato la procedura descritta nell' [attività 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Dopo aver attivato l'accesso LDAP sicuro tramite Internet al dominio gestito, è necessario aggiornare il server DNS in modo che i computer client possano trovare il dominio gestito. Al termine dell'attività 3, nel campo **INDIRIZZO IP ESTERNO PER L'ACCESSO LDAPS** del pannello **Proprietà** viene visualizzato un indirizzo IP esterno.

Configurare il provider DNS esterno in modo che il nome DNS del dominio gestito, ad esempio "ldaps.contoso100.com", punti a questo indirizzo IP esterno. Nell'esempio è necessario creare la voce DNS seguente:

    ldaps.contoso100.com  -> 52.165.38.113

La procedura è terminata ed è possibile connettersi al dominio gestito usando l'accesso LDAP sicuro tramite Internet.

> [!WARNING]
> Tenere presente che i computer client devono considerare attendibile l'autorità emittente del certificato LDAPS per potersi connettere al dominio gestito tramite LDAPS. Se si usa un'autorità di certificazione pubblicamente attendibile, non sarà necessario eseguire alcuna operazione perché queste autorità sono considerate attendibili dai computer client. Se si usa un certificato autofirmato, installare la parte pubblica del certificato autofirmato nell'archivio certificati attendibili del computer client.
>
>


## <a name="task-5---lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Attività 5: Bloccare l'accesso LDAPS al dominio gestito su Internet
> [!NOTE]
> **Attività facoltativa**: ignorare questa attività di configurazione se non è stato abilitato l'accesso LDAPS al dominio gestito su Internet.
>
>

Prima di iniziare questa attività, assicurarsi di aver completato la procedura descritta nell' [attività 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

L'esposizione del dominio gestito per l'accesso LDAPS su Internet rappresenta una minaccia alla sicurezza. Il dominio gestito è raggiungibile da Internet presso la porta usata per l'accesso LDAP sicuro, ovvero la porta 636. Di conseguenza, è possibile scegliere di limitare l'accesso al dominio gestito a determinati indirizzi IP noti. Per migliorare la sicurezza, creare un gruppo di sicurezza di rete (NSG) e associarlo alla subnet in cui è stato abilitato Azure AD Domain Services.

La tabella seguente illustra un esempio di gruppo di sicurezza di rete che è possibile configurare per bloccare l'accesso LDAP sicuro su Internet. Il gruppo di sicurezza di rete contiene alcune regole che consentono l'accesso LDAPS in ingresso sulla porta TCP 636 solo da un set specificato di indirizzi IP. La regola predefinita "DenyAll" si applica a tutto il traffico in ingresso da Internet. La regola del gruppo di sicurezza di rete per consentire l'accesso LDAPS su Internet da indirizzi IP specificati ha una priorità superiore rispetto alla regola DenyAll del gruppo di sicurezza di rete.

![Gruppo di sicurezza di rete di esempio per proteggere l'accesso LDAPS su Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Altre informazioni** - [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

<br>

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administer Group Policy on an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-administer-group-policy.md) (Amministrare i Criteri di gruppo in un dominio gestito da Azure AD Domain Services)
* [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
* [Creare un gruppo di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

