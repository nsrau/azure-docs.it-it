---
title: Concetti di gestione per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come amministrare un Azure Active Directory Domain Services dominio gestito e il comportamento degli account utente e delle password
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481017"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Concetti relativi alla gestione di account utente, password e amministrazione in Azure Active Directory Domain Services

Quando si crea ed esegue un dominio gestito di Azure Active Directory Domain Services (AD DS), esistono alcune differenze nel comportamento rispetto a un ambiente di servizi di dominio Active Directory locale tradizionale. Si utilizzano gli stessi strumenti di amministrazione in Azure AD DS come dominio gestito autonomamente, ma non è possibile accedere direttamente ai controller di dominio (DC). Esistono inoltre alcune differenze nel comportamento per i criteri password e gli hash delle password a seconda dell'origine della creazione dell'account utente.

Questo articolo concettuale illustra come amministrare un dominio gestito di Azure AD DS e il comportamento diverso degli account utente a seconda del modo in cui sono stati creati.

## <a name="domain-management"></a>Gestione del dominio

In Azure AD DS, i controller di dominio che contengono tutte le risorse, ad esempio utenti e gruppi, le credenziali e i criteri, fanno parte del servizio gestito. Per la ridondanza, vengono creati due controller di dominio come parte di un dominio gestito Azure AD DS. Non è possibile accedere a questi controller di dominio per eseguire attività di gestione. Si crea invece una macchina virtuale di gestione aggiunta al dominio gestito di Azure AD DS, quindi si installano gli strumenti di gestione di servizi di dominio Active Directory regolari. Ad esempio, è possibile utilizzare gli snap-in di Centro di amministrazione di Active Directory o di Microsoft Management Console (MMC) come DNS o Criteri di gruppo oggetti.

## <a name="user-account-creation"></a>Creazione dell'account utente

Gli account utente possono essere creati in Azure AD DS in diversi modi. La maggior parte degli account utente viene sincronizzata da Azure AD, che può anche includere account utente sincronizzati da un ambiente di servizi di dominio Active Directory locale. È anche possibile creare manualmente gli account direttamente in Azure AD DS. Alcune funzionalità, ad esempio la sincronizzazione iniziale delle password o i criteri password, si comportano in modo diverso a seconda di come e dove vengono creati gli account utente.

* L'account utente può essere sincronizzato da Azure AD. Sono inclusi gli account utente solo cloud creati direttamente in Azure AD e gli account utente ibridi sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect.
    * La maggior parte degli account utente in Azure AD DS vengono creati tramite il processo di sincronizzazione da Azure AD.
* L'account utente può essere creato manualmente in un dominio gestito Azure AD DS e non esiste nel Azure AD.
    * Se è necessario creare account di servizio per le applicazioni che vengono eseguite solo in Azure AD DS, è possibile crearle manualmente nel dominio gestito. Poiché la sincronizzazione è unidirezionale da Azure AD, gli account utente creati in Azure AD DS non vengono sincronizzati di nuovo con Azure AD.

## <a name="password-policy"></a>Criteri password

Azure AD DS include un criterio predefinito per le password che definisce le impostazioni per elementi come il blocco degli account, la validità massima della password e la complessità della password. Impostazioni come i criteri di blocco degli account si applicano a tutti gli utenti in Azure AD DS, indipendentemente dalla modalità di creazione dell'utente, come illustrato nella sezione precedente. Alcune impostazioni, come la lunghezza minima della password e la complessità delle password, si applicano solo agli utenti creati direttamente in Azure AD DS.

È possibile creare criteri password personalizzati per sostituire i criteri predefiniti in Azure AD DS. Questi criteri personalizzati possono quindi essere applicati a gruppi di utenti specifici in base alle esigenze.

Per ulteriori informazioni sulle differenze tra l'applicazione dei criteri password a seconda dell'origine della creazione dell'utente, vedere [criteri di blocco di password e account nei domini gestiti][password-policy].

## <a name="password-hashes"></a>Hash delle password

