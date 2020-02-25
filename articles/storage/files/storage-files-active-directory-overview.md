---
title: Panoramica-File di Azure autorizzazione basata sull'identità
description: File di Azure supporta l'autenticazione basata su identità su SMB (Server Message Block) tramite Azure Active Directory Domain Services (AD DS) e Active Directory. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 3a9a2a903bd9979cd0f9a09b7589edc6d4fd8962
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565086"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Panoramica del supporto dell'autenticazione basata su identità File di Azure per l'accesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per informazioni su come abilitare l'autenticazione di Active Directory per le condivisioni file di Azure, vedere [abilitare l'autenticazione Active Directory su SMB per le condivisioni file di Azure](storage-files-active-directory-domain-services-enable.md).

Per informazioni su come abilitare l'autenticazione di Azure AD DS per le condivisioni file di Azure, vedere [abilitare l'autenticazione di Azure Active Directory Domain Services su SMB per file di Azure](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossario 
È utile comprendere alcuni termini chiave relativi all'autenticazione Azure AD servizio del dominio su SMB per le condivisioni file di Azure:

-   **Autenticazione Kerberos**

    Kerberos è un protocollo di autenticazione usato per verificare l'identità di un utente o di un host. Per altre informazioni su Kerberos, vedere [Panoramica dell'autenticazione Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocollo Server Message Block (SMB)**

    SMB è un protocollo standard di settore per la condivisione dei file in rete. SMB è anche noto come Common Internet File System o CIFS. Per altre informazioni su SMB, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Azure AD combina servizi di directory importanti, gestione dell'accesso all'applicazione e protezione dell'identità in un'unica soluzione. Azure AD consente alle macchine virtuali (VM) Windows appartenenti a un dominio di accedere alle condivisioni file di Azure con le credenziali Azure AD. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (Azure AD DS)**

    Azure AD Domain Services (GA) fornisce servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Questi servizi sono completamente compatibili con il server active directory di Windows. Per altre informazioni, vedere [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (servizi di dominio Active Directory, noto anche come annuncio)**

    Active Directory (AD) (anteprima) fornisce i metodi per l'archiviazione dei dati di directory, rendendoli disponibili per gli amministratori e gli utenti della rete. La sicurezza è integrata con Active Directory tramite l'autenticazione di accesso e il controllo di accesso agli oggetti nella directory. Con un unico accesso alla rete, gli amministratori possono gestire i dati e l'organizzazione della directory in tutta la rete e gli utenti di rete autorizzati possono accedere alle risorse in qualsiasi punto della rete. AD viene comunemente adottato dalle aziende in locale e usa le credenziali di Active Directory come identità per il controllo di accesso. Per ulteriori informazioni, vedere [Active Directory Domain Services Overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Controllo degli accessi in base al ruolo (RBAC)**

    Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando RBAC è possibile gestire l'accesso alle risorse concedendo agli utenti il minor numero di autorizzazioni necessarie per svolgere il proprio lavoro. Per ulteriori informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo (RBAC) in Azure](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Casi d'uso comuni

Per i casi d'uso, è consigliabile usare l'autenticazione basata su identità e il supporto per gli ACL di Windows in File di Azure per i casi d'uso seguenti:

### <a name="replace-on-premises-file-servers"></a>Sostituisci file server locali

La deprecazione e la sostituzione dei file server locali sparsi è un problema comune che ogni azienda incontra nel percorso di modernizzazione IT. Le condivisioni file di Azure con l'autenticazione AD (anteprima) è la scelta migliore quando è possibile eseguire la migrazione dei dati in File di Azure. Una migrazione completa consente di sfruttare i vantaggi della disponibilità elevata e della scalabilità, riducendo al contempo le modifiche sul lato client, in particolare l'infrastruttura di dominio Active Directory. Offre un'esperienza di migrazione senza problemi agli utenti finali, in modo che possano continuare ad accedere ai dati con le stesse credenziali usando i computer aggiunti a un dominio esistente.

### <a name="lift-and-shift-applications-to-azure"></a>Lift-and-Shift di applicazioni in Azure

Quando si spostano le applicazioni nel cloud, è necessario tenere lo stesso modello di autenticazione per i dati. Quando si estende l'esperienza di controllo degli accessi in base all'identità alle condivisioni file di Azure, si elimina la necessità di modificare l'applicazione in metodi di autenticazione moderni e accelerare l'adozione del cloud. Condivisioni file di Azure offre la possibilità di eseguire l'integrazione con Azure AD DS (GA) o AD (anteprima) per l'autenticazione. Se il piano deve essere 100% nativo del cloud e ridurre al minimo le attività di gestione delle infrastrutture cloud, Azure AD DS rappresenta una soluzione migliore come un servizio del dominio completamente gestito. Se è necessaria la compatibilità completa con le funzionalità di servizi di dominio Active Directory, potrebbe essere opportuno estendere l'ambiente di Active Directory al cloud tramite i controller di dominio con hosting automatico nelle VM. In entrambi i casi, offriamo la flessibilità necessaria per scegliere i servizi del dominio in base alle esigenze aziendali.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e ripristino di emergenza (DR)

Se si mantiene l'archiviazione file principale in locale, le condivisioni file di Azure possono fungere da risorsa di archiviazione ideale per il backup o il ripristino di emergenza, per migliorare la continuità aziendale. È possibile usare le condivisioni file di Azure per eseguire il backup dei dati dai file server esistenti, mantenendo al tempo stesso gli elenchi DACL di Windows. Per gli scenari di ripristino di emergenza è possibile configurare un'opzione di autenticazione per supportare l'applicazione del controllo di accesso appropriata al failover.

## <a name="supported-scenarios"></a>Scenari supportati

La tabella seguente riepiloga gli scenari di autenticazione di condivisioni file di Azure supportati per Azure AD DS (GA) e AD (anteprima). Si consiglia di selezionare il servizio del dominio adottato per l'ambiente client per l'integrazione con File di Azure. Se AD (anteprima) è già configurato in locale o in Azure, dove i dispositivi sono aggiunti a un dominio ad Active Directory, è consigliabile usare AD (anteprima) per l'autenticazione delle condivisioni file di Azure. Analogamente, se si è già adottato Azure AD DS (GA), è consigliabile usarlo per l'autenticazione delle condivisioni file di Azure.


|Autenticazione di Azure AD DS (GA)  |Autenticazione AD (anteprima)  |
|---------|---------|
|Azure AD i computer Windows aggiunti a un dominio possono accedere alle condivisioni file di Azure con Azure AD credenziali su SMB.     |I computer Windows Active Directory aggiunti a un dominio possono accedere alle condivisioni file di Azure con le credenziali di Active Directory sincronizzate con Azure AD tramite SMB.         |

### <a name="unsupported-scenarios"></a>Scenari non supportati

- Azure AD DS (GA) e l'autenticazione AD (anteprima) non supportano l'autenticazione per gli account computer. È possibile prendere in considerazione l'uso di un account di accesso al servizio.
- L'autenticazione di Azure AD DS (GA) non supporta l'autenticazione per i dispositivi aggiunti Azure AD cloud.

## <a name="advantages-of-identity-based-authentication"></a>Vantaggi dell'autenticazione basata sull'identità
L'autenticazione basata su identità per File di Azure offre diversi vantaggi rispetto all'uso dell'autenticazione con chiave condivisa:

-   **Estendi la tradizionale esperienza di accesso alla condivisione file basata sull'identità al cloud con AD e Azure AD DS**  
    Se si prevede di trasferire l'applicazione nel cloud, sostituendo i file server tradizionali con le condivisioni file di Azure, è possibile che l'applicazione esegua l'autenticazione con le credenziali di Active Directory o Azure AD per accedere ai dati del file. File di Azure supporta l'uso di credenziali AD o Azure AD per accedere alle condivisioni file di Azure tramite SMB da Active Directory o Azure AD VM appartenenti a un dominio.

-   **Applicare il controllo dell'accesso granulare sulle condivisioni file di Azure**  
    È possibile concedere autorizzazioni a un'identità specifica a livello di condivisione, directory o file. Si supponga, ad esempio, che più team usino una condivisione file di Azure singola per la collaborazione a progetti. È possibile concedere a tutti i team l'accesso alle directory non sensibili, ma limitare l'accesso alle directory contenenti i dati finanziari sensibili solo al team Finanza. 

-   **Eseguire il backup degli elenchi di accesso Windows (noti anche come NTFS) insieme ai dati**  
    È possibile usare le condivisioni file di Azure per eseguire il backup delle condivisioni file locali esistenti. File di Azure conserva gli ACL insieme ai dati quando si esegue il backup di una condivisione file in condivisioni file di Azure tramite SMB.

## <a name="how-it-works"></a>Funzionamento
Condivisioni file di Azure supporta l'autenticazione Kerberos per l'integrazione con Azure AD DS (GA) o AD (anteprima). Prima di abilitare l'autenticazione in condivisioni file di Azure, è necessario innanzitutto configurare l'ambiente di dominio. Per l'autenticazione Azure AD DS (GA), è necessario abilitare Azure AD Domain Services e aggiungere il dominio alle VM a cui si prevede di accedere ai dati dei file. La VM aggiunta a un dominio deve risiedere nella stessa rete virtuale (VNET) del Azure AD Domain Services. Analogamente, per l'autenticazione AD (anteprima), è necessario configurare il controller di dominio Active Directory e aggiungere il dominio a computer o macchine virtuali.

Quando un'identità associata a un'applicazione in esecuzione in una macchina virtuale tenta di accedere ai dati nelle condivisioni file di Azure, la richiesta viene inviata a Azure AD Domain Services per autenticare l'identità. Se l'autenticazione ha esito positivo, Azure AD Domain Services restituisce un token Kerberos. L'applicazione invia una richiesta che include il token Kerberos e le condivisioni file di Azure usano tale token per autorizzare la richiesta. Condivisioni file di Azure riceve solo il token e non mantiene le credenziali Azure AD. L'autenticazione di Active Directory funziona in modo simile, in cui AD fornisce il token Kerberos.

![La screenshot mostra un diagramma di autenticazione di Azure AD tramite SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Abilitare l'autenticazione basata sull'identità

È possibile abilitare l'autenticazione basata sull'identità con Azure AD DS (GA) o AD (anteprima) per le condivisioni file di Azure negli account di archiviazione nuovi ed esistenti. È possibile usare un solo servizio del dominio per l'autenticazione di accesso ai file nell'account di archiviazione, che si applica a tutte le condivisioni file nell'account. Istruzioni dettagliate per la configurazione delle condivisioni file per l'autenticazione con Azure AD DS (GA) nell'articolo [abilitare l'autenticazione Azure Active Directory Domain Services su SMB per file di Azure](storage-files-active-directory-enable.md) e le linee guida per ad (anteprima) nell'altro articolo [abilitare Active Directory su SMB per le condivisioni file di Azure](storage-files-active-directory-domain-services-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di condivisione per File di Azure

Quando è abilitata l'autenticazione Azure AD DS (GA) o AD (anteprima), è possibile usare i ruoli RBAC predefiniti o configurare ruoli personalizzati per le identità di Azure AD e assegnare i diritti di accesso a tutte le condivisioni file negli account di archiviazione. l'autorizzazione assegnata consente all'identità concessa di ottenere l'accesso solo alla condivisione, nient'altro, neanche alla directory radice. È comunque necessario configurare separatamente le autorizzazioni a livello di directory o di file per le condivisioni file di Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di directory o di file per File di Azure

Le condivisioni file di Azure applicano le autorizzazioni standard per i file di Windows a livello di directory e di file, inclusa la directory radice. La configurazione delle autorizzazioni a livello di directory o di file è supportata sia in SMB che in REST. Montare la condivisione file di destinazione dalla macchina virtuale e configurare le autorizzazioni mediante Esplora file di Windows, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)o il comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usare la chiave dell'account di archiviazione per le autorizzazioni di utente con privilegi avanzati

Un utente che possiede la chiave dell'account di archiviazione può accedere alle condivisioni file di Azure con autorizzazioni superuser. Le autorizzazioni superuser ignorano tutte le restrizioni di controllo di accesso.

> [!IMPORTANT]
> La procedura consigliata di sicurezza consigliata consiste nell'evitare di condividere le chiavi dell'account di archiviazione e sfruttare l'autenticazione basata sull'identità laddove possibile.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Mantenere gli ACL di file e directory durante l'importazione di dati nelle condivisioni file di Azure

File di Azure supporta la conservazione degli ACL a livello di directory o di file durante la copia dei dati nelle condivisioni file di Azure. È possibile copiare gli ACL in una directory o un file in condivisioni file di Azure usando i set di strumenti Sincronizzazione file di Azure o comuni di spostamento dei file. Ad esempio, è possibile usare [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) con il flag di `/copy:s` per copiare i dati e gli ACL in una condivisione file di Azure. Gli ACL vengono conservati per impostazione predefinita, non è necessario abilitare l'autenticazione basata su identità nell'account di archiviazione per mantenere gli ACL.

## <a name="pricing"></a>Prezzi
Per abilitare l'autenticazione basata sull'identità su SMB nell'account di archiviazione non è previsto alcun addebito per il servizio aggiuntivo. Per altre informazioni sui prezzi, vedere la pagina relativa ai prezzi [file di Azure](https://azure.microsoft.com/pricing/details/storage/files/) e alle pagine relative ai [prezzi Azure ad Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) se si cercano le informazioni su AAD DS.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'autenticazione File di Azure e basata sull'identità su SMB, vedere le risorse seguenti:

- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Abilitare l'autenticazione Active Directory su SMB per le condivisioni file di Azure](storage-files-active-directory-domain-services-enable.md)
- [Abilitare l'autenticazione Azure Active Directory Domain Services in File di Azure](storage-files-active-directory-enable.md)
- [Domande frequenti](storage-files-faq.md)
