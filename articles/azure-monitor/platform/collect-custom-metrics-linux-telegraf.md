---
title: Raccogliere metriche personalizzate per la VM Linux con l'agente Telegraf InfluxData
description: Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 05cc1dcb2a6fa4e7790fa57cd2136d21d94b8a0b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200530"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf

Monitoraggio di Azure consente di raccogliere metriche personalizzate tramite i dati di telemetria delle applicazioni, un agente in esecuzione nelle risorse di Azure o anche sistemi di monitoraggio outside-in. Quindi è possibile inviarli direttamente a Monitoraggio di Azure. Questo articolo include le istruzioni su come distribuire l'agente [InfluxData](https://www.influxdata.com/) Telegraf in una VM Linux in Azure e configurare l'agente per la pubblicazione delle metriche in Monitoraggio di Azure. 

## <a name="influxdata-telegraf-agent"></a>Agente InfluxData Telegraf 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) è un agente basato su plug-in che consente la raccolta delle metriche di oltre 150 origini diverse. A seconda dei carichi di lavoro eseguiti sulla macchina virtuale è possibile configurare l'agente per sfruttare plug-in di input specializzati per raccogliere le metriche. Tra gli esempi vi sono Apache, MySQL e NGINX. Tramite i plug-in di output, l'agente può quindi scrivere nelle destinazioni scelte dall'utente. L'agente Telegraf è integrato direttamente nell'API REST di metriche personalizzate di Monitoraggio di Azure. e supporta un plug-in di output di Monitoraggio di Azure. In questo modo l'agente può raccogliere metriche specifiche del carico di lavoro nella VM Linux e inviarle come metriche personalizzate a Monitoraggio di Azure. 

 ![Panoramica dell'agente Telegraf](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Inviare metriche personalizzate 

Ai fini di questa esercitazione, si distribuirà una VM Linux che esegue il sistema operativo Ubuntu 16.04 LTS. L'agente Telegraf è supportato per la maggior parte dei sistemi operativi Linux. Insieme ai file binari di Linux estratti, nel [portale di download di InfluxData](https://portal.influxdata.com/downloads) sono disponibili pacchetti Debian e RPM. Vedere questa [guida all'installazione di Telegraf](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) per opzioni e istruzioni di installazione aggiuntive. 

Accedere al [portale di Azure](https://portal.azure.com).

Creare una nuova VM Linux: 

1. Selezionare l'opzione **Crea una risorsa** nel riquadro di spostamento a sinistra. 
1. Cercare **Macchina virtuale**.  
1. Selezionare **Ubuntu 16.04 LTS** e fare clic su **Crea**. 
1. Specificare un nome di macchina virtuale come **MyTelegrafVM**.  
1. Lasciare il tipo di disco come **SSD**. Specificare quindi un **nome utente**, ad esempio **azureuser**. 
1. In **tipo di autenticazione**selezionare **password**. Quindi immettere una password da usare in un secondo momento per la connessione SSH in questa macchina virtuale. 
1. Scegliere di **creare un nuovo gruppo di risorse**. Specificare quindi un nome, ad esempio **myResourceGroup**. Scegliere la **località**. Selezionare **OK**. 

    ![Creare una VM Ubuntu](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Selezionare una dimensione per la VM. È possibile filtrare in base a **Tipo di calcolo** oppure a **Tipo di disco**, ad esempio. 

    ![Panoramica delle dimensioni della macchina virtuale dell'agente Telegraf](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. Nella pagina **Impostazioni** del **gruppo di sicurezza** di **rete > **  > **selezionare porte in ingresso pubbliche**, selezionare **http** e **SSH (22)** . Accettare tutte le impostazioni predefinite e selezionare **OK**. 

1. Nella pagina di riepilogo selezionare **Crea** per avviare la distribuzione della macchina virtuale. 

1. La macchina virtuale viene aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale. 

1. Nel riquadro VM passare alla scheda Identity ( **identità** ). Assicurarsi che la macchina virtuale disponga di un'identità assegnata dal sistema impostata **su on**. 
 
    ![Anteprima dell'identità della VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Connettersi alla VM 

Creare una connessione SSH con la macchina virtuale. Selezionare il pulsante **Connetti** nella pagina di panoramica per la macchina virtuale. 

![Pagina di panoramica della VM Telegraf](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

Nella pagina **Connetti a macchina virtuale** mantenere le opzioni predefinite per la connessione con nome DNS sulla porta 22. In **login using VM local account**viene visualizzato un comando Connection. Fare clic sul pulsante per copiare il comando. Di seguito è mostrato un esempio di comando di connessione SSH: 

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
Il file di configurazione di Telegraf definisce le operazioni Telegraf. Per impostazione predefinita, un file di configurazione di esempio viene installato nel percorso **/etc/telegraf/telegraf.conf**. Il file di configurazione di esempio elenca tutti i plug-in di input e di output possibili. Verrà tuttavia creato un file di configurazione personalizzato che verrà usato dall'agente eseguendo i comandi seguenti: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Il codice precedente consente solo due plug-in di input: **cpu** e **mem**. È possibile aggiungere più plug-in di input, a seconda del carico di lavoro eseguito nel computer. Tra gli esempi vi sono Docker, MySQL e NGINX. Per un elenco completo dei plug-in di input vedere la sezione **Configurazione aggiuntiva**. 

Infine, affinché l'agente usi la nuova configurazione, vengono forzati l'arresto e l'avvio dell'agente eseguendo i comandi seguenti: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
A questo punto l'agente raccoglierà le metriche da ogni plug-in di input specificato e le invierà a Monitoraggio di Azure. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Tracciare le metriche Telegraf nel portale di Azure 

1. Aprire il [portale di Azure](https://portal.azure.com). 

1. Passare alla scheda nuovo **monitoraggio** . Selezionare quindi **metrica**.  

     ![Monitor - Metrica (anteprima)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Selezionare la macchina virtuale nel selettore di risorse.

     ![Grafico della metrica](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Selezionare lo spazio dei nomi **Telegraf/CPU** e la metrica **usage_system**. È possibile scegliere di filtrare in base alle dimensioni in questa metrica o dividerle.  

     ![Selezionare lo spazio dei nomi e la metrica](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Configurazione aggiuntiva 

Nella procedura dettagliata precedente vengono fornite informazioni su come configurare l'agente di Telegraf per raccogliere le metriche da alcuni plug-in di input di base. L'agente Telegraf dispone del supporto per oltre 150 plug-in di input, con alcuni supportano opzioni di configurazione aggiuntive. InfluxData ha pubblicato un [elenco di plug-in supportati](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) e le istruzioni su [come configurarli](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Inoltre, questa procedura dettagliata ha consentito di usare l'agente Telegraf per generare metriche sulla macchina virtuale in cui è distribuito l'agente. L'agente Telegraf può essere usato anche come agente di raccolta e servizio di inoltro di metriche per altre risorse. Per informazioni su come configurare l'agente per la generazione di metriche per altre risorse di Azure, vedere [Azure Monitor Custom Metric Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md) (Output di metriche personalizzate di Monitoraggio di Azure per Telegraf).  

## <a name="clean-up-resources"></a>Pulire le risorse 

Quando non servono più è possibile eliminare il gruppo di risorse, la macchina virtuale e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale e selezionare **Elimina**. Confermare quindi il nome del gruppo di risorse da eliminare. 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).



