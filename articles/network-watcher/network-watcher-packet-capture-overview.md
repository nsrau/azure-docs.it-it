---
title: Introduzione all&quot;acquisizione di pacchetti in Azure Network Watcher | Documentazione Microsoft
description: "Questa pagina fornisce una panoramica delle funzionalità di acquisizione di pacchetti di Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: dde3d1b1b0708d49ac713e18242c3376d301c675
ms.lasthandoff: 04/10/2017


---

# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Introduzione all'acquisizione di pacchetti di variabili in Azure Network Watcher

Il servizio di acquisizione di pacchetti di variabili di Network Watcher consente di creare sessioni di acquisizione di pacchetti per registrare il traffico da e verso una macchina virtuale. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri usi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora.

L'acquisizione di pacchetti è un'estensione macchina virtuale che viene avviata da remoto tramite Network Watcher. Questa funzionalità evita di dover eseguire manualmente questa operazione sulla macchina virtuale desiderata, consentendo un notevole risparmio di tempo. L'acquisizione di pacchetti può essere attivata tramite il portale, PowerShell, l'interfaccia della riga di comando o l'API REST. Un esempio di modalità di attivazione è rappresentata dagli avvisi della macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito il traffico che si desidera monitorare. I filtri si basano su informazioni a 5 tuple (protocollo, indirizzo IP locale, indirizzo IP remoto, porta locale e porta remota). I dati acquisiti vengono archiviati nel disco locale o in un BLOB di archiviazione. È previsto un limite di 10 sessioni di acquisizione dei pacchetti per area per sottoscrizione. Questo limite si applica solo alle sessioni e non si applica ai file di acquisizione di pacchetti salvati in locale nella VM o in un account di archiviazione.

> [!IMPORTANT]
> L'acquisizione di pacchetti richiede un'estensione macchina virtuale `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/windows/extensions-nwa.md) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/linux/extensions-nwa.md).

Per limitare le informazioni acquisite alle sole informazioni desiderate, sono disponibili le opzioni seguenti per una sessione di acquisizione di pacchetti:

**Acquisisci configurazione**

|Proprietà|Descrizione|
|---|---|
|**Numero massimo di byte per pacchetto (byte)** | Numero di byte acquisiti da ogni pacchetto. Se lasciato vuoto, vengono acquisiti tutti i byte. Numero di byte acquisiti da ogni pacchetto. Se lasciato vuoto, vengono acquisiti tutti i byte. Se è necessaria solo l'intestazione IPv4, indicare qui 60. |
|**Numero massimo di byte per sessione (byte)** | Numero totale di byte acquisiti, quando viene raggiunto il valore di termine della sessione.|
|**Limite di tempo (secondi)** | Imposta un vincolo di tempo per la sessione di acquisizione del pacchetto. Il valore predefinito è 18000 secondi o 5 ore.|

**Filtro (facoltativo)**

|Proprietà|Descrizione|
|---|---|
|**Protocollo** | Protocollo per filtrare l'acquisizione di pacchetti. I valori disponibili sono TCP, UDP e Tutti.|
|**Indirizzo IP locale** | Questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui l'indirizzo IP locale corrisponde a questo valore del filtro.|
|**Porta locale** | Questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui la porta locale corrisponde a questo valore del filtro.|
|**Indirizzo IP remoto** | Questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui l'indirizzo IP remoto corrisponde a questo valore del filtro.|
|**Porta remota** | Questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui la porta remota corrisponde a questo valore del filtro.|

### <a name="next-steps"></a>Passaggi successivi

Le informazioni su come gestire le acquisizioni di pacchetti tramite il portale sono disponibili in [Manage packet capture in the Azure portal](network-watcher-packet-capture-manage-portal.md) (Gestire l'acquisizione di pacchetti nel portale di Azure) o con PowerShell in [Manage Packet Capture with PowerShell](network-watcher-packet-capture-manage-powershell.md) (Gestire l'acquisizione di pacchetti con PowerShell).

Le informazioni su come creare acquisizioni di pacchetti proattive tramite gli avvisi della macchina virtuale sono disponibili in [Create an alert triggered packet capture](network-watcher-alert-triggered-packet-capture.md) (Creare un'acquisizione di pacchetti attivata da un avviso)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png














