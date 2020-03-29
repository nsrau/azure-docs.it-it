---
title: Scenari di distribuzione comuni per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su alcuni degli scenari comuni e dei casi d'uso per Servizi di dominio Azure Active Directory per fornire valore e soddisfare le esigenze aziendali.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917231"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casi d'uso e scenari comuni per Servizi di dominio Azure Active DirectoryCommon use-cases and scenarios for Azure Active Directory Domain Services

Servizi di dominio Azure Active Directory (Azure AD DS) fornisce servizi di dominio gestito, ad esempio l'aggiunta a un dominio, criteri di gruppo, LDAP (Lightweight Directory Access Protocol) e l'autenticazione Kerberos / NTLM. Azure Active Directory Domain Services si integra con il tenant di Azure AD esistente, consentendo così agli utenti di eseguire l'accesso usando le proprie credenziali già configurate. Questi servizi di dominio vengono utilizzati senza la necessità di distribuire, gestire e applicare patch ai controller di dominio nel cloud, che offre un passaggio più agevole delle risorse locali in Azure.You use these domain services without the need to deploy, manage, and patch domain controllers in the cloud, which provides a smomore lift-and-shift of on-premises resources to Azure.

Questo articolo descrive alcuni scenari aziendali comuni in cui Azure AD DS fornisce valore e soddisfa tali esigenze.

## <a name="secure-administration-of-azure-virtual-machines"></a>Amministrazione sicura delle macchine virtuali di AzureSecure administration of Azure virtual machines

Per usare un singolo set di credenziali di Active Directory, è possibile creare alcune macchine virtuali di Azure a un dominio gestito di Servizi di dominio Active Directory di Azure.To let you use a single set of AD credentials, Azure virtual machines (VMs) can be joined to an Azure AD DS managed domain. Questo approccio riduce i problemi di gestione delle credenziali, ad esempio la gestione degli account amministratore locale in ogni macchina virtuale o in ogni macchina virtuale o in account e password separati tra gli ambienti.

Le macchine virtuali aggiunte a un dominio gestito di Azure AD DS possono anche essere gestite e protette tramite Criteri di gruppo. Le linee di base di sicurezza necessarie possono essere applicate alle macchine virtuali per bloccarle in conformità alle linee guida per la sicurezza aziendale. Ad esempio, è possibile utilizzare le funzionalità di gestione dei criteri di gruppo per limitare i tipi di applicazioni che possono essere avviate nella macchina virtuale.

