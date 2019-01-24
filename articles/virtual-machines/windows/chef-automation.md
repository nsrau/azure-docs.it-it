---
title: Distribuzione delle macchine virtuali di Azure con Chef | Microsoft Docs
description: Imparare a utilizzare Chef per effettuare la distribuzione automatizzata della macchina virtuale e la configurazione in Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: d57a7baafc533aee52ec8012d410d5f25b510b60
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359953"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automazione della distribuzione delle macchine virtuali di Azure con Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef rappresenta uno strumento molto utile che fornisce soluzioni automatizzate e configurazioni di stato personalizzate.

Con la versione più recente dell'API cloud, Chef offre una perfetta integrazione con Azure, consentendo di effettuare il provisioning e distribuire gli stati delle configurazioni attraverso un unico comando.

Questo articolo illustra come configurare l'ambiente Chef per il provisioning delle macchine virtuali di Azure e descrive la procedura dettagliata per creare un criterio o una guida di riferimento dettagliata e distribuirla successivamente in una macchina virtuale di Azure.

## <a name="chef-basics"></a>Nozioni di base su Chef
Prima di iniziare, [rivedere i concetti di base di Chef](http://www.chef.io/chef).

Il seguente diagramma illustra l'architettura di alto livello di Chef.

![][2]

Chef ha tre componenti principali dell'architettura: server Chef, client Chef (nodo) e workstation Chef.

Il server Chef rappresenta il punto di gestione per cui sono disponibili due opzioni: una soluzione in hosting e una soluzione locale.

Il client Chef (nodo) rappresenta l'agente che risiede nei server gestiti.

La workstation Chef rappresenta il nome sia della workstation di amministrazione, in cui si creano i criteri e si eseguono i comandi gestione, che del pacchetto software di strumenti Chef.

In genere, con _la workstation_ si intende la posizione in cui si eseguono le azioni e con _workstation Chef_ si intende il pacchetto software.
Il comando knife, ad esempio, verrà scaricato come parte della _workstation Chef_, ma i comandi knife vengono eseguiti dalla _workstation_ per gestire l'infrastruttura.

Chef usa anche i concetti di "guida di riferimento dettagliata" e di "recipe", che sono i criteri definiti e applicati ai server.

## <a name="preparing-your-workstation"></a>Predisposizione della workstation

In primo luogo, preparare la workstation creando una directory in cui archiviare file di configurazione e guide di riferimento dettagliate di Chef.

Creare una directory denominata C:\chef.

Scaricare le [impostazioni di pubblicazione](https://docs.microsoft.com/en-us/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information) di Azure PowerShell.

## <a name="setup-chef-server"></a>Configurare il server Chef

Questa guida presuppone che si effettuerà l'iscrizione a Chef ospitato.

Se non si usa già un server Chef, è possibile:

* Iscriversi a [Chef ospitato](https://manage.chef.io/signup), che è il modo più rapido per iniziare a usare Chef.
* Installare un server Chef autonomo nel computer basato su Linux, seguendo le [istruzioni di installazione](https://docs.chef.io/install_server.html) della [documentazione di Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Creazione di un account Chef ospitato

Iscriversi per ottenere un account Chef ospitato [qui](https://manage.chef.io/signup).

Durante il processo di iscrizione, verrà richiesto di creare una nuova organizzazione.

![][3]

Una volta creata l'organizzazione, scaricare lo Starter Kit.

![][4]

> [!NOTE]
> Se viene visualizzato un messaggio di avviso che indica che le chiavi verranno reimpostate, è consigliabile proseguire poiché non è ancora configurata alcuna infrastruttura.
>

Il file ZIP dello starter kit contiene i file di configurazione dell'organizzazione e la chiave utente nella directory `.chef`.

`organization-validator.pem` deve essere scaricato separatamente, perché è una chiave privata e le chiavi private non devono essere archiviate nel server Chef. Passare a [Chef Manage](https://manage.chef.io/) (Gestione Chef) e selezionare "Reset Validation Key" (Reimposta chiave di convalida), per ottenere un file da scaricare separatamente. Salvare il file in c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurazione della workstation Chef

Estrarre il contenuto del file chef-starter.zip in c:\chef.

Copiare tutti i file presenti nella directory chef-starter\chef-repo\.chef to your c:\chef.

Copiare il file `organization-validator.pem` in c:\chef, se è stato salvato in c:\Download

La directory avrà ora un aspetto analogo al seguente esempio:

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Si avranno ora cinque file e quattro directory (inclusa la directory vuota chef-repo) nella radice di c:\chef.

### <a name="edit-kniferb"></a>Modificare knife.rb

I file PEM contengono le chiavi private dell'organizzazione e amministrative per la comunicazione e il file knife.rb contiene la configurazione Knife. È necessario modificare il file knife.rb.

Aprire il file knife.rb nell'editor preferito. Il file non modificato sarà simile al seguente:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Aggiungere le informazioni seguenti al file knife.rb:

validation_client_name   "myorg-validator" validation_key           ""#{current_dir}/myorg.pem"

Aggiungere inoltre la seguente riga, in cui è necessario specificare il nome del file delle impostazioni di pubblicazione di Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Modificare "cookbook_path" rimuovendo i caratteri /../ dal percorso. La riga avrà l'aspetto seguente:

    cookbook_path  ["#{current_dir}/cookbooks"]

Queste righe sono necessarie per garantire la presenza di riferimenti Knife nella directory di cookbook c:\chef\cookbooks e per usare il file delle impostazioni di pubblicazione di Azure durante le operazioni con Azure.

Il file knife.rb dovrebbe avere un aspetto simile all'esempio seguente:

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Installare la workstation Chef

[Scaricare e installare](https://downloads.chef.io/chef-workstation/) la workstation Chef.
Installare la workstation Chef nel percorso predefinito. Questa installazione potrebbe richiedere alcuni minuti.

Nel desktop verrà visualizzato "CW PowerShell", un ambiente caricato con lo strumento, necessario per interagire con i prodotti Chef. CW PowerShell rende disponibili nuovi comandi ad hoc, ad esempio `chef-run`, oltre ai tradizionali comandi dell'interfaccia della riga di comando di Chef, ad esempio `chef`. Per conoscere la versione installata della workstation Chef e degli strumenti Chef, eseguire `chef -v`. È anche possibile controllare la versione della workstation selezionando "About Chef Workstation" (Informazioni sulla workstation Chef) nell'app della workstation Chef.

`chef --version` restituirà un output simile al seguente:

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
```

> [!NOTE]
> L'ordine del percorso è importante.  Se i percorsi opscode non sono presenti nell'ordine corretto, si verificheranno dei problemi.
>

Prima di continuare, riavviare la workstation.

### <a name="install-knife-azure"></a>Installare Knife Azure

Questa esercitazione presuppone che si usi Azure Resource Manager per interagire con la macchina virtuale.

Installare l'estensione Knife Azure. che fornisce il "plug-in Azure" a Knife.

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

A questo punto la workstation è configurata.

## <a name="creating-a-cookbook"></a>Creazione di un cookbook

Chef usa i cookbook per definire i set di comandi che si desidera eseguire nel client gestito. La creazione di una guida di riferimento dettagliata è molto semplice. A tale scopo viene usato il comando **chef generate cookbook** per generare il modello. Questa guida di riferimento dettagliata è relativa a un server Web che distribuisce automaticamente IIS.

Nella directory C:\Chef eseguire il seguente comando.

    chef generate cookbook webserver

Questo comando genererà un set di file nella directory C:\Chef\cookbooks\webserver. Definire quindi il set di comandi che il client Chef dovrà eseguire nella macchina virtuale gestita.

I comandi vengono archiviati nel file default.rb. In questo file definire un set di comandi che consente di installare IIS, avviare IIS e copiare un file di modello nella cartella wwwroot.

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
In questo passaggio si genererà un file di modello da usare come pagina default.html.

Eseguire il comando seguente per generare il modello:

    chef generate template webserver Default.htm

Passare al file `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Modificare il file aggiungendo un semplice codice HTML, ad esempio alcune parole di benvenuto, e salvare il file.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Caricare il cookbook nel server Chef
In questo passaggio si crea una copia della guida di riferimento dettagliata creata nel computer locale che verrà caricata nel server Chef ospitato. Dopo il caricamento, la guida di riferimento dettagliata viene visualizzata nella scheda **Policy** (Criteri).

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuzione di una macchina virtuale con il comando Knife Azure
Distribuire una macchina virtuale di Azure e applicare la guida di riferimento dettagliata "Webserver", che installa il servizio Web IIS e la pagina Web predefinita.

Per eseguire questa operazione, utilizzare il comando **knife azure server create** .

Un esempio del comando è visualizzato qui di seguito.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

La funzione dei parametri è facilmente comprensibile. Sostituire le variabili desiderate ed eseguire il comando.

> [!NOTE]
> Nella riga di comando sono state automatizzate anche le regole di filtro per la rete degli endpoint mediante il parametro –tcp-endpoints. Sono state aperte le porte 80 e 3389 per fornire l'accesso per la pagina Web e la sessione RDP.
>
>

Dopo aver eseguito il comando, passare al portale di Azure, dove il computer inizia già a effettuare il provisioning.

![][13]

Di seguito viene visualizzato il prompt dei comandi.

![][10]

Al termine della distribuzione, dovrebbe essere possibile connettersi al servizio Web attraverso la porta 80, che è stata aperta quando è stato effettuato il provisioning della macchina virtuale con il comando knife azure. Dal momento che questa macchina virtuale è l'unica presente in questo servizio cloud, connettersi a essa con l'URL del servizio cloud.

![][11]

Questo esempio usa un codice HTML creativo.

Tenere presente che è anche possibile connettersi tramite una sessione RDP dal Portale di Azure attraverso la porta 3389.

Grazie! Ora è possibile avviare l'infrastruttura come percorso di codice con Azure.

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
