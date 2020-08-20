---
title: Criteri di sicurezza e utilizzo
description: Informazioni su sicurezza e criteri per le macchine virtuali in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 075bd2d4743a33b189d1c76c69dfd0a9d99b80dd
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658219"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Applicare la sicurezza e usare criteri in macchine virtuali in Azure

È importante proteggere le macchine virtuali (VM) per le applicazioni in esecuzione. La protezione delle macchine virtuali può includere uno o più servizi o funzionalità di Azure che gestiscono l'accesso sicuro alle macchine virtuali e l'archiviazione sicura dei dati. Questo articolo contiene informazioni che permettono di mantenere sicure le macchine virtuali e le applicazioni.

## <a name="antimalware"></a>Antimalware

L'attuale panorama delle minacce per gli ambienti cloud è dinamico e impone sempre di più una protezione efficace e continua per soddisfare i requisiti di conformità e sicurezza. [Microsoft Antimalware per Azure](~/articles/security/fundamentals/antimalware.md) è una funzionalità di protezione in tempo reale gratuita che aiuta a identificare e rimuovere virus, spyware e altro software dannoso. Gli avvisi possono essere configurati in modo da ricevere notifiche quando software dannoso o indesiderato prova a installare o eseguire se stesso nella macchina virtuale. Non è supportata nelle macchine virtuali che eseguono Linux o Windows Server 2008.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

Con il [Centro sicurezza di Azure](~/articles/security-center/security-center-intro.md) è possibile impedire, rilevare e gestire le minacce per le macchine virtuali. Il Centro sicurezza offre un monitoraggio di sicurezza integrato e gestione dei criteri per le sottoscrizioni di Azure, aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate ed è compatibile con un ampio ecosistema di soluzioni di sicurezza.

L'accesso JIT del Centro sicurezza può essere applicato attraverso la distribuzione di VM per bloccare il traffico in ingresso verso le macchine virtuali di Azure, riducendo l'esposizione agli attacchi e fornendo un facile accesso per connettersi alle macchine virtuali quando necessario. Quando JIT è abilitato e un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza controlla le autorizzazioni di cui dispone l'utente per la macchina virtuale. Se l'utente dispone delle autorizzazioni corrette, la richiesta viene approvata e il Centro sicurezza configura automaticamente i gruppi di sicurezza di rete per consentire il traffico in entrata alle porte selezionate per un periodo di tempo limitato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. 

## <a name="encryption"></a>Crittografia

Per i dischi gestiti sono disponibili due metodi di crittografia. Crittografia a livello di sistema operativo, ovvero crittografia dischi di Azure e crittografia a livello di piattaforma, ovvero la crittografia lato server.

### <a name="server-side-encryption"></a>Modello di crittografia lato server

I dischi gestiti di Azure crittografano automaticamente i dati per impostazione predefinita quando vengono resi persistenti nel cloud. La crittografia lato server consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati nei dischi gestiti di Azure sono crittografati in modo trasparente con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide disponibili, conforme a FIPS 140-2.

La crittografia non influisca sulle prestazioni dei dischi gestiti. Non sono previsti costi aggiuntivi per la crittografia.

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati presenti nei dischi gestiti. 

