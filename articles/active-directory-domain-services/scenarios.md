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
ms.date: 08/14/2020
ms.author: iainfou
ms.openlocfilehash: 4cd6a37ad2d5081cdc587290c361fbc992c69bfb
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245163"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casi d'uso comuni e scenari per Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, Lightweight Directory Access Protocol (LDAP) e l'autenticazione Kerberos/NTLM. Azure Active Directory Domain Services si integra con il tenant di Azure AD esistente, consentendo così agli utenti di eseguire l'accesso usando le proprie credenziali già configurate. Questi servizi di dominio vengono usati senza la necessità di distribuire, gestire e applicare patch ai controller di dominio nel cloud, che fornisce un lift-and-Shift più agevole delle risorse locali in Azure.

In questo articolo vengono illustrati alcuni scenari aziendali comuni in cui Azure AD DS fornisce valore e soddisfa tali esigenze.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Modi comuni per fornire soluzioni di gestione delle identità nel cloud

Quando si esegue la migrazione dei carichi di lavoro esistenti nel cloud, le applicazioni compatibili con directory possono usare LDAP per l'accesso in lettura o scrittura a una directory di Active Directory Domain Services locale. Le applicazioni in esecuzione in Windows Server vengono in genere distribuite in macchine virtuali (VM) aggiunte a un dominio e possono quindi essere gestite in modo sicuro tramite Criteri di gruppo. Per autenticare gli utenti finali, le applicazioni possono anche basarsi sull'autenticazione integrata di Windows, ad esempio l'autenticazione Kerberos o NTLM.

Gli amministratori IT usano spesso una delle soluzioni seguenti per fornire un servizio di gestione delle identità alle applicazioni che vengono eseguite in Azure:

* Configurare una connessione VPN da sito a sito tra i carichi di lavoro in esecuzione in Azure e un ambiente di AD DS locale.
    * I controller di dominio locali forniscono quindi l'autenticazione tramite la connessione VPN.
* Creare controller di dominio di replica usando macchine virtuali di Azure per estendere il dominio o la foresta di AD DS dall'ambiente locale.
    * I controller di dominio eseguiti nelle macchine virtuali di Azure forniscono l'autenticazione e replicano le informazioni della directory tra l'ambiente di AD DS locale.
* Distribuire un ambiente di Active Directory Domain Services autonomo in Azure usando controller di dominio in esecuzione in macchine virtuali di Azure.
    * I controller di dominio eseguiti nelle macchine virtuali di Azure forniscono l'autenticazione, ma non vengono replicate informazioni della directory da un ambiente di AD DS locale.

Con queste soluzioni, le connessioni VPN alla directory locale rendono le applicazioni vulnerabili a errori o interruzioni di rete temporanee. Se si distribuiscono controller di dominio usando macchine virtuali in Azure, il team IT deve gestire le VM, quindi eseguire le attività di protezione, applicazione di patch, monitoraggio, backup e risoluzione dei problemi.

Azure Active Directory Domain Services offre alternative alla necessità di creare connessioni VPN a un ambiente di Active Directory Domain Services locale o di eseguire e gestire macchine virtuali in Azure per fornire servizi di gestione delle identità. Come servizio gestito, Azure Active Directory Domain Services riduce la complessità che comporta la creazione di una soluzione di gestione delle identità integrata per ambienti ibridi e solo cloud.

