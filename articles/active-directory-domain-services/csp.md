---
title: Servizi di dominio Azure AD per i provider di soluzioni cloud Documenti Microsoft
description: Informazioni su come abilitare e gestire i domini gestiti di Servizi di dominio Azure Active Directory per i provider di soluzioni cloud di Azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519095"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Domain Services deployment and management for Azure Cloud Solution Providers

Azure Cloud Solution Provider (CSP) è un programma per i partner Microsoft e fornisce un canale di licenza per vari servizi cloud Microsoft.Azure Cloud Solution Providers (CSP) is a program for Microsoft Partners and provides a license channel for various Microsoft cloud services. Azure CSP permette ai partner di gestire le vendite, avere il controllo sulla relazione di fatturazione, fornire supporto tecnico e per la fatturazione e rappresentare l'unico punto di contatto dei clienti. Inoltre, Azure CSP include un set completo di strumenti, tra cui un portale self-service e le API associate. Questi strumenti consentono ai partner CSP di effettuare agevolmente il provisioning, gestire le risorse di Azure e fornire la fatturazione dei clienti e delle relative sottoscrizioni.

The [Partner Center portal](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) is the entry point for all Azure CSP partners, and provides rich customer management capabilities, automated processing, and more. I partner di Azure CSP possono utilizzare le funzionalità del Centro per i partner tramite un'interfaccia utente basata sul Web o mediante PowerShell e varie chiamate API.

Il diagramma seguente illustra il funzionamento del modello CSP a livello generale. In questo caso Contoso dispone di un tenant di Azure Active Directory (Azure AD). Ha una partnership con un CSP, che distribuisce e gestisce le risorse nella propria sottoscrizione Azure CSP. Contoso può anche avere sottoscrizioni di Azure regolari (dirette), fatturate direttamente alla società stessa.

![Panoramica del modello CSP](./media/csp/csp_model_overview.png)

Il tenant del partner CSP dispone di tre gruppi di agenti speciali: agenti *di amministrazione,* agenti *helpdesk* e agenti *di vendita.*

Il gruppo *Agenti di amministrazione* viene assegnato al ruolo di amministratore tenant nel tenant di Azure AD di Contoso.The Admin agents group is assigned to the tenant administrator role in Contoso's Azure AD tenant. Di conseguenza, un utente appartenente al gruppo di agenti di amministrazione del partner CSP dispone di privilegi di amministratore tenant nel tenant di Azure AD di Contoso.As a result, a user belonging to the CSP partner's admin agents group has tenant admin privileges in Contoso's Azure AD tenant.

Quando il partner CSP effettua il provisioning di una sottoscrizione di Azure CSP per Contoso, il gruppo di agenti di amministrazione viene assegnato al ruolo di proprietario della sottoscrizione. Gli agenti di amministrazione del partner CSP dispongono pertanto dei privilegi necessari per eseguire il provisioning delle risorse di Azure, quali macchine virtuali, reti virtuali e Azure AD Domain Services per conto di Contoso.

Per altre informazioni, vedere la [Panoramica di Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Vantaggi dell'uso di Azure AD DS in una sottoscrizione di Azure CSP

Azure Active Directory Domain Services (Azure AD DS) provides managed domain services such as domain join, group policy, LDAP, Kerberos/NTLM authentication that is fully compatible with Windows Server Active Directory Domain Services. Nel corso dei decenni, sono state create molte applicazioni per funzionare in Active Directory utilizzando tali funzionalità. Molti fornitori di software indipendenti hanno compilato e distribuito applicazioni presso la sede dei clienti. Queste applicazioni sono difficili da supportare poiché spesso è necessario accedere ai diversi ambienti in cui vengono distribuite le applicazioni. Le sottoscrizioni di Azure CSP costituiscono un'alternativa più semplice con la scalabilità e la flessibilità di Azure.

Servizi di dominio Active Directory di Azure supporta le sottoscrizioni di Azure CSP. È possibile distribuire l'applicazione in una sottoscrizione di Azure CSP legata al tenant di Azure AD del cliente. Di conseguenza, i dipendenti (personale di supporto) possono gestire, amministrare e gestire le macchine virtuali in cui l'applicazione viene distribuita usando le credenziali aziendali dell'organizzazione.

È anche possibile distribuire un dominio gestito di Servizi di dominio Active Directory di Azure nel tenant di Azure AD del cliente. L'applicazione viene quindi connessa al dominio gestito del cliente. Le funzionalità all'interno dell'applicazione che si basano su Kerberos / NTLM, LDAP o [l'API System.DirectoryServices](/dotnet/api/system.directoryservices) funzionano senza problemi con il dominio del cliente. I clienti finali traggono vantaggio dall'utilizzo dell'applicazione come servizio, senza doversi preoccupare di mantenere l'infrastruttura in cui viene distribuita l'applicazione.