Per altre informazioni sulla crittografia lato server, vedere gli articoli per [Windows](~/articles/virtual-machines/windows/disk-encryption.md) o [Linux](~/articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Per migliorare la sicurezza e la conformità delle [macchine virtuali Windows](~/articles/virtual-machines/windows/disk-encryption-overview.md) e delle [macchine virtuali Linux](~/articles/virtual-machines/linux/disk-encryption-overview.md), i dischi virtuali in Azure possono essere crittografati. I dischi virtuali delle VM di Windows vengono crittografati a riposo mediante BitLocker. I dischi virtuali delle VM Linux vengono crittografati quando sono inattivi usando dm-crypt. 

Non è previsto alcun addebito per la crittografia dei dischi virtuali in Azure. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso. Un'entità servizio di Azure Active Directory offre un meccanismo protetto per il rilascio delle chiavi di crittografia all'accensione e allo spegnimento delle VM.

## <a name="key-vault-and-ssh-keys"></a>Insieme di credenziali delle chiavi e chiavi SSH

È possibile creare segreti e certificati come risorse e fornirli tramite [Key Vault](~/articles/key-vault/key-vault-whatis.md). È possibile usare Azure PowerShell e l'interfaccia della riga di comando di Azure per creare insiemi di credenziali delle chiavi rispettivamente per [macchine virtuali Windows](~/articles/virtual-machines/windows/key-vault-setup.md) e [macchine virtuali Linux](~/articles/virtual-machines/linux/key-vault-setup.md). È anche possibile creare chiavi per la crittografia.

I criteri di accesso dell'insieme di credenziali delle chiavi concedono autorizzazioni separate per chiavi, segreti e certificati. È ad esempio possibile concedere a un utente l'accesso alle chiavi, ma non le autorizzazioni per i segreti. Tuttavia, le autorizzazioni per accedere a chiavi, segreti o certificati vengono definite a livello di insieme di credenziali. In altre parole, i [criteri di accesso](~/articles/key-vault/key-vault-secure-your-key-vault.md) dell'insieme di credenziali delle chiavi non supportano le autorizzazioni a livello di oggetto.

Quando ci si connette alle macchine virtuali (VM), è consigliabile usare la crittografia a chiave pubblica per fornire un modo più sicuro per accedere alle VM. Questo processo comporta uno scambio di chiavi pubbliche e private mediante il comando Sicure Shell (SSH) per l'autenticazione, anziché nome utente e password. Le password sono vulnerabili agli attacchi di forza bruta, soprattutto nelle VM con connessione Internet, ad esempio i server Web. Con una coppia di chiavi SSH (Secure Shell) è possibile creare una [macchina virtuale Linux](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) che usa chiavi SSH per l'autenticazione, eliminando la necessità di immettere password per l'accesso. È anche possibile usare chiavi SSH per la connessione da una [macchina virtuale Windows](~/articles/virtual-machines/linux/ssh-from-windows.md) a una macchina virtuale Linux.

## <a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di Azure

Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione delle credenziali presenti nel codice per l'autenticazione ai servizi cloud. Proteggere le credenziali è un'attività importante. In teoria, le credenziali non sono mai presenti nelle workstation di sviluppo e non vengono verificate nel controllo del codice sorgente. Azure Key Vault consente di archiviare in modo sicuro le credenziali, i segreti e altre chiavi, ma è necessario autenticare il codice in Key Vault per recuperarle. 

La funzionalità delle identità gestite per le risorse di Azure in Azure Active Directory (Azure AD) consente di risolvere questo problema. La funzionalità offre ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare l'identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.  Il codice in esecuzione in una macchina virtuale può richiedere un token da due endpoint accessibili solo dalla macchina virtuale. Per altre informazioni su questo servizio, esaminare la pagina di panoramica delle [identità gestite per le risorse di Azure](~/articles/active-directory/managed-identities-azure-resources/overview.md).   

## <a name="policies"></a>Criteri

È possibile usare [criteri di Azure](~/articles/azure-policy/azure-policy-introduction.md) per definire il comportamento desiderato per le [macchine virtuali Windows](~/articles/virtual-machines/windows/policy.md) e le [macchine virtuali Linux](~/articles/virtual-machines/linux/policy.md) dell'organizzazione. Tramite i criteri è possibile imporre diverse convenzioni e regole in tutta l'organizzazione. L'imposizione del comportamento desiderato consente di attenuare i rischi, contribuendo nello stesso tempo al successo dell'organizzazione.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Usando il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](~/articles/role-based-access-control/overview.md), è possibile separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti della macchina virtuale necessaria per svolgere i propri processi. Invece di concedere a tutti autorizzazioni senza restrizioni per la macchina virtuale, è possibile consentire solo determinate azioni. È possibile configurare il controllo di accesso per la macchina virtuale nel [portale di Azure](~/articles/role-based-access-control/role-assignments-portal.md), nell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/role) o con [Azure PowerShell](~/articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Passaggi successivi
- Eseguire la procedura per il monitoraggio della sicurezza delle macchine virtuali tramite il Centro sicurezza di Azure per [Linux](~/articles/security/fundamentals/overview.md) o [Windows](~/articles/virtual-machines/windows/tutorial-azure-security.md).

