---
title: "Porte e protocolli necessari per la soluzione ibrida di gestione delle identità | Documentazione Microsoft"
description: "Questa pagina è una pagina di riferimento tecnico relativa alle porte che devono essere aperte per Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 935246ad590af0dcdb6d52257a94169f94dd66e5
ms.openlocfilehash: 3ff7256578cffd30ae9b75ead2dd7f08babca171
ms.lasthandoff: 02/17/2017


---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Porte e protocolli necessari per la soluzione ibrida di gestione delle identità
Il documento seguente è un riferimento tecnico per fornire informazioni sulle porte e i protocolli necessari per l'implementazione di una soluzione ibrida di gestione delle identità. Usare la figura riportata di seguito e vedere la tabella corrispondente.

![Cos'è Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabella 1 - Azure AD Connect e AD locale
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e l'AD locale.

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Ricerche DNS della foresta di destinazione. |
| Kerberos |88 (TCP/UDP) |Autenticazione Kerberos per la foresta Active Directory. |
| MS-RPC |135 (TCP/UDP) |Usato durante la configurazione iniziale della procedura guidata Azure AD Connect quando si esegue l'associazione alla foresta Active Directory,  |
| LDAP |389 (TCP/UDP) |Usato per l'importazione di dati da Active Directory. I dati vengono crittografati con la firma e il sigillo Kerberos. |
| LDAP/SSL |636 (TCP/UDP) |Usato per l'importazione di dati da Active Directory. Il trasferimento dati è firmato e crittografato. Usato solo se si utilizza SSL. |
| RPC |49152- 65535 (porta RPC elevata casuale)(TCP/UDP) |Usato durante la configurazione iniziale di Azure AD Connect quando si esegue l'associazione alla foresta Active Directory. Per altre informazioni, vedere gli articoli [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) e [KB224196](https://support.microsoft.com/kb/224196). |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabella 2 - Azure AD Connect e AD locale
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e Azure AD.

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usato per il download di CRL (Certificate Revocation List) per verificare i certificati SSL. |
| HTTPS |443 (TCP/UDP) |Usato per la sincronizzazione con Azure AD. |

Per un elenco degli URL e degli indirizzi IP da aprire nel firewall, vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabella 3 - Azure AD Connect e server federativi/WAP di AD FS
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e i server federativi/WAP di AD FS.  

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Usato per il download di CRL (Certificate Revocation List) per verificare i certificati SSL. |
| HTTPS |443 (TCP/UDP) |Usato per la sincronizzazione con Azure AD. |
| WinRM |5985 |Listener di Gestione remota Windows |

## <a name="table-4---wap-and-federation-servers"></a>Tabella 4 - server federativi e WAP
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra i server federativi e i server WAP.

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Usato per l'autenticazione. |

## <a name="table-5---wap-and-users"></a>Tabella 5 - WAP e utenti
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra gli utenti e i server WAP.

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Usato per l'autenticazione del dispositivo. |
| TCP |49443 (TCP) |Usato per l'autenticazione del certificato. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabella 6a e 6b - Autenticazione pass-through con Single Sign-On (SSO) e Sincronizzazione degli hash delle password con Single Sign-On (SSO)
La tabella seguente descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabella 6a - Autenticazione pass-through con SSO
|Protocol|Numero della porta|Descrizione
| --- | --- | ---
|HTTP|80|Abilitare il traffico HTTP in uscita per la convalida di sicurezza quale SSL.
|HTTPS|443|    Abilitare l'autenticazione utente con Azure AD
|HTTPS|10100–10120|    Abilitare risposte dal connettore ad Azure AD 
|Bus di servizio di Azure|9352, 5671|    Abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso.
|HTTPS|9350|    Facoltativo, per consentire prestazioni migliori per le richieste in ingresso
|HTTPS|8080/443|    Abilitare la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore
|HTTPS|9090|    Abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore)
|HTTPS|9091|    Abilitare il rinnovo automatico dei certificati di attendibilità del connettore

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabella 6b - Sincronizzazione degli hash delle password con SSO

|Protocol|Numero della porta|Descrizione
| --- | --- | ---
|HTTPS|9090|    Abilitare la registrazione SSO (necessaria solo per il processo di registrazione SSO).

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabelle 7a e 7b - Agente di Azure AD Connect Health (AD FS/sincronizzazione) e Azure AD
Le tabelle seguenti descrivono gli endpoint, le porte e i protocolli necessari per la comunicazione tra gli agenti di Azure AD Connect Health e Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabella 7a - Porte e protocolli per l'agente di Azure AD Connect Health (AD FS/sincronizzazione) e Azure AD
Questa tabella descrive le porte in uscita seguenti e i protocolli necessari per la comunicazione tra gli agenti di Azure AD Connect Health e Azure AD.  

| Protocol | Porte | Descrizione |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |In uscita |
| Bus di servizio di Azure |5671 (TCP/UDP) |In uscita |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Endpoint per l'agente di Azure AD Connect Health (AD FS/sincronizzazione) e Azure AD
Per un elenco di endpoint vedere la sezione [Requisiti in Installazione dell'agente di Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).


