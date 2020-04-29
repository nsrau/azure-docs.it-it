---
title: Panoramica-File di Azure autorizzazione basata sull'identità
description: File di Azure supporta l'autenticazione basata su identità su SMB (Server Message Block) tramite Azure Active Directory Domain Services (AD DS) e Active Directory. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536582"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per informazioni su come abilitare l'autenticazione Active Directory Domain Services locale per le condivisioni file di Azure (anteprima), vedere [abilitare l'autenticazione Active Directory Domain Services locale su SMB per le condivisioni file di Azure](storage-files-identity-auth-active-directory-enable.md).

Per informazioni su come abilitare l'autenticazione di Azure AD DS per le condivisioni file di Azure, vedere [abilitare l'autenticazione Azure Active Directory Domain Services in file di Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossario 
È utile comprendere alcuni termini chiave relativi all'autenticazione Azure AD servizio del dominio su SMB per le condivisioni file di Azure:

-   **Autenticazione Kerberos**

    Kerberos è un protocollo di autenticazione usato per verificare l'identità di un utente o di un host. Per altre informazioni su Kerberos, vedere [Panoramica dell'autenticazione Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocollo Server Message Block (SMB)**

    SMB è un protocollo standard di settore per la condivisione dei file in rete. SMB è anche noto come Common Internet File System o CIFS. Per altre informazioni su SMB, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e Directory basato sul cloud multi-tenant. Azure AD combina servizi di directory importanti, gestione dell'accesso all'applicazione e protezione dell'identità in un'unica soluzione. Le macchine virtuali Windows (VM) Unite a Azure AD possono accedere alle condivisioni file di Azure con le credenziali Azure AD. Per ulteriori informazioni, vedere [che cos'è Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS fornisce servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Questi servizi sono completamente compatibili con Active Directory Domain Services. Per ulteriori informazioni, vedere [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services locale (AD DS)**

    L'integrazione di Active Directory Domain Services locale (AD DS) con File di Azure (anteprima) fornisce i metodi per archiviare i dati della directory, rendendoli disponibili per gli amministratori e gli utenti della rete. La sicurezza è integrata con servizi di dominio Active Directory tramite l'autenticazione di accesso e il controllo degli accessi agli oggetti nella directory. Con un unico accesso alla rete, gli amministratori possono gestire i dati e l'organizzazione della directory in tutta la rete e gli utenti di rete autorizzati possono accedere alle risorse in qualsiasi punto della rete. Active Directory Domain Services viene comunemente adottato dalle aziende negli ambienti locali e le credenziali di Active Directory Domain Services vengono usate come identità per il controllo di accesso. Per ulteriori informazioni, vedere [Active Directory Domain Services Overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Controllo degli accessi in base al ruolo (RBAC)**

    Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando RBAC è possibile gestire l'accesso alle risorse concedendo agli utenti il minor numero di autorizzazioni necessarie per svolgere il proprio lavoro. Per ulteriori informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo (RBAC) in Azure](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Casi d'uso comuni

Per i casi d'uso, è consigliabile usare l'autenticazione basata su identità e il supporto per gli ACL di Windows in File di Azure per i casi d'uso seguenti:

### <a name="replace-on-premises-file-servers"></a>Sostituisci file server locali

La deprecazione e la sostituzione dei file server locali sparsi è un problema comune che ogni azienda incontra nel percorso di modernizzazione IT. Le condivisioni file di Azure con l'autenticazione locale di servizi di dominio Active Directory (anteprima) è la scelta migliore quando è possibile eseguire la migrazione dei dati in File di Azure. Una migrazione completa consente di sfruttare i vantaggi della disponibilità elevata e della scalabilità, riducendo al contempo le modifiche sul lato client. Offre un'esperienza di migrazione senza problemi agli utenti finali, in modo che possano continuare ad accedere ai dati con le stesse credenziali usando i computer aggiunti a un dominio esistente.

### <a name="lift-and-shift-applications-to-azure"></a>Lift-and-Shift di applicazioni in Azure

Quando si sollevano e si spostano le applicazioni nel cloud, si vuole tenere lo stesso modello di autenticazione per i dati. Quando si estende l'esperienza di controllo degli accessi in base all'identità alle condivisioni file di Azure, si elimina la necessità di modificare l'applicazione in metodi di autenticazione moderni e accelerare l'adozione del cloud. Le condivisioni file di Azure offrono la possibilità di eseguire l'integrazione con Azure AD DS o servizi di dominio Active Directory (anteprima) locali per l'autenticazione. Se il piano deve essere 100% nativo del cloud e ridurre al minimo le attività di gestione delle infrastrutture cloud, Azure AD DS rappresenta una soluzione migliore come un servizio del dominio completamente gestito. Se è necessaria la compatibilità completa con le funzionalità di servizi di dominio Active Directory, è opportuno valutare l'opportunità di estendere l'ambiente di servizi di dominio Active Directory al cloud ospitando controller di dominio indipendenti nelle VM. In entrambi i casi, offriamo la flessibilità necessaria per scegliere i servizi del dominio in base alle esigenze aziendali.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e ripristino di emergenza (DR)

Se si mantiene l'archiviazione file principale in locale, le condivisioni file di Azure possono fungere da risorsa di archiviazione ideale per il backup o il ripristino di emergenza, per migliorare la continuità aziendale. È possibile usare le condivisioni file di Azure per eseguire il backup dei dati dai file server esistenti, mantenendo al tempo stesso gli elenchi DACL di Windows. Per gli scenari di ripristino di emergenza è possibile configurare un'opzione di autenticazione per supportare l'applicazione del controllo di accesso appropriata al failover.

## <a name="supported-scenarios"></a>Scenari supportati

La tabella seguente riepiloga gli scenari di autenticazione condivisioni file di Azure per Azure AD DS e servizi di dominio Active Directory (anteprima) locali. Si consiglia di selezionare il servizio del dominio adottato per l'ambiente client per l'integrazione con File di Azure. Se Active Directory Domain Services (anteprima) è già configurato in locale o in Azure, dove i dispositivi sono aggiunti a un dominio di ad, è consigliabile usare servizi di dominio Active Directory (anteprima) per l'autenticazione delle condivisioni file di Azure. Analogamente, se si è già adottato Azure AD DS (GA), è consigliabile usarlo per l'autenticazione delle condivisioni file di Azure.


|Autenticazione di Azure AD DS  | autenticazione locale di servizi di dominio Active Directory (anteprima)  |
|---------|---------|
|Azure AD computer Windows aggiunti a DS possono accedere alle condivisioni file di Azure con Azure AD credenziali tramite SMB.     |I computer Windows AD DS aggiunti in locale possono accedere alle condivisioni file di Azure con credenziali Active Directory locali sincronizzate con Azure AD tramite SMB.         |

### <a name="unsupported-scenarios"></a>Scenari non supportati

- Azure AD DS e l'autenticazione di servizi di dominio Active Directory locale non supportano l'autenticazione per gli account computer. È possibile prendere in considerazione l'uso di un account di accesso al servizio.
- Azure AD autenticazione DS non supporta l'autenticazione per i dispositivi aggiunti a Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Vantaggi dell'autenticazione basata sull'identità
L'autenticazione basata su identità per File di Azure offre diversi vantaggi rispetto all'uso dell'autenticazione con chiave condivisa:

-   **Estendi la tradizionale esperienza di accesso alla condivisione file basata sull'identità al cloud con servizi di dominio Active Directory e Azure AD DS locali**  
    Se si prevede di sollevare e spostare l'applicazione nel cloud, sostituendo i file server tradizionali con le condivisioni file di Azure, è possibile che l'applicazione esegua l'autenticazione con servizi di dominio Active Directory locale o Azure AD credenziali DS per accedere ai dati del file. File di Azure supporta l'uso di servizi di dominio Active Directory locale o credenziali di Azure AD DS per accedere alle condivisioni file di Azure tramite SMB da servizi di dominio Active Directory o Azure AD VM aggiunti a un dominio locale.

-   **Applicare il controllo dell'accesso granulare sulle condivisioni file di Azure**  
    È possibile concedere autorizzazioni a un'identità specifica a livello di condivisione, directory o file. Si supponga, ad esempio, che più team usino una condivisione file di Azure singola per la collaborazione a progetti. È possibile concedere a tutti i team l'accesso alle directory non sensibili, ma limitare l'accesso alle directory contenenti i dati finanziari sensibili solo al team Finanza. 

-   **Eseguire il backup degli elenchi di accesso Windows (noti anche come NTFS) insieme ai dati**  
    È possibile usare le condivisioni file di Azure per eseguire il backup delle condivisioni file locali esistenti. File di Azure conserva gli ACL insieme ai dati quando si esegue il backup di una condivisione file in condivisioni file di Azure tramite SMB.

## <a name="how-it-works"></a>Come funziona

Condivisioni file di Azure supporta l'autenticazione Kerberos per l'integrazione con Azure AD DS o AD DS locale (anteprima). Prima di abilitare l'autenticazione in condivisioni file di Azure, è prima necessario configurare l'ambiente di dominio. Per l'autenticazione Azure AD DS, è necessario abilitare Azure AD Domain Services e aggiungere a un dominio le VM a cui si prevede di accedere ai dati dei file. La VM aggiunta a un dominio deve risiedere nella stessa rete virtuale (VNET) del Azure AD DS. Analogamente, per l'autenticazione locale di servizi di dominio Active Directory (anteprima), è necessario configurare il controller di dominio e il dominio aggiungere i computer o le macchine virtuali.

Quando un'identità associata a un'applicazione in esecuzione in una macchina virtuale tenta di accedere ai dati nelle condivisioni file di Azure, la richiesta viene inviata a Azure AD DS per autenticare l'identità. Se l'autenticazione ha esito positivo, Azure AD DS restituisce un token Kerberos. L'applicazione invia una richiesta che include il token Kerberos e le condivisioni file di Azure usano tale token per autorizzare la richiesta. Le condivisioni file di Azure ricevono solo il token e non vengono mantenute Azure AD le credenziali DS. L'autenticazione di servizi di dominio Active Directory locale funziona in modo simile, in cui servizi di dominio Active Directory fornisce il token Kerberos.

![La screenshot mostra un diagramma di autenticazione di Azure AD tramite SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Abilitare l'autenticazione basata sull'identità

È possibile abilitare l'autenticazione basata su identità con Azure AD DS o servizi di dominio Active Directory locale (anteprima) per le condivisioni file di Azure negli account di archiviazione nuovi ed esistenti. È possibile usare un solo servizio del dominio per l'autenticazione di accesso ai file nell'account di archiviazione, che si applica a tutte le condivisioni file nell'account. Per informazioni dettagliate sulla configurazione delle condivisioni file per l'autenticazione con Azure AD DS, vedere l'articolo [abilitare l'autenticazione Azure Active Directory Domain Services per file di Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e le linee guida per servizi di dominio Active Directory (anteprima) locali in un altro articolo, [abilitare l'autenticazione Active Directory Domain Services locale su SMB per le condivisioni file di Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di condivisione per File di Azure

Quando è abilitata l'autenticazione di servizi di dominio Active Directory (anteprima) Azure AD DS o locale, è possibile usare i ruoli RBAC predefiniti o configurare ruoli personalizzati per le identità di Azure AD e assegnare diritti di accesso a tutte le condivisioni file negli account di archiviazione. L'autorizzazione assegnata consente all'identità concessa di ottenere l'accesso solo alla condivisione, nient'altro, neanche alla directory radice. È comunque necessario configurare separatamente le autorizzazioni a livello di directory o di file per le condivisioni file di Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di directory o di file per File di Azure

Le condivisioni file di Azure applicano le autorizzazioni standard per i file di Windows a livello di directory e di file, inclusa la directory radice. La configurazione delle autorizzazioni a livello di directory o di file è supportata sia in SMB che in REST. Montare la condivisione file di destinazione dalla macchina virtuale e configurare le autorizzazioni mediante Esplora file di Windows, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)o il comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usare la chiave dell'account di archiviazione per le autorizzazioni di utente con privilegi avanzati

Un utente con la chiave dell'account di archiviazione può accedere alle condivisioni file di Azure con autorizzazioni superuser. Le autorizzazioni superuser ignorano tutte le restrizioni di controllo di accesso.

> [!IMPORTANT]
> La procedura consigliata di sicurezza consigliata consiste nell'evitare di condividere le chiavi dell'account di archiviazione e sfruttare l'autenticazione basata sull'identità laddove possibile.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Mantenere gli ACL di file e directory durante l'importazione di dati nelle condivisioni file di Azure

File di Azure supporta la conservazione degli ACL a livello di directory o di file durante la copia dei dati nelle condivisioni file di Azure. È possibile copiare gli ACL in una directory o un file in condivisioni file di Azure usando i set di strumenti Sincronizzazione file di Azure o comuni di spostamento dei file. Ad esempio, è possibile usare [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) con il `/copy:s` flag per copiare i dati e gli ACL in una condivisione file di Azure. Gli ACL vengono conservati per impostazione predefinita, non è necessario abilitare l'autenticazione basata su identità nell'account di archiviazione per mantenere gli ACL.

## <a name="pricing"></a>Prezzi
Per abilitare l'autenticazione basata sull'identità su SMB nell'account di archiviazione non è previsto alcun addebito per il servizio aggiuntivo. Per altre informazioni sui prezzi, vedere [file di Azure prezzi](https://azure.microsoft.com/pricing/details/storage/files/) e prezzi [Azure ad Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'autenticazione File di Azure e basata sull'identità su SMB, vedere le risorse seguenti:

- [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md)
- [Abilitare l'autenticazione Active Directory Domain Services locale tramite SMB per le condivisioni file di Azure](storage-files-identity-auth-active-directory-enable.md)
- [Abilitare l'autenticazione Azure Active Directory Domain Services in File di Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
