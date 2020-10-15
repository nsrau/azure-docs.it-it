---
title: Concetti di gestione per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come amministrare un Azure Active Directory Domain Services dominio gestito e il comportamento degli account utente e delle password
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: joflore
ms.openlocfilehash: 95c6a1f24335849fb2d2c4de56b4ed60e8e5f73f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962820"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Concetti relativi alla gestione di account utente, password e amministrazione in Azure Active Directory Domain Services

Quando si crea ed esegue un dominio gestito di Azure Active Directory Domain Services (AD DS), esistono alcune differenze nel comportamento rispetto a un ambiente di servizi di dominio Active Directory locale tradizionale. Si utilizzano gli stessi strumenti di amministrazione in Azure AD DS come dominio gestito autonomamente, ma non è possibile accedere direttamente ai controller di dominio (DC). Esistono inoltre alcune differenze nel comportamento per i criteri password e gli hash delle password a seconda dell'origine della creazione dell'account utente.

Questo articolo concettuale illustra come amministrare un dominio gestito e il comportamento diverso degli account utente a seconda della modalità di creazione.

## <a name="domain-management"></a>Gestione dei domini

Un dominio gestito è costituito da uno spazio dei nomi DNS e da una directory corrispondente. In un dominio gestito, i controller di dominio che contengono tutte le risorse, ad esempio utenti e gruppi, le credenziali e i criteri, fanno parte del servizio gestito. Per la ridondanza, vengono creati due controller di dominio come parte di un dominio gestito. Non è possibile accedere a questi controller di dominio per eseguire attività di gestione. Si crea invece una macchina virtuale di gestione aggiunta al dominio gestito, quindi si installano i normali strumenti di gestione di servizi di dominio Active Directory. Ad esempio, è possibile utilizzare gli snap-in di Centro di amministrazione di Active Directory o di Microsoft Management Console (MMC) come DNS o Criteri di gruppo oggetti.

## <a name="user-account-creation"></a>Creazione dell'account utente

Gli account utente possono essere creati in un dominio gestito in diversi modi. La maggior parte degli account utente viene sincronizzata da Azure AD, che può anche includere account utente sincronizzati da un ambiente di servizi di dominio Active Directory locale. È anche possibile creare manualmente gli account direttamente nel dominio gestito. Alcune funzionalità, ad esempio la sincronizzazione iniziale delle password o i criteri password, si comportano in modo diverso a seconda di come e dove vengono creati gli account utente.

* L'account utente può essere sincronizzato da Azure AD. Sono inclusi gli account utente solo cloud creati direttamente in Azure AD e gli account utente ibridi sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect.
    * La maggior parte degli account utente in un dominio gestito viene creata tramite il processo di sincronizzazione da Azure AD.
* L'account utente può essere creato manualmente in un dominio gestito e non esiste in Azure AD.
    * Se è necessario creare account del servizio per le applicazioni che vengono eseguite solo nel dominio gestito, è possibile crearle manualmente nel dominio gestito. Poiché la sincronizzazione è unidirezionale da Azure AD, gli account utente creati nel dominio gestito non vengono sincronizzati di nuovo con Azure AD.

## <a name="password-policy"></a>Criteri password

Azure AD DS include un criterio predefinito per le password che definisce le impostazioni per elementi come il blocco degli account, la validità massima della password e la complessità della password. Impostazioni come i criteri di blocco degli account si applicano a tutti gli utenti in un dominio gestito, indipendentemente dalla modalità di creazione dell'utente, come illustrato nella sezione precedente. Alcune impostazioni, come la lunghezza minima della password e la complessità delle password, si applicano solo agli utenti creati direttamente in un dominio gestito.

È possibile creare criteri password personalizzati per sostituire i criteri predefiniti in un dominio gestito. Questi criteri personalizzati possono quindi essere applicati a gruppi di utenti specifici in base alle esigenze.

Per ulteriori informazioni sulle differenze tra l'applicazione dei criteri password a seconda dell'origine della creazione dell'utente, vedere [criteri di blocco di password e account nei domini gestiti][password-policy].

## <a name="password-hashes"></a>Hash delle password

Per autenticare gli utenti nel dominio gestito, Azure AD DS necessita dell'hash delle password in un formato idoneo per l'autenticazione NTLM (NT LAN Manager) e Kerberos. Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

Per gli account utente solo cloud, gli utenti devono modificare le password prima di poter usare il dominio gestito. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM. L'account non viene sincronizzato da Azure AD ad Azure AD DS fino a quando non si cambia la password.