Tutta la fatturazione per le risorse di Azure usare nella sottoscrizione, incluso Servizi di dominio Azure AD, viene addebitata all'utente. Mantenete il pieno controllo sul rapporto con il cliente quando si tratta di vendite, fatturazione, supporto tecnico, ecc. Con la flessibilità della piattaforma Azure CSP, un piccolo team di agenti di supporto può servire molti di questi clienti che dispongono di istanze dell'applicazione distribuita.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelli di distribuzione CSP per Azure AD DS

Esistono due modi in cui è possibile usare Azure AD DS con una sottoscrizione di Azure CSP. Scegliere quella adeguata in base alle considerazioni sulla sicurezza e sulla semplicità dei clienti.

### <a name="direct-deployment-model"></a>Modello di distribuzione diretta

In questo modello di distribuzione, Servizi di dominio Active Directory di Azure è abilitato all'interno di una rete virtuale appartenente alla sottoscrizione di Azure CSP. Gli agenti di amministrazione del partner CSP dispongono dei privilegi seguenti:

* *Privilegi di amministratore globale* nel tenant di Azure AD del cliente.
* *Privilegi del proprietario* della sottoscrizione nella sottoscrizione di Azure CSP.

![Modello di distribuzione diretta](./media/csp/csp_direct_deployment_model.png)

In questo modello di distribuzione, gli agenti di amministrazione del provider CSP possono amministrare le identità per il cliente. Questi agenti di amministrazione possono eseguire attività come il provisioning di nuovi utenti o gruppi o aggiungere applicazioni all'interno del tenant di Azure AD del cliente.

Questo modello di distribuzione può essere adatto per le organizzazioni più piccole che non dispongono di un amministratore di identità dedicato o preferiscono che il partner CSP amministri le identità per loro conto.

### <a name="peered-deployment-model"></a>Modello di distribuzione con peering

In questo modello di distribuzione, Servizi di dominio Active Directory di Azure è abilitato all'interno di una rete virtuale appartenente al cliente, ovvero una sottoscrizione diretta di Azure pagata dal cliente. Il partner CSP può distribuire applicazioni all'interno di una rete virtuale appartenente alla sottoscrizione CSP del cliente. Le reti virtuali possono essere connesse tramite il peering di rete virtuale di Azure.

Con questa distribuzione, i carichi di lavoro o le applicazioni distribuite dal partner CSP nella sottoscrizione di Azure CSP possono connettersi al dominio gestito del cliente di cui è stato eseguito il provisioning nella sottoscrizione diretta di Azure del cliente.

![Modello di distribuzione con peering](./media/csp/csp_peered_deployment_model.png)

Questo modello di distribuzione offre una separazione dei privilegi e permette agli agenti dell'help desk del partner CSP di amministrare la sottoscrizione di Azure e distribuire e gestire le risorse al suo interno. Tuttavia, gli agenti di helpdesk del partner CSP non devono disporre di privilegi di amministratore globale nella directory di Azure AD del cliente. Gli amministratori delle identità del cliente possono continuare a gestire le identità per la propria organizzazione.

