---
title: Servizi di dominio di Azure Active Directory per Azure Cloud Solution Provider | Documenti Microsoft
description: Servizi di dominio Azure Active Directory per Azure Cloud Solution Provider.
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 313c4853b0f585921739779bb764c50036c9ac8a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Servizi di dominio di Azure Active Directory (AD) per Azure Cloud Solution Provider (CSP)
In questo articolo viene illustrato come utilizzare i servizi di dominio Azure Active Directory in una sottoscrizione di Azure CSP.

## <a name="overview-of-azure-csp"></a>Panoramica di Azure CSP
Azure CSP è un programma per Microsoft Partners e fornisce un canale di licenza per vari servizi cloud Microsoft. CSP Azure consente ai partner gestire vendite, proprietari della relazione di fatturazione, fornire il supporto tecnico e di fatturazione e da punto di riferimento del cliente. Inoltre, CSP di Azure fornisce un set completo di strumenti, tra cui un portale self-service e le API di accompagnamento. Questi strumenti consentono ai partner CSP di effettuare il provisioning e Gestione risorse di Azure e forniscono una fatturazione per i clienti e le relative sottoscrizioni.

Il [portale Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funge da un punto di ingresso per tutti i partner di Azure CSP. Fornisce funzionalità di gestione di cliente, l'elaborazione automatica e altro ancora. Partner CSP Azure è possibile utilizzare funzionalità Partner Center tramite un'interfaccia utente basata sul web o tramite PowerShell e varie chiamate API.

Il diagramma seguente illustra il funzionamento del modello di CSP a un livello elevato. Contoso dispone di un'istanza di Azure AD Active Directory. Hanno una relazione con un provider CSP, che consente di distribuire e gestire le risorse nella propria sottoscrizione Azure CSP. Contoso può anche essere regolare (dirette) le sottoscrizioni di Azure, che vengono fatturate direttamente a Contoso.

![Panoramica del modello di CSP](./media/csp/csp_model_overview.png)

Tenant del partner CSP ha tre gruppi speciali agente - Admin agenti, gli agenti di help desk e gli agenti di vendita. Il gruppo di agenti amministratore viene assegnato al ruolo amministratore tenant nella directory di Azure Active Directory di Contoso. Di conseguenza, un utente appartenente al gruppo di agenti admin CSP partner ha privilegi di amministratore tenant nella directory di Azure Active Directory di Contoso. Quando le disposizioni partner CSP una sottoscrizione di Azure CSP per Contoso, il gruppo di agenti di amministratore viene assegnata al ruolo di proprietario per la sottoscrizione. Di conseguenza, gli agenti CSP partner amministratore dispone dei privilegi necessari per eseguire il provisioning delle risorse di Azure quali macchine virtuali, reti virtuali e servizi di dominio Azure Active Directory per conto di Contoso.

Per ulteriori informazioni, vedere il [Panoramica CSP di Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Vantaggi dell'utilizzo di servizi di dominio Active Directory di Azure in una sottoscrizione di Azure CSP
Servizi di dominio Active Directory di Azure fornisce servizi compatibili di Windows Server Active Directory in Azure, ad esempio LDAP, l'autenticazione Kerberos/NTLM, aggiunta a un dominio, criteri di gruppo e DNS. Su decenni, molte applicazioni sono state incorporate per funzionare con Active Directory utilizzando tali funzionalità. Molti fornitori di software indipendenti (ISV) aver compilato e distribuito applicazioni presso i clienti. Queste applicazioni sono onerose per supportare poiché spesso che richiede l'accesso agli ambienti diversi in cui queste applicazioni vengono distribuite. Le sottoscrizioni di Azure CSP, offrono un'alternativa più semplice con la scala e la flessibilità di Azure.

Servizi di dominio Active Directory di Azure supporta ora le sottoscrizioni di Azure CSP. È ora possibile distribuire l'applicazione in una sottoscrizione di Azure CSP associata alla directory di Azure AD del cliente. Di conseguenza, i dipendenti (personale di supporto) possono gestire, amministrare e le macchine virtuali in cui è distribuita l'applicazione utilizzando le credenziali aziendali dell'organizzazione del servizio. Inoltre, è possibile eseguire il provisioning di un dominio gestito di servizi di dominio Active Directory di Azure per la directory di Azure AD del cliente. L'applicazione è connesso al dominio gestito del cliente. Pertanto, le funzionalità all'interno dell'applicazione che si basano su Kerberos/NTLM, LDAP, o [API System. DirectoryServices](https://msdn.microsoft.com/library/system.directoryservices) funzionano perfettamente con la directory del cliente. I clienti finali traggono dall'utilizzo dell'applicazione come servizio, senza dover preoccuparsi di manutenzione dell'infrastruttura che in cui viene distribuita l'applicazione.

Tutti di fatturazione per le risorse di Azure che è utilizzare in tale sottoscrizione, inclusi i servizi di dominio Active Directory di Azure, viene addebitata all'utente. È necessario mantenere il controllo completo la relazione con il cliente per quanto riguarda il supporto tecnico delle vendite, fatturazione e così via. Con la flessibilità della piattaforma Azure CSP, un team di piccole dimensioni di agenti di supporto può servire molti tali clienti che dispongono di istanze di un'applicazione distribuita.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelli di distribuzione CSP per servizi di dominio Active Directory di Azure
Esistono due modi in cui è possibile utilizzare servizi di dominio Active Directory di Azure con una sottoscrizione di Azure CSP. Scegliere quello corretto in base alle considerazioni sulla sicurezza e la semplicità che dei clienti.

### <a name="direct-deployment-model"></a>Modello di distribuzione diretta
In questo modello di distribuzione, servizi di dominio Active Directory di Azure è abilitata in una rete virtuale che appartengono alla sottoscrizione Azure CSP. Gli agenti CSP partner amministratore dispone dei privilegi seguenti:
* Privilegi di amministratore globale nella directory di Azure AD del cliente.
* Privilegi di proprietario sottoscrizione per la sottoscrizione di Azure CSP.

![Modello di distribuzione diretta](./media/csp/csp_direct_deployment_model.png)

In questo modello di distribuzione, gli agenti di amministrazione del provider CSP possono amministrare le identità per il cliente. Questi agenti admin hanno la possibilità di eseguire il provisioning di nuovi utenti, gruppi, aggiungere applicazioni all'interno di directory di Azure AD del cliente e così via. Questo modello di distribuzione potrebbe essere adatta per organizzazioni di piccole dimensioni che non dispongono di un amministratore di identità dedicato o Preferiti per il partner CSP amministrare le identità per loro conto.


### <a name="peered-deployment-model"></a>Modello di distribuzione di peering
In questo modello di distribuzione, servizi di dominio Active Directory di Azure è abilitata in una rete virtuale che appartengono al cliente, vale a dire una sottoscrizione di Azure diretta pagata dal cliente. Il partner CSP è quindi possibile distribuire le applicazioni all'interno di una rete virtuale che appartengono alla sottoscrizione di CSP del cliente. Le reti virtuali possono essere connesso tramite una rete virtuale di Azure peering. Di conseguenza, i carichi di lavoro/applicazioni distribuite dal partner CSP nella sottoscrizione Azure CSP può connettersi al dominio gestito del cliente effettuato il provisioning nella sottoscrizione di Azure diretto del cliente.

![Modello di distribuzione di peering](./media/csp/csp_peered_deployment_model.png)

Questo modello di distribuzione crea una separazione dei privilegi e attiva gli agenti di supporto tecnico del partner CSP amministrare la sottoscrizione di Azure e distribuire e gestire le risorse in esso contenuti. Tuttavia, gli agenti di supporto tecnico del partner CSP non è necessario disporre di privilegi di amministratore globale nella directory di Azure AD del cliente. Gli amministratori di identità del cliente possono continuare a gestire le identità per l'organizzazione.

Questo modello di distribuzione potrebbe essere adatta a scenari in cui un ISV (fornitore di software indipendente) fornisce un ospitato versione della propria applicazione locale, che deve anche per la connessione al cliente di Active Directory.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Amministrazione dei servizi di dominio Active Directory di Azure gestiti domini nelle sottoscrizioni CSP
Le importanti considerazioni seguenti si applicano quando si amministra un dominio gestito in una sottoscrizione di Azure CSP:

* **Gli agenti di amministrazione CSP possono eseguire il provisioning di un dominio gestito usando le credenziali:** servizi di dominio Active Directory di Azure supporta le sottoscrizioni di Azure CSP. Pertanto, gli utenti appartenenti al gruppo di agenti di amministrazione di un partner CSP possono effettuare il provisioning un nuovo dominio di servizi di dominio Active Directory di Azure gestito.

* **CSP possono creare uno script di creazione di nuovi domini gestiti per i loro clienti con PowerShell:** vedere [come abilitare il dominio di Active Directory di Azure di servizi tramite PowerShell](active-directory-ds-enable-using-powershell.md) per informazioni dettagliate.

* **Gli agenti di amministrazione CSP non è possibile eseguire attività di gestione in corso nel dominio gestito usando le credenziali:** utenti amministratori CSP non è possibile eseguire attività di gestione di routine all'interno del dominio gestito usando le credenziali. Questi utenti sono esterni alla directory di Azure AD del cliente e le credenziali non sono disponibili nella directory di Azure AD del cliente. Di conseguenza, i servizi di dominio Active Directory di Azure non ha accesso per i hash delle password di autenticazione Kerberos e NTLM per questi utenti. Di conseguenza, tali utenti non possono essere autenticati in domini di servizi di dominio Azure Active Directory gestiti.

  > [!WARNING]
  > **È necessario creare un account utente nella directory del cliente per eseguire attività di amministrazione in corso nel dominio gestito.**
  > Impossibile accedere al dominio gestito utilizzando le credenziali dell'utente di amministrazione CSP. A tale scopo, utilizzare le credenziali di un account utente appartenente a una directory di Azure AD del cliente. Queste credenziali è necessario per attività quali l'aggiunta di macchine virtuali al dominio gestito, amministrazione DNS, l'amministrazione dei criteri di gruppo e così via.
  >

* **L'account utente creati per l'amministrazione in corso deve essere aggiunto al gruppo 'Administrators di controller di dominio di AAD':** il gruppo 'Administrators controller di dominio di AAD' dispone dei privilegi per eseguire determinate attività di amministrazione delegata per il dominio gestito. Queste attività includono la configurazione DNS, la creazione di unità organizzative, l'amministrazione dei criteri di gruppo e così via. Per un partner CSP eseguire attività in un dominio gestito, un account utente deve essere creati all'interno di directory di Azure AD del cliente. Le credenziali per questo account devono essere condivisa con gli agenti di amministrazione del partner CSP. Inoltre, questo account utente deve essere aggiunto al gruppo 'Administrators di controller di dominio di AAD' per abilitare l'attività di configurazione del dominio gestito deve essere eseguita con questo account utente.


## <a name="next-steps"></a>Passaggi successivi
* [Registrazione al programma Azure CSP](https://partnercenter.microsoft.com/partner/programs) e avviare la creazione di business tramite Azure CSP.
* Esaminare l'elenco di [servizi di Azure disponibili in Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Abilitare Azure AD Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md)
* [Introduzione ad Azure AD Domain Services](active-directory-ds-getting-started.md)