Per gli utenti sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect, [abilitare la sincronizzazione degli hash delle password][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect sincronizza solo gli hash delle password legacy quando si Abilita Azure AD DS per il tenant di Azure AD. Gli hash delle password legacy non vengono usati se si usa solo Azure AD Connect per sincronizzare un ambiente Active Directory Domain Services locale con Azure AD.
>
> Se le applicazioni legacy non usano l'autenticazione NTLM o binding semplici LDAP, è consigliabile disabilitare la sincronizzazione dell'hash delle password NTLM per Azure AD DS. Per altre informazioni, vedere disabilitare i pacchetti di [crittografia vulnerabili e la sincronizzazione degli hash delle credenziali NTLM][secure-domain].

Dopo la corretta configurazione, gli hash delle password utilizzabili vengono archiviati nel dominio gestito. Se si elimina il dominio gestito, verranno eliminati anche gli hash delle password archiviati in quel momento. Le informazioni sulle credenziali sincronizzate in Azure AD non possono essere riutilizzate se successivamente si crea un altro dominio gestito. è necessario riconfigurare la sincronizzazione dell'hash delle password per archiviare di nuovo gli hash delle password. Le VM o gli utenti precedentemente aggiunti al domino non saranno in grado di eseguire immediatamente l'autenticazione. Azure AD deve generare e archiviare gli hash delle password nel nuovo dominio gestito. Per altre informazioni, vedere [Processo di sincronizzazione degli hash delle password per Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti AD DS locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito per sincronizzare gli oggetti con Azure AD.

## <a name="forests-and-trusts"></a>Foreste e trust

Una *foresta* è un costrutto logico usato da Active Directory Domain Services (AD DS) per raggruppare uno o più *domini*. I domini archiviano quindi gli oggetti per utenti o gruppi e offrono servizi di autenticazione.

In Azure AD DS la foresta contiene solo un dominio. Le foreste AD DS locali contengono spesso molti domini. Nelle organizzazioni di grandi dimensioni, soprattutto dopo fusioni e acquisizioni, è possibile che vi siano più foreste locali, ognuna delle quali con più domini.

Per impostazione predefinita, un dominio gestito viene creato come foresta *utente* . Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Gli account utente possono eseguire l'autenticazione direttamente nel dominio gestito, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli hash delle password possono essere sincronizzati e gli utenti non usano metodi di accesso esclusivi come l'autenticazione mediante smart card.

In una foresta di *risorse* Azure AD DS, gli utenti eseguono l'autenticazione su un *trust* tra foreste da AD DS locale. Con questo approccio, gli oggetti utente e gli hash delle password non vengono sincronizzati con Azure AD DS. Gli oggetti e le credenziali utente esistono solo in AD DS locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme applicative in Azure che dipendono dall'autenticazione classica, ad esempio LDAPS, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione.

Per altre informazioni sui tipi di foresta in Azure AD DS, vedere [che cosa sono le foreste di risorse?][concepts-forest] e [come funzionano i trust tra foreste in Azure AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>SKU di Azure AD DS

In Azure AD DS, le prestazioni e le funzionalità disponibili sono basate sullo SKU. Si seleziona uno SKU quando si crea il dominio gestito ed è possibile cambiare SKU in seguito alla modifica dei requisiti aziendali dopo la distribuzione del dominio gestito. La tabella seguente descrive gli SKU disponibili e le differenze tra di essi:

| Nome SKU   | Numero massimo oggetti | Frequenza di backup | Numero massimo di trust tra foreste in uscita |
|------------|----------------------|------------------|----|
| Standard   | Nessuna limitazione            | Ogni 7 giorni     | 0  |
| Enterprise | Nessuna limitazione            | Ogni 3 giorni     | 5  |
| Premium    | Nessuna limitazione            | Ogni giorno            | 10 |

Prima di questi SKU Azure AD DS, è stato usato un modello di fatturazione basato sul numero di oggetti (account utente e computer) nel dominio gestito. Non sono più disponibili prezzi variabili in base al numero di oggetti nel dominio gestito.

Per ulteriori informazioni, vedere la [pagina dei prezzi di Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Prestazioni del dominio gestito

Le prestazioni del dominio variano a seconda della modalità di implementazione dell'autenticazione per un'applicazione. Risorse di calcolo aggiuntive possono contribuire a migliorare i tempi di risposta alle query e a ridurre il tempo impiegato per le operazioni di sincronizzazione. Con l'aumentare del livello di SKU, vengono aumentate le risorse di calcolo disponibili per il dominio gestito. Monitorare le prestazioni delle applicazioni e pianificare le risorse necessarie.

Se le esigenze dell'azienda o dell'applicazione cambiano ed è necessaria una potenza di calcolo aggiuntiva per il dominio gestito, è possibile passare a uno SKU diverso.

### <a name="backup-frequency"></a>Frequenza di backup

La frequenza di backup determina la frequenza con cui viene eseguito uno snapshot del dominio gestito. I backup sono un processo automatizzato gestito dalla piattaforma Azure. In caso di problemi con il dominio gestito, il supporto tecnico di Azure può risultare utile per il ripristino da un backup. Poiché la sincronizzazione si verifica solo una *delle modalità di* Azure ad, eventuali problemi in un dominio gestito non avranno alcun effetto Azure ad o ambienti e funzionalità locali di servizi di dominio Active Directory.

Con l'aumentare del livello di SKU, viene aumentata la frequenza degli snapshot di backup. Esaminare i requisiti aziendali e l'obiettivo del punto di ripristino (RPO) per determinare la frequenza di backup necessaria per il dominio gestito. Se i requisiti aziendali o dell'applicazione cambiano e sono necessari backup più frequenti, è possibile passare a uno SKU diverso.

### <a name="outbound-forest-trusts"></a>Trust tra foreste in uscita

Nella sezione precedente sono stati descritti i trust tra foreste in uscita unidirezionali da un dominio gestito a un ambiente di servizi di dominio Active Directory locale. Lo SKU determina il numero massimo di trust tra foreste che è possibile creare per un dominio gestito. Esaminare i requisiti aziendali e delle applicazioni per determinare il numero di trust effettivamente necessari e scegliere lo SKU Azure AD DS appropriato. Anche in questo caso, se i requisiti aziendali cambiano ed è necessario creare trust tra foreste aggiuntivi, è possibile passare a uno SKU diverso.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [creare un dominio gestito Azure AD DS][create-instance].

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
