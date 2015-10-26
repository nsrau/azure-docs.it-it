<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Utilizzare l'interfaccia della riga di comando di Azure con Gestione risorse di Azure

Prima di utilizzare l'interfaccia della riga di comando di Azure con i comandi e i modelli di Gestione risorse al fine di distribuire i carichi di lavoro e le risorse di Azure tramite gruppi di risorse, è necessario disporre di un account Azure. Se non si dispone di un account, è possibile ottenere un [Azure qui versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE]Se non è già un account Azure, ma è una sottoscrizione di abbonamento MSDN, è possibile ottenere i crediti di Azure attivando il [abbonati MSDN dei vantaggi qui](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure è possibile utilizzare l'account gratuito. Uno funzionerà per l'accesso ad Azure.

### Passaggio 1: verificare la versione dell'interfaccia della riga di comando di Azure

Al fine di utilizzare l'interfaccia della riga di comando di Azure per comandi imperativi e modelli di Gestione risorse, è necessario verificare che sia disponibile almeno la versione 0.8.17. Per verificare la versione, digitare `azure --version`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se si desidera aggiornare la versione dell'interfaccia della riga di comando di Azure vedere [Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli).

### Passaggio 2: Verificare che si utilizza un lavoro o scuola identità con Azure

È possibile utilizzare la modalità di comando ARM solo se si utilizza un [tenant Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) o [nome dell'entità servizio](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Detti anche *ID organizzativo*.)

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

Se questo non è visualizzato, è necessario creare un nuovo tenant (o entità servizio) con l'identità dell'account Microsoft. (Si tratta spesso avviene per gli abbonamenti MSDN personali o sottoscrizioni di valutazione gratuite.) Per creare un id di lavoro o scuola dall'account creato con un id di Microsoft Azure, vedere [associare una Directory di Azure AD una nuova sottoscrizione Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se si ritiene di che aver già un id organizzazione, potrebbe essere necessario comunicare con la persona che ha creato l'account per l'utente.

### Passaggio 3: Scegliere la sottoscrizione di Azure

Se si dispone solo una sottoscrizione nell'account Azure, l'interfaccia della riga di comando di Azure viene associata con quell'abbonamento (impostazione predefinita). Se si dispone di più di una sottoscrizione, è necessario selezionare la sottoscrizione che si desidera utilizzare digitando `azure account set <subscription id or name> true` dove _nome o id sottoscrizione_ è l'id sottoscrizione o il nome della sottoscrizione che si desidera utilizzare nella sessione corrente.

Verrà visualizzata una schermata simile alla seguente:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### Passaggio 4: inserire l'interfaccia della riga di comando di Azure nella modalità Gestione risorse di Azure

Per utilizzare la modalità Gestione risorse di Azure con l'interfaccia della riga di comando di Azure, digitare `azure config mode arm`. Verrà visualizzata una schermata simile alla seguente:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]È possibile passare nuovamente per l'utilizzo di comandi di gestione del servizio di Azure digitando `azure config mode asm`.

<!---HONumber=Oct15_HO3-->