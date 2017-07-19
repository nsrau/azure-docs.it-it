---
title: Distribuzione delle macchine virtuali di Azure con Chef | Microsoft Docs
description: Imparare a utilizzare Chef per effettuare la distribuzione automatizzata della macchina virtuale e la configurazione in Microsoft Azure
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: b6db0fbb4e0de896994954974ddcc39daad9c125
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017


---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automazione della distribuzione delle macchine virtuali di Azure con Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef rappresenta uno strumento molto utile che fornisce soluzioni automatizzate e configurazioni di stato personalizzate.

Con il rilascio della più recente API cloud, Chef fornisce una perfetta integrazione con Azure, offrendo la possibilità di eseguire il provisioning e distribuire le configurazioni di stato attraverso un unico comando.

Questo articolo illustra come configurare l'ambiente Chef per il provisioning di macchine virtuali di Azure e descrive i passaggi per creare un criterio o "CookBook" e successivamente distribuire tale cookbook in una macchina virtuale di Azure.

Verranno ora illustrate alcune nozioni di base su Chef.

## <a name="chef-basics"></a>Nozioni di base su Chef
Prima di iniziare, si consiglia di acquisire familiarità con i concetti di base di Chef. <a href="http://www.chef.io/chef" target="_blank">qui</a> è disponibile del materiale che sarebbe opportuno leggere rapidamente prima di tentare di eseguire la procedura. Di seguito viene comunque presentato un riassunto delle nozioni di base su Chef.

Il seguente diagramma illustra l'architettura di alto livello di Chef.

![][2]

Chef ha tre componenti principali dell'architettura: Chef Workstation, Chef Server e Chef Client (nodo).

Il server Chef rappresenta il punto di gestione e per esso sono disponibili due opzioni: una soluzione in hosting e una soluzione in locale. Si utilizzerà una soluzione in hosting.

Il client Chef (nodo) rappresenta l'agente che risiede nei server gestiti.

La workstation Chef rappresenta la workstation amministrativa in cui vengono creati i criteri e si eseguono i comandi di gestione. Dalla Chef Workstation è possibile eseguire il comando **knife** per gestire l'infrastruttura.

I "cookbook" e i "recipe" costituiscono invece i criteri effettivi che vengono definiti e applicati ai server.

## <a name="preparing-the-workstation"></a>Predisposizione della workstation
In primo luogo, è necessario predisporre la workstation. Nel seguente esempio viene utilizzata una workstation Windows standard. È necessario creare una directory per archiviare i file di configurazione e i cookbook.

Creare innanzitutto una directory denominata C:\chef.

Creare quindi una seconda directory denominata c:\chef\cookbooks.

A questo punto, è necessario scaricare il file di impostazioni di Azure affinché Chef possa comunicare con la sottoscrizione Azure.

