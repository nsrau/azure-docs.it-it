---
title: Scenari di distribuzione comuni per Azure AD Domain Services | Microsoft Docs
description: Informazioni su alcuni scenari comuni e casi d'uso per Azure Active Directory Domain Services per fornire valore e soddisfare le esigenze aziendali.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: iainfou
ms.openlocfilehash: 6f81bc2ccf11cbcc3621dc1149879864c88cf0cf
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980510"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casi d'uso comuni e scenari per Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) fornisce servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Azure Active Directory Domain Services si integra con il tenant di Azure AD esistente, consentendo così agli utenti di eseguire l'accesso usando le proprie credenziali già configurate. Questi servizi di dominio vengono usati senza la necessità di distribuire, gestire e applicare patch ai controller di dominio nel cloud, che fornisce un lift-and-Shift più agevole delle risorse locali in Azure.

In questo articolo vengono illustrati alcuni scenari aziendali comuni in cui Azure AD DS fornisce valore e soddisfa tali esigenze.

## <a name="secure-administration-of-azure-virtual-machines"></a>Amministrazione sicura di macchine virtuali di Azure

Per consentire l'uso di un singolo set di credenziali di Active Directory, le macchine virtuali (VM) di Azure possono essere unite a un dominio gestito di Azure AD DS. Questo approccio consente di ridurre i problemi di gestione delle credenziali, ad esempio la gestione degli account amministratore locale in ogni VM o la separazione di account e password tra ambienti.

Le macchine virtuali che fanno parte di un dominio gestito Azure AD DS possono essere gestite e protette anche usando Criteri di gruppo. Le linee di base di sicurezza richieste possono essere applicate alle macchine virtuali per bloccarle in base alle linee guida di sicurezza aziendali. Ad esempio, è possibile usare le funzionalità di gestione di criteri di gruppo per limitare i tipi di applicazioni che possono essere avviate nella macchina virtuale.

