---
title: Risolvere i problemi relativi a LDAP sicuro in Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come risolvere i problemi relativi a LDAP (LDAPS) sicuro per un dominio gestito di Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132174"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Risolvere i problemi di connettività LDAP sicura a un dominio gestito di Servizi di dominio Azure Active DirectoryTroubleshoot secure LDAP connectivity issues to an Azure Active Directory Domain Services managed domain

Le applicazioni e i servizi che usano LDAP (Lightweight Directory Access Protocol) per comunicare con Servizi di dominio Azure Active Directory (Azure AD DS) possono essere [configurati per l'uso](tutorial-configure-ldaps.md)di LDAP sicuro. Affinché LDAP protetto funzioni correttamente, è necessario che siano aperti un certificato appropriato e le porte di rete necessarie.

Questo articolo consente di risolvere i problemi relativi all'accesso LDAP sicuro in Servizi di dominio Active Directory di Azure.This article helps you troubleshoot issues with secure LDAP access in Azure AD DS.

## <a name="common-connection-issues"></a>Problemi di connessione comuni

In caso di problemi di connessione a un dominio gestito di Servizi di dominio Active Directory di Azure tramite LDAP sicuro, vedere la procedura di risoluzione dei problemi seguente. Dopo ogni passaggio di risoluzione dei problemi, provare a connettersi nuovamente al dominio gestito di Servizi di dominio Active Directory di Azure:After each troubleshooting step, try to connect to the Azure AD DS managed domain again:

* La catena di autorità di certificazione del certificato LDAP sicuro deve essere considerata attendibile nel client. È possibile aggiungere l'autorità di certificazione (CA) radice all'archivio certificati radice attendibile nel client per stabilire l'attendibilità.
    * Assicurarsi di [esportare e applicare il certificato ai computer client.][client-cert]
* Verificare che il certificato LDAP sicuro per il dominio gestito disponga del nome DNS nell'attributo *Soggetto* o *Nomi alternativi soggetto.*
    * Esaminare i requisiti del [certificato LDAP sicuro][certs-prereqs] e creare un certificato sostitutivo, se necessario.
* Verificare che il client LDAP, ad esempio *ldp.exe,* si connetta all'endpoint LDAP protetto utilizzando un nome DNS, non l'indirizzo IP.
    * Il certificato applicato al dominio gestito di Servizi di dominio Active Directory di Azure non include gli indirizzi IP del servizio, ma solo i nomi DNS.
* Controllare il nome DNS a cui si connette il client LDAP. Deve essere risolto nell'indirizzo IP pubblico per LDAP sicuro nel dominio gestito di Servizi di dominio Active Directory di Azure.It must resolve to the public IP address for secure LDAP on the Azure AD DS managed domain.
    * Se il nome DNS viene risolto nell'indirizzo IP interno, aggiornare il record DNS per risolvere l'indirizzo IP esterno.
* Per la connettività esterna, il gruppo di sicurezza di rete deve includere una regola che consente il traffico verso la porta TCP 636 da Internet.For external connectivity, the network security group must include a rule that allows the traffic to TCP port 636 from the internet.
    * Se è possibile connettersi al dominio gestito di Servizi di dominio Active Directory di Azure usando LDAP sicuro dalle risorse connesse direttamente alla rete virtuale ma non da connessioni esterne, assicurarsi di creare una regola del gruppo di sicurezza di rete per consentire il [traffico LDAP sicuro.][ldaps-nsg]

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, [aprire una richiesta][azure-support] di supporto di Azure per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
