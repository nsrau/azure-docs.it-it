---
title: Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima) - Archiviazione di Azure
description: File di Azure supporta l'autenticazione basata su identità tramite SMB (Server Message Block) (anteprima) con Azure Active Directory (Azure AD) Domain Services. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 1962a3237fb54409d17fefa314605bafa91c3e9c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427638"
---
# <a name="overview-of-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Panoramica dell'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per informazioni sull'autenticazione di Azure AD tramite SMB per File di Azure, vedere [Attiva autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossario 
È utile comprendere alcuni termini principali relativi all'autenticazione di Azure AD tramite SMB per File di Azure:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Azure AD combina servizi di directory importanti, gestione dell'accesso all'applicazione e protezione dell'identità in un'unica soluzione. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Servizi di dominio Azure Active Directory**  
    Azure Active Directory Domain Services offre servizi di dominio gestito, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Questi servizi sono completamente compatibili con il server active directory di Windows. Per altre informazioni, vedere [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md).

-   **Controllo degli accessi in base al ruolo (RBAC)**  
    Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando RBAC è possibile gestire l'accesso alle risorse concedendo agli utenti il minor numero di autorizzazioni necessarie per svolgere il proprio lavoro. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md).

-   **Autenticazione Kerberos**

    Kerberos è un protocollo di autenticazione usato per verificare l'identità di un utente o di un host. Per altre informazioni su Kerberos, vedere [Panoramica dell'autenticazione Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocollo Server Message Block (SMB)**  
    SMB è un protocollo standard di settore per la condivisione dei file in rete. SMB è anche noto come Common Internet File System o CIFS. Per altre informazioni su SMB, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-authentication"></a>Vantaggi dell'autenticazione di Azure AD
Azure AD tramite SMB per File di Azure offre diversi vantaggi nell'uso dell'autenticazione tramite chiave condivisa:

-   **Estendere al cloud l'esperienza di accesso alla condivisione dei file basata sull'identità tradizionale con Azure AD**  
    Se si pianifica di trasferire le applicazioni sul cloud in modalità "lift and shift", sostituendo file server con File di Azure, può essere necessario autenticare l'applicazione con Azure AD per accedere ai dati del file. File di Azure supporta i file usando le credenziali di Azure AD da macchine virtuali aggiunte a un dominio tramite SMB per accedere alle condivisioni di file, directory o file. È anche possibile scegliere di sincronizzare tutti gli oggetti di Active Directory in locale ad Azure AD per mantenere nomi utente, password e altre assegnazioni di gruppo.

-   **Applicare il controllo dell'accesso granulare sulle condivisioni file di Azure**  
    Con l'autenticazione Azure AD tramite SMB, è possibile concedere autorizzazioni a un'identità specifica a livello di condivisione, directory o di file. Si supponga, ad esempio, che più team usino una condivisione file di Azure singola per la collaborazione a progetti. È possibile concedere a tutti i team l'accesso alle directory non sensibili, ma limitare l'accesso alle directory contenenti i dati finanziari sensibili solo al team Finanza. 

-   **Eseguire il backup degli elenchi di controllo di accesso (ACL) insieme ai dati**  
    È possibile usare File di Azure per eseguire il backup delle condivisioni di file locali esistenti. File di Azure conserva gli ACL insieme ai dati quando si esegue il backup di un file di condivisione in File di Azure tramite SMB.

## <a name="how-it-works"></a>Funzionamento
File di Azure Usa Azure AD Domain Services per supportare l'autenticazione Kerberos con le credenziali di Azure AD da macchine virtuali aggiunte a un dominio. Prima di poter usare Azure AD con File di Azure, è necessario attivare Azure AD Domain Services e aggiungere il dominio dalle macchine virtuali da cui si è programmato di accedere ai dati del file. Le VM aggiunte a un dominio devono trovarsi nella stessa rete virtuale (VNET) di Azure AD Domain Services. 

Quando un'identità associata a un'applicazione in esecuzione in una macchina virtuale tenta di accedere ai dati di File di Azure, la richiesta viene inviata ad Azure AD Domain Services per l'autenticazione dell'identità. Se l'autenticazione ha esito positivo, Azure AD Domain Services restituisce un token Kerberos. L'applicazione invia una richiesta che include il token Kerberos e File di Azure usa quel token per autorizzare la richiesta. File di Azure riceve solo il token e non rende permanenti le credenziali di Azure AD.

