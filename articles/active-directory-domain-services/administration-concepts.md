---
title: Management concepts for Azure AD Domain Services | Microsoft Docs
description: Learn about how to administer an Azure Active Directory Domain Services managed domain and the behavior of user accounts and passwords
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481017"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Management concepts for user accounts, passwords, and administration in Azure Active Directory Domain Services

When you create and run an Azure Active Directory Domain Services (AD DS) managed domain, there are some differences in behavior compared to a traditional on-premises AD DS environment. You use the same administrative tools in Azure AD DS as a self-managed domain, but you can't directly access the domain controllers (DC). There's also some differences in behavior for password policies and password hashes depending on the source of the user account creation.

This conceptual article details how to administer an Azure AD DS managed domain and the different behavior of user accounts depending on the way they're created.

## <a name="domain-management"></a>Domain management

In Azure AD DS, the domain controllers (DCs) that contain all the resources like users and groups, credentials, and policies are part of the managed service. For redundancy, two DCs are created as part of an Azure AD DS managed domain. You can't sign in to these DCs to perform management tasks. Instead, you create a management VM that's joined to the Azure AD DS managed domain, then install your regular AD DS management tools. You can use the Active Directory Administrative Center or Microsoft Management Console (MMC) snap-ins like DNS or Group Policy objects, for example.

## <a name="user-account-creation"></a>User account creation

User accounts can be created in Azure AD DS in multiple ways. Most user accounts are synchronized in from Azure AD, which can also include user account synchronized from an on-premises AD DS environment. You can also manually create accounts directly in Azure AD DS. Some features, like initial password synchronization or password policy, behave differently depending on how and where user accounts are created.

* The user account can be synchronized in from Azure AD. This includes cloud-only user accounts created directly in Azure AD, and hybrid user accounts synchronized from an on-premises AD DS environment using Azure AD Connect.
    * The majority of user accounts in Azure AD DS are created through the synchronization process from Azure AD.
* The user account can be manually created in an Azure AD DS managed domain, and doesn't exist in Azure AD.
    * If you need to create service accounts for applications that only run in Azure AD DS, you can manually create them in the managed domain. As synchronization is one-way from Azure AD, user accounts created in Azure AD DS aren't synchronized back to Azure AD.

## <a name="password-policy"></a>Criteri password

Azure AD DS includes a default password policy that defines settings for things like account lockout, maximum password age, and password complexity. Settings like account lockout policy apply to all users in Azure AD DS, regardless of how the user was created as outlined in the previous section. A few settings, like minimum password length and password complexity, only apply to users created directly in Azure AD DS.

You can create your own custom password policies to override the default policy in Azure AD DS. These custom policies can then be applied to specific groups of users as needed.

For more information on the differences in how password policies are applied depending on the source of user creation, see [Password and account lockout policies on managed domains][password-policy].

## <a name="password-hashes"></a>Password hashes

Per autenticare gli utenti nel dominio gestito, Azure AD DS necessita dell'hash delle password in un formato idoneo per l'autenticazione NTLM (NT LAN Manager) e Kerberos. Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

Per gli account utente solo cloud, gli utenti devono cambiare le loro password prima di poter usare Azure AD DS. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM.

For users synchronized from an on-premises AD DS environment using Azure AD Connect, [enable synchronization of password hashes][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect only synchronizes legacy password hashes when you enable Azure AD DS for your Azure AD tenant. Legacy password hashes aren't used if you only use Azure AD Connect to synchronize an on-premises AD DS environment with Azure AD.
>
> If your legacy applications don't use NTLM authentication or LDAP simple binds, we recommend that you disable NTLM password hash synchronization for Azure AD DS. For more information, see [Disable weak cipher suites and NTLM credential hash synchronization][secure-domain].

Dopo la corretta configurazione, gli hash delle password utilizzabili vengono archiviati nel dominio gestito di Azure AD DS. Se si elimina il dominio gestito di Azure AD DS, verranno eliminati anche gli hash delle password archiviati in quel momento. Synchronized credential information in Azure AD can't be reused if you later create an Azure AD DS managed domain - you must reconfigure the password hash synchronization to store the password hashes again. Le VM o gli utenti precedentemente aggiunti al domino non saranno in grado di eseguire immediatamente l'autenticazione. Azure AD deve generare e archiviare gli hash delle password nel nuovo dominio gestito di Azure AD DS. Per altre informazioni, vedere [Processo di sincronizzazione degli hash delle password per Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect should only be installed and configured for synchronization with on-premises AD DS environments. It's not supported to install Azure AD Connect in an Azure AD DS managed domain to synchronize objects back to Azure AD.

## <a name="forests-and-trusts"></a>Foreste e trust

A *forest* is a logical construct used by Active Directory Domain Services (AD DS) to group one or more *domains*. The domains then store objects for user or groups, and provide authentication services.

In Azure AD DS, the forest only contains one domain. On-premises AD DS forests often contain many domains. In large organizations, especially after mergers and acquisitions, you may end up with multiple on-premises forests that each then contain multiple domains.

By default, an Azure AD DS managed domain is created as a *user* forest. This type of forest synchronizes all objects from Azure AD, including any user accounts created in an on-premises AD DS environment. User accounts can directly authenticate against the Azure AD DS managed domain, such as to sign in to a domain-joined VM. A user forest works when the password hashes can be synchronized and users aren't using exclusive sign-in methods like smart card authentication.

In an Azure AD DS *resource* forest, users authenticate over a one-way forest *trust* from their on-premises AD DS. With this approach, the user objects and password hashes aren't synchronized to Azure AD DS. The user objects and credentials only exist in the on-premises AD DS. This approach lets enterprises host resources and application platforms in Azure that depend on classic authentication such LDAPS, Kerberos, or NTLM, but any authentication issues or concerns are removed. Azure AD DS resource forests are currently in preview.

For more information about forest types in Azure AD DS, see [What are resource forests?][concepts-forest] and [How do forest trusts work in Azure AD DS?][concepts-trust]

## <a name="next-steps"></a>Passaggi successivi

To get started, [create an Azure AD DS managed domain][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
