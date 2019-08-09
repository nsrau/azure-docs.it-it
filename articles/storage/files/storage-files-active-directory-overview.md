---
title: Panoramica dell'autenticazione di Azure Active Directory su SMB per File di Azure-archiviazione di Azure
description: File di Azure supporta l'autenticazione basata su identità su SMB (Server Message Block) tramite i servizi del dominio Azure Active Directory (Azure AD). Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/30/2019
ms.author: rogarana
ms.openlocfilehash: 604cf2bbe0cf8ab036c76ee9223d1ee34fd4bd3d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854557"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Panoramica del supporto per l'autenticazione File di Azure Azure Active Directory Domain Service (Azure AD DS) per l'accesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per informazioni su come abilitare l'autenticazione di Azure AD DS per File di Azure, vedere [abilitare l'autenticazione del servizio di Azure Active Directory dominio su SMB per file di Azure](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossario 
È utile comprendere alcuni termini chiave relativi all'autenticazione Azure AD servizio del dominio su SMB per File di Azure:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Azure AD combina servizi di directory importanti, gestione dell'accesso all'applicazione e protezione dell'identità in un'unica soluzione. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Servizi di dominio Azure Active Directory**  
    Azure Active Directory Domain Services offre servizi di dominio gestito, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Questi servizi sono completamente compatibili con il server active directory di Windows. Per altre informazioni, vedere [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

-   **Controllo degli accessi in base al ruolo (RBAC)**  
    Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando RBAC è possibile gestire l'accesso alle risorse concedendo agli utenti il minor numero di autorizzazioni necessarie per svolgere il proprio lavoro. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).

-   **Autenticazione Kerberos**

    Kerberos è un protocollo di autenticazione usato per verificare l'identità di un utente o di un host. Per altre informazioni su Kerberos, vedere [Panoramica dell'autenticazione Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocollo Server Message Block (SMB)**  
    SMB è un protocollo standard di settore per la condivisione dei file in rete. SMB è anche noto come Common Internet File System o CIFS. Per altre informazioni su SMB, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Vantaggi dell'autenticazione del servizio Azure AD dominio
Azure AD autenticazione del servizio del dominio per File di Azure offre diversi vantaggi rispetto all'uso dell'autenticazione con chiave condivisa:

-   **Estendi la tradizionale esperienza di accesso alla condivisione file basata sull'identità al cloud con Azure AD e Azure AD servizio del dominio**  
    Se si prevede di spostare l'applicazione nel cloud, sostituendo i file server tradizionali con File di Azure, è possibile che l'applicazione esegua l'autenticazione con Azure AD credenziali per accedere ai dati dei file. File di Azure supporta l'uso di credenziali Azure AD per accedere File di Azure tramite SMB dalle macchine virtuali Windows Azure AD dominio. È anche possibile scegliere di sincronizzare tutti gli oggetti di Active Directory in locale ad Azure AD per mantenere nomi utente, password e altre assegnazioni di gruppo.

-   **Applicare il controllo dell'accesso granulare sulle condivisioni file di Azure**  
    È possibile concedere autorizzazioni a un'identità specifica a livello di condivisione, directory o file. Si supponga, ad esempio, che più team usino una condivisione file di Azure singola per la collaborazione a progetti. È possibile concedere a tutti i team l'accesso alle directory non sensibili, ma limitare l'accesso alle directory contenenti i dati finanziari sensibili solo al team Finanza. 

-   **Eseguire il backup degli elenchi di controllo di accesso (ACL) insieme ai dati**  
    È possibile usare File di Azure per eseguire il backup delle condivisioni di file locali esistenti. File di Azure conserva gli ACL insieme ai dati quando si esegue il backup di un file di condivisione in File di Azure tramite SMB.

## <a name="how-it-works"></a>Funzionamento
File di Azure Usa Azure AD Domain Services per supportare l'autenticazione Kerberos con le credenziali di Azure AD da macchine virtuali aggiunte a un dominio. Prima di poter usare Azure AD con File di Azure, è necessario attivare Azure AD Domain Services e aggiungere il dominio dalle macchine virtuali da cui si è programmato di accedere ai dati del file. La VM aggiunta a un dominio deve risiedere nella stessa rete virtuale (VNET) come Azure AD Domain Services. 

Quando un'identità associata a un'applicazione in esecuzione in una macchina virtuale tenta di accedere ai dati di File di Azure, la richiesta viene inviata ad Azure AD Domain Services per l'autenticazione dell'identità. Se l'autenticazione ha esito positivo, Azure AD Domain Services restituisce un token Kerberos. L'applicazione invia una richiesta che include il token Kerberos e File di Azure usa quel token per autorizzare la richiesta. File di Azure riceve solo il token e non rende permanenti le credenziali di Azure AD.

![La screenshot mostra un diagramma di autenticazione di Azure AD tramite SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Abilitare l'autenticazione del servizio del dominio Azure AD per l'accesso SMB
È possibile abilitare l'autenticazione Azure AD servizio del dominio per File di Azure per gli account di archiviazione nuovi ed esistenti creati dopo il 24 settembre 2018. 

Prima di abilitare questa funzionalità, verificare che Azure AD Domain Services sia stata distribuita per il tenant di Azure AD primario a cui è associato l'account di archiviazione. Se Azure AD Domain Services non è stato ancora configurato, seguire passo passo la procedura descritta in [Abilitare Azure Active Directory Domain Services usando il portale di Azure](../../active-directory-domain-services/create-instance.md).

La distribuzione di Azure AD Domain Services richiede in genere 10-15 minuti. Dopo la distribuzione di Azure AD Domain Services, è possibile abilitare l'autenticazione di Azure AD tramite SMB per File di Azure. Per ulteriori informazioni, vedere [abilitare l'autenticazione del servizio Azure Active Directory dominio su SMB per file di Azure](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di condivisione per File di Azure
Una volta abilitata l'autenticazione Azure AD servizio del dominio, è possibile configurare ruoli RBAC personalizzati per Azure AD identità e assegnare i diritti di accesso a tutte le condivisioni file nell'account di archiviazione.

Quando un'applicazione in esecuzione in una macchina virtuale aggiunta a un dominio tenta di montare una condivisione file di Azure o di accedere a una directory o a un file, vengono verificate le credenziali dell'applicazione Azure AD per garantire le corrette autorizzazioni a livello di condivisione e le autorizzazioni NTFS. Per informazioni sulla configurazione delle autorizzazioni a livello di condivisione, vedere [abilitare Azure Active Directory autenticazione del servizio del dominio su SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di directory o di file per File di Azure 
File di Azure applica autorizzazioni standard di file NTFS a livello di file e directory, persino alla directory radice. La configurazione delle autorizzazioni a livello di directory o di file è supportata solo tramite SMB. Montare la condivisione file di destinazione dalla macchina virtuale e configurare le autorizzazioni usando il comando Esplora file di Windows, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) o [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usare la chiave dell'account di archiviazione per le autorizzazioni di utente con privilegi avanzati 
Un utente che dispone di una chiave dell'account di archiviazione può accedere a File di Azure con le autorizzazioni da utente con privilegi avanzati. Le autorizzazioni dell'utente con privilegi avanzati superano tutte le restrizioni del controllo di accesso configurate a livello di condivisione con RBCA e applicate da Azure AD. Le autorizzazioni dell'utente con privilegi avanzati sono necessarie per montare una condivisione file di Azure. 

> [!IMPORTANT]
> Essendo parte delle procedure consigliate per la sicurezza, evitare di condividere le chiavi dell'account di archiviazione e sfruttare le autorizzazioni di Azure AD quando possibile.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Mantenere gli ACL di directory e file per importare dati in condivisioni file di Azure
File di Azure supporta ora la conservazione degli ACL di directory o file quando si copiano i dati in condivisioni file di Azure. È possibile copiare gli ACL in una directory o un file in File di Azure. Ad esempio, è possibile usare [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) con il flag `/copy:s` per copiare sia i dati che gli ACL in una condivisione file di Azure. La conservazione degli ACL è attiva per impostazione predefinita e non è necessario abilitare in modo esplicito Azure AD funzionalità di autenticazione del servizio del dominio nell'account di archiviazione. 

## <a name="pricing"></a>Prezzi
Non sono previsti ulteriori addebiti per abilitare l'autenticazione di Azure AD tramite SMB nell'account di archiviazione. Per altre informazioni sui prezzi, consultare le pagine [Prezzi di File di Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [Prezzi di Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure e sull'autenticazione di Azure AD tramite SMB, vedere queste risorse:

- [Introduzione a File di Azure](storage-files-introduction.md)
- [Abilitare l'autenticazione Azure Active Directory su SMB per File di Azure](storage-files-active-directory-enable.md)
- [Domande frequenti](storage-files-faq.md)
