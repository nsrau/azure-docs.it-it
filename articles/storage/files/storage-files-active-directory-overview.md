---
title: Panoramica - Autorizzazione basata sull'identità di File di AzureOverview - Azure Files identity-based authorization
description: File di Azure supporta l'autenticazione basata sull'identità tramite SMB (Server Message Block) tramite Servizi di dominio Azure Active Directory e Active Directory. Le macchine virtuali di Windows aggiunte a dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536582"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Panoramica del supporto dell'autenticazione basata sull'identità di File di Azure per l'accesso SMBOverview of Azure Files identity-based authentication support for SMB access
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Per informazioni su come abilitare l'autenticazione di Servizi di dominio Active Directory locale per le condivisioni file di Azure (anteprima), vedere [Abilitare l'autenticazione di Servizi di dominio Active Directory locale su SMB per condivisioni file di Azure.](storage-files-identity-auth-active-directory-enable.md)

Per informazioni su come abilitare l'autenticazione di Servizi di dominio Active Directory di Azure per le condivisioni file di Azure, vedere [Abilitare l'autenticazione](storage-files-identity-auth-active-directory-domain-service-enable.md)di Servizi di dominio Azure in File di Azure.To learn how to enable Azure AD DS authentication for Azure file shares, see Enable Azure Active Directory Domain Services authentication on Azure Files.

## <a name="glossary"></a>Glossario 
È utile comprendere alcuni termini chiave relativi all'autenticazione del servizio di dominio Azure AD tramite SMB per le condivisioni file di Azure:It's helpful to understand some key terms relating to Azure AD Domain Service authentication over SMB for Azure file shares:

-   **Autenticazione Kerberos**

    Kerberos è un protocollo di autenticazione usato per verificare l'identità di un utente o di un host. Per altre informazioni su Kerberos, vedere [Panoramica dell'autenticazione Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocollo Server Message Block (SMB)**

    SMB è un protocollo standard di settore per la condivisione dei file in rete. SMB è anche noto come Common Internet File System o CIFS. Per altre informazioni su SMB, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) è il servizio di gestione delle identità e delle directory basato su cloud multi-tenant di Microsoft.Azure Active Directory (Azure AD) is Microsoft's multi-tenant cloud-based directory and identity management service. Azure AD combina servizi di directory importanti, gestione dell'accesso all'applicazione e protezione dell'identità in un'unica soluzione. Le macchine virtuali Windows (VM) aggiunte ad Azure AD possono accedere alle condivisioni file di Azure con le credenziali di Azure AD. Per altre informazioni, vedere [Che cos'è Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Servizi di dominio Active Directory di Azure fornisce servizi di dominio gestito, ad esempio l'aggiunta a un dominio, criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM.Azure AD DS provides managed domain services such as domain join, group policies, LDAP, and Kerberos/NTLM authentication. Questi servizi sono completamente compatibili con Servizi di dominio Active Directory. Per altre informazioni, vedere Servizi di [dominio Azure Active Directory](../../active-directory-domain-services/overview.md).

- **Servizi di dominio Active Directory locale**

    L'integrazione di Servizi di dominio Active Directory locale con File di Azure (anteprima) offre i metodi per archiviare i dati della directory rendendoli disponibili agli utenti e agli amministratori di rete. La sicurezza è integrata con Servizi di dominio Active Directory tramite l'autenticazione di accesso e il controllo di accesso agli oggetti nella directory. Con un singolo accesso alla rete, gli amministratori possono gestire i dati della directory e l'organizzazione in tutta la rete e gli utenti autorizzati della rete possono accedere alle risorse in qualsiasi punto della rete. Servizi di dominio Active Directory viene comunemente adottato dalle aziende in ambienti locali e le credenziali di Servizi di dominio Active Directory vengono utilizzate come identità per il controllo di accesso. Per ulteriori informazioni, vedere [Panoramica](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)di Servizi di dominio Active Directory .

-   **Controllo degli accessi in base al ruolo (RBAC)**

    Il Controllo degli accessi in base al ruolo di Azure (RBAC) consente la gestione specifica degli accessi per Azure. Usando RBAC è possibile gestire l'accesso alle risorse concedendo agli utenti il minor numero di autorizzazioni necessarie per svolgere il proprio lavoro. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Che cos'è](../../role-based-access-control/overview.md)il controllo degli accessi in base al ruolo in Azure.

## <a name="common-use-cases"></a>Casi d'uso comuni

