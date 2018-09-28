---
title: Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf
description: Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990706"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf

Monitoraggio di Azure consente di raccogliere metriche personalizzate tramite la telemetria applicazioni, un agente in esecuzione nelle risorse di Azure o anche sistemi di monitoraggio outside-in e di inviarli direttamente al Monitoraggio di Azure. Questo articolo include le istruzioni su come distribuire l'agente [InfluxData](https://www.influxdata.com/) Telegraf in una VM Linux in Azure e configurare l'agente per la pubblicazione delle metriche in Monitoraggio di Azure. 

## <a name="influxdata-telegraf-agent"></a>Agente InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) è un agente basato su plug-in che consente la raccolta delle metriche di oltre 150 origini diverse. A seconda dei carichi di lavoro in esecuzione nella VM (ad esempio, MySQL, NGINX, Apache e così via), è possibile configurare l'agente in modo per l'uso plug-in di input specializzati per raccogliere le metriche. Configurare i plug-in di output e consentire all'agente la scrittura nelle destinazioni desiderate. L'agente Telegraf è integrato direttamente nell'API REST di metriche personalizzate di Monitoraggio di Azure e supporta un "plug-in di output di Monitoraggio di Azure". In questo modo l'agente può raccogliere metriche specifiche del carico di lavoro nella VM Linux e inviarle come metriche personalizzate a Monitoraggio di Azure. 

 ![Panoramica dell'agente Telegraf](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Inviare metriche personalizzate 

Ai fini di questa esercitazione, si distribuirà una VM Linux che esegue il sistema operativo Ubuntu 16.04 LTS. L'agente Telegraf è supportato per la maggior parte dei sistemi operativi Linux. Insieme ai file binari di Linux estratti, nel portale di download di InfluxData sono disponibili pacchetti Debian e RPM. Vedere questa guida all'installazione per opzioni e istruzioni di installazione aggiuntive di Telegraf. 

Eseguire l'accesso al [portale di Azure](https://portal.azure.com)

Per creare una nuova VM Linux: 

1. Fare clic sull'opzione **Crea una risorsa** nel riquadro di spostamento a sinistra. 
1. Cercare *Macchina virtuale*  
1. Selezionare *Ubuntu 16.04 LTS* e fare clic su **Crea** 
1. Specificare un nome per la VM, ad esempio *MacchinaVirtualeTelegraf*.  
1. Lasciare il tipo di disco **SSD**, quindi specificare un **nome utente**, ad esempio *utenteazure*. 
1. Per *Tipo di autenticazione* selezionare **Password** e digitare una password che verrà usata in un secondo momento per la connessione SSH a questa VM. 
1. Scegliere **Crea nuovo gruppo di risorse** e specificare un nome, ad esempio *GruppoRisorse*.  Scegliere la posizione desiderata e selezionare **OK**. 

     ![Creare una VM Ubuntu](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Selezionare una dimensione per la VM. È possibile filtrare, ad esempio, in base a tipo di calcolo oppure a tipo di disco. 

     ![Panoramica delle dimensioni della macchina virtuale dell'agente Telegraf](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. Nella pagina **Impostazioni**, in  **Rete** > **Gruppo di sicurezza di rete** > **Selezionare le porte in ingresso pubbliche**, selezionare *HTTP* e *SSH (22)*. Accettare tutte le impostazioni predefinite e selezionare **OK**. 

1. Nella pagina di riepilogo selezionare Crea per avviare la distribuzione della macchina virtuale. 

1. La macchina virtuale viene aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale. 

1. Nel pannello della VM passare alla scheda **Identità** e assicurarsi che nella VM sia *attivata* un'identità assegnata dal sistema. 
 
![fillin](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Connettersi alla VM 

Creare una connessione SSH con la macchina virtuale. Selezionare il pulsante Connetti nella pagina di panoramica per la macchina virtuale. 

![fillin](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

Nella pagina Connetti a macchina virtuale mantenere le opzioni predefinite per la connessione con nome DNS sulla porta 22. In Accedi con l'account locale della macchina virtuale viene visualizzato un comando di connessione. Fare clic sul pulsante per copiare il comando. Di seguito è mostrato un esempio di comando di connessione SSH: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Incollare il comando di connessione SSH in una shell, ad esempio Azure Cloud Shell o Bash in Ubuntu in Windows, oppure usare un client SSH qualsiasi per creare la connessione. 

## <a name="install-and-configure-telegraf"></a>Installare e configurare Telegraf 

Per installare il pacchetto Debian Telegraf nella VM, eseguire i comandi seguenti dalla sessione SSH: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Il file di configurazione di Telegraf definisce le operazioni Telegraf. Per impostazione predefinita, un file di configurazione di esempio viene installato nel percorso "/etc/telegraf/telegraf.conf". Il file di configurazione di esempio elenca tutti i plug-in di input e output possibili. Si creerà tuttavia un file di configurazione personalizzato, che verrà usato dall'agente eseguendo i comandi seguenti 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> Il codice precedente consente solo due plug-in di input, "cpu" e "mem", ma è possibile aggiungere altri plug-in di input (Docker, NGINX, MySQL e così via) a seconda del carico di lavoro in esecuzione nel computer. Un elenco completo dei plug-in di input è disponibile qui. 

Infine, affinché l'agente usi la nuova configurazione, vengono forzati l'arresto e l'avvio dell'agente eseguendo i comandi seguenti 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
A questo punto l'agente raccoglierà le metriche da ogni plug-in di input specificato e le invierà a Monitoraggio di Azure. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Tracciare le metriche Telegraf nel portale di Azure 

1. Aprire il [portale di Azure](https://portal.azure.com) 

1. Passare alla nuova scheda Monitoraggio e selezionare **Metriche**.  
     ![fillin](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Selezionare la macchina virtuale nel selettore di risorse

     ![fillin](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Selezionare lo spazio dei nomi *Telegraf/CPU* e la metrica *usage_system*. È possibile scegliere di filtrare in base alle dimensioni in questa metrica o dividerle.  

     ![fillin](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configurazione aggiuntiva 

La procedura dettagliata precedente fornisce informazioni su come configurare l'agente Telegraf per la raccolta delle metriche da alcuni plug-in di input di base. L'agente Telegraf supporta più di 150 plug-in di input con alcune opzioni di configurazione aggiuntive. InfluxData ha pubblicato un [elenco di plug-in supportati](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) e le istruzioni su [come configurarli](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Inoltre, questa procedura dettagliata ha consentito di usare l'agente Telegraf per generare metriche sulla macchina virtuale in cui è distribuito l'agente. L'agente Telegraf può essere usato anche come agente di raccolta e servizio di inoltro di metriche per altre risorse. Per informazioni su come configurare l'agente per la generazione di metriche per altre risorse di Azure, vedere [Azure Monitor Custom Metric Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md) (Output di metriche personalizzate di Monitoraggio di Azure per Telegraf).  

## <a name="clean-up-resources"></a>Pulire le risorse 

Quando non serve più, è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare Elimina e quindi confermare il nome del gruppo di risorse da eliminare. 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).