Questo modello di distribuzione può essere adatto a scenari in cui un ISV fornisce una versione ospitata dell'applicazione locale, che deve anche connettersi ad Azure AD del cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Amministrare Azure AD DS nelle sottoscrizioni CSPAdminister Azure AD DS in CSP subscriptions

Le considerazioni importanti che seguono si applicano all'amministrazione di un dominio gestito in una sottoscrizione di Azure CSP:

* **Gli agenti di amministrazione di CSP possono eseguire il provisioning di un dominio gestito usando le proprie credenziali:CSP admin agents can provision a managed domain using their credentials:** Servizi di dominio Active Directory di Azure supporta le sottoscrizioni di Azure CSP. Gli utenti appartenenti al gruppo di agenti di amministrazione di un partner CSP possono eseguire il provisioning di un nuovo dominio gestito di Azure AD DS.Users belonging to a CSP partner's admin agents group can provision a new Azure AD DS managed domain.

* **I CSP possono creare script di nuovi domini gestiti per i clienti utilizzando PowerShell:CPs can script creation of new managed domains for their customers using PowerShell:** Per informazioni dettagliate, vedere [come abilitare Servizi di dominio Active Directory di Azure usando PowerShell.See how to enable Azure AD DS using PowerShell](powershell-create-instance.md) for details.

* **Gli agenti di amministrazione di CSP non possono eseguire attività di gestione in corso nel dominio gestito utilizzando le proprie credenziali:CSP admin agents can't perform ongoing management tasks on the managed domain using their credentials:** Gli utenti amministratori di CSP non possono eseguire attività di gestione di routine all'interno del dominio gestito utilizzando le proprie credenziali. Questi utenti sono esterni al tenant di Azure AD del cliente e le relative credenziali non sono disponibili all'interno del tenant di Azure AD del cliente. Servizi di dominio Active Directory di Azure non ha accesso agli accessi delle password Kerberos e NTLM per questi utenti, pertanto gli utenti non possono essere autenticati nei domini gestiti di Servizi di dominio Active Directory di Azure.Azure AD DS doesn't have access to the Kerberos and NTLM password hashes for these users, so users can't be authenticated on Azure AD DS managed domains.

  > [!WARNING]
  > È necessario creare un account utente nella directory del cliente per eseguire attività di amministrazione continuative nel dominio gestito.
  >
  > Non è possibile accedere al dominio gestito utilizzando le credenziali di un utente amministratore CSP. A tale scopo, usare le credenziali di un account utente appartenente al tenant di Azure AD del cliente. Queste credenziali sono necessarie per attività quali l'aggiunta di macchine virtuali al dominio gestito, l'amministrazione di DNS o l'amministrazione di Criteri di gruppo.

* **L'account utente creato per l'amministrazione in corso deve essere aggiunto al gruppo *Amministratori controller di dominio AAD:* ** il gruppo *AAD DC Administrators* dispone dei privilegi per eseguire determinate attività di amministrazione delegata nel dominio gestito. Queste attività includono la configurazione del DNS, la creazione di unità organizzative e l'amministrazione dei criteri di gruppo.
    
    Affinché un partner CSP esegua queste attività in un dominio gestito, è necessario creare un account utente all'interno del tenant di Azure AD del cliente. Le credenziali per questo account devono essere condivise con gli agenti di amministrazione del partner CSP. Inoltre, questo account utente deve essere aggiunto al gruppo *Amministratori controller di dominio AAD* per consentire l'esecuzione di attività di configurazione nel dominio gestito utilizzando questo account utente.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [iscriversi al programma Azure CSP](/partner-center/enrolling-in-the-csp-program). È quindi possibile abilitare Servizi di dominio Azure AD usando il portale di Azure o Azure PowerShell.You can then enable Azure AD Domain Services using [the Azure portal](tutorial-create-instance.md) or Azure [PowerShell.](powershell-create-instance.md)