L'autenticazione basata sull'identità e il supporto per gli ACL di Windows in File di Azure vengono utilizzati in modo ottimale per i casi d'uso seguenti:Identity-based authentication and support for Windows ACLs on Azure Files is best leveraged for the following use cases:

### <a name="replace-on-premises-file-servers"></a>Sostituire i file server locali

La deprecazione e la sostituzione di file server locali sparsi è un problema comune che ogni azienda incontra nel proprio percorso di modernizzazione IT. Azure file shares with on-premises AD DS (preview) authentication is the best fit here, when you can migrate the data to Azure Files. Una migrazione completa consentirà di sfruttare i vantaggi di disponibilità elevata e scalabilità riducendo al minimo le modifiche lato client. Offre un'esperienza di migrazione senza interruzioni per gli utenti finali, in modo che possano continuare ad accedere ai dati con le stesse credenziali utilizzando i computer aggiunti al dominio esistenti.

### <a name="lift-and-shift-applications-to-azure"></a>Sollevare e spostare le applicazioni in Azure

Quando si sollevano e si spostano le applicazioni nel cloud, si desidera mantenere lo stesso modello di autenticazione per i dati. Quando si estende l'esperienza di controllo degli accessi basata sull'identità alle condivisioni file di Azure, si elimina la necessità di modificare l'applicazione in metodi di autenticazione moderni e accelerare l'adozione del cloud. Le condivisioni file di Azure offrono l'opzione per l'integrazione con Servizi di dominio Active Directory di Azure o con Servizi di dominio Active Directory locale (anteprima) per l'autenticazione. Se il piano deve essere nativo al 100% nel cloud e ridurre al minimo gli sforzi per la gestione delle infrastrutture cloud, Servizi di dominio Active Directory di Azure sarebbe più adatto come servizio di dominio completamente gestito. Se è necessaria la piena compatibilità con le funzionalità di Servizi di dominio Active Directory, è consigliabile estendere l'ambiente di Servizi di dominio Active Directory al cloud tramite controller di dominio self-hosting nelle macchine virtuali. In entrambi i casi, offriamo la flessibilità necessaria per scegliere i servizi di dominio più adatto alle esigenze aziendali.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e ripristino di emergenza

Se si mantiene l'archiviazione di file primari in locale, le condivisioni file di Azure possono fungere da archivio ideale per il backup o il ripristino di emergenza, per migliorare la continuità aziendale. È possibile usare le condivisioni file di Azure per eseguire il backup dei dati dai file server esistenti, preservando gli elenco DACL di Windows.You can use Azure file shares to back up your data from existing file servers, while preserving Windows DACLs. Per gli scenari di ripristino di emergenza, è possibile configurare un'opzione di autenticazione per supportare l'imposizione del controllo di accesso corretta al momento del failover.

## <a name="supported-scenarios"></a>Scenari supportati

La tabella seguente riepiloga gli scenari di autenticazione delle condivisioni file di Azure supportati per Azure AD DS e Servizi di dominio Active Directory locale (anteprima). È consigliabile selezionare il servizio di dominio adottato per l'ambiente client per l'integrazione con File di Azure.We recommend selecting the domain service that you adopted for your client environment for integration with Azure Files. Se si dispone di Servizi di dominio Active Directory (anteprima) già configurato in locale o in Azure in cui i dispositivi fanno parte di un dominio ad Active Directory, è consigliabile scegliere di sfruttare Servizi di dominio Active Directory (anteprima) per l'autenticazione delle condivisioni file di Azure.If you have AD DS (preview) already setup on-premises or in Azure where your devices are domain joined to your AD, you should choose to leverage AD DS (preview) for Azure file shares authentication. Analogamente, se è già stato adottato Azure AD DS (GA), è necessario usarlo per l'autenticazione delle condivisioni file di Azure.Similarly, if you've already have adottato Azure AD DS (GA), you should use that for Azure file shares authentication.


|Autenticazione di Azure AD DSAzure AD DS authentication  | autenticazione di Servizi di dominio Active Directory (anteprima) locale  |
|---------|---------|
|I computer Windows aggiunti a Servizi di dominio Active Directory possono accedere alle condivisioni file di Azure con credenziali di Azure AD su SMB.     |I computer Windows aggiunti a Servizi di dominio Active Directory locali possono accedere alle condivisioni file di Azure con credenziali di Active Directory locali sincronizzate con Azure AD tramite SMB.         |

