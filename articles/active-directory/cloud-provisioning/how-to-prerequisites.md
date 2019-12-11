---
title: Prerequisiti per il provisioning del Cloud Azure AD Connect in Azure AD
description: Questo argomento descrive i prerequisiti e i requisiti hardware per il provisioning del cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f033563bbd7888e53d910773cd1e0c501eaad098
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997106"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Prerequisiti per il provisioning di Azure AD Connect Cloud
Questo argomento fornisce indicazioni sulla scelta e sull'uso di Azure AD Connect provisioning cloud come soluzione di identità.



## <a name="cloud-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning cloud
Per usare Azure AD Connect provisioning cloud è necessario quanto segue:
    
- un account amministratore globale per il tenant di Azure AD
- un server locale per l'agente di provisioning con Windows 2012 R2 o versione successiva
- configurazioni del firewall locali

Nel resto del documento vengono fornite istruzioni dettagliate per questi prerequisiti.

### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore globale di tipo solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). Il completamento di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-domains-add-azure-portal.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server host aggiunto al dominio che esegue Windows Server 2012 R2 o versione successiva con almeno 4 GB di RAM e il runtime di .NET 4.7.1 o versione successiva 

2. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Assicurarsi che gli agenti possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:

     | Numero della porta | Uso |
     | --- | --- |
     | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
     | **443** | Gestisce tutte le comunicazioni in uscita con il servizio |
     | **8080** (facoltativo) | Gli agenti segnalano il proprio stato ogni dieci minuti sulla porta 8080, se la porta 443 non è disponibile. Lo stato viene visualizzato nel portale di Azure AD. |
     
     Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente di specificare suffissi sicuri, aggiungere connessioni a **\*. msappproxy.NET** e **\*. ServiceBus.Windows.NET**. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti devono poter accedere a **login.windows.net** e **login.microsoftonline.net** per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - Per la convalida del certificato, sbloccare questi URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www\.microsoft.com:80**. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, questi URL potrebbero essere già sbloccati.

### <a name="verify-the-port"></a>Verificare la porta
Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente sia in grado di comunicare con esso, è possibile usare quanto segue:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verificherà che gli agenti siano in grado di comunicare con Azure tramite la porta 443.  Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.
![Servizi](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisiti aggiuntivi
- [4.7.1 Framework di Microsoft .NET](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisiti TLS

>[!NOTE]
>Il Transport Layer Security (TLS) è un protocollo che fornisce comunicazioni sicure.  La modifica delle impostazioni TLS influiscono sull'intera foresta.  Per altre informazioni [, vedere Aggiornamento per abilitare tls 1,1 e tls 1,2 come protocolli di sicurezza predefiniti in WinHTTP in Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

Il server Windows in cui verrà ospitato il Azure AD Connect agente di provisioning cloud deve avere TLS 1,2 abilitato prima di installarlo.

Per abilitare TLS 1.2:

1. Impostare le chiavi del Registro di sistema seguenti:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Riavviare il server.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

