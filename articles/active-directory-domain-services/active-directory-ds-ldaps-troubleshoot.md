---
title: Risolvere i problemi relativi all'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Risolvere i problemi relativi all'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: 97a2ee23435d0b29565bc3bf1dcb426e9e83fd31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416756"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Risolvere i problemi relativi all'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure Active Directory Domain Services

## <a name="connection-issues"></a>Problemi di connessione
Nel caso di problemi di connessione al dominio gestito tramite il protocollo LDAP sicuro:

* La catena di autorità di certificazione del certificato LDAP sicuro deve essere considerata attendibile nel client. È possibile scegliere di aggiungere l'autorità di certificazione radice nell'archivio certificati radice attendibili nel client per stabilire la relazione di attendibilità.
* Verificare che il client LDAP (ad esempio ldp.exe) si connetta all'endpoint LDAP sicuro usando un nome DNS, non l'indirizzo IP.
* Controllare il nome DNS a cui si connette il client LDAP. È necessario risolvere l'indirizzo IP pubblico per l'accesso LDAP sicuro nel dominio gestito.
* Verificare che il certificato LDAP sicuro per il dominio gestito contenga il nome DNS nell'attributo soggetto o nell'attributo nomi alternativi del soggetto.
* Le impostazioni del gruppo di sicurezza di rete per la rete virtuale devono consentire il traffico verso la porta 636 da Internet. Questo passaggio si applica solo se l'accesso LDAP sicuro tramite Internet è stato abilitato.


## <a name="need-help"></a>Richiesta di assistenza
Se i problemi di connessione al dominio gestito con il protocollo LDAP sicuro persistono, [contattare il team di prodotto](active-directory-ds-contact-us.md) per assistenza. Includere le informazioni seguenti per agevolare la diagnostica del problema:
* Schermata di ldp.exe che effettua la connessione con esito negativo.
* ID tenant di Azure AD e nome di dominio DNS per il dominio gestito.
* Nome utente esatto con cui si tenta di eseguire il binding.


## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Nozioni di base sulle query LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administer Group Policy on an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-administer-group-policy.md) (Amministrare i Criteri di gruppo in un dominio gestito da Azure AD Domain Services)
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md)
* [Creare un gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)