### <a name="unsupported-scenarios"></a>Scenari non supportati

- L'autenticazione di Servizi di dominio Active Directory di Azure AD e locale non supporta l'autenticazione in base agli account computer. È possibile utilizzare un account di accesso al servizio.
- L'autenticazione di Servizi di dominio Azure AD non supporta l'autenticazione nei dispositivi aggiunti ad Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Vantaggi dell'autenticazione basata sull'identità
L'autenticazione basata sull'identità per i file di Azure offre diversi vantaggi rispetto all'uso dell'autenticazione a chiave condivisa:Identity-based authentication for Azure Files offers several benefits over using Shared Key authentication:

-   **Estendere l'esperienza di accesso alla condivisione file tradizionale basata su identità al cloud con Servizi di dominio Active Directory e Azure AD DS localiExtend the traditional identity-based file share access experience to the cloud with on-premises AD DS and Azure AD DS**  
    Se si prevede di sollevare e spostare l'applicazione nel cloud, sostituendo i file server tradizionali con condivisioni file di Azure, è consigliabile che l'applicazione esegua l'autenticazione con le credenziali di Servizi di dominio Active Directory o Di sdi di Azure locale per accedere ai dati dei file. Azure Files supports using both on-premises AD DS or Azure AD DS credentials to access Azure file shares over SMB from either on-premises AD DS or Azure AD DS domain-joined VMs.

-   **Applicare il controllo dell'accesso granulare sulle condivisioni file di Azure**  
    È possibile concedere autorizzazioni a un'identità specifica a livello di condivisione, directory o file. Si supponga, ad esempio, che più team usino una condivisione file di Azure singola per la collaborazione a progetti. È possibile concedere a tutti i team l'accesso alle directory non sensibili, ma limitare l'accesso alle directory contenenti i dati finanziari sensibili solo al team Finanza. 

-   **Eseguire il backup degli ACL di Windows (noto anche come NTFS) insieme ai dati**  
    È possibile usare le condivisioni file di Azure per eseguire il backup delle condivisioni file locali esistenti. File di Azure mantiene gli ACL insieme ai dati quando si esegue il backup di una condivisione file in condivisioni file di Azure su SMB.

## <a name="how-it-works"></a>Funzionamento

Le condivisioni file di Azure supportano l'autenticazione Kerberos per l'integrazione con Servizi di dominio Active Directory di Azure o con Servizi di dominio Active Directory (anteprima). Prima di abilitare l'autenticazione nelle condivisioni file di Azure, è necessario configurare l'ambiente di dominio. Per l'autenticazione di Servizi di dominio Active Directory di Azure, è necessario abilitare Servizi di dominio Azure AD e aggiungere al dominio le macchine virtuali da cui si prevede di accedere ai dati dei file. Your domain-joined VM must reside in the same virtual network (VNET) as your Azure AD DS. Analogamente, per l'autenticazione di Servizi di dominio Active Directory (anteprima) locale, è necessario configurare il controller di dominio e il dominio aggiungere i computer o le macchine virtuali.

Quando un'identità associata a un'applicazione in esecuzione in una macchina virtuale tenta di accedere ai dati nelle condivisioni file di Azure, la richiesta viene inviata ad Azure AD DS per autenticare l'identità. Se l'autenticazione ha esito positivo, Servizi di dominio Active Directory di Azure restituisce un token Kerberos.If authentication is successful, Azure AD DS returns a Kerberos token. L'applicazione invia una richiesta che include il token Kerberos e le condivisioni file di Azure usano tale token per autorizzare la richiesta. Le condivisioni file di Azure ricevono solo il token e non mantengono le credenziali di Servizi di dominio Active Directory di Azure.Azure file shares receive the token only and does not persist Azure AD DS credentials. L'autenticazione di Servizi di dominio Active Directory locale funziona in modo simile, in cui Servizi di dominio Active Directory fornisce il token Kerberos.