> [!div class="nextstepaction"]
> [Confrontare Active Directory Domain Services con Azure AD e Active Directory Domain Services autogestito in macchine virtuali di Azure o in locale][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure Active Directory Domain Services per le organizzazioni ibride

Molte organizzazioni eseguono un'infrastruttura ibrida che include i carichi di lavoro di applicazioni cloud e locali. Le applicazioni legacy di cui è stata eseguita la migrazione in Azure nell'ambito di una strategia di trasferimento in modalità lift-and-shift possono usare le connessioni LDAP tradizionali per fornire informazioni sulle identità. Per supportare questa infrastruttura ibrida, è possibile sincronizzare le informazioni sulle identità da un ambiente di Active Directory Domain Services locale a un tenant di Azure AD. Azure Active Directory Domain Services fornisce quindi a queste applicazioni legacy in Azure un'origine di identità, senza che sia necessario configurare e gestire la connettività delle applicazioni in servizi di directory locali.

Di seguito è riportato un esempio di Litware Corporation, un'organizzazione ibrida che esegue risorse in locale e in Azure:

![Azure Active Directory Domain Services per un'organizzazione ibrida che include la sincronizzazione locale](./media/overview/synced-tenant.png)

* I carichi di lavoro di server e applicazioni che richiedono servizi di dominio sono distribuiti in una rete virtuale in Azure.
    * Tra queste applicazioni possono essere incluse le applicazioni legacy di cui è stata eseguita la migrazione in Azure come parte di una strategia di trasferimento in modalità lift-and-shift.
* Per sincronizzare le informazioni relative alle identità della directory locale con il tenant di Azure AD, Litware Corporation distribuisce [Azure AD Connect][azure-ad-connect].
    * Le informazioni sulle identità che vengono sincronizzate includono gli account utente e le appartenenze ai gruppi.
* Il team IT di Litware abilita Azure Active Directory Domain Services per il tenant di Azure AD in questa rete virtuale o in una con peering.
* Le applicazioni e le macchine virtuali distribuite all'interno della rete virtuale di Azure possono quindi usare le funzionalità di Azure Active Directory Domain Services come l'aggiunta a un dominio, la lettura e il binding LDAP, l'autenticazione NTLM e Kerberos e Criteri di gruppo.

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti AD DS locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito per sincronizzare gli oggetti con Azure AD.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure Active Directory Domain Services per le organizzazioni solo cloud

Un tenant di Azure AD solo cloud non ha un'origine di identità locale. Gli account utente e le appartenenze ai gruppi, ad esempio, vengono creati e gestiti direttamente in Azure AD.

Si esaminerà ora un esempio per Contoso, un'organizzazione solo cloud che usa Azure AD per la gestione delle identità. Tutte le identità degli utenti, le relative credenziali e le appartenenze ai gruppi vengono create e gestite in Azure AD. Non esiste alcuna configurazione aggiuntiva di Azure AD Connect per sincronizzare le informazioni sulle identità da una directory locale.

![Azure Active Directory Domain Services per un'organizzazione solo cloud senza sincronizzazione in locale](./media/overview/cloud-only-tenant.png)

* I carichi di lavoro di server e applicazioni che richiedono servizi di dominio sono distribuiti in una rete virtuale in Azure.
* Il team IT di Contoso abilita Azure Active Directory Domain Services per il tenant di Azure AD in questa rete virtuale o in una con peering.
* Le applicazioni e le macchine virtuali distribuite all'interno della rete virtuale di Azure possono quindi usare le funzionalità di Azure Active Directory Domain Services come l'aggiunta a un dominio, la lettura e il binding LDAP, l'autenticazione NTLM e Kerberos e Criteri di gruppo.

## <a name="secure-administration-of-azure-virtual-machines"></a>Amministrazione sicura di macchine virtuali di Azure

Per consentire l'uso di un singolo set di credenziali di Active Directory, le macchine virtuali (VM) di Azure possono essere unite a un dominio gestito di Azure AD DS. Questo approccio consente di ridurre i problemi di gestione delle credenziali, ad esempio la gestione degli account amministratore locale in ogni VM o la separazione di account e password tra ambienti.

Le macchine virtuali che fanno parte di un dominio gestito possono anche essere amministrate e protette tramite criteri di gruppo. Le linee di base di sicurezza richieste possono essere applicate alle macchine virtuali per bloccarle in base alle linee guida di sicurezza aziendali. Ad esempio, è possibile usare le funzionalità di gestione di criteri di gruppo per limitare i tipi di applicazioni che possono essere avviate nella macchina virtuale.

![Gestione ottimizzata delle macchine virtuali di Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Esaminiamo uno scenario di esempio comune. Poiché i server e altre infrastrutture raggiungono la fine del ciclo di vita, Contoso desidera spostare le applicazioni attualmente ospitate in locale nel cloud. Il rispettivo standard IT impone che i server che ospitano applicazioni aziendali debbano essere aggiunti a un dominio e gestiti tramite criteri di gruppo.

L'amministratore IT di Contoso preferisce aggiungere a un dominio le VM distribuite in Azure per semplificare l'amministrazione perché gli utenti possono quindi accedere con le proprie credenziali aziendali. Quando si aggiunge un dominio, le macchine virtuali possono anche essere configurate per essere conformi alle linee di base di sicurezza obbligatorie usando oggetti Criteri di gruppo (GPO). Contoso preferisce non distribuire, monitorare e gestire i propri controller di dominio in Azure.

Azure AD DS rappresenta un'ottima soluzione per questo caso d'uso. Un dominio gestito consente di aggiungere le VM al dominio, usare un singolo set di credenziali e applicare criteri di gruppo. Poiché si tratta di un dominio gestito, non è necessario configurare e gestire manualmente i controller di dominio.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Per impostazione predefinita, i domini gestiti usano una singola struttura di unità organizzativa (OU) flat. Tutte le macchine virtuali appartenenti a un dominio si trovano in una singola unità organizzativa. Se lo si desidera, è possibile creare [unità organizzative personalizzate][custom-ou].
* Azure AD DS utilizza un oggetto Criteri di gruppo incorporato per i contenitori utenti e computer. Per un controllo aggiuntivo, è possibile [creare oggetti Criteri][create-gpo] di gruppo personalizzati e indirizzarli a unità organizzative personalizzate.
* Azure AD DS supporta lo schema dell'oggetto computer AD di base. Non è possibile estendere lo schema dell'oggetto computer.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Applicazioni locali Lift-and-Shift che usano l'autenticazione di binding LDAP

Come scenario di esempio, Contoso ha un'applicazione locale che è stata acquistata da un ISV diversi anni fa. L'applicazione è attualmente in modalità di manutenzione da parte dell'ISV e la richiesta di modifiche all'applicazione è eccessivamente costosa. Questa applicazione dispone di un front-end basato sul Web che raccoglie le credenziali utente usando un modulo Web e quindi autentica gli utenti eseguendo un'associazione LDAP all'ambiente di servizi di dominio Active Directory locale.

![Binding LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso desidera eseguire la migrazione di questa applicazione in Azure. L'applicazione dovrebbe continuare a funzionare così com'è, senza che siano necessarie modifiche. Inoltre, gli utenti devono essere in grado di eseguire l'autenticazione usando le credenziali aziendali esistenti e senza formazione aggiuntiva. Dovrebbe essere trasparente per gli utenti finali in cui l'applicazione è in esecuzione.

Per questo scenario, Azure AD DS consente alle applicazioni di eseguire associazioni LDAP come parte del processo di autenticazione. Le applicazioni locali legacy possono eseguire il lift-and-Shift in Azure e continuare a autenticare senza problemi gli utenti senza alcuna modifica nella configurazione o nell'esperienza utente.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito non è supportato.
* Non è possibile modificare le password direttamente in un dominio gestito. Gli utenti finali possono modificare la password usando il [meccanismo di modifica della password self-service Azure ad][sspr] o sulla directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e disponibili nel dominio gestito.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Applicazioni locali Lift-and-Shift che usano LDAP Read per accedere alla directory

Come lo scenario di esempio precedente, si supponga che Contoso disponga di un'applicazione line-of-business (LOB) locale sviluppata quasi un decennio fa. Questa applicazione è compatibile con le directory ed è stata progettata per utilizzare LDAP per leggere informazioni/attributi sugli utenti di servizi di dominio Active Directory. L'applicazione non modifica gli attributi o altrimenti scrive nella directory.

Contoso vuole eseguire la migrazione di questa applicazione in Azure e ritirare l'hardware locale obsoleto che ospita attualmente questa applicazione. Non è possibile riscrivere l'applicazione per usare API di directory moderne, ad esempio l'API Microsoft Graph basata su REST. Si desidera un'opzione Lift-and-Shift in cui è possibile eseguire la migrazione dell'applicazione per l'esecuzione nel cloud, senza modificare il codice o riscrivere l'applicazione.

Per semplificare questo scenario, Azure AD DS consente alle applicazioni di eseguire letture LDAP sul dominio gestito per ottenere le informazioni sugli attributi necessarie. Non è necessario riscrivere l'applicazione, quindi un lift-and-Shift in Azure consente agli utenti di continuare a usare l'app senza rendersi conto che è presente una modifica in cui viene eseguito.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che l'applicazione non debba modificare/scrivere nella directory. L'accesso in scrittura LDAP a un dominio gestito non è supportato.
* Assicurarsi che l'applicazione non richieda uno schema di Active Directory personalizzato/esteso. Le estensioni dello schema non sono supportate in Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Eseguire la migrazione di un servizio locale o di un'applicazione daemon in Azure

Alcune applicazioni includono più livelli, in cui uno dei livelli deve eseguire chiamate autenticate a un livello back-end, ad esempio un database. Gli account del servizio Active Directory sono comunemente utilizzati in questi scenari. Quando si esegue il lift-and-Shift delle applicazioni in Azure, Azure AD DS consente di continuare a usare gli account del servizio nello stesso modo. È possibile scegliere di utilizzare lo stesso account del servizio sincronizzato dalla directory locale per Azure AD o creare un'unità organizzativa personalizzata, quindi creare un account di servizio separato in tale unità organizzativa. Con entrambi gli approcci, le applicazioni continuano a funzionare allo stesso modo per eseguire chiamate autenticate ad altri livelli e servizi.

![Account del servizio mediante WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In questo scenario di esempio, Contoso dispone di un'applicazione di insieme di credenziali software personalizzata che include un front-end Web, un server SQL e un server FTP back-end. Autenticazione integrata di Windows con gli account del servizio autentica il front-end Web con il server FTP. Il front-end Web è configurato per l'esecuzione come account del servizio. Il server back-end è configurato per autorizzare l'accesso dall'account del servizio per il front-end Web. Contoso non desidera distribuire e gestire le proprie macchine virtuali del controller di dominio nel cloud per spostare l'applicazione in Azure.

Per questo scenario, i server che ospitano il front-end Web, SQL Server e il server FTP possono essere migrati alle macchine virtuali di Azure e aggiunti a un dominio gestito. Le macchine virtuali possono quindi usare lo stesso account del servizio nella directory locale per l'autenticazione dell'app, che viene sincronizzato tramite Azure AD usando Azure AD Connect.

### <a name="deployment-notes"></a>Note sulla distribuzione

Le considerazioni sulla distribuzione seguenti si applicano al caso d'uso di esempio:

* Assicurarsi che le applicazioni usino un nome utente e una password per l'autenticazione. L'autenticazione basata su smart card o certificato non è supportata da Azure AD DS.
* Non è possibile modificare le password direttamente in un dominio gestito. Gli utenti finali possono modificare la password usando il [meccanismo di modifica della password self-service Azure ad][sspr] o sulla directory locale. Queste modifiche vengono quindi sincronizzate automaticamente e disponibili nel dominio gestito.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Distribuzioni di Servizi Desktop remoto di Windows Server in Azure

È possibile usare Azure AD DS per fornire servizi di dominio gestiti ai server desktop remoto distribuiti in Azure.

Per ulteriori informazioni su questo scenario di distribuzione, vedere [come integrare Azure ad Domain Services con la distribuzione di][windows-rds]Servizi Desktop remoto.

## <a name="domain-joined-hdinsight-clusters"></a>Cluster HDInsight aggiunti al dominio

È possibile configurare un cluster Azure HDInsight aggiunto a un dominio gestito con Apache Ranger abilitato. È possibile creare e applicare criteri hive tramite Apache Ranger e consentire agli utenti, ad esempio i data scientist, di connettersi a hive usando strumenti basati su ODBC come Excel o Tableau. Continuiamo a lavorare per aggiungere altri carichi di lavoro, ad esempio HBase, Spark e Storm, a HDInsight aggiunto al dominio.

Per altre informazioni su questo scenario di distribuzione, vedere [come configurare cluster HDInsight aggiunti al dominio][hdinsight]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [creare e configurare un Azure Active Directory Domain Services dominio gestito][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
