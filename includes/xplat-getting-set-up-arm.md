---
services: virtual-machines
title: Usare l&quot;interfaccia della riga di comando di Azure con Azure Resource Manager
author: squillace
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machine
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: linux
ms.workload: infrastructure
ms.date: 04/13/2015
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: e664ce9426a2852a35dfdade5d41a9ce8b37a3b7
ms.openlocfilehash: e2f9d2c74e5dfa0a08f25685062903a985ba641c


---
## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Utilizzare l'interfaccia della riga di comando di Azure con Gestione risorse di Azure
Prima di utilizzare l'interfaccia della riga di comando di Azure con i comandi e i modelli di Gestione risorse al fine di distribuire i carichi di lavoro e le risorse di Azure tramite gruppi di risorse, è necessario disporre di un account Azure. Se non si dispone di un account, è possibile ottenere un [Azure qui versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Se non è già un account Azure, ma è una sottoscrizione di abbonamento MSDN, è possibile ottenere i crediti di Azure attivando il [abbonati MSDN dei vantaggi qui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure è possibile utilizzare l'account gratuito. Uno funzionerà per l'accesso ad Azure.
> 
> 

### <a name="step-1-verify-the-azure-cli-version"></a>Passaggio 1: verificare la versione dell'interfaccia della riga di comando di Azure
Al fine di utilizzare l'interfaccia della riga di comando di Azure per comandi imperativi e modelli di Gestione risorse, è necessario verificare che sia disponibile almeno la versione 0.8.17. Per verificare la versione, digitare `azure --version`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se si desidera aggiornare la versione dell'interfaccia della riga di comando di Azure vedere [Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Passaggio 2: Verificare che si utilizza un lavoro o scuola identità con Azure
È possibile usare la modalità di comando Azure Resource Manager solo se si usa un [tenant Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) o un [nome dell'entità servizio](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Detti anche *ID organizzativo*.)

Per vedere se si dispone di uno, connettersi digitando `azure login` e l'utilizzo del lavoro o scuola username e password quando richiesto. Se si dispone di uno, si otterrà qualcosa di simile al seguente:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Se questo non è visualizzato, è necessario creare un nuovo tenant (o entità servizio) con l'identità dell'account Microsoft. Questo problema si verifica spesso con le sottoscrizioni di MSDN personali o con le sottoscrizioni di una versione di valutazione gratuita. Per creare un ID aziendale o dell'istituto di istruzione dall'account Azure creato con un ID Microsoft, vedere [Associare una directory di Azure AD a una nuova sottoscrizione di Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se si ritiene di che aver già un id organizzazione, potrebbe essere necessario comunicare con la persona che ha creato l'account per l'utente.

### <a name="step-3-choose-your-azure-subscription"></a>Passaggio 3: Scegliere la sottoscrizione di Azure
Se si dispone solo una sottoscrizione nell'account Azure, l'interfaccia della riga di comando di Azure viene associata con quell'abbonamento (impostazione predefinita). Se si ha più di una sottoscrizione, è necessario selezionare la sottoscrizione che si vuole usare digitando `azure account set <subscription id or name> true` dove *subscription id or name* è l'ID sottoscrizione o il nome della sottoscrizione che si vuole usare nella sessione corrente.

Verrà visualizzata una schermata simile alla seguente:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Passaggio 4: inserire l'interfaccia della riga di comando di Azure nella modalità Gestione risorse di Azure
Per utilizzare la modalità Gestione risorse di Azure con l'interfaccia della riga di comando di Azure, digitare `azure config mode arm`. Verrà visualizzata una schermata simile alla seguente:

    $ azure config mode arm
    info:    New mode is arm

> [!NOTE]
> È possibile passare nuovamente per l'utilizzo di comandi di gestione del servizio di Azure digitando `azure config mode asm`.
> 
> 




<!--HONumber=Jan17_HO3-->


