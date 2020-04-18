---
title: Concetti relativi alla gestione di Servizi di dominio di Azure AD Documenti Microsoft
description: Informazioni su come amministrare un dominio gestito di Servizi di dominio Azure Active Directory e sul comportamento di account utente e password
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640014"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Concetti di gestione per account utente, password e amministrazione in Servizi di dominio Azure Active Directory

Quando si crea ed esegue un dominio gestito di Servizi di dominio Azure Active Directory, esistono alcune differenze nel comportamento rispetto a un ambiente di Servizi di dominio Active Directory locale tradizionale. Gli stessi strumenti di amministrazione in Azure AD DS vengono utilizzati come dominio autogestito, ma non è possibile accedere direttamente ai controller di dominio (DC). Ci sono anche alcune differenze nel comportamento per i criteri password e gli scarsi delle password a seconda dell'origine della creazione dell'account utente.

Questo articolo concettuale descrive in dettaglio come amministrare un dominio gestito di Servizi di dominio Active Directory di Azure e il diverso comportamento degli account utente a seconda del modo in cui vengono creati.

## <a name="domain-management"></a>Gestione dei domini

In Servizi di dominio Active Directory di Azure, i controller di dominio (DC) che contengono tutte le risorse, ad esempio utenti e gruppi, credenziali e criteri, fanno parte del servizio gestito. Per la ridondanza, vengono creati due controller di dominio come parte di un dominio gestito di Servizi di dominio Active Directory di Azure.For redundancy, two DCs are created as part of an Azure AD DS managed domain. Non è possibile accedere a questi controller di dominio per eseguire attività di gestione. Si crea invece una macchina virtuale di gestione aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure, quindi si installano i normali strumenti di gestione di Servizi di dominio Active Directory.Instead, you create a management VM that's joined to the Azure AD DS managed domain, then install your regular AD DS management tools. È possibile utilizzare, ad esempio, il Centro di amministrazione di Active Directory o gli snap-in di Microsoft Management Console (MMC) come gli oggetti DNS o Criteri di gruppo.

## <a name="user-account-creation"></a>Creazione dell'account utente

Gli account utente possono essere creati in Azure AD DS in diversi modi. La maggior parte degli account utente viene sincronizzata da Azure AD, che può includere anche l'account utente sincronizzato da un ambiente di Servizi di dominio Active Directory locale. È anche possibile creare manualmente gli account direttamente in Servizi di dominio Active Directory di Azure.You can also manually create accounts directly in Azure AD DS. Alcune funzionalità, come la sincronizzazione iniziale delle password o i criteri password, si comportano in modo diverso a seconda di come e dove vengono creati gli account utente.

* L'account utente può essere sincronizzato da Azure AD. Sono inclusi gli account utente solo cloud creati direttamente in Azure AD e gli account utente ibridi sincronizzati da un ambiente di Servizi di dominio Active Directory locale tramite Azure AD Connect.This includes cloud-only user accounts created directly in Azure AD, and hybrid user accounts synchronized from an on-premises AD DS environment using Azure AD Connect.
    * La maggior parte degli account utente in Servizi di dominio Active Directory di Azure viene creata tramite il processo di sincronizzazione da Azure AD.
* L'account utente può essere creato manualmente in un dominio gestito di Servizi di dominio Active Directory di Azure e non esiste in Azure AD.
    * Se è necessario creare account di servizio per le applicazioni che vengono eseguite solo in Servizi di dominio Active Directory di Azure, è possibile crearli manualmente nel dominio gestito. Poiché la sincronizzazione è un modo da Azure AD, gli account utente creati in Servizi di dominio Active Directory di Azure non vengono sincronizzati di nuovo con Azure AD.

## <a name="password-policy"></a>Criteri password

Servizi di dominio Active Directory di Azure include criteri password predefiniti che definiscono le impostazioni per elementi quali il blocco degli account, la validità massima della password e la complessità della password. Impostazioni come i criteri di blocco degli account si applicano a tutti gli utenti in Servizi di dominio Active Directory di Azure, indipendentemente dal modo in cui l'utente è stato creato come descritto nella sezione precedente. Alcune impostazioni, ad esempio la lunghezza minima della password e la complessità della password, si applicano solo agli utenti creati direttamente in Servizi di dominio Active Directory di Azure.A few settings, like minimum password length and password complexity, only apply to users created directly in Azure AD DS.

