---
title: 'Azure Active Directory Domain Services: confronto tra Azure Active Directory Domain Services e controller di dominio fai da te | Documentazione Microsoft'
description: Confronto tra Servizi di dominio Azure AD e controller di dominio fai da te
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: c384046d280e03de5a808d245dd273fdf7b44549
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Come decidere Servizi di dominio Azure AD è adatto alle esigenze del caso di utilizzo
Con Azure Active Directory Domain Services è possibile distribuire i carichi di lavoro nei servizi di infrastruttura di Azure senza preoccuparsi di mantenere l'infrastruttura di identità in Azure. Questo servizio gestito è diverso da una tipica distribuzione di Windows Server Active Directory che viene distribuita e amministrata in modo autonomo. Il servizio è facile da distribuire e offre il monitoraggio e la correzione dell'integrità automatizzati. Il servizio è in continua evoluzione per l'aggiunta del supporto di scenari di distribuzione comuni.

Per decidere se usare Azure AD Domain Services, si consiglia la lettura della seguente documentazione:

* Vedere l'elenco delle [funzionalità offerte da Servizi di dominio Azure AD](active-directory-ds-features.md).
* Esaminare comuni [scenari di distribuzione per Servizi di dominio Azure Active Directory](active-directory-ds-scenarios.md).
* Infine [confrontare Servizi di dominio AZURE AD con un'opzione AD fai da te](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Confrontare Servizi di dominio Azure AD con un dominio AD fai da te in Azure
La tabella seguente riporta informazioni utili per scegliere se usare Servizi di dominio Azure AD o gestire una propria infrastruttura di AD in Azure.

| **Funzionalità** | **Servizi di dominio Azure Active Directory** | **AD "fai da te" in VM di Azure** |
| --- |:---:|:---:|
| [**Servizi gestiti**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&amp;#x2715;** |
| [**Distribuzioni sicure**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |L'amministratore deve proteggere la distribuzione. |
| [**Server DNS**](active-directory-ds-comparison.md#dns-server) |**&#x2713;** (servizio gestito) |**&#x2713;** |
| [**Domain or Enterprise administrator privileges**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&amp;#x2715;** |**&#x2713;** |
| [**Aggiunta a un dominio**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Autenticazione di dominio tramite NTLM e Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Delega vincolata Kerberos**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|basata sulle risorse|basata sulle risorse e basata su account|
| [**Struttura personalizzata per le unità organizzative**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Estensioni dello schema**](active-directory-ds-comparison.md#schema-extensions) |**&amp;#x2715;** |**&#x2713;** |
| [**Relazioni di trust di dominio/foresta di AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP read**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP sicuro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP write**](active-directory-ds-comparison.md#ldap-write) |**&amp;#x2715;** |**&#x2713;** |
| [**Group Policy**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Distribuzione in diverse aree geografiche**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&amp;#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Servizi gestiti
Servizi di dominio Azure AD è gestito da Microsoft. Non è necessario preoccuparsi di patch, aggiornamenti, monitoraggio, backup e di garantire la disponibilità del dominio. Queste attività di gestione sono disponibili come servizio di Microsoft Azure per i domini gestiti.

#### <a name="secure-deployments"></a>Distribuzioni sicure
Il dominio gestito è bloccato in modo sicuro in base alle raccomandazioni sulla sicurezza di Microsoft per le distribuzioni di AD. Queste raccomandazioni derivano dell'esperienza nella progettazione e nel supporto delle distribuzioni di Active Directory maturata in vari decenni dal team del prodotto AD. Per le distribuzioni fai da te è necessario eseguire passaggi di distribuzione specifici per bloccare/proteggere la distribuzione.

#### <a name="dns-server"></a>Server DNS
Un dominio gestito da Servizi di dominio Azure AD include servizi DNS gestiti. I membri del gruppo "AAD DC Administrators" possono gestire il DNS sul dominio gestito. Ai membri di questo gruppo sono concessi privilegi di amministrazione DNS completi per il dominio gestito. La gestione del DNS può essere eseguita mediante la console di amministrazione DNS inclusa nel pacchetto degli Strumenti di amministrazione remota del server.
[Altre informazioni](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilegi amministrativi per il dominio o per l'organizzazione
Questi privilegi elevati non sono disponibili in un dominio gestito di Servizi di dominio Azure AD. Le applicazioni che richiedono questi privilegi elevati non possono essere distribuite nei domini gestiti di AAD-DS. Un sottoinsieme più piccolo di privilegi amministrativi è disponibile per i membri del gruppo di amministrazione delegato chiamato "AAD DC Administrators". Tali privilegi comprendono privilegi per configurare il server DNS, configurare i criteri di gruppo, ottenere privilegi amministrativi sui computer appartenenti a un dominio e così via.

#### <a name="domain-join"></a>Aggiunta a un dominio
È possibile aggiungere macchine virtuali al dominio gestito analogamente a come si aggiungono computer a un dominio AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticazione di dominio tramite NTLM e Kerberos
Con Servizi di dominio Azure AD è possibile usare le credenziali aziendali per l'autenticazione con il dominio gestito. Le credenziali vengono mantenute sincronizzate con il tenant di Azure AD. Per i tenant sincronizzati, Azure AD Connect garantisce che le modifiche apportate alle credenziali in locale siano sincronizzate con Azure AD. Con una configurazione di dominio fai da te, potrebbe essere necessario impostare un trust di dominio AD con l'AD locale per l'autenticazione degli utenti con le proprie credenziali aziendali. In alternativa potrebbe essere necessario impostare la replica di AD per assicurarsi che le password utente siano sincronizzate con le macchine virtuali controller di dominio di Azure.

#### <a name="kerberos-constrained-delegation"></a>Delega vincolata Kerberos
Non si dispone dei privilegi di "Domain Admin" in un dominio gestito Active Directory Domain Services. Pertanto non è possibile configurare la delega vincolata Kerberos basata su account (tradizionale). Si può comunque configurare la più sicura delega vincolata basata sulle risorse.
[Altre informazioni](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Struttura personalizzata per le unità organizzative
I membri del gruppo "AAD DC Administrators" possono creare unità organizzative nel dominio gestito. Agli utenti che creano unità organizzative personalizzate vengono concessi privilegi amministrativi completi per l'unità organizzativa.
[Altre informazioni](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Estensioni dello schema
Non è possibile estendere lo schema di base di un dominio gestito di Servizi di dominio Azure AD. Di conseguenza le applicazioni che fanno affidamento sulle estensioni allo schema di AD (ad esempio nuovi attributi nell'oggetto utente) non possono essere elevate e spostate su domini di Servizi di dominio Azure AD.

#### <a name="ad-domain-or-forest-trusts"></a>Relazioni di trust di dominio o foresta di AD
Non è possibile configurare i domini gestiti per impostare relazioni di trust (in ingresso/in uscita) con altri domini. Pertanto, gli scenari di distribuzione della foresta di risorse non possono usare Azure AD Domain Services. Analogamente, le distribuzioni in cui non si desidera sincronizzare le password in Azure AD non possono usare Azure AD Domain Services.

#### <a name="ldap-read"></a>Lettura LDAP
Il dominio gestito supporta i carichi di lavoro di lettura LDAP. Pertanto è possibile distribuire applicazioni che eseguono operazioni di lettura LDAP nel dominio gestito.

#### <a name="secure-ldap"></a>LDAP sicuro
È possibile configurare Servizi di dominio Azure AD in modo da fornire l'accesso LDAP sicuro al dominio gestito anche su Internet.
[Altre informazioni](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Scrittura LDAP
Il dominio gestito è di sola lettura per gli oggetti utente. Di conseguenza, le applicazioni che eseguono operazioni di scrittura LDAP su attributi dell'oggetto utente non funzionano in un dominio gestito. Inoltre, le password utente non possono essere modificate dall'interno del dominio gestito. Un altro esempio sarebbe la modifica dell'appartenenza ai gruppi o degli attributi di gruppo all'interno del dominio gestito, che non è consentita. Tuttavia, le modifiche agli attributi o alle password utente apportate in Azure AD (tramite PowerShell o il portale di Azure) o in AD locale vengono sincronizzate con il dominio gestito di Servizi di dominio Azure AD.

#### <a name="group-policy"></a>Criteri di gruppo
Esiste un oggetto Criteri di gruppo integrato per il contenitore "AADDC Computers" e uno per il contenitore "AADDC Users". È possibile personalizzare questi oggetti Criteri di gruppo predefiniti per configurare Criteri di gruppo. I membri del gruppo "AAD DC Administrators" possono anche creare oggetti criterio di gruppo personalizzati e collegarli a unità organizzative esistenti (incluse le unità organizzative personalizzate).
[Altre informazioni](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Distribuzioni geograficamente sparse
I domini gestiti di Servizi di dominio Azure AD sono disponibili in una singola rete virtuale in Azure. Per scenari che richiedono controller di dominio disponibili in più aree di Azure in tutto il mondo, l'impostazione di controller di dominio in VM IaaS di Azure potrebbe essere l'alternativa migliore.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opzioni di distribuzione AD "fai da te"
Possono esistere situazioni di distribuzione in cui sono necessarie alcune delle funzionalità offerte da un'installazione Active Directory di Windows Server. In questi casi è possibile prendere in considerazione una delle seguenti opzioni fai da te:

* **Dominio cloud autonomo:** è possibile configurare un "dominio cloud" autonomo usando macchine virtuali Azure che sono state configurate come controller di dominio. Questa infrastruttura non si integra con l'ambiente Active Directory locale. Questa opzione richiede un insieme diverso di "credenziali del cloud" per l'accesso e l'amministrazione delle VM nel cloud.
* **Distribuzione di foreste di risorse:** è possibile configurare un dominio nella topologia delle foreste di risorse usando macchine virtuali Azure configurate come controller di dominio. Quindi si può impostare una relazione di trust di Active Directory con l'ambiente Active Directory locale. È possibile aggiungere computer (VM Azure) al dominio in questa foresta di risorse nel cloud. L'autenticazione utente avviene tramite una connessione VPN o ExpressRoute alla directory locale.
* **Estendere il dominio locale in Azure:** è possibile connettere una rete virtuale di Azure alla rete locale tramite una connessione VPN o ExpressRoute. Questa impostazione consente di aggiungere le macchine virtuali di Azure all'AD locale. Un'alternativa è alzare di livello dei controller di dominio di replica del dominio locale in Azure portandoli al livello di VM. È possibile configurare il sistema in modo che la replica sia eseguita tramite una connessione VPN o ExpressRoute alla directory locale. Questa modalità di distribuzione estende efficacemente il dominio locale in Azure.

> [!NOTE]
> Un'opzione fai da te potrebbe essere più adatta per determinate situazioni di distribuzione. [Condividendo i loro commenti](active-directory-ds-contact-us.md) gli utenti ci aiuteranno a comprendere quali funzionalità potrebbero far ricadere la loro scelta in futuro su Servizi di dominio Azure AD. Questi commenti e suggerimenti ci consentiranno di migliorare il servizio in modo da adattarlo ad altre esigenze di distribuzione e casi di utilizzo.
>
>

Microsoft ha pubblicato alcune [linee guida per la distribuzione di Active Directory di Windows Server nelle macchine virtuali Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) come aiuto per l'installazione.

## <a name="related-content"></a>Contenuti correlati
* [Funzionalità - Servizi di dominio Azure AD](active-directory-ds-features.md)
* [Scenari di distribuzione - Servizi di dominio Azure AD](active-directory-ds-scenarios.md)
* [Linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
