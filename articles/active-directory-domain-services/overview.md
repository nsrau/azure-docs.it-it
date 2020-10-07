---
title: Panoramica dei Servizi di dominio Azure Active Directory | Documentazione Microsoft
description: Questa panoramica illustra le funzionalità offerte da Azure Active Directory Domain Services e descrive come usare questa soluzione in un'organizzazione per fornire servizi di gestione delle identità ad applicazioni e servizi nel cloud.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2020
ms.author: iainfou
ms.custom: contperfq1
ms.openlocfilehash: 2255f2193378d0cc3611680c6aa5f8e0837e99aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88245179"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Che cos'è Azure Active Directory Domain Services?

Azure Active Directory Domain Services (AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, Lightweight Directory Access Protocol (LDAP) e l'autenticazione Kerberos/NTLM. È possibile usare questi servizi di dominio senza dover distribuire, gestire e applicare patch ai controller di dominio nel cloud.

Un dominio gestito di Azure AD DS consente di eseguire applicazioni legacy nel cloud che non possono usare metodi di autenticazione moderni o in cui non si vuole che le ricerche nelle directory tornino sempre a un ambiente Active Directory Domain Services locale. È possibile trasferire in modalità lift-and-shift tali applicazioni legacy dall'ambiente locale a un dominio gestito, senza dover gestire l'ambiente Active Directory Domain Services nel cloud.

Azure AD DS si integra con il tenant di Azure AD esistente. Grazie a questa integrazione agli utenti possono accedere a servizi e applicazioni connessi al dominio gestito usando le credenziali esistenti. Per proteggere l'accesso alle risorse, è anche possibile usare gruppi e account utente esistenti. Queste funzionalità consentono un trasferimento in modalità lift-and-shift più agevole delle risorse locali in Azure.

> [!div class="nextstepaction"]
> [Per iniziare, creare un dominio gestito di Azure Active Directory Domain Services usando il portale di Azure][tutorial-create]

## <a name="how-does-azure-ad-ds-work"></a>Come funziona Azure Active Directory Domain Services?

Quando si crea un dominio gestito di Azure AD DS, si definisce uno spazio dei nomi univoco. Questo spazio dei nomi è il nome di dominio, ad esempio *aaddscontoso.com*. Due controller di dominio Windows Server vengono quindi distribuiti nell'area di Azure selezionata. Questa distribuzione di controller di dominio è nota come set di repliche.

Non è necessario gestire, configurare o aggiornare questi controller di dominio. La piattaforma Azure gestisce i controller di dominio, inclusi i backup, come parte del dominio gestito.

Un dominio gestito è configurato in modo da eseguire una sincronizzazione unidirezionale da Azure AD per consentire l'accesso a un set centrale di utenti, gruppi e credenziali. È possibile creare risorse direttamente nel dominio gestito, ma queste non vengono risincronizzate con Azure AD. Le applicazioni, i servizi e le VM in Azure che si connettono al dominio gestito possono quindi usare funzionalità comuni di Active Directory Domain Services come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM.

In un ambiente ibrido con un ambiente Active Directory Domain Services locale, [Azure AD Connect][azure-ad-connect] sincronizza le informazioni sulle identità con Azure AD e quindi con il dominio gestito.

![Sincronizzazione in Azure AD Domain Services con Azure AD e Active Directory Domain Services in locale usando AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS replica le informazioni relative alle identità da Azure AD e usa quindi tenant di Azure AD solo cloud o sincronizzati con un ambiente di Active Directory Domain Services locale. Lo stesso set di funzionalità di Azure Active Directory Domain Services è disponibile per entrambi gli ambienti.

* Se è già presente un ambiente di Active Directory Domain Services locale, è possibile sincronizzare le informazioni degli account utente per fornire agli utenti un'identità coerente. Per altre informazioni, vedere [Sincronizzazione di oggetti e credenziali in un dominio gestito][synchronization].
* Per gli ambienti solo cloud, non è necessario un ambiente di Active Directory Domain Services locale tradizionale per usare i servizi di gestione delle identità centralizzati di Azure Active Directory Domain Services.

È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. È possibile aggiungere i set di repliche a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporti Azure AD DS. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline. I set di repliche sono attualmente disponibili in anteprima. Per altre informazioni, vedere [Concetti e funzionalità dei set di repliche per i domini gestiti][concepts-replica-sets].

Il video seguente offre una panoramica del modo in cui Azure Active Directory Domain Services si integra con le applicazioni e i carichi di lavoro per offrire servizi di gestione delle identità nel cloud:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Per informazioni sul funzionamento degli scenari di distribuzione di Azure AD DS, è possibile esaminare gli esempi seguenti:

* [Azure Active Directory Domain Services per le organizzazioni ibride](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure Active Directory Domain Services per le organizzazioni solo cloud](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Funzionalità e vantaggi di Azure Active Directory Domain Services

Per fornire servizi di gestione delle identità ad applicazioni e macchine virtuali nel cloud, Azure Active Directory Domain Services è completamente compatibile con un ambiente di Active Directory Domain Services tradizionale per operazioni come l'aggiunta a un dominio, LDAP sicuro (LDAPS), Criteri di gruppo, Gestione DNS e il supporto per la lettura e il binding LDAP. Il supporto per la scrittura LDAP è disponibile per gli oggetti creati nel dominio gestito, ma non per le risorse sincronizzate da Azure AD.

Per altre informazioni sulle opzioni per le identità, [confrontare Azure AD DS con Azure AD, Active Directory Domain Services in VM di Azure e Active Directory Domain Services in locale][compare].

Le funzionalità di Azure Active Directory Domain Services descritte di seguito semplificano le operazioni di distribuzione e gestione:

* **Esperienza di distribuzione semplificata:** Azure Active Directory Domain Services viene abilitato per il tenant di Azure AD tramite una singola procedura guidata nel portale di Azure.
* **Integrazione con Azure AD:** gli account utente, le appartenenze ai gruppi e le credenziali sono automaticamente disponibili dal tenant di Azure AD. I nuovi utenti, i gruppi o le modifiche relative agli attributi dal tenant di Azure AD o dall'ambiente di Active Directory Domain Services locale vengono sincronizzati automaticamente in Azure Active Directory Domain Services.
    * Gli account in directory esterne collegate all'istanza di Azure AD non sono disponibili in Azure Active Directory Domain Services. Le credenziali non sono disponibili per tali directory esterne e quindi non possono essere sincronizzate in un dominio gestito.
* **Usare le credenziali e/o le password aziendali:** le password degli utenti in Azure AD DS sono uguali a quelle del tenant Azure AD. Gli utenti possono usare le credenziali aziendali per aggiungere computer al dominio, effettuare l'accesso in modo interattivo o tramite desktop remoto e autenticarsi nel dominio gestito.
* **Autenticazione NTLM e Kerberos:** con il supporto per l'autenticazione NTLM e Kerberos, è possibile distribuire applicazioni basate sull'autenticazione integrata di Windows.
* **Disponibilità elevata:** Azure Active Directory Domain Services include più controller di dominio, che garantiscono la disponibilità elevata per il dominio gestito, con conseguenti vantaggi in termini di tempo di attività del servizio e resilienza agli errori.
    * Nelle aree in cui sono supportate le [zone di disponibilità di Azure][availability-zones], questi controller di dominio vengono distribuiti anche tra le zone per una maggiore resilienza.
    * È anche possibile usare [set di replica][concepts-replica-sets] per offrire il ripristino di emergenza geografico di applicazioni legacy se un'area di Azure passa in modalità offline.

Ecco alcuni aspetti chiave di un dominio gestito:

* Il dominio gestito rappresenta un dominio autonomo. Non è un'estensione di un dominio locale.
    * Se necessario, è possibile creare trust tra foreste in uscita unidirezionali da Azure AD DS a un ambiente di AD DS locale. Per altre informazioni, vedere [Concetti e funzionalità della foresta di risorse per Azure AD DS][ forest-trusts].
* Il team IT non deve gestire, applicare patch o monitorare i controller di dominio per il dominio gestito.

Per gli ambienti ibridi che eseguono Active Directory Domain Services in locale, non è necessario gestire la replica di Active Directory nel dominio gestito. Gli account utente, le appartenenze ai gruppi e le credenziali della directory locale vengono sincronizzati in Azure AD tramite [Azure AD Connect][azure-ad-connect]. Gli account utente, le appartenenze ai gruppi e le credenziali sono automaticamente disponibili all'interno del dominio gestito.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul confronto tra Azure Active Directory Domain Services e altre soluzioni di gestione delle identità e sulla modalità di funzionamento della sincronizzazione, vedere gli articoli seguenti:

* [Confrontare Azure AD DS con Azure AD, Active Directory Domain Services in macchine virtuali di Azure e Active Directory Domain Services in locale][compare]
* [Informazioni sulla sincronizzazione di Azure Active Directory Domain Services con la directory di Azure AD][synchronization]
* Per informazioni su come amministrare un dominio gestito, vedere [Concetti relativi alla gestione di account utente, password e amministrazione in Azure Active Directory Domain Services][administration-concepts].

Per iniziare, [creare un dominio gestito usando il portale di Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
