---
title: Gestire l'utilizzo e la fatturazione per lo Stack di Azure come Provider di servizi Cloud | Documenti Microsoft
description: Una procedura di registrazione dello Stack di Azure come Provider di Cloud e l'aggiunta di clienti.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gestire l'utilizzo e la fatturazione per lo Stack di Azure come Provider di servizi Cloud 

*Si applica a: Azure Stack integrate di sistemi*

Questo articolo illustra in dettaglio la registrazione dello Stack di Azure come un Provider di Cloud (CSP) e l'aggiunta di clienti.

Come un provider CSP, si è probabile che molti clienti diversi con lo Stack di Azure. Ogni cliente ha una sottoscrizione di CSP in Azure e sarà necessario indicare l'utilizzo dallo Stack di Azure alla sottoscrizione di ogni utente.

Nel diagramma seguente vengono illustrati i passaggi che è necessario scegliere l'account di servizi condivisi e registrare un account azure con l'account. Quando si è fatto, è possibile caricare i clienti finali.

**Procedura di aggiunta come un CSP di rilevamento dell'utilizzo**

![Processo per l'abilitazione dell'utilizzo e gestione dei Provider di servizi Cloud.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Creare una sottoscrizione CSP o CSPSS

### <a name="cloud-service-provider-subscription-types"></a>Tipi di sottoscrizione di Provider di servizi cloud

È necessario scegliere il tipo di account di servizi condivisi che è utilizzato per lo Stack di Azure. I tipi di sottoscrizioni che possono essere utilizzate per la registrazione di un tenant Azure Stack sono:

 - Provider di servizi cloud 
 - Sottoscrizione partner di servizi condivisi 

#### <a name="csp-shared-services"></a>CSP di servizi condivisi

Le sottoscrizioni di servizi condivisi Provider del servizio (CSPSS) cloud sono la scelta migliore per la registrazione quando un CSP diretta o un server di distribuzione CSP opera dello Stack di Azure.

Le sottoscrizioni CSPSS sono associate a un tenant di servizi condivisi. Quando si registra dello Stack di Azure, è necessario fornire le credenziali per un account che è un proprietario della sottoscrizione. L'account utilizzato per registrare Azure Stack può essere diverso dall'account dell'amministratore che utilizzano per la distribuzione. eseguire i due *non* devono appartenere allo stesso dominio. In altre parole, è possibile distribuire con il tenant già in uso. Ad esempio potrebbe utilizzare ContosoCSP.onmicrosoft.com, quindi registrare l'utilizzo di un tenant diverso, ad esempio IURContosoCSP.onmicrosoft.com. È necessario tenere presente che si accede tramite ContosoCSP.onmicrosoft.com quando si esegue l'operazione di amministrazione di Azure Stack giorno da fare. Quando accede a Azure utilizzando IURContosoCSP.onmicrosoft.com quando è necessario eseguire le operazioni di registrazione.

Fare riferimento al seguente per una descrizione di CSPSS sottoscrizioni e le istruzioni su come creare una sottoscrizione [servizi condivisi di aggiungere Azure Partner](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Sottoscrizioni di CSP

Le sottoscrizioni di Service Provider (CSP) cloud sono la scelta migliore per la registrazione quando un rivenditore CSP o un cliente finale opera dello Stack di Azure.

## <a name="register-azure-stack"></a>Registro dello Stack di Azure

Per registrare con lo Stack di Azure, vedere [registro dello Stack di Azure con la sottoscrizione Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Aggiungere utenti finali

Per configurare Azure Stack in modo che quando un nuovo tenant utilizza risorse alla propria sottoscrizione del Provider del servizio Cloud (CSP) verrà segnalato il loro utilizzo, vedere [aggiungere tenant per l'utilizzo e fatturazione per Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Ricaricare le sottoscrizioni a destra

Stack di Azure Usa una funzionalità di registrazione. La registrazione è un oggetto, archiviato in Azure, che documenta le sottoscrizioni di Azure da usare per pagare per un determinato Stack di Azure. In questa sezione illustra l'importanza della registrazione.

Registrazione dello Stack di Azure consente di:
 - Inoltrare i dati di utilizzo dello Stack di Azure a Azure Commerce e fatturare una sottoscrizione di Azure.
 - Report sull'utilizzo di ogni cliente di una sottoscrizione diversa con una distribuzione multi-tenant di Azure Stack. Multi-tenancy consente dello Stack di Azure supportare diverse organizzazioni nella stessa istanza dello Stack di Azure.

Per ogni Stack di Azure, è disponibile una sottoscrizione predefinita e come molti tenant sottoscrizioni in base alle esigenze. La sottoscrizione di predefinito è una sottoscrizione di Azure viene addebitata se è disponibile alcuna sottoscrizione specifico del tenant. Deve essere il primo a essere registrati. Creazione dei report di utilizzo di multi-tenant, la sottoscrizione deve essere un CSP o CSPSS.

Quindi, la registrazione viene aggiornata con una sottoscrizione di Azure per ogni tenant che utilizzano lo Stack di Azure. Le sottoscrizioni di tenant devono essere del tipo di provider CSP e devono riportare al partner a cui appartiene la sottoscrizione predefinita. In altre parole, è possibile registrare i clienti di un altro utente.

Quando Azure Stack inoltra le informazioni sull'utilizzo per Azure globale, un servizio in Azure consulta la registrazione e viene eseguito il mapping dell'utilizzo di ogni tenant per la sottoscrizione tenant appropriato. Se non è stato registrato un tenant, che l'utilizzo passa a questa sottoscrizione predefinita per l'istanza di Azure Stack da cui ha avuto origine.

Poiché gli abbonamenti tenant hanno CSP, la fattura viene inviata al partner CSP e informazioni sull'utilizzo non sono visibile al cliente finale.



## <a name="next-steps"></a>Passaggi successivi

 - Per ulteriori informazioni sul programma CSP, vedere [programma Cloud Solution Provider](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Per ulteriori informazioni su come recuperare informazioni sull'utilizzo di risorse dallo Stack di Azure, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](/azure-stack-billing-and-chargeback.md).
