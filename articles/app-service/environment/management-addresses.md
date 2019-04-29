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
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766048"
---
# <a name="app-service-environment-management-addresses"></a>Indirizzi di gestione dell'Ambiente del servizio app

L'ambiente del servizio App (ASE) è una distribuzione a singolo tenant del servizio App di Azure che esegue nella rete virtuale di Azure (VNet).  Mentre l'ambiente del servizio App viene eseguita in una rete virtuale, deve ancora essere accessibile da un numero di indirizzi IP dedicati che sono usati dal servizio App di Azure per gestire il servizio.  Nel caso di un ambiente del servizio App, il traffico di gestione attraversa la rete controllata dall'utente. Se questo traffico è bloccato o indirizzato in modo errato l'ambiente ASE verrà sospeso. Per informazioni dettagliate sulle dipendenze di rete dell'ambiente del servizio app, leggere [Considerazioni sulla rete e ambiente del servizio app][networking]. Per informazioni generali sull'ambiente ASE, è possibile iniziare con [Introduzione agli ambienti del servizio app][intro].

Tutti gli ambienti del servizio app hanno un indirizzo VIP pubblico a cui arriva il traffico di gestione. Il traffico di gestione in ingresso da questi indirizzi proviene dalle porte 454 e 455 sull'indirizzo VIP pubblico dell'ambiente ASE. Questo documento elenca gli indirizzi origine del servizio app per il traffico di gestione all'ambiente ASE. Questi indirizzi sono anche nel Tag IP del servizio denominato AppServiceManagement.

Gli indirizzi indicati di seguito possono essere configurati in una tabella di route per evitare problemi di routing asimmetrici con il traffico di gestione. Le route intervenire sul traffico a livello di IP e non hanno consapevolezza della direzione del traffico o che il traffico è una parte di un messaggio di risposta TCP. Se è diverso dall'indirizzo per che è stato inviato l'indirizzo di risposta per una richiesta TCP, è necessario un problema di routing asimmetrico. Per evitare problemi relativi al routing asimmetrici con il traffico di gestione di ambiente del servizio App, è necessario assicurarsi che le risposte vengono inviate nuovamente dallo stesso indirizzo che a cui sono stati inviati. Per informazioni dettagliate su come configurare l'ambiente ASE per operare in un ambiente in cui il traffico in uscita viene inviato in locale, leggere [Configurare l'ambiente del servizio app con il tunneling forzato][forcedtunnel].

## <a name="list-of-management-addresses"></a>Elenco di indirizzi di gestione ##

| Region | Indirizzi |
|--------|-----------|
| Tutte le aree pubbliche | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure per enti pubblici | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configurazione di un gruppo di sicurezza di rete

Con i gruppi di sicurezza di rete, non occorre preoccuparsi di singoli indirizzi o mantenere la propria configurazione. È un tag di servizio IP denominato AppServiceManagement che viene mantenuto aggiornato con tutti gli indirizzi. Per usare questo tag di servizio IP nel gruppo di sicurezza di rete, passare al portale, aprire l'interfaccia utente gruppi di sicurezza di rete e selezionare le regole di sicurezza in ingresso. Se si dispone di una regola di pre-esistente per il traffico di gestione in ingresso, è necessario modificarlo. Se il gruppo di sicurezza di rete non è stato creato con l'ambiente del servizio app o se è completamente nuovo, selezionare **Aggiungi**. Nell'elenco a discesa Origine selezionare **Service Tag** (Tag di servizio).  Sotto il tag di servizio di origine, selezionare **AppServiceManagement**. Impostare gli intervalli di porte di origine su \*, la destinazione su **Qualsiasi**, gli intervalli di porte di destinazione su **454-455**, il protocollo su **TCP** e l'azione su **Consenti** . Se si sta creando la regola, quindi è necessario impostare la priorità. 

![creazione di un gruppo di sicurezza di rete con il tag di servizio][1]

## <a name="configuring-a-route-table"></a>Configurazione di una tabella di route

Gli indirizzi di gestione possono essere inseriti in una tabella di route con un hop successivo di Internet per assicurarsi che tutto il traffico di gestione in ingresso sia in grado di tornare indietro attraverso lo stesso percorso. Queste route sono necessarie quando si configura il tunneling forzato. Per creare la tabella di route, è possibile usare il portale, PowerShell o l'interfaccia della riga di comando di Azure.  Di seguito sono elencati i comandi per creare una tabella di route usando l'interfaccia della riga di comando di Azure da un prompt di PowerShell. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

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