![La screenshot mostra un diagramma di autenticazione di Azure AD tramite SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Abilitare l'autenticazione basata sull'identitàEnable identity-based authentication

È possibile abilitare l'autenticazione basata sull'identità con Servizi di dominio Active Directory di Azure o con Servizi di dominio Active Directory (anteprima) locali per le condivisioni file di Azure negli account di archiviazione nuovi ed esistenti. È possibile usare un solo servizio di dominio per l'autenticazione dell'accesso ai file nell'account di archiviazione, che si applica a tutte le condivisioni file nell'account. Indicazioni dettagliate sulla configurazione delle condivisioni file per l'autenticazione con Servizi di dominio Azure ad Azure nel nostro articolo [Abilitare l'autenticazione](storage-files-identity-auth-active-directory-domain-service-enable.md) di Servizi di dominio Azure Active Directory in File di Azure e indicazioni per Servizi di dominio Active Directory (anteprima) locale nell'altro articolo Abilitare l'autenticazione di Servizi di dominio Active Directory in locale [tramite SMB per le condivisioni file](storage-files-identity-auth-active-directory-enable.md)di Azure.

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di condivisione per File di Azure

Dopo aver abilitato l'autenticazione di Servizi di dominio Active Directory di Azure o locale (anteprima), è possibile usare i ruoli RBAC predefiniti o configurare ruoli personalizzati per le identità di Azure AD e assegnare diritti di accesso a qualsiasi condivisione file negli account di archiviazione. L'autorizzazione assegnata consente all'identità concessa di accedere solo alla condivisione, nient'altro, nemmeno alla directory radice. È comunque necessario configurare separatamente le autorizzazioni a livello di directory o di file per le condivisioni file di Azure.You still need to separately configure directory or file-level permissions for Azure file shares.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configurare le autorizzazioni a livello di directory o di file per i file di AzureConfigure directory or file-level permissions for Azure Files

Le condivisioni file di Azure applicano le autorizzazioni standard per i file di Windows sia a livello di directory che di file, inclusa la directory radice. La configurazione delle autorizzazioni a livello di directory o di file è supportata sia su SMB che su REST. Montare la condivisione file di destinazione dalla macchina virtuale e configurare le autorizzazioni utilizzando Esplora file di Windows, [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)di Windows o il comando [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usare la chiave dell'account di archiviazione per le autorizzazioni di utente con privilegi avanzati

Un utente con la chiave dell'account di archiviazione può accedere alle condivisioni file di Azure con autorizzazioni di utente avanzato. Le autorizzazioni per gli utenti con privilegi avanzati ignorano tutte le restrizioni di controllo di accesso.

> [!IMPORTANT]
> La nostra procedura consigliata per la sicurezza consiste nell'evitare di condividere le chiavi dell'account di archiviazione e sfruttare l'autenticazione basata sull'identità quando possibile.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Conservare gli ACL di directory e file durante l'importazione dei dati nelle condivisioni file di Azure

File di Azure supporta la conservazione degli ACL a livello di directory o di file durante la copia dei dati in condivisioni file di Azure.Azure Files supports preserving directory or file level ACLs when copying data to Azure file shares. È possibile copiare gli ACL in una directory o in un file in condivisioni file di Azure usando Sincronizzazione file di Azure o set di strumenti comuni per lo spostamento dei file. Ad esempio, è possibile usare `/copy:s` robocopy con il flag per copiare i dati e gli ACL in una condivisione file di Azure.For example, you can use [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) with the flag to copy data as cLs to an Azure file share. Gli ACL vengono mantenuti per impostazione predefinita, non è necessario abilitare l'autenticazione basata sull'identità nell'account di archiviazione per conservare gli ACL.

## <a name="pricing"></a>Prezzi
Non sono previsti costi aggiuntivi per il servizio per abilitare l'autenticazione basata sull'identità tramite SMB nell'account di archiviazione. Per altre informazioni sui prezzi, vedere Prezzi dei file di Azure e Prezzi di Servizi di dominio Azure AD.For more information on pricing, see [Azure Files pricing](https://azure.microsoft.com/pricing/details/storage/files/) and Azure AD Domain Services [pricing.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui file di Azure e sull'autenticazione basata sulle identità su SMB, vedere queste risorse:For more information about Azure Files and identity-based authentication over SMB, see these resources:

- [Pianificazione per la distribuzione dei file di Azure](storage-files-planning.md)
- [Abilitare l'autenticazione di Servizi di dominio Active Directory locale tramite SMB per le condivisioni file di AzureEnable on-premises Active Directory Domain Services authentication over SMB for Azure file shares](storage-files-identity-auth-active-directory-enable.md)
- [Abilitare l'autenticazione di Servizi di dominio Azure Active Directory nei file di AzureEnable Azure Active Directory Domain Services authentication on Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [DOMANDE FREQUENTI](storage-files-faq.md)
