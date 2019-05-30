---
title: Risolvere i problemi relativi all'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Risolvere i problemi relativi all'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246630"
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
Se i problemi di connessione al dominio gestito con il protocollo LDAP sicuro persistono, [contattare il team di prodotto](contact-us.md) per assistenza. Includere le informazioni seguenti per agevolare la diagnostica del problema:
* Schermata di ldp.exe che effettua la connessione con esito negativo.
* ID tenant di Azure AD e nome di dominio DNS per il dominio gestito.
* Nome utente esatto con cui si tenta di eseguire il binding.


## <a name="related-content"></a>Contenuti correlati
* [Servizi di dominio Azure AD: introduzione](create-instance.md)
* [Gestire un dominio di Azure AD Domain Services](manage-domain.md)
* [Nozioni di base sulle query LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Gestire i criteri di gruppo per Azure Active Directory Domain Services](manage-group-policy.md)
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md)
* [Creare un gruppo di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)