![Gestione ottimizzata delle macchine virtuali di Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Esaminiamo uno scenario di esempio comune. Quando i server e altre infrastrutture raggiungono la fine del ciclo di vita, Contoso desidera spostare nel cloud le applicazioni attualmente ospitate in locale. Il loro standard IT attuale impone che i server che ospitano applicazioni aziendali debbano essere aggiunti a un dominio e gestiti tramite Criteri di gruppo. L'amministratore IT di Contoso preferirebbe aggiungere al dominio le macchine virtuali distribuite in Azure per semplificare l'amministrazione in quanto gli utenti possono quindi accedere usando le credenziali aziendali. Quando si fa parte di un dominio, le macchine virtuali possono anche essere configurate per soddisfare le linee di base di sicurezza necessarie usando gli oggetti Criteri di gruppo. Contoso would prefer not to deploy, monitor, and manage their own domain controllers in Azure.

Servizi di dominio Active Directory di Azure è ideale per questo caso d'uso. Un dominio gestito di Servizi di dominio Active Directory di Azure consente di aggiungere macchine virtuali, usare un singolo set di credenziali e applicare criteri di gruppo. Come dominio gestito, non è necessario configurare e gestire manualmente i controller di dominio.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano a questo caso d'uso di esempio:The following deployment considerations apply to this example use case:

* I domini gestiti di Servizi di dominio Active Directory di Azure usano una struttura di unità organizzativa singola e semplice per impostazione predefinita. Tutte le macchine virtuali aggiunte al dominio si trovano in un'unica unità organizzativa. Se lo si desidera, è possibile creare unità utente personalizzate.
* Servizi di dominio Active Directory di Azure usa un oggetto Criteri di gruppo incorporato per i contenitori di utenti e computer. Per un controllo aggiuntivo, è possibile creare oggetti Criteri di gruppo personalizzati e assegnarli come destinazione alle piattaforme utente personalizzate.
* Servizi di dominio Active Directory di Azure supporta lo schema di oggetti computer di Active Directory di base. Non è possibile estendere lo schema dell'oggetto computer.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Le applicazioni locali lift-and-shift che utilizzano l'autenticazione di binding LDAP

Come scenario di esempio, Contoso dispone di un'applicazione locale che è stata acquistata da un ISV molti anni fa. L'applicazione è attualmente in modalità di manutenzione da parte dell'ISV e la richiesta di modifiche all'applicazione è proibitiva. Questa applicazione dispone di un frontend basato sul Web che raccoglie le credenziali utente utilizzando un modulo Web e quindi autentica gli utenti eseguendo un binding LDAP all'ambiente di Servizi di dominio Active Directory locale.

![Binding LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso desidera eseguire la migrazione di questa applicazione in Azure.Contoso would to migrate this application to Azure. L'applicazione deve continuare a funzionare così com'è, senza che siano necessarie modifiche. Inoltre, gli utenti devono essere in grado di eseguire l'autenticazione utilizzando le credenziali aziendali esistenti e senza ulteriore formazione. Deve essere trasparente per gli utenti finali in cui è in esecuzione l'applicazione.

Per questo scenario, Servizi di dominio Active Directory di Azure consente alle applicazioni di eseguire binding LDAP come parte del processo di autenticazione. Le applicazioni locali legacy possono passare in Azure e continuare ad autenticare gli utenti senza problemi di configurazione o di esperienza utente.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano a questo caso d'uso di esempio:The following deployment considerations apply to this example use case:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito di Azure AD DS non è supportato.
* Non è possibile modificare le password direttamente in un dominio gestito di Servizi di dominio Active Directory di Azure.You can't change passwords directly against an Azure AD DS managed domain. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e sono disponibili nel dominio gestito di Servizi di dominio Active Directory di Azure.These changes are then automatically synchronized and available in the Azure AD DS managed domain.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Impianti locali di sollevamento e spostamento che utilizzano la lettura LDAP per accedere alla directory

Come lo scenario di esempio precedente, si supponga che Contoso disponga di un'applicazione line-of-business (LOB) locale sviluppata quasi dieci anni fa. Questa applicazione è in grado di riconoscere le directory ed è stata progettata per utilizzare LDAP per leggere informazioni/attributi sugli utenti da Servizi di dominio Active Directory. L'applicazione non modifica gli attributi né scrive in altro modo nella directory.

Contoso desidera eseguire la migrazione di questa applicazione in Azure e ritirare l'hardware locale obsoleto attualmente che ospita l'applicazione. L'applicazione non può essere riscritta per usare le API di directory moderne, ad esempio l'API Microsoft Graph basata su REST. Si desidera un'opzione lift-and-shift in cui è possibile eseguire la migrazione dell'applicazione per l'esecuzione nel cloud, senza modificare il codice o riscrivere l'applicazione.

Per risolvere questo scenario, Azure AD DS consente alle applicazioni di eseguire letture LDAP nel dominio gestito per ottenere le informazioni sugli attributi necessarie. Non è necessario riscrivere l'applicazione, pertanto un lift-and-shift in Azure consente agli utenti di continuare a usare l'app senza rendersi conto che c'è una modifica in cui viene eseguita.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano a questo caso d'uso di esempio:The following deployment considerations apply to this example use case:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito di Azure AD DS non è supportato.
* Assicurarsi che l'applicazione non necessiti di uno schema di Active Directory personalizzato/esteso. Schema extensions aren't supported in Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Eseguire la migrazione di un servizio locale o di un'applicazione daemon in AzureMigrate an on-premises service or daemon application to Azure

Alcune applicazioni includono più livelli, in cui uno dei livelli deve eseguire chiamate autenticate a un livello back-end, ad esempio un database. Gli account di servizio di Active Directory vengono comunemente usati in questi scenari. Quando si sollevano e si spostano le applicazioni in Azure, Servizi di dominio Active Directory di Azure consente di continuare a usare gli account di servizio nello stesso modo. È possibile scegliere di usare lo stesso account di servizio sincronizzato dalla directory locale ad Azure AD o creare un'unità organizzativa personalizzata e quindi creare un account di servizio separato in tale unità organizzativa. Con entrambi gli approcci, le applicazioni continuano a funzionare allo stesso modo per effettuare chiamate autenticate ad altri livelli e servizi.

![Account del servizio mediante WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In questo scenario di esempio, Contoso dispone di un'applicazione dell'insieme di credenziali software personalizzata che include un front-end Web, un server SQL e un server FTP back-end. L'autenticazione integrata di Windows tramite account di servizio autentica il front-end Web al server FTP. Il front-end Web è configurato per l'esecuzione come account del servizio. Il server back-end è configurato per autorizzare l'accesso dall'account del servizio per il front-end Web. Contoso doesn't want to deploy and manage their own domain controller VMs in the cloud to move this application to Azure.

Per questo scenario, i server che ospitano il front-end Web, IL server SQL e il server FTP possono essere migrati alle macchine virtuali di Azure e aggiunti a un dominio gestito di Servizi di dominio Active Directory di Azure.For this scenario, the servers hosting the web front end, SQL Server, and the FTP server can be migrated to Azure VMs and joined to an Azure AD DS managed domain. The VMs can then use the same service account in their on-premises directory for the app's authentication purposes, which is synchronized through Azure AD using Azure AD Connect.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano a questo caso d'uso di esempio:The following deployment considerations apply to this example use case:

* Assicurarsi che le applicazioni utilizzino un nome utente e una password per l'autenticazione. L'autenticazione basata su certificati o smart card non è supportata da Servizi di dominio Active Directory di Azure.Certificate or smartcard-based authentication isn't supported by Azure AD DS.
* Non è possibile modificare le password direttamente in un dominio gestito di Servizi di dominio Active Directory di Azure.You can't change passwords directly against an Azure AD DS managed domain. Gli utenti finali possono modificare le password tramite il meccanismo di reimpostazione della password self-service o nella directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e sono disponibili nel dominio gestito di Servizi di dominio Active Directory di Azure.These changes are then automatically synchronized and available in the Azure AD DS managed domain.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server remote desktop services deployments in Azure

È possibile usare Servizi di dominio Active Directory di Azure per fornire servizi di dominio gestito ai server desktop remoti distribuiti in Azure.You can use Azure AD DS to provide managed domain services to remote desktop servers deployed in Azure. Per altre informazioni su questo scenario di distribuzione, vedere [come integrare Servizi di][windows-rds]dominio Azure AD con la distribuzione di Servizi Desktop remoto.

## <a name="domain-joined-hdinsight-clusters"></a>Cluster HDInsight aggiunti al dominio

È possibile configurare un cluster di Azure HDInsight aggiunto a un dominio gestito di Azure AD DS con Apache Ranger abilitato. È possibile creare e applicare criteri Hive tramite Apache Ranger e consentire agli utenti, ad esempio gli esperti di dati, di connettersi a Hive utilizzando strumenti basati su ODBC come Excel o Tableau.You can create and apply Hive policies through Apache Ranger, and allow users, such as data scientist, to connect to Hive using ODBC-based tools like Excel or Tableau. Continuiamo a lavorare per aggiungere altri carichi di lavoro, ad esempio HBase, Spark e Storm a HDInsight aggiunto al dominio.

Per altre informazioni su questo scenario di distribuzione, vedere come configurare cluster HDInsight aggiunti a un dominioFor more information about this deployment [scenario, see how to configure domain-joined HDInsight clusters][hdinsight]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [creare e configurare un'istanza][tutorial-create-instance] di Servizi di dominio Azure Active DirectoryTo get started, Create and configure an Azure Active Directory Domain Services instance

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
