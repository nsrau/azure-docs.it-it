---
title: Prerequisiti per il provisioning del cloud di Azure AD Connect in Azure ADPrerequisites for Azure AD Connect cloud provisioning in Azure AD
description: Questo articolo descrive i prerequisiti e i requisiti hardware necessari per il provisioning del cloud.
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
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332078"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Prerequisiti per il provisioning cloud di Azure AD ConnectPrerequisites for Azure AD Connect cloud provisioning
Questo articolo fornisce indicazioni su come scegliere e usare il provisioning cloud di Azure Active Directory (Azure AD) Connect come soluzione di identità.



## <a name="cloud-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning cloud
Per usare il provisioning cloud di Azure AD Connect, è necessario quanto segue:You need the following to use Azure AD Connect cloud provisioning:
    
- Un account amministratore globale per il tenant di Azure AD che non è un utente guest.
- Un server locale per l'agente di provisioning con Windows 2012 R2 o versione successiva.
- Configurazioni del firewall locali.

>[!NOTE]
>L'agente di provisioning può attualmente essere installato solo nei server in lingua inglese. L'installazione di un Language Pack in lingua inglese in un server non in lingua inglese non è una soluzione valida e comporterà la mancata installazione dell'agente. 

Il resto del documento fornisce istruzioni dettagliate per questi prerequisiti.

### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore globale di tipo solo cloud nel tenant di Azure AD. In questo modo, è possibile gestire la configurazione del tenant in caso di errore o di disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale solo cloud.](../active-directory-users-create-azure-portal.md) Completare questo passaggio è fondamentale per assicurarsi di non essere bloccato dal tenant.
1. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-domains-add-azure-portal.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-directory-in-active-directory"></a>Nella directory in Active Directory

Eseguire lo [strumento IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) per preparare gli attributi di directory per la sincronizzazione.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server host aggiunto al dominio che esegue Windows Server 2012 R2 o versione successiva con un minimo di 4 GB di RAM e un runtime di .NET 4.7.1.

1. Se è presente un firewall tra i server e Azure AD, configurare gli elementi seguenti:If there's a firewall between your servers and Azure AD, configure the following items:
   - Assicurarsi che gli agenti possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:

        | Numero della porta | Uso |
        | --- | --- |
        | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato TLS/SSL.  |
        | **443** | Gestisce tutte le comunicazioni in uscita con il servizio. |
        | **8080** (facoltativo) | Se la porta 443 non è disponibile, gli agenti di autenticazione segnalano il proprio stato ogni dieci minuti attraverso la porta 8080. Questo stato viene visualizzato nel portale di Azure AD. |
     
   - Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente di specificare suffissi sicuri, aggiungere connessioni a \*.msappproxy.net e \*.servicebus.windows.net. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti devono poter accedere a login.windows.net e login.microsoftonline.net per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - Per la convalida dei certificati, sblocca i seguenti URL:\.mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www microsoft.com:80. Questi URL vengono utilizzati per la convalida dei certificati con altri prodotti Microsoft, pertanto è possibile che questi URL siano già sbloccati.

### <a name="verify-the-port"></a>Verificare la porta
Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente possa comunicare con esso, usare l'URL seguente:To verify that Azure is listening on port 443 and that your agent can communicate with it, use the following URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verifica che gli agenti possano comunicare con Azure tramite la porta 443.This test verifies that your agents can communicate with Azure over port 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità del porto](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisiti aggiuntivi
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisiti TLS

>[!NOTE]
>Transport Layer Security (TLS) è un protocollo che fornisce comunicazioni protette. La modifica delle impostazioni TLS influisce sull'intera foresta. Per ulteriori informazioni, vedere [Aggiornamento per abilitare TLS 1.1 e TLS 1.2 come protocolli protetti predefiniti in WinHTTP in Windows.](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

Il server Windows che ospita l'agente di provisioning cloud di Azure AD Connect deve avere TLS 1.2 abilitato prima di installarlo.

Per abilitare TLS 1.2, attenersi alla seguente procedura.

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

