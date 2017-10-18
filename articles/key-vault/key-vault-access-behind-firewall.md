---
title: Accedere a un insieme di credenziali delle chiavi protetto da un firewall | Documentazione Microsoft
description: Informazioni su come accedere a un insieme di credenziali delle chiavi di Azure da un'applicazione protetta da un firewall
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: d00c6e0acf437d2bfc3c27e948f4646a6685b08f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Accedere a Insieme di credenziali delle chiavi di Azure protetto da firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>D: L'applicazione client dell'insieme di credenziali delle chiavi deve essere protetta da firewall. Quali porte, host o indirizzi IP è necessario aprire per consentire l'accesso a un insieme di credenziali delle chiavi?
Per accedere a un insieme di credenziali delle chiavi, l'applicazione client dell'insieme di credenziali delle chiavi deve poter accedere a più endpoint per varie funzionalità:

* Autenticazione tramite Azure Active Directory (Azure AD).
* Gestione di Insieme di credenziali delle chiavi di Azure, inclusa la creazione, la lettura, l'aggiornamento, l'eliminazione e l'impostazione dei criteri di accesso tramite Azure Resource Manager.
* Accesso e gestione di oggetti (chiavi e segreti) archiviati nell'insieme di credenziali delle chiavi eseguiti tramite l'endpoint specifico dell'insieme di credenziali delle chiavi, ad esempio [https://nomeinsiemecredenziali.vault.azure.net](https://yourvaultname.vault.azure.net).  

Esistono alcune varianti a seconda della configurazione e dell'ambiente.   

## <a name="ports"></a>Porte
Tutto il traffico verso un insieme di credenziali delle chiavi per tutte e tre le funzioni (autenticazione, gestione e accesso al piano dati) passa per la porta HTTPS 443. Verrà tuttavia generato occasionalmente traffico HTTP (porta 80) per CRL. I client che supportano OCSP non devono raggiungere CRL, ma possono in alcuni casi raggiungere [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticazione
Le applicazioni client dell'insieme di credenziali delle chiavi dovrà accedere agli endpoint di Azure Active Directory per l'autenticazione. L'endpoint usato dipende dalla configurazione del tenant di Azure AD, dal tipo di entità (entità utente o entità servizio) e dal tipo di account, ad esempio account Microsoft o account aziendale o dell'istituto di istruzione.  

| Tipo di entità | Endpoint:porta |
| --- | --- |
| Utente che usa un account Microsoft<br> (ad esempio, user@hotmail.com) |**Globale:**<br> login.microsoftonline.com:443<br><br> **Azure per la Cina:**<br> login.chinacloudapi.cn:443<br><br>**Azure per enti pubblici statunitensi:**<br> login-us.microsoftonline.com:443<br><br>**Azure per la Germania:**<br> login.microsoftonline.de:443<br><br> e <br>login.live.com:443 |
| Utente o entità servizio che usa un account aziendale o dell'istituto di istruzione con Azure AD (ad esempio, user@contoso.com) |**Globale:**<br> login.microsoftonline.com:443<br><br> **Azure per la Cina:**<br> login.chinacloudapi.cn:443<br><br>**Azure per enti pubblici statunitensi:**<br> login-us.microsoftonline.com:443<br><br>**Azure per la Germania:**<br> login.microsoftonline.de:443 |
| Utente o entità servizio che usa un account aziendale o dell'istituto di istruzione oltre ad Active Directory Federation Services (AD FS) o un altro endpoint federato (ad esempio, user@contoso.com) |Tutti gli endpoint per un account aziendale o dell'istituto di istruzione oltre ad AD FS o altri endpoint federati |

Esistono altri possibili scenari complessi. Per altre informazioni, vedere [Azure Active Directory Authentication Flow](/documentation/articles/active-directory-authentication-scenarios/) (Flussi di autenticazione di Azure Active Directory), [Integrazione di applicazioni con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx) (Protocolli di autenticazione di Active Directory Authentication).  

## <a name="key-vault-management"></a>Gestione dell'insieme di credenziali delle chiavi
Per la gestione dell'insieme di credenziali delle chiavi (CRUD e impostazione dei criteri di accesso), l'applicazione client dell'insieme di credenziali delle chiavi deve accedere all'endpoint di Azure Resource Manager.  

| Tipo di operazione | Endpoint:porta |
| --- | --- |
| Operazioni del piano di controllo dell'insieme di credenziali delle chiavi<br> tramite Azure Resource Manager |**Globale:**<br> management.azure.com:443<br><br> **Azure per la Cina:**<br> management.chinacloudapi.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> management.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> management.microsoftazure.de:443 |
| API Graph di Azure Active Directory |**Globale:**<br> graph.windows.net:443<br><br> **Azure per la Cina:**<br> graph.chinacloudapi.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> graph.windows.net:443<br><br> **Azure per la Germania:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operazioni dell'insieme di credenziali delle chiavi
Per tutte le operazioni di gestione e crittografia degli oggetti (chiavi e segreti) dell'insieme di credenziali delle chiavi, il client dell'insieme di credenziali delle chiavi deve accedere all'endpoint dell'insieme stesso. Il suffisso DNS dell'endpoint varia a seconda della posizione dell'insieme di credenziali delle chiavi. Il formato dell'endpoint dell'insieme di credenziali delle chiavi è *nome-insiemecredenziali*.*suffisso-dns-area-geografica*, come descritto nella tabella seguente.  

| Tipo di operazione | Endpoint:porta |
| --- | --- |
| Operazioni come crittografia sulle chiavi; creazione, lettura, aggiornamento ed eliminazione di chiavi e segreti; impostazione o definizione di tag e altri attributi per gli oggetti dell'insieme di credenziali delle chiavi (chiavi o segreti) |**Globale:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.net:443<br><br> **Azure per la Cina:**<br> &lt;nome-insiemecredenziali&gt;.vault.azure.cn:443<br><br> **Azure per enti pubblici statunitensi:**<br> &lt;nome-insiemecredenziali&gt;.vault.usgovcloudapi.net:443<br><br> **Azure per la Germania:**<br> &lt;nome-insiemecredenziali&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalli di indirizzi IP
Il servizio Insieme di credenziali delle chiavi usa altre risorse di Azure come l'infrastruttura PaaS. Non è quindi possibile fornire uno specifico intervallo di indirizzi IP disponibile per gli endpoint del servizio Insieme di credenziali delle chiavi in un determinato momento. Se il firewall supporta solo gli intervalli di indirizzi IP, vedere il documento [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalli di indirizzi IP del data center di Azure). Per l'autenticazione e l'identità (Azure Active Directory), l'applicazione deve potersi connettere agli endpoint descritti in [Authentication and identity addresses](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) (Indirizzi di identità e autenticazione).

## <a name="next-steps"></a>Passaggi successivi
In caso di domande sull'insieme di credenziali delle chiavi, visitare i [forum di Insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

