---
title: Gestire l'utilizzo e fatturazione per Azure Stack come Provider di servizi Cloud | Microsoft Docs
description: Una procedura dettagliata la registrazione di Azure Stack come un Provider Cloud (CSP) e aggiungendo i clienti per la fatturazione.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: 26abc338b617b967c6919ebbe556a81226ff47d8
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247861"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gestire l'utilizzo e fatturazione per Azure Stack come Provider di servizi Cloud 

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo illustra come la registrazione di Azure Stack come un Provider Cloud (CSP) e aggiungendo i clienti.

Come CSP si lavora con diversi clienti che usano Azure Stack. Ogni cliente ha una sottoscrizione CSP in Azure. È necessario indirizzare l'utilizzo di Azure Stack per ogni sottoscrizione utente.

Il diagramma seguente illustra i passaggi che dovrai scegliere l'account di servizi condivisi e registrare l'account di Azure con l'account di Azure Stack. È possibile registrato, eseguire l'onboarding i clienti finali.

**Passaggi per aggiungere l'utilizzo di rilevamento come CSP**

[ ![Processo per l'abilitazione dell'utilizzo e la gestione come un Provider di servizi Cloud](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "processo per l'abilitazione dell'utilizzo e la gestione come un Provider di servizi Cloud") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Creare una sottoscrizione CSP o questi servizi avanzati

### <a name="cloud-service-provider-subscription-types"></a>Tipi di sottoscrizione di Provider di servizi cloud

È necessario scegliere il tipo di account di servizi condivisi che è utilizzato per Azure Stack. I tipi di sottoscrizioni che possono essere utilizzate per la registrazione di un tenant di Azure Stack sono:

 - Provider di servizi cloud 
 - Sottoscrizione partner di servizi condivisi 

#### <a name="azure-partner-shared-services"></a>Servizi condivisi di Partner di Azure

Le sottoscrizioni di Azure questi (Partner servizi avanzati Shared Services) rappresentano la scelta preferita per la registrazione quando un CSP diretto o un server di distribuzione CSP opera Azure Stack.

Le sottoscrizioni di questi servizi avanzati sono associate a un tenant di servizi condivisi. Quando si registra Azure Stack, è necessario fornire le credenziali per un account che è un proprietario della sottoscrizione. L'account usato per registrare Azure Stack può essere diverso dall'account amministratore usato per la distribuzione. Inoltre, i due account effettuare *non* devono appartenere allo stesso dominio. In altre parole, è possibile distribuire con il tenant che usi già. Ad esempio potrebbe usare ContosoCSP.onmicrosoft.com, quindi la registrazione usando un tenant diverso, ad esempio IURContosoCSP.onmicrosoft.com. È necessario ricordare che si accede tramite ContosoCSP.onmicrosoft.com quando si esegue l'operazione di amministrazione di Azure Stack giorno a tale scopo. Quando si accede ad Azure tramite IURContosoCSP.onmicrosoft.com quando è necessario eseguire le operazioni di registrazione.

Vedere quanto segue per una descrizione delle sottoscrizioni di questi servizi avanzati e istruzioni su come creare la sottoscrizione [Aggiungi Partner Azure Shared servizi](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Sottoscrizioni CSP

Le sottoscrizioni cloud di Service Provider (CSP) sono la scelta preferita per la registrazione quando un rivenditore CSP o un cliente finale opera Azure Stack.

## <a name="register-azure-stack"></a>Registrare Azure Stack

Usare la sottoscrizione di questi servizi avanzati creato seguendo le informazioni nella sezione precedente per registrare Azure Stack con Azure. Per altre informazioni, vedere [registrare Azure Stack con la sottoscrizione di Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Aggiungere utenti finali

Per configurare Azure Stack, in modo che quando un nuovo tenant Usa risorse il loro utilizzo viene segnalato alla propria sottoscrizione di Cloud Service Provider (CSP), vedere [aggiungere tenant per l'utilizzo e fatturazione per Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Addebitare le sottoscrizioni a destra

Azure Stack utilizza una funzionalità denominata registration. Una registrazione è un oggetto archiviato in Azure. L'oggetto di registrazione vengono documentate le sottoscrizioni di Azure da usare per l'importo addebitato per un determinato Stack di Azure. Questa sezione viene illustrato l'importanza della registrazione.

Azure Stack usando il servizio registrazione possibile:
 - Inoltrare i dati di utilizzo di Azure Stack per e-Commerce di Azure e la fatturazione di una sottoscrizione di Azure.
 - Report sull'utilizzo di ogni cliente di una sottoscrizione diversa con una distribuzione multi-tenant di Azure Stack. Multi-tenancy consente a Azure Stack supportare diverse organizzazioni nella stessa istanza di Azure Stack.

Per ogni Stack di Azure, è presente una sottoscrizione predefinita e molte sottoscrizioni del tenant. La sottoscrizione predefinita è una sottoscrizione di Azure che viene addebitata se non c'è una sottoscrizione specifico del tenant. Deve essere la prima sottoscrizione registrata. Creazione dei report di utilizzo di multi-tenant, la sottoscrizione deve essere una sottoscrizione CSP o questi servizi avanzati.

Quindi, la registrazione viene aggiornata con una sottoscrizione di Azure per ogni tenant che sta per usare Azure Stack. Le sottoscrizioni tenant devono essere del tipo CSP e devono rollup verso il partner proprietario della sottoscrizione predefinita. In altre parole, non è possibile registrare i clienti di un altro utente.

Quando Azure Stack inoltra le informazioni sull'utilizzo per Azure globale, un servizio in Azure consulta la registrazione e viene eseguito il mapping dell'utilizzo di ogni tenant alla sottoscrizione tenant appropriato. Se non è stato registrato un tenant, che l'utilizzo viene indirizzato all'abbonamento predefinito per l'istanza di Azure Stack da cui ha avuto origine.

Poiché le sottoscrizioni tenant sono le sottoscrizioni CSP, fattura viene inviata al partner CSP e le informazioni sull'utilizzo non sono visibile al cliente finale.

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sul programma CSP, vedere [programma Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Per altre informazioni su come recuperare le informazioni sull'utilizzo di risorse di Azure Stack, vedere [informazioni sull'utilizzo e fatturazione in Azure Stack](azure-stack-billing-and-chargeback.md).