![Gestione ottimizzata delle macchine virtuali di Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Esaminiamo uno scenario di esempio comune. Poiché i server e altre infrastrutture raggiungono la fine del ciclo di vita, Contoso desidera spostare le applicazioni attualmente ospitate in locale nel cloud. Il rispettivo standard IT impone che i server che ospitano applicazioni aziendali debbano essere aggiunti a un dominio e gestiti tramite criteri di gruppo. L'amministratore IT di Contoso preferisce aggiungere a un dominio le VM distribuite in Azure per semplificare l'amministrazione perché gli utenti possono quindi accedere con le proprie credenziali aziendali. Quando si aggiunge un dominio, le macchine virtuali possono anche essere configurate per essere conformi alle linee di base di sicurezza obbligatorie usando Criteri di gruppo. Contoso preferisce non distribuire, monitorare e gestire i propri controller di dominio in Azure.

Azure AD DS rappresenta un'ottima soluzione per questo caso d'uso. Un dominio gestito di Azure AD DS consente l'aggiunta di un dominio alle VM, l'utilizzo di un singolo set di credenziali e l'applicazione di criteri di gruppo. In quanto dominio gestito, non è necessario configurare e gestire manualmente i controller di dominio.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Per impostazione predefinita, i domini gestiti di Azure AD DS usano una singola struttura di unità organizzativa (OU) flat. Tutte le macchine virtuali appartenenti a un dominio si trovano in una singola unità organizzativa. Se lo si desidera, è possibile creare unità organizzative personalizzate.
* Azure AD DS utilizza un oggetto Criteri di gruppo incorporato per i contenitori utenti e computer. Per un controllo aggiuntivo, è possibile creare oggetti Criteri di gruppo personalizzati e indirizzarli a unità organizzative personalizzate.
* Azure AD DS supporta lo schema dell'oggetto computer AD di base. Non è possibile estendere lo schema dell'oggetto computer.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Applicazioni locali Lift-and-Shift che usano l'autenticazione di binding LDAP

Come scenario di esempio, Contoso ha un'applicazione locale che è stata acquistata da un ISV diversi anni fa. L'applicazione è attualmente in modalità di manutenzione da parte dell'ISV e la richiesta di modifiche all'applicazione è eccessivamente costosa. Questa applicazione dispone di un front-end basato sul Web che raccoglie le credenziali utente usando un modulo Web e quindi autentica gli utenti eseguendo un'associazione LDAP all'ambiente di servizi di dominio Active Directory locale.

![Binding LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso desidera eseguire la migrazione di questa applicazione in Azure. L'applicazione dovrebbe continuare a funzionare così com'è, senza che siano necessarie modifiche. Inoltre, gli utenti devono essere in grado di eseguire l'autenticazione usando le credenziali aziendali esistenti e senza formazione aggiuntiva. Dovrebbe essere trasparente per gli utenti finali in cui l'applicazione è in esecuzione.

Per questo scenario, Azure AD DS consente alle applicazioni di eseguire associazioni LDAP come parte del processo di autenticazione. Le applicazioni locali legacy possono eseguire il lift-and-Shift in Azure e continuare a autenticare senza problemi gli utenti senza alcuna modifica nella configurazione o nell'esperienza utente.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito di Azure AD DS non è supportato.
* Non è possibile modificare le password direttamente in un dominio gestito Azure AD DS. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e disponibili nel dominio gestito Azure AD DS.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Applicazioni locali Lift-and-Shift che usano LDAP Read per accedere alla directory

Come lo scenario di esempio precedente, si supponga che Contoso disponga di un'applicazione line-of-business (LOB) locale sviluppata quasi un decennio fa. Questa applicazione è compatibile con le directory ed è stata progettata per utilizzare LDAP (Lightweight Directory Access Protocol) per leggere informazioni/attributi sugli utenti di servizi di dominio Active Directory. L'applicazione non modifica gli attributi o altrimenti scrive nella directory.

Contoso vuole eseguire la migrazione di questa applicazione in Azure e ritirare l'hardware locale obsoleto che ospita attualmente questa applicazione. Non è possibile riscrivere l'applicazione per usare le API di directory moderne, ad esempio le Azure AD basate su REST API Graph. Si desidera un'opzione Lift-and-Shift in cui è possibile eseguire la migrazione dell'applicazione per l'esecuzione nel cloud, senza modificare il codice o riscrivere l'applicazione.

Per semplificare questo scenario, Azure AD DS consente alle applicazioni di eseguire letture LDAP sul dominio gestito per ottenere le informazioni sugli attributi necessarie. Non è necessario riscrivere l'applicazione, quindi un lift-and-Shift in Azure consente agli utenti di continuare a usare l'app senza rendersi conto che è presente una modifica in cui viene eseguito.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito di Azure AD DS non è supportato.
* Assicurarsi che l'applicazione non richieda uno schema di Active Directory personalizzato/esteso. Le estensioni dello schema non sono supportate in Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Eseguire la migrazione di un servizio locale o di un'applicazione daemon in Azure

Alcune applicazioni includono più livelli, in cui uno dei livelli deve eseguire chiamate autenticate a un livello back-end, ad esempio un database. Gli account del servizio Active Directory sono comunemente utilizzati in questi scenari. Quando si esegue il lift-and-Shift delle applicazioni in Azure, Azure AD DS consente di continuare a usare gli account del servizio nello stesso modo. È possibile scegliere di utilizzare lo stesso account del servizio sincronizzato dalla directory locale per Azure AD o creare un'unità organizzativa personalizzata, quindi creare un account di servizio separato in tale unità organizzativa. Con entrambi gli approcci, le applicazioni continuano a funzionare allo stesso modo per eseguire chiamate autenticate ad altri livelli e servizi.

![Account del servizio mediante WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In questo scenario di esempio, Contoso dispone di un'applicazione di insieme di credenziali software personalizzata che include un front-end Web, un server SQL e un server FTP back-end. Autenticazione integrata di Windows con gli account del servizio autentica il front-end Web con il server FTP. Il front-end Web è configurato per l'esecuzione come account del servizio. Il server back-end è configurato per autorizzare l'accesso dall'account del servizio per il front-end Web. Contoso non desidera distribuire e gestire le proprie macchine virtuali del controller di dominio nel cloud per spostare l'applicazione in Azure.

Per questo scenario, i server che ospitano il front-end Web, SQL Server e il server FTP possono essere migrati alle macchine virtuali di Azure e aggiunti a un dominio gestito di Azure AD DS. Le macchine virtuali possono quindi usare lo stesso account del servizio nella directory locale per l'autenticazione dell'app, che viene sincronizzato tramite Azure AD usando Azure AD Connect.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che le applicazioni usino un nome utente e una password per l'autenticazione. L'autenticazione basata su smart card o certificato non è supportata da Azure AD DS.
* Non è possibile modificare le password direttamente in un dominio gestito Azure AD DS. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e disponibili nel dominio gestito Azure AD DS.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Distribuzioni di Servizi Desktop remoto di Windows Server in Azure

È possibile usare Azure AD DS per fornire servizi di dominio gestiti ai server desktop remoto distribuiti in Azure. Per ulteriori informazioni su questo scenario di distribuzione, vedere [come integrare Azure ad Domain Services con la distribuzione di][windows-rds]Servizi Desktop remoto.

## <a name="domain-joined-hdinsight-clusters-preview"></a>Cluster HDInsight aggiunti al dominio (anteprima)

È possibile configurare un cluster Azure HDInsight aggiunto a un dominio gestito di Azure AD DS con Apache Ranger abilitato. Questa funzionalità è attualmente in anteprima. È possibile creare e applicare criteri hive tramite Apache Ranger e consentire agli utenti, ad esempio i data scientist, di connettersi a hive usando strumenti basati su ODBC come Excel o Tableau. Continuiamo a lavorare per aggiungere altri carichi di lavoro, ad esempio HBase, Spark e Storm, a HDInsight aggiunto al dominio.

Per altre informazioni su questo scenario di distribuzione, vedere [come configurare cluster HDInsight aggiunti al dominio][hdinsight]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [creare e configurare un'istanza di Azure Active Directory Domain Services][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
