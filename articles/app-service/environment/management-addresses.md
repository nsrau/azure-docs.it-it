---
title: Indirizzi di gestione dell'ambiente del servizio app - Azure
description: Elenca gli indirizzi di gestione usati per inviare comandi a un Ambiente del servizio app
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/25/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cc08d98c79e6c0e79f5a77111ee75e1ba491a4ed
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069159"
---
# <a name="app-service-environment-management-addresses"></a>Indirizzi di gestione dell'Ambiente del servizio app

Il ambiente del servizio app (ASE) è una distribuzione a tenant singolo del servizio app Azure eseguito nella rete virtuale di Azure (VNet).  Mentre l'ambiente del servizio app viene eseguito nella VNet, è necessario che sia ancora accessibile da diversi indirizzi IP dedicati utilizzati dal servizio app Azure per gestire il servizio.  Nel caso di un ambiente del servizio app, il traffico di gestione attraversa la rete controllata dall'utente. Se questo traffico è bloccato o indirizzato in modo errato l'ambiente ASE verrà sospeso. Per informazioni dettagliate sulle dipendenze di rete dell'ambiente del servizio app, vedere [considerazioni sulla rete e ambiente del servizio app][networking]. Per informazioni generali sull'ambiente del servizio app, è possibile iniziare con [Introduzione al ambiente del servizio app][intro].

Tutti gli ambienti del servizio app hanno un indirizzo VIP pubblico a cui arriva il traffico di gestione. Il traffico di gestione in ingresso da questi indirizzi proviene dalle porte 454 e 455 sull'indirizzo VIP pubblico dell'ambiente ASE. Questo documento elenca gli indirizzi origine del servizio app per il traffico di gestione all'ambiente ASE. Questi indirizzi si trovano anche nel tag del servizio IP denominato AppServiceManagement.

Gli indirizzi indicati di seguito possono essere configurati in una tabella di route per evitare problemi di routing asimmetrica con il traffico di gestione. Le route agiscono sul traffico a livello di IP e non sono consapevoli della direzione del traffico o che il traffico fa parte di un messaggio di risposta TCP. Se l'indirizzo di risposta per una richiesta TCP è diverso dall'indirizzo a cui è stato inviato, si è verificato un problema di routing asimmetrico. Per evitare problemi di routing asimmetrica con il traffico di gestione dell'ambiente del servizio app, è necessario assicurarsi che le risposte vengano restituite dallo stesso indirizzo a cui sono state inviate. Per informazioni dettagliate su come configurare l'ambiente del servizio app in modo che operi in un ambiente in cui il traffico in uscita viene inviato in locale, vedere [configurare l'][forcedtunnel] ambiente del servizio app con il tunneling forzato

## <a name="list-of-management-addresses"></a>Elenco di indirizzi di gestione ##

| Region | Indirizzi |
|--------|-----------|
| Tutte le aree pubbliche | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.127.117, 13.77.50.128, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure per enti pubblici | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configurazione di un gruppo di sicurezza di rete

Con i gruppi di sicurezza di rete non è necessario preoccuparsi dei singoli indirizzi o mantenere la propria configurazione. È disponibile un tag di servizio IP denominato AppServiceManagement che viene mantenuto aggiornato con tutti gli indirizzi. Per usare questo tag del servizio IP in NSG, passare al portale, aprire l'interfaccia utente dei gruppi di sicurezza di rete e selezionare regole di sicurezza in ingresso. Se si dispone di una regola preesistente per il traffico di gestione in ingresso, modificarla. Se il gruppo di sicurezza di rete non è stato creato con l'ambiente del servizio app o se è completamente nuovo, selezionare **Aggiungi**. Nell'elenco a discesa Origine selezionare **Service Tag** (Tag di servizio).  Sotto il tag del servizio di origine selezionare **AppServiceManagement**. Impostare gli intervalli di porte di origine su \*, la destinazione su **Qualsiasi**, gli intervalli di porte di destinazione su **454-455**, il protocollo su **TCP** e l'azione su **Consenti** . Se si sta effettuando la regola, è necessario impostare la priorità. 

![creazione di un gruppo di sicurezza di rete con il tag di servizio][1]

## <a name="configuring-a-route-table"></a>Configurazione di una tabella di route

Gli indirizzi di gestione possono essere inseriti in una tabella di route con un hop successivo di Internet per assicurarsi che tutto il traffico di gestione in ingresso sia in grado di tornare indietro attraverso lo stesso percorso. Queste route sono necessarie quando si configura il tunneling forzato. Per creare la tabella di route, è possibile usare il portale, PowerShell o l'interfaccia della riga di comando di Azure.  Di seguito sono elencati i comandi per creare una tabella di route usando l'interfaccia della riga di comando di Azure da un prompt di PowerShell. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.127.117", "13.77.50.128", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Dopo aver creato la tabella di route, è necessario impostarla nella subnet dell'ambiente del servizio app.  

## <a name="get-your-management-addresses-from-api"></a>Ottenere gli indirizzi di gestione dall'API ##

È possibile elencare gli indirizzi di gestione che corrispondono all'ambiente del servizio app con la chiamata API seguente.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

L'API restituisce un documento JSON che include tutti gli indirizzi in ingresso per l'ambiente del servizio app. L'elenco di indirizzi include gli indirizzi di gestione, l'indirizzo VIP usato dall'ambiente del servizio app e l'intervallo di indirizzi subnet dell'ambiente stesso.  

Per chiamare l'API con [armclient](https://github.com/projectkudu/ARMClient), usare i comandi seguenti sostituendo i segnaposto con l'ID sottoscrizione, il gruppo di risorse e il nome dell'ambiente del servizio app.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