Per autenticare gli utenti nel dominio gestito, Azure AD DS necessita dell'hash delle password in un formato idoneo per l'autenticazione NTLM (NT LAN Manager) e Kerberos. Azure AD non genera né archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure AD DS per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

Per gli account utente solo cloud, gli utenti devono cambiare le loro password prima di poter usare Azure AD DS. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM.

Per gli utenti sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect, [abilitare la sincronizzazione degli hash delle password][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect sincronizza solo gli hash delle password legacy quando si Abilita Azure AD DS per il tenant di Azure AD. Gli hash delle password legacy non vengono usati se si usa solo Azure AD Connect per sincronizzare un ambiente Active Directory Domain Services locale con Azure AD.
>
> Se le applicazioni legacy non usano l'autenticazione NTLM o binding semplici LDAP, è consigliabile disabilitare la sincronizzazione dell'hash delle password NTLM per Azure AD DS. Per altre informazioni, vedere disabilitare i pacchetti di [crittografia vulnerabili e la sincronizzazione degli hash delle credenziali NTLM][secure-domain].

Dopo la corretta configurazione, gli hash delle password utilizzabili vengono archiviati nel dominio gestito di Azure AD DS. Se si elimina il dominio gestito di Azure AD DS, verranno eliminati anche gli hash delle password archiviati in quel momento. Le informazioni sulle credenziali sincronizzate in Azure AD non possono essere riutilizzate se in un secondo momento si crea un dominio gestito Azure AD DS. è necessario riconfigurare la sincronizzazione dell'hash delle password per archiviare di nuovo gli hash delle password. Le VM o gli utenti precedentemente aggiunti al domino non saranno in grado di eseguire immediatamente l'autenticazione. Azure AD deve generare e archiviare gli hash delle password nel nuovo dominio gestito di Azure AD DS. Per altre informazioni, vedere [Processo di sincronizzazione degli hash delle password per Azure AD DS e Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti di servizi di dominio Active Directory locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito Azure AD DS per sincronizzare nuovamente gli oggetti con Azure AD.

## <a name="forests-and-trusts"></a>Foreste e trust

Una *foresta* è un costrutto logico usato da Active Directory Domain Services (ad DS) per raggruppare uno o più *domini*. I domini archiviano quindi gli oggetti per utenti o gruppi e forniscono servizi di autenticazione.

In Azure AD DS la foresta contiene solo un dominio. Le foreste di servizi di dominio Active Directory locali contengono spesso molti domini. Nelle organizzazioni di grandi dimensioni, soprattutto dopo fusioni e acquisizioni, è possibile che si verifichino più foreste locali, ognuna delle quali contiene più domini.

Per impostazione predefinita, un dominio gestito Azure AD DS viene creato come foresta *utente* . Questo tipo di foresta Sincronizza tutti gli oggetti da Azure AD, inclusi tutti gli account utente creati in un ambiente di servizi di dominio Active Directory locale. Gli account utente possono eseguire l'autenticazione direttamente nel dominio gestito di Azure AD DS, ad esempio per accedere a una macchina virtuale aggiunta a un dominio. Una foresta utente funziona quando gli hash delle password possono essere sincronizzati e gli utenti non usano metodi di accesso esclusivi come l'autenticazione con smart card.

In una foresta di *risorse* Azure AD DS, gli utenti eseguono l'autenticazione su un *trust* tra foreste unidirezionale rispetto ai servizi di dominio Active Directory locali. Con questo approccio, gli oggetti utente e gli hash delle password non vengono sincronizzati con Azure AD DS. Gli oggetti e le credenziali utente esistono solo in servizi di dominio Active Directory locale. Questo approccio consente alle aziende di ospitare risorse e piattaforme applicative in Azure che dipendono dall'autenticazione classica, ad esempio LDAPs, Kerberos o NTLM, ma vengono rimossi eventuali problemi di autenticazione o problemi. Le foreste di risorse Azure AD DS sono attualmente in anteprima.

Per altre informazioni sui tipi di foresta in Azure AD DS, vedere [che cosa sono le foreste di risorse?][concepts-forest] e [come funzionano i trust tra foreste in Azure AD DS?][concepts-trust]

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
