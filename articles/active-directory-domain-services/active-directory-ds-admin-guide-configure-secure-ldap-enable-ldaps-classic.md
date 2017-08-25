---
title: Configurare l'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Servizi di dominio Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services

## <a name="before-you-begin"></a>Prima di iniziare
Assicurarsi di aver completato l'[Attività 2: Esportare il certificato LDAP sicuro in un file PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).

Scegliere se usare l'esperienza di anteprima del portale di Azure o il portale di Azure classico per completare questa attività.
> [!div class="op_single_selector"]
> * **Portale di Azure (anteprima)**: [Abilitare l'accesso LDAP sicuro tramite il portale di Azure](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * **Portale di Azure classico**: [Abilitare l'accesso LDAP sicuro tramite il portale di Azure classico](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>Attività 3: Abilitare l'accesso LDAP sicuro per il dominio gestito tramite il portale di Azure classico
Per abilitare l'accesso LDAP sicuro, seguire questa procedura di configurazione:

1. Passare al **[portale di Azure classico](https://manage.windowsazure.com)**.
2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
3. Selezionare la directory di Azure AD, detta anche "tenant", per cui sono stati abilitati i Servizi di dominio Azure AD.

    ![Selezionare una directory di Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Fare clic sulla scheda **Configure** .

    ![Scheda Configura della directory](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Scorrere fino alla sezione **Servizi di dominio**. Viene visualizzata l'opzione **Accesso LDAP sicuro (LDAPS)** , come illustrato nella schermata seguente:

    ![Sezione per la configurazione di Servizi di dominio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. Fare clic sul pulsante **Configura certificato...** per visualizzare la finestra di dialogo **Configura certificato per accesso LDAP sicuro**.

    ![Configura certificato per accesso LDAP sicuro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. Fare clic sull'icona cartella che segue il **File PFX con certificato** per specificare il file PFX contenente il certificato da usare per l'accesso LDAP sicuro al dominio gestito. Immettere anche la password specificata durante l'esportazione del certificato nel file PFX. Quindi fare clic sul pulsante Fine nella parte inferiore.

    ![Specificare un file PFX di accesso LDAP sicuro e la password](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. La sezione **Servizi di dominio** della scheda **Configura** verrà disattivata e rimarrà nello stato **In sospeso...** per alcuni minuti. Durante questo periodo, viene verificata l'accuratezza del certificato LDAPS e viene configurato l'accesso LDAP sicuro per il dominio gestito.

    ![LDAP sicuro - In sospeso](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > Per abilitare l'accesso LDAP sicuro per il dominio gestito saranno necessari circa 10-15 minuti. Se il certificato LDAP sicuro fornito non soddisfa i criteri necessari, l'accesso LDAP sicuro non viene abilitato per la directory e viene visualizzato un errore. Ad esempio, il nome di dominio non è corretto, il certificato è già scaduto o scadrà presto.
   >
   >

9. Il messaggio **In sospeso...** scomparirà non appena l'accesso LDAP sicuro per il dominio gestito sarà abilitato. Verrà visualizzata l'identificazione personale del certificato visualizzato.

    ![LDAP sicuro abilitato](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Attività 4: Abilitare l'accesso LDAP sicuro su Internet
**Attività facoltativa** : ignorare questa attività di configurazione se non si intende accedere al dominio gestito usando l'accesso LDAP sicuro tramite Internet.

Prima di iniziare questa attività, assicurarsi di aver completato la procedura descritta nell' [attività 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal).

1. Nella sezione **Servizi di dominio** della pagina **Configura** viene visualizzata l'opzione **Abilita accesso LDAP sicuro tramite Internet**. Il valore predefinito di questa impostazione è **No** , perché l'accesso LDAP sicuro al dominio gestito tramite Internet è disabilitato per impostazione predefinita.

    ![LDAP sicuro abilitato](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. Impostare **Abilita accesso LDAP sicuro tramite Internet** su **Sì**. Fare clic sul pulsante **Salva** nel riquadro inferiore.
    ![LDAP sicuro abilitato](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. La sezione **Servizi di dominio** della scheda **Configura** verrà disattivata e rimarrà nello stato **In sospeso...** per alcuni minuti. Dopo un po' di tempo verrà abilitato l'accesso LDAP sicuro tramite Internet al dominio gestito.

    ![LDAP sicuro - In sospeso](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > Per abilitare l'accesso LDAP sicuro tramite Internet al dominio gestito sono necessari circa 10 minuti.
   >
   >
4. Il messaggio **In sospeso...** scomparirà non appena l'accesso LDAP sicuro al dominio gestito tramite Internet sarà abilitato. Nel campo **Indirizzo IP esterno per l'accesso LDAPS**viene visualizzato l'indirizzo IP esterno che può essere usato per accedere alla directory tramite accesso LDAP sicuro.

    ![LDAP sicuro abilitato](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Attività 5: Configurare il server DNS per l'accesso al dominio gestito da Internet
**Attività facoltativa** : ignorare questa attività di configurazione se non si intende accedere al dominio gestito usando l'accesso LDAP sicuro tramite Internet.

Prima di iniziare questa attività, assicurarsi di aver completato la procedura descritta nell' [attività 4](#task-4---enable-secure-ldap-access-over-the-internet).

Dopo aver attivato l'accesso LDAP sicuro tramite Internet al dominio gestito, è necessario aggiornare il server DNS in modo che i computer client possano trovare il dominio gestito. Al termine dell'attività 4 nel campo **INDIRIZZO IP ESTERNO PER L'ACCESSO LDAPS** della scheda **Configura** viene visualizzato un indirizzo IP esterno.

Configurare il provider DNS esterno in modo che il nome DNS del dominio gestito, ad esempio "ldaps.contoso100.com", punti a questo indirizzo IP esterno. Nell'esempio è necessario creare la voce DNS seguente:

    ldaps.contoso100.com  -> 52.165.38.113

La procedura è terminata ed è possibile connettersi al dominio gestito usando l'accesso LDAP sicuro tramite Internet.

> [!WARNING]
> Tenere presente che i computer client devono considerare attendibile l'autorità emittente del certificato LDAPS per potersi connettere al dominio gestito tramite LDAPS. Un'autorità di certificazione aziendale o un'autorità di certificazione pubblicamente attendibile sono considerate attendibili dai computer client. Se si usa un certificato autofirmato è necessario installare la parte pubblica del certificato autofirmato nell'archivio certificati attendibili del computer client.
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>Blocco dell'accesso LDAPS al dominio gestito su Internet
> [!NOTE]
> **Attività facoltativa**: ignorare questa attività di configurazione se non si ha l'accesso LDAPS abilitato al dominio gestito su Internet.
>
>

Prima di iniziare questa attività, assicurarsi di aver completato la procedura descritta nell' [attività 4](#task-4---enable-secure-ldap-access-over-the-internet).

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

