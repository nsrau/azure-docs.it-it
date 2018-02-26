---
title: Azure Active Directory Domain Services per Azure Cloud Solution Provider | Documentazione Microsoft
description: Azure Active Directory Domain Services per Azure Cloud Solution Provider.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services per Azure Cloud Solution Provider (CSP)
Questo articolo spiega come utilizzare Azure AD Domain Services in una sottoscrizione Azure CSP.

## <a name="overview-of-azure-csp"></a>Panoramica di Azure CSP
Azure CSP è un programma per Microsoft Partner e offre un canale di licenza per vari servizi cloud di Microsoft. Azure CSP permette ai partner di gestire le vendite, avere il controllo sulla relazione di fatturazione, fornire supporto tecnico e per la fatturazione e rappresentare l'unico punto di contatto dei clienti. Inoltre, Azure CSP include un set completo di strumenti, tra cui un portale self-service e le API associate. Questi strumenti consentono ai partner CSP di effettuare agevolmente il provisioning, gestire le risorse di Azure e fornire la fatturazione dei clienti e delle relative sottoscrizioni.

Il [portale Centro per i partner](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funge da punto di ingresso per tutti i partner di Azure CSP. Offre funzionalità complete per la gestione dei clienti, elaborazione automatica e altro ancora. I partner di Azure CSP possono utilizzare le funzionalità del Centro per i partner tramite un'interfaccia utente basata sul Web o mediante PowerShell e varie chiamate API.

Il diagramma seguente illustra il funzionamento del modello CSP a livello generale. Contoso ha una directory di Azure AD. Ha una partnership con un CSP, che distribuisce e gestisce le risorse nella propria sottoscrizione Azure CSP. Contoso può anche avere sottoscrizioni di Azure regolari (dirette), fatturate direttamente alla società stessa.

![Panoramica del modello CSP](./media/csp/csp_model_overview.png)

Il tenant del partner CSP ha tre gruppi di agenti speciali: agenti di amministrazione, agenti dell'help desk e agenti di vendita. Il gruppo di agenti di amministrazione viene assegnato al ruolo di amministratore del tenant nella directory di Azure AD di Contoso. Di conseguenza, un utente appartenente al gruppo di agenti di amministrazione del partner CSP ha privilegi di amministratore del tenant nella directory di Azure AD di Contoso. Quando il partner CSP effettua il provisioning di una sottoscrizione di Azure CSP per Contoso, il gruppo di agenti di amministrazione viene assegnato al ruolo di proprietario della sottoscrizione. Gli agenti di amministrazione del partner CSP dispongono pertanto dei privilegi necessari per eseguire il provisioning delle risorse di Azure, quali macchine virtuali, reti virtuali e Azure AD Domain Services per conto di Contoso.

Per altre informazioni, vedere la [Panoramica di Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Vantaggi dell'uso di Azure AD Domain Services in una sottoscrizione di Azure CSP
Azure AD Domain Services offre servizi compatibili di AD di Windows Server in Azure, ad esempio LDAP, autenticazione Kerberos/NTLM, aggiunta a un dominio, criteri di gruppo e DNS. Nel corso dei decenni, sono state create molte applicazioni per funzionare in Active Directory utilizzando tali funzionalità. Molti fornitori di software indipendenti hanno compilato e distribuito applicazioni presso la sede dei clienti. Queste applicazioni sono onerose da supportare poiché spesso richiedono l'accesso ai diversi ambienti in cui vengono distribuite. Le sottoscrizioni di Azure CSP costituiscono un'alternativa più semplice con la scalabilità e la flessibilità di Azure.

Azure AD Domain Services ora supporta le sottoscrizioni di Azure CSP. È ora possibile distribuire un'applicazione in una sottoscrizione di Azure CSP associata alla directory di Azure AD del cliente. Di conseguenza, il personale di supporto può gestire, amministrare e offrire assistenza per le macchine virtuali in cui è distribuita l'applicazione utilizzando le credenziali aziendali. Inoltre, è possibile effettuare il provisioning di un dominio gestito di Azure AD Domain Services per la directory di Azure AD del cliente. L'applicazione è connessa al dominio gestito del cliente. Le funzionalità all'interno dell'applicazione basate su Kerberos/NTLM, LDAP o l'[API System.DirectoryServices](https://msdn.microsoft.com/library/system.directoryservices) funzionano perfettamente con la directory del cliente. I clienti finali traggono notevoli vantaggi dall'utilizzo dell'applicazione come servizio, senza doversi preoccupare della manutenzione dell'infrastruttura in cui è distribuita l'applicazione.

Tutta la fatturazione per le risorse di Azure utilizzate nella sottoscrizione, tra cui Azure AD Domain Services, viene addebitata all'utente. L'utente ha il controllo completo della relazione con il cliente per quanto riguarda vendite, fatturazione, supporto tecnico e così via. Con la flessibilità della piattaforma Azure CSP, un piccolo team di agenti di supporto può dare assistenza a molti clienti con istanze dell'applicazione distribuita.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelli di distribuzione CSP per Azure AD Domain Services
Esistono due modalità di utilizzo di Azure AD Domain Services con una sottoscrizione di Azure CSP. Scegliere quella adeguata in base alle considerazioni sulla sicurezza e sulla semplicità dei clienti.

### <a name="direct-deployment-model"></a>Modello di distribuzione diretta
In questo modello di distribuzione, Azure AD Domain Services è abilitato in una rete virtuale che appartiene alla sottoscrizione di Azure CSP. Gli agenti di amministrazione del partner CSP dispongono dei privilegi seguenti:
* Privilegi di amministratore globale nella directory di Azure AD del cliente.
* Privilegi di proprietario della sottoscrizione per la sottoscrizione di Azure CSP.

![Modello di distribuzione diretta](./media/csp/csp_direct_deployment_model.png)

In questo modello di distribuzione, gli agenti di amministrazione del provider CSP possono amministrare le identità per il cliente. Gli agenti di amministrazione hanno la possibilità di effettuare il provisioning di nuovi utenti e gruppi, di aggiungere applicazioni all'interno della directory di Azure AD del cliente e così via. Questo modello di distribuzione è adatto a organizzazioni di piccole dimensioni che non dispongono di un amministratore di identità dedicato o preferiscono che sia il partner CSP ad amministrare le identità per loro conto.


### <a name="peered-deployment-model"></a>Modello di distribuzione con peering
In questo modello di distribuzione Azure AD Domain Services è abilitato in una rete virtuale che appartiene al cliente, ovvero una sottoscrizione di Azure diretta pagata dal cliente. Il partner CSP può quindi distribuire le applicazioni all'interno di una rete virtuale che appartiene alla sottoscrizione di CSP del cliente. Le reti virtuali possono essere connesse tramite il peering di rete virtuale di Azure. Di conseguenza, carichi di lavoro e applicazioni distribuiti dal partner CSP nella sottoscrizione di Azure CSP possono connettersi al dominio gestito del cliente di cui viene effettuato il provisioning nella sottoscrizione di Azure diretta del cliente.

![Modello di distribuzione con peering](./media/csp/csp_peered_deployment_model.png)

Questo modello di distribuzione offre una separazione dei privilegi e permette agli agenti dell'help desk del partner CSP di amministrare la sottoscrizione di Azure e distribuire e gestire le risorse al suo interno. Gli agenti dell'help desk del partner CSP, tuttavia, non devono necessariamente disporre di privilegi di amministratore globale nella directory di Azure AD del cliente. Gli amministratori delle identità del cliente possono continuare a gestire le identità per la propria organizzazione.

Questo modello di distribuzione è adatto a scenari in cui un fornitore di software indipendente offre una versione ospitata della propria applicazione locale, che deve anch'essa connettersi alla directory Active Directory del cliente.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Amministrazione di Azure AD Domain Services nelle sottoscrizioni di CSP
Le considerazioni importanti che seguono si applicano all'amministrazione di un dominio gestito in una sottoscrizione di Azure CSP:

* **Gli agenti di amministrazione CSP possono effettuare il provisioning di un dominio gestito usando le proprie credenziali:** Azure AD Domain Services supporta le sottoscrizioni di Azure CSP. Gli utenti appartenenti al gruppo di agenti di amministrazione di un partner CSP, pertanto, possono effettuare il provisioning di un nuovo dominio gestito di Azure AD Domain Services.

* **I CSP possono creare script per la creazione di nuovi domini gestiti per i clienti con PowerShell:** vedere [come abilitare Azure AD Domain Services tramite PowerShell](active-directory-ds-enable-using-powershell.md) per informazioni dettagliate.

* **Gli agenti di amministrazione CSP non possono eseguire attività di gestione continuative nel dominio gestito usando le proprie credenziali:** gli utenti amministratori CSP non possono eseguire attività di gestione di routine all'interno del dominio gestito usando le proprie credenziali. Questi utenti sono esterni alla directory Azure AD del cliente e le loro credenziali non sono disponibili in tale directory. Azure AD Domain Services, pertanto, non ha accesso agli hash delle password Kerberos e NTLM di tali utenti. Questi utenti, di conseguenza, non possono essere autenticati in domini gestiti di Azure AD Domain Services.

  > [!WARNING]
  > **È necessario creare un account utente nella directory del cliente per eseguire attività di amministrazione continuative nel dominio gestito.**
  > Non è possibile accedere al dominio gestito utilizzando le credenziali di un utente amministratore CSP. A tale scopo, utilizzare le credenziali di un account utente appartenente alla directory Azure AD del cliente. Queste credenziali sono necessarie per attività quali l'aggiunta di macchine virtuali al dominio gestito, l'amministrazione di DNS, l'amministrazione di criteri di gruppo e così via.
  >

* **L'account utente creato per l'amministrazione continuativa deve essere aggiunto al gruppo "Amministratori di AAD DC":** tale gruppo ha i privilegi necessari per eseguire alcune attività di amministrazione delegate per il dominio gestito. Tra le attività sono incluse la configurazione DNS, la creazione di unità organizzative, l'amministrazione dei criteri di gruppo e così via. Per consentire a un partner CSP di eseguire tali attività in un dominio gestito, è necessario creare un account utente nella directory Azure AD del cliente. Le credenziali per questo account devono essere condivise con gli agenti di amministrazione del partner CSP. L'account utente, inoltre, deve essere aggiunto al gruppo "Amministratori di AAD DC" per abilitare l'esecuzione delle attività di configurazione nel dominio gestito con questo account utente.


## <a name="next-steps"></a>Passaggi successivi
* [Effettuare la registrazione al programma Azure CSP](https://partnercenter.microsoft.com/partner/programs) e iniziare a generare nuovi affari tramite Azure CSP.
* Esaminare l'elenco di [servizi di Azure disponibili in Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Abilitare Azure AD Domain Services con PowerShell](active-directory-ds-enable-using-powershell.md)
* [Introduzione ad Azure AD Domain Services](active-directory-ds-getting-started.md)
