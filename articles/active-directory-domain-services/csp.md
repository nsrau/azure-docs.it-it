---
title: Azure AD Domain Services per i provider di soluzioni cloud | Microsoft Docs
description: Informazioni su come abilitare e gestire Azure Active Directory Domain Services domini gestiti per i provider di soluzioni cloud di Azure
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
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Distribuzione e gestione di Azure Active Directory Domain Services per i provider di soluzioni cloud di Azure

Azure Cloud Solution Provider (CSP) è un programma per i partner Microsoft e fornisce un canale di licenza per diversi servizi cloud Microsoft. Azure CSP permette ai partner di gestire le vendite, avere il controllo sulla relazione di fatturazione, fornire supporto tecnico e per la fatturazione e rappresentare l'unico punto di contatto dei clienti. Inoltre, Azure CSP include un set completo di strumenti, tra cui un portale self-service e le API associate. Questi strumenti consentono ai partner CSP di effettuare agevolmente il provisioning, gestire le risorse di Azure e fornire la fatturazione dei clienti e delle relative sottoscrizioni.

Il [portale del centro](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) per i partner è il punto di ingresso di tutti i partner di Azure CSP e offre funzionalità avanzate di gestione dei clienti, elaborazione automatizzata e altro ancora. I partner di Azure CSP possono utilizzare le funzionalità del Centro per i partner tramite un'interfaccia utente basata sul Web o mediante PowerShell e varie chiamate API.

Il diagramma seguente illustra il funzionamento del modello CSP a livello generale. Qui, Contoso ha un tenant di Azure Active Directory (Azure AD). Ha una partnership con un CSP, che distribuisce e gestisce le risorse nella propria sottoscrizione Azure CSP. Contoso può anche avere sottoscrizioni di Azure regolari (dirette), fatturate direttamente alla società stessa.

![Panoramica del modello CSP](./media/csp/csp_model_overview.png)

Il tenant del partner CSP ha tre gruppi di agenti speciali: agenti di *Amministrazione* , agenti *helpdesk* e agenti di *vendita* .

Il gruppo agenti di *Amministrazione* viene assegnato al ruolo di amministratore tenant nel tenant Azure ad di contoso. Di conseguenza, un utente appartenente al gruppo di agenti di amministrazione del partner CSP dispone dei privilegi di amministratore del tenant nel tenant di Azure AD di contoso.

Quando il partner CSP effettua il provisioning di una sottoscrizione di Azure CSP per Contoso, il gruppo di agenti di amministrazione viene assegnato al ruolo di proprietario della sottoscrizione. Gli agenti di amministrazione del partner CSP dispongono pertanto dei privilegi necessari per eseguire il provisioning delle risorse di Azure, quali macchine virtuali, reti virtuali e Azure AD Domain Services per conto di Contoso.

Per altre informazioni, vedere la [Panoramica di Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Vantaggi dell'uso di Azure AD DS in una sottoscrizione di Azure CSP

Azure Active Directory Domain Services (Azure AD DS) fornisce servizi di dominio gestiti, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP, autenticazione Kerberos/NTLM completamente compatibile con Windows Server Active Directory Domain Services. Nel corso dei decenni, sono state create molte applicazioni per funzionare in Active Directory utilizzando tali funzionalità. Molti fornitori di software indipendenti hanno compilato e distribuito applicazioni presso la sede dei clienti. Queste applicazioni sono difficili da supportare perché spesso è necessario accedere ai diversi ambienti in cui vengono distribuite le applicazioni. Le sottoscrizioni di Azure CSP costituiscono un'alternativa più semplice con la scalabilità e la flessibilità di Azure.

Azure AD DS supporta le sottoscrizioni di Azure CSP. È possibile distribuire l'applicazione in una sottoscrizione di Azure CSP collegata al tenant Azure AD del cliente. Di conseguenza, i dipendenti (personale di supporto) possono gestire, amministrare e gestire le macchine virtuali in cui l'applicazione viene distribuita usando le credenziali aziendali dell'organizzazione.

È anche possibile distribuire un dominio gestito Azure AD DS nel tenant Azure AD del cliente. L'applicazione viene quindi connessa al dominio gestito del cliente. Le funzionalità all'interno dell'applicazione che si basano su Kerberos/NTLM, LDAP o sull' [API System. DirectoryServices](/dotnet/api/system.directoryservices) funzionano in modo uniforme rispetto al dominio del cliente. I clienti finali traggono vantaggio dall'utilizzo dell'applicazione come servizio, senza doversi preoccupare della manutenzione dell'infrastruttura in cui è distribuita l'applicazione.

Tutta la fatturazione per le risorse di Azure usate nella sottoscrizione, incluso Azure AD DS, viene addebitata all'utente. Si mantiene il controllo completo sulla relazione con il cliente quando si tratta di vendite, fatturazione, supporto tecnico e così via. Con la flessibilità della piattaforma Azure CSP, un piccolo team di agenti di supporto può servire molti clienti di questo tipo che dispongono di istanze dell'applicazione distribuita.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelli di distribuzione CSP per Azure AD DS

Esistono due modi in cui è possibile usare Azure AD DS con una sottoscrizione di Azure CSP. Scegliere quella adeguata in base alle considerazioni sulla sicurezza e sulla semplicità dei clienti.

### <a name="direct-deployment-model"></a>Modello di distribuzione diretta

In questo modello di distribuzione, Azure AD DS è abilitato in una rete virtuale che appartiene alla sottoscrizione di Azure CSP. Gli agenti di amministrazione del partner CSP dispongono dei privilegi seguenti:

* Privilegi di *amministratore globale* nel tenant Azure ad del cliente.
* Privilegi di *proprietario della sottoscrizione* per la sottoscrizione di Azure CSP.

![Modello di distribuzione diretta](./media/csp/csp_direct_deployment_model.png)

In questo modello di distribuzione, gli agenti di amministrazione del provider CSP possono amministrare le identità per il cliente. Questi agenti di amministrazione possono eseguire attività come il provisioning di nuovi utenti o gruppi o aggiungere applicazioni all'interno del tenant Azure AD del cliente.

Questo modello di distribuzione può essere adatto per organizzazioni di piccole dimensioni che non dispongono di un amministratore di identità dedicato o che preferiscono che il partner CSP gestisca le identità per loro conto.

### <a name="peered-deployment-model"></a>Modello di distribuzione con peering

In questo modello di distribuzione, Azure AD DS è abilitato in una rete virtuale appartenente al cliente, una sottoscrizione di Azure diretta pagata dal cliente. Il partner CSP può distribuire le applicazioni all'interno di una rete virtuale appartenente alla sottoscrizione CSP del cliente. Le reti virtuali possono essere connesse tramite il peering di rete virtuale di Azure.

Con questa distribuzione, i carichi di lavoro o le applicazioni distribuite dal partner CSP nella sottoscrizione di Azure CSP possono connettersi al dominio gestito del cliente sottoposta a provisioning nella sottoscrizione di Azure diretta del cliente.

![Modello di distribuzione con peering](./media/csp/csp_peered_deployment_model.png)

Questo modello di distribuzione offre una separazione dei privilegi e permette agli agenti dell'help desk del partner CSP di amministrare la sottoscrizione di Azure e distribuire e gestire le risorse al suo interno. Gli agenti helpdesk del partner CSP, tuttavia, non devono avere privilegi di amministratore globale per la directory Azure AD del cliente. Gli amministratori delle identità del cliente possono continuare a gestire le identità per la propria organizzazione.

Questo modello di distribuzione può essere adatto a scenari in cui un ISV fornisce una versione ospitata dell'applicazione locale, che deve anche connettersi all'Azure AD del cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Amministrare Azure AD DS nelle sottoscrizioni CSP

Le considerazioni importanti che seguono si applicano all'amministrazione di un dominio gestito in una sottoscrizione di Azure CSP:

* **Gli agenti di amministrazione CSP possono eseguire il provisioning di un dominio gestito usando le credenziali:** Azure AD DS supporta le sottoscrizioni di Azure CSP. Gli utenti appartenenti al gruppo di agenti di amministrazione del partner CSP possono eseguire il provisioning di un nuovo dominio gestito di Azure AD DS.

* I **CSP possono creare script per la creazione di nuovi domini gestiti per i clienti tramite PowerShell:** Per informazioni dettagliate, vedere [come abilitare Azure AD DS usando PowerShell](powershell-create-instance.md) .

* **Gli agenti di amministrazione CSP non possono eseguire attività di gestione in corso sul dominio gestito usando le proprie credenziali:** Gli utenti amministratori CSP non possono eseguire attività di gestione di routine all'interno del dominio gestito usando le proprie credenziali. Questi utenti sono esterni al tenant Azure AD del cliente e le relative credenziali non sono disponibili nel tenant di Azure AD del cliente. Azure AD DS non ha accesso agli hash delle password Kerberos e NTLM per questi utenti, quindi gli utenti non possono essere autenticati nei domini gestiti Azure AD DS.

  > [!WARNING]
  > È necessario creare un account utente nella directory del cliente per eseguire attività di amministrazione continuative nel dominio gestito.
  >
  > Non è possibile accedere al dominio gestito usando le credenziali dell'utente amministratore CSP. A tale scopo, usare le credenziali di un account utente appartenente al tenant Azure AD del cliente. Queste credenziali sono necessarie per attività quali l'aggiunta di macchine virtuali al dominio gestito, l'amministrazione di DNS o l'amministrazione di Criteri di gruppo.

* **L'account utente creato per l'amministrazione continua deve essere aggiunto al gruppo di *amministratori di AAD DC* :** il gruppo di *amministratori di AAD DC* dispone dei privilegi necessari per eseguire determinate attività amministrative delegate nel dominio gestito. Queste attività includono la configurazione di DNS, la creazione di unità organizzative e l'amministrazione di criteri di gruppo.
    
    Affinché un partner CSP esegua queste attività in un dominio gestito, è necessario creare un account utente all'interno del tenant Azure AD del cliente. Le credenziali per questo account devono essere condivise con gli agenti di amministrazione del partner CSP. Inoltre, è necessario aggiungere questo account utente al gruppo di *amministratori di AAD DC* per consentire l'esecuzione delle attività di configurazione nel dominio gestito utilizzando questo account utente.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [iscriversi al programma Azure CSP](/partner-center/enrolling-in-the-csp-program). È quindi possibile abilitare Azure AD Domain Services usando [il portale di Azure](tutorial-create-instance.md) o [Azure PowerShell](powershell-create-instance.md).