È possibile creare criteri password personalizzati per eseguire l'override dei criteri predefiniti in Servizi di dominio Active Directory di Azure.You can create your own custom password policies to override the default policy in Azure AD DS. Questi criteri personalizzati possono quindi essere applicati a gruppi specifici di utenti in base alle esigenze.

Per ulteriori informazioni sulle differenze nella modalità di applicazione dei criteri password a seconda dell'origine della creazione dell'utente, vedere Criteri di blocco di [password e account nei domini gestiti][password-policy].

## <a name="password-hashes"></a>Hashes password

Per autenticare gli utenti nel dominio gestito, Azure AD DS necessita dell'hash delle password in un formato idoneo per l'autenticazione NTLM (NT LAN Manager) e Kerberos. Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

Per gli account utente solo cloud, gli utenti devono cambiare le loro password prima di poter usare Azure AD DS. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM. L'account non viene sincronizzato da Azure AD ad Azure AD DS finché non viene modificata la password.

Per gli utenti sincronizzati da un ambiente di Servizi di dominio Active Directory locale tramite Azure AD Connect, [abilitare la sincronizzazione degli hash delle password.][hybrid-phs]

> [!IMPORTANT]
> Azure AD Connect sincronizza solo gli assi delle password legacy quando si abilita Servizi di dominio Active Directory di Azure per il tenant di Azure AD. Gli errori delle password legacy non vengono usati se si usa Azure AD Connect solo per sincronizzare un ambiente DiS di Servizi di dominio Active Directory locale con Azure AD.
>
> Se le applicazioni legacy non usano l'autenticazione NTLM o i binding semplici LDAP, è consigliabile disabilitare la sincronizzazione dell'hash delle password NTLM per Servizi di dominio Azure AD. Per ulteriori informazioni, vedere Disabilitare le suite di [crittografia deboli e la sincronizzazione dell'hash delle credenziali NTLM.][secure-domain]

Dopo la corretta configurazione, gli hash delle password utilizzabili vengono archiviati nel dominio gestito di Azure AD DS. Se si elimina il dominio gestito di Azure AD DS, verranno eliminati anche gli hash delle password archiviati in quel momento. Le informazioni sulle credenziali sincronizzate in Azure AD non possono essere riutilizzate se in seguito si crea un dominio gestito di Servizi di dominio Active Directory di Azure: è necessario riconfigurare la sincronizzazione dell'hash delle password per archiviare nuovamente gli hash delle password. Le VM o gli utenti precedentemente aggiunti al domino non saranno in grado di eseguire immediatamente l'autenticazione. Azure AD deve generare e archiviare gli hash delle password nel nuovo dominio gestito di Azure AD DS. Per altre informazioni, vedere [Processo di sincronizzazione degli hash delle password per Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti AD DS locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito di Azure AD DS per sincronizzare gli oggetti con Azure AD.

## <a name="forests-and-trusts"></a>Foreste e trust

Una *foresta* è un costrutto logico utilizzato da Servizi di dominio Active Directory per raggruppare uno o più *domini.* I domini archiviano quindi gli oggetti per utenti o gruppi e forniscono servizi di autenticazione.

In Servizi di dominio Active Directory di Azure la foresta contiene un solo dominio. Le foreste locali di Servizi di dominio Active Directory spesso contengono molti domini. Nelle organizzazioni di grandi dimensioni, in particolare dopo fusioni e acquisizioni, è possibile che si verifichino più foreste locali che contengono più domini.

Per impostazione predefinita, un dominio gestito di Servizi di dominio Active Directory di Azure viene creato come foresta *utente.* Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Gli account utente possono eseguire direttamente l'autenticazione nel dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli isemi delle password possono essere sincronizzati e gli utenti non utilizzano metodi di accesso esclusivi come l'autenticazione con smart card.

In una foresta di *risorse* di Servizi di dominio Active Directory di Azure gli utenti eseguono l'autenticazione tramite un *trust* tra foreste unidirezionale dal dominio Active Directory locale. Con questo approccio, gli oggetti utente e gli ishe delle password non vengono sincronizzati con Azure AD DS. Gli oggetti utente e le credenziali esistono solo in Servizi di dominio Active Directory locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme di applicazioni in Azure che dipendono dall'autenticazione classica, ad esempio LDAPS, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione. Le foreste di risorse Azure AD Domain Services sono attualmente disponibili in anteprima.

Per altre informazioni sui tipi di foresta in Servizi di dominio Active Directory di Azure, vedere Che cosa sono le foreste di [risorse?][concepts-forest] e Come funzionano i trust della [foresta in Azure AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

In Servizi di dominio Active Directory di Azure le prestazioni e le funzionalità disponibili sono basate sullo SKU. Si seleziona uno SKU quando si crea il dominio gestito ed è possibile passare SKU come i requisiti aziendali cambiano dopo la distribuzione del dominio gestito. Nella tabella seguente vengono descritti gli SKU disponibili e le differenze tra di essi:

| Nome SKU   | Numero massimo di oggetti | Frequenza di backup | Numero massimo di trust tra foreste in uscita |
|------------|----------------------|------------------|----|
| Standard   | Nessuna limitazione            | Ogni 7 giorni     | 0  |
| Enterprise | Nessuna limitazione            | Ogni 3 giorni     | 5  |
| Premium    | Nessuna limitazione            | Giornaliera            | 10 |

Prima di questi SKU di Servizi di dominio Active Directory di Azure, è stato usato un modello di fatturazione basato sul numero di oggetti (account utente e computer) nel dominio gestito di Servizi di dominio Active Directory di Azure.Before these Azure AD DS SKUs, a billing model based on the number of objects (user and computer accounts) in the Azure AD DS managed domain was used. Non esiste più prezzi variabili in base al numero di oggetti nel dominio gestito.

Per altre informazioni, vedere la [pagina dei prezzi di Azure AD DS][pricing].For more information, see the Azure AD DS pricing page .

### <a name="managed-domain-performance"></a>Prestazioni del dominio gestito

Le prestazioni del dominio variano in base alla modalità di implementazione dell'autenticazione per un'applicazione. Ulteriori risorse di calcolo possono contribuire a migliorare il tempo di risposta alle query e ridurre il tempo impiegato nelle operazioni di sincronizzazione. Con l'aumentare del livello SKU, le risorse di calcolo disponibili per il dominio gestito vengono aumentate. Monitorare le prestazioni delle applicazioni e pianificare le risorse necessarie.

Se le richieste aziendali o dell'applicazione cambiano e è necessaria una potenza di calcolo aggiuntiva per il dominio gestito di Azure AD DS, è possibile passare a uno SKU diverso.

### <a name="backup-frequency"></a>Frequenza di backup

La frequenza di backup determina la frequenza con cui viene creato uno snapshot del dominio gestito. I backup sono un processo automatizzato gestito dalla piattaforma Azure.Backups are an automated process managed by the Azure platform. In caso di problemi con il dominio gestito, il supporto di Azure può essere supportato per il ripristino dal backup. Poiché la sincronizzazione si verifica *solo in* un modo da Azure AD, eventuali problemi in un dominio gestito di Servizi di dominio Active Directory di Azure non influiranno sugli ambienti e sulle funzionalità di Servizi di dominio Active Directory di Azure o locali.

Con l'aumentare del livello SKU, aumenta la frequenza di tali snapshot di backup. Esaminare i requisiti aziendali e l'obiettivo del punto di ripristino (RPO) per determinare la frequenza di backup richiesta per il dominio gestito. Se i requisiti aziendali o dell'applicazione cambiano e sono necessari backup più frequenti, è possibile passare a uno SKU diverso.

### <a name="outbound-forests"></a>Foreste in uscita

Nella sezione precedente sono stati descritti in dettaglio i trust di foresta in uscita unidirezionali da un dominio gestito di Servizi di dominio Active Directory di Azure a un ambiente di Servizi di dominio Active Directory locale (attualmente in anteprima). Lo SKU determina il numero massimo di trust tra foreste che è possibile creare per un dominio gestito di Azure AD DS.The SKU determines the maximum number of forest trusts you can create for an Azure AD DS managed domain. Esaminare i requisiti aziendali e delle applicazioni per determinare il numero di trust effettivamente necessari e scegliere lo SKU di Azure AD DS appropriato. Anche in questo caso, se i requisiti aziendali cambiano ed è necessario creare ulteriori trust tra foreste, è possibile passare a uno SKU diverso.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, creare un dominio gestito di [Azure AD DS.][create-instance]

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