<!--Download your publish settings from [here](https://manage.windowsazure.com/publishsettings/).-->
Scaricare le impostazioni di pubblicazione tramite il comando di Azure PowerShell [Get-AzurePublishSettingsFile](https://docs.microsoft.com/en-us/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0). 

Salvare il file delle impostazioni di pubblicazione in C:\chef.

## <a name="creating-a-managed-chef-account"></a>Creazione di un account Chef gestito
Iscriversi per ottenere un account Chef ospitato [qui](https://manage.chef.io/signup)

Durante il processo di iscrizione, verrà richiesto di creare una nuova organizzazione.

![][3]

Una volta creata l'organizzazione, scaricare lo Starter Kit.

![][4]

> [!NOTE]
> Se viene visualizzato un messaggio di avviso che indica che le chiavi verranno reimpostate, è comunque possibile proseguire, in quanto non è stata ancora configurata alcuna infrastruttura.
> 
> 

Il file ZIP dello Starter Kit contiene i file di configurazione e le chiavi dell'organizzazione.

## <a name="configuring-the-chef-workstation"></a>Configurazione della workstation Chef
Estrarre il contenuto del file chef-starter.zip in C:\chef.

Copiare tutti i file presenti nella directory chef-starter\chef-repo\.chef to your c:\chef.

La directory avrà ora un aspetto analogo al seguente esempio:

![][5]

Dovrebbero essere presenti quattro file, incluso il file di pubblicazione di Azure nella cartella c:\chef.

I file PEM contengono le chiavi private dell'organizzazione e amministrative per la comunicazione, mentre il file knife.rb contiene la configurazione Knife. È necessario modificare il file knife.rb.

Aprire il file in un editor di testo e modificare la voce "cookbook_path" rimuovendo i caratteri /../ dal percorso. L'aspetto della riga dovrebbe essere simile al seguente:

    cookbook_path  ["#{current_dir}/cookbooks"]

Aggiungere inoltre la seguente riga, in cui è necessario specificare il nome del file delle impostazioni di pubblicazione di Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Il file knife.rb dovrebbe avere un aspetto simile al seguente esempio:

![][6]

Queste righe sono necessarie per garantire la presenza di riferimenti Knife nella directory di cookbook c:\chef\cookbooks e per usare il file delle impostazioni di pubblicazione di Azure durante le operazioni con Azure.

## <a name="installing-the-chef-development-kit"></a>Installazione di Chef Development Kit
A questo punto, [scaricare e installare](http://downloads.getchef.com/chef-dk/windows) il ChefDK (Chef Development Kit) per configurare la workstation Chef.

![][7]

Installare nel percorso predefinito c:\opscode. L'installazione richiederà all'incirca 10 minuti.

Verificare che la variabile PATH contenga voci relative ai percorsi C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se questi percorsi non sono presenti, assicurarsi di aggiungerli.

*CONSIDERARE CHE L'ORDINE DEL PERCORSO È IMPORTANTE.* Se i percorsi opscode non sono presenti nell'ordine corretto, si verificheranno dei problemi.

Prima di continuare, riavviare la workstation.

Il passaggio successivo prevede l'installazione dell'estensione Knife di Azure, che fornisce il "plug-in Azure" a Knife.

Eseguire il comando indicato di seguito.

    chef gem install knife-azure ––pre

> [!NOTE]
> L'argomento "-pre" assicura che si riceverà la versione RC più recente del plug-in Azure di Knife, che fornisce l'accesso al set di API più recente.
> 
> 

È probabile che durante l'installazione verranno installate anche diverse dipendenze.

![][8]

Per assicurarsi che tutto sia configurato correttamente, eseguire il seguente comando:

    knife azure image list

Se tutto è stato configurato correttamente, verrà visualizzato un elenco delle immagini di Azure disponibili.

A questo punto La workstation è impostata.

## <a name="creating-a-cookbook"></a>Creazione di un cookbook
Chef usa i cookbook per definire i set di comandi che si desidera eseguire nel client gestito. La creazione di un cookbook è molto semplice. A tale scopo viene usato il comando **chef generate cookbook** per generare il modello di cookbook. In questo esempio il cookbook verrà denominato webserver, in quanto si desidera creare un criterio che distribuisca automaticamente IIS.

Nella directory C:\Chef eseguire il seguente comando.

    chef generate cookbook webserver

Verrà generato un set di file nella directory C:\Chef\cookbooks\webserver. A questo punto è necessario definire il set di comandi che il client Chef dovrà eseguire nella macchina virtuale gestita.

I comandi vengono archiviati nel file default.rb. In questo file verrà definito un set di comandi che consente di installare IIS, avviare IIS e copiare un file di modello nella cartella wwwroot.

Modificare il file C:\chef\cookbooks\webserver\recipes\default.rb e aggiungere le seguenti righe:

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Al termine dell'operazione, salvare il file.

## <a name="creating-a-template"></a>Creazione di un modello
Come accennato in precedenza, è necessario generare un file di modello che verrà usato come pagina Default.html.

Eseguire il seguente comando per generare il modello.

    chef generate template webserver Default.htm

Selezionare ora il file C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Modificare il file aggiungendo un semplice codice HTML, ad esempio alcune parole di benvenuto, e salvare il file.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Caricare il cookbook nel server Chef
In questo passaggio il cookbook creato nel computer locale verrà copiato e caricato nel server Chef di hosting. Una volta caricato, il cookbook verrà visualizzato nella scheda dei **Criteri** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuzione di una macchina virtuale con il comando Knife Azure
Questo passaggio descrive come distribuire una macchina virtuale di Azure e applicare il cookbook "Webserver", che installerà il servizio Web IIS e la pagina Web predefinita.

Per eseguire questa operazione, utilizzare il comando **knife azure server create** .

Un esempio del comando è visualizzato qui di seguito.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

La funzione dei parametri è facilmente comprensibile. Sostituire le variabili desiderate ed eseguire il comando.

> [!NOTE]
> Nella riga di comando sono state automatizzate anche le regole di filtro per la rete degli endpoint mediante il parametro –tcp-endpoints. Sono state aperte le porte 80 e 3389 per fornire l'accesso per la pagina Web e la sessione RDP.
> 
> 

Una volta eseguito il comando, passare al portale di Azure, dove il computer inizia già a eseguire il provisioning.

![][13]

Di seguito viene visualizzato il prompt dei comandi.

![][10]

Una volta completata la distribuzione, dovrebbe essere possibile connettersi al servizio Web attraverso la porta 80, che è stata aperta quando è stato eseguito il provisioning della macchina virtuale con il comando knife azure. Dal momento che questa macchina virtuale è l'unica macchina virtuale presente nel servizio cloud di questo esempio, verrà connessa con l'URL del servizio cloud.

![][11]

In questo esempio è stata impiegata una certa dose di creatività nell'uso del codice HTML.

Tenere anche presente che è anche possibile connettersi tramite una sessione RDP dal Portale di Azure attraverso la porta 3389.

Si spera che questa guida sia stata utile. Ora è possibile avviare l'infrastruttura come percorso di codice con Azure.

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->

