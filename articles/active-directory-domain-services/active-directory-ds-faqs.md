<properties
    pageTitle="Domande frequenti su Servizi di dominio Azure Active Directory | Microsoft Azure"
    description="Domande frequenti su Servizi di dominio Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>


# <a name="azure-active-directory-domain-services:-frequently-asked-questions-(faqs)"></a>Servizi di dominio Azure Active Directory: domande frequenti

Questa pagina include le risposte a domande frequenti relative a Servizi di dominio Azure AD. Controllarla costantemente per eventuali aggiornamenti.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
Fare riferimento alla [Guida per la risoluzione dei problemi](active-directory-ds-troubleshooting.md) per soluzioni relative ai problemi comuni riscontrati durante la configurazione o l'amministrazione dei servizi di dominio di Azure AD.


### <a name="configuration"></a>Configurazione

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory?"></a>È possibile creare più domini per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Servizi di dominio Azure AD per una singola directory di Azure AD.  

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription?"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Servizi di dominio Azure AD è disponibile in una sola rete virtuale alla volta. È comunque possibile configurare la connettività tra più reti virtuali per esporre Servizi di dominio Azure AD ad altre reti virtuali. Questo articolo descrive come [connettere più reti virtuali in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell?"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
PowerShell e la distribuzione automatizzata di Servizi di dominio Azure AD non sono attualmente disponibili.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal?"></a>Servizi di dominio Azure AD è disponibile nel nuovo portale di Azure?
No. È possibile configurare Servizi di dominio Azure AD solo nel [portale di Azure classico](https://manage.windowsazure.com). Microsoft prevede di estendere il supporto per il [portale di Azure](https://portal.azure.com) in futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain?"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
No. Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario eseguire il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione vengono fornite come servizio da Microsoft. Non sarà quindi possibile aggiungere altri controller di dominio, di lettura-scrittura o sola lettura, per il dominio gestito.

### <a name="administration-and-operations"></a>Amministrazione e gestione

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop?"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni necessarie per connettersi ai controller di dominio nel dominio gestito con Desktop remoto. I membri del gruppo "AAD DC Administrators" possono occuparsi del dominio gestito usando gli strumenti di amministrazione di Active Directory, ad esempio Centro amministrativo di Active Directory e Active Directory PowerShell. Tramite la funzionalità Strumenti di amministrazione remota del server, questi strumenti vengono installati su un server Windows aggiunto al dominio gestito.

#### <a name="i’ve-enabled-azure-ad-domain-services.-what-user-account-do-i-use-to-domain-join-machines-to-this-domain?"></a>Dopo aver abilitato Servizi di dominio Azure AD, quale account utente è necessario usare per aggiungere i computer a questo dominio?
Gli utenti aggiunti al gruppo amministrativo, ad esempio il gruppo "AAD DC Administrators", sono autorizzati ad aggiungere computer a questo dominio. Gli utenti di questo gruppo, inoltre, sono autorizzati per l'accesso con Desktop remoto ai computer aggiunti al dominio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services?"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio fornito da Servizi di dominio Azure AD?
No. Non si ottengono privilegi amministrativi nel dominio gestito. Non è possibile avere né privilegi di amministratore di dominio né di amministratore dell'organizzazione per il dominio. Anche i gruppi di amministratori di dominio o dell'organizzazione esistenti nella directory di Azure AD non avranno privilegi di amministratore di dominio/dell'organizzazione per questo dominio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services?"></a>È possibile modificare le appartenenze ai gruppi tramite LDAP o altri strumenti di amministrazione di Active Directory nei domini forniti da Servizi di dominio Azure AD?
No. Le appartenenze ai gruppi non possono essere modificate nei domini gestiti da Servizi di dominio Azure AD. Lo stesso vale per gli attributi utente. È tuttavia possibile modificare le appartenenze ai gruppi o gli attributi utente in Azure AD o nel dominio locale. Tali modifiche verranno sincronizzate automaticamente in Servizi di dominio Azure AD.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services?"></a>È possibile estendere lo schema del dominio fornito da Servizi di dominio Azure Active Directory?
No. Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Servizi di dominio Azure Active Directory.

#### <a name="can-i-modify-dns-records-provided-by-azure-ad-domain-services?"></a>È possibile modificare i record DNS forniti da Servizi di dominio di Azure AD?
Sì. Agli utenti del gruppo "AAD DC Administrators" vengono concessi privilegi di amministratore DNS per modificare i record DNS nel dominio gestito. Per la gestione DNS questi utenti possono usare la console Gestore DNS su un computer con Windows Server aggiunto al dominio gestito. Per usare questa console, installare sul server Strumenti server DNS, che fa parte della funzionalità facoltativa Strumenti di amministrazione remota del server. Altre informazioni sulle [utilità di amministrazione, monitoraggio e risoluzione dei problemi DNS](https://technet.microsoft.com/library/cc753579.aspx) sono disponibili su TechNet.

### <a name="billing-and-availability"></a>Fatturazione e disponibilità

#### <a name="is-azure-ad-domain-services-a-paid-service?"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service?"></a>È disponibile una versione di valutazione gratuita per il servizio?
Questo servizio è incluso nella versione di valutazione gratuita di Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-(ems)?"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)?
No. Servizi di dominio Azure AD è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS. Servizi di dominio Azure AD è disponibile per tutti gli SKU di Azure AD, ovvero Free, Basic e Premium e il servizio viene fatturato su base oraria in base all'uso.

#### <a name="what-azure-regions-is-the-service-available-in?"></a>In quale aree di Azure è disponibile il servizio?
Per un elenco di aree di Azure per cui è disponibile Servizi di dominio Azure AD, fare riferimento alla [pagina delle aree](active-directory-ds-regions.md) .



<!--HONumber=Oct16_HO2-->