![La screenshot mostra un diagramma di autenticazione di Azure AD tramite SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-authentication-over-smb"></a>Abilitare l'autenticazione di Azure AD tramite SMB
È possibile abilitare l'autenticazione di Azure AD tramite SMB per File di Azure su account di archiviazione nuovi ed esistenti creati dopo il 24 settembre 2018. 

Prima di abilitare l'autenticazione di Azure AD tramite SMB, verificare che sia stato distribuito Azure AD Domain Services per il tenant primario di Azure AD a cui è associato l'account di archiviazione. Se Azure AD Domain Services non è stato ancora configurato, seguire passo passo la procedura descritta in [Abilitare Azure Active Directory Domain Services usando il portale di Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

La distribuzione di Azure AD Domain Services richiede in genere 10-15 minuti. Dopo la distribuzione di Azure AD Domain Services, è possibile abilitare l'autenticazione di Azure AD tramite SMB per File di Azure. Per altre informazioni, vedere [Abilitare l'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di condivisione per File di Azure
Dopo aver abilitato l'autenticazione di Azure AD, è possibile configurare i ruoli personalizzati RBCA per le identità di Azure AD e assegnare i diritti di accesso a tutte le condivisioni file nell'account di archiviazione.

Quando un'applicazione in esecuzione in una macchina virtuale aggiunta a un dominio tenta di montare una condivisione file di Azure o di accedere a una directory o a un file, vengono verificate le credenziali dell'applicazione Azure AD per garantire le corrette autorizzazioni a livello di condivisione e le autorizzazioni NTFS. Per informazioni sulla configurazione delle autorizzazioni a livello di condivisione, vedere [Abilitare l'autenticazione di Azure Active Directory tramite SMB (anteprima)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di directory o di file per File di Azure 
File di Azure applica autorizzazioni standard di file NTFS a livello di file e directory, persino alla directory radice. La configurazione delle autorizzazioni a livello di directory o di file è supportata solo tramite SMB. Montare la condivisione di file di destinazione dalla macchina virtuale e configurare le autorizzazioni tramite Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) oppure il comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) (Imposta elenco di controllo di accesso). 

> [!NOTE]
> Configurazione delle autorizzazioni NTFS tramite File explorer di Windows non è supportata nell'anteprima.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usare la chiave dell'account di archiviazione per le autorizzazioni di utente con privilegi avanzati 
Un utente che dispone di una chiave dell'account di archiviazione può accedere a File di Azure con le autorizzazioni da utente con privilegi avanzati. Le autorizzazioni dell'utente con privilegi avanzati superano tutte le restrizioni del controllo di accesso configurate a livello di condivisione con RBCA e applicate da Azure AD. Le autorizzazioni dell'utente con privilegi avanzati sono necessarie per montare una condivisione file di Azure. 

> [!IMPORTANT]
> Essendo parte delle procedure consigliate per la sicurezza, evitare di condividere le chiavi dell'account di archiviazione e sfruttare le autorizzazioni di Azure AD quando possibile.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Mantenere gli ACL di directory e file per importare dati in condivisioni file di Azure
L'autenticazione di Azure AD tramite SMB supporta il mantenimento degli ACL di directory o di file quando si copiano dati in condivisioni file di Azure. Nella versione di anteprima, è possibile copiare gli ACL di un file o directory in File di Azure. Ad esempio, è possibile usare [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) con il flag `/copy:s` per copiare sia i dati che gli ACL in una condivisione file di Azure.

## <a name="pricing"></a>Prezzi
Non sono previsti ulteriori addebiti per abilitare l'autenticazione di Azure AD tramite SMB nell'account di archiviazione. Per altre informazioni sui prezzi, consultare le pagine [Prezzi di File di Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [Prezzi di Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure e sull'autenticazione di Azure AD tramite SMB, vedere queste risorse:

- [Introduzione a File di Azure](storage-files-introduction.md)
- [Abilitare l'autenticazione di Azure Active Directory tramite SMB per File di Azure (anteprima)](storage-files-active-directory-enable.md)
- [Domande frequenti](storage-files-faq.md)