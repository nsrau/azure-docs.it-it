---
title: 'Gestire le acquisizioni di pacchetti con Azure Network Watcher: portale di Azure | Microsoft Docs'
description: "Questa pagina illustra come gestire la funzionalità di acquisizione di pacchetti di Network Watcher usando il portale di Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d2a65104743d9497debdc85c134fd1a06114c514
ms.openlocfilehash: f7739d38822d9cb57bc47f1c33f48045e5e97406
ms.lasthandoff: 02/23/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gestire le acquisizioni di pacchetti con Azure Network Watcher usando il portale

> [!div class="op_single_selector"]
> - [Portale di Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [API REST](network-watcher-packet-capture-manage-rest.md)

Il servizio di acquisizione di pacchetti di Network Watcher consente di creare sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri utilizzi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione sul computer desiderato, consentendo un notevole risparmio di tempo.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

Questo articolo illustra le diverse attività di gestione attualmente disponibili per l'acquisizione di pacchetti.

- [**Avviare un'acquisizione di pacchetti**](#start-a-packet-capture)
- [**Interrompere un'acquisizione di pacchetti**](#stop-a-packet-capture)
- [**Eliminare un'acquisizione di pacchetti**](#delete-a-packet-capture)
- [**Scaricare un'acquisizione di pacchetti**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente disponga delle risorse seguenti:

- Un'istanza di Network Watcher nell'area in cui creare un'acquisizione di pacchetti.
- Una macchina virtuale con l'estensione di acquisizione di pacchetti abilitata.

> [!IMPORTANT]
> L'acquisizione di pacchetti richiede un'estensione macchina virtuale `AzureNetworkWatcherExtension`. Per installare l'estensione in una VM Windows, vedere [Estensione macchina virtuale agente Azure Network Watcher per Windows](../virtual-machines/virtual-machines-windows-extensions-nwa.md) e per una VM Linux VM vedere [Estensione macchina virtuale Azure Network Watcher Agent per Linux](../virtual-machines/virtual-machines-linux-extensions-nwa.md).

### <a name="packet-capture-agent-extension-through-the-portal"></a>Estensione agente di acquisizione di pacchetti tramite il portale

Per installare l'agente di VM per l'acquisizione di pacchetti tramite il portale, andare alla macchina virtuale, fare clic su **Estensioni** > **Aggiungi** e cercare **Network Watcher Agent for Windows** (Agente Network Watcher per Windows)

![Visualizzazione agente][agent]

## <a name="packet-capture-overview"></a>Panoramica dell'acquisizione di pacchetti

Andare al [portale di Azure](https://portal.azure.com) e fare clic su **Rete** > **Network Watcher** > **Acquisizione pacchetti**

La pagina della panoramica visualizza un elenco di tutte le acquisizioni di pacchetti esistenti indipendentemente dallo stato.

> [!NOTE]
> L'acquisizione di pacchetti richiede la connettività all'account di archiviazione tramite la porta 443.

![Schermata della panoramica di acquisizione di pacchetti][1]

## <a name="start-a-packet-capture"></a>Avviare un'acquisizione di pacchetti

Fare clic su **Aggiungi** per creare un'acquisizione di pacchetti.

Le proprietà che possono essere definite in un'acquisizione di pacchetti sono:

**Impostazioni principali**

- **Sottoscrizione**: questo valore è la sottoscrizione usata. Un'istanza di Network Watcher è necessaria in ogni sottoscrizione.
- **Gruppo di risorse**: gruppo di risorse della macchina virtuale di destinazione.
- **Macchina virtuale di destinazione**: macchina virtuale che esegue l'acquisizione di pacchetti
- **Nome acquisizione pacchetti**: questo valore è il nome dell'acquisizione di pacchetti.

**Acquisisci configurazione**

- **Archiviazione**: determina se l'acquisizione di pacchetti viene salvata in un'account di archiviazione.
- **File**: determina se un'acquisizione di pacchetti viene salvata in locale nella macchina virtuale.
- **Account di archiviazione**: account di archiviazione selezionato in cui salvare l'acquisizione di pacchetti. Il percorso predefinito è https://{nome account di archiviazione}.blob.core.windows.net/network-watcher-logs/subscriptions/{ID sottoscrizione}/resourcegroups/{nome gruppo di risorse}/providers/microsoft.compute/virtualmachines/{nome macchina virtuale}/{AA}/{MM}/{GG}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap. È abilitato solo se viene selezionato **Archiviazione**.
- **Percorso file locale**: percorso locale in una macchina virtuale in cui salvare l'acquisizione di pacchetti. È abilitato solo se viene selezionato **File**. È necessario specificare un percorso valido
- **Numero massimo di byte per pacchetto**: numero di byte acquisiti da ogni pacchetto. Se lasciato vuoto, vengono acquisiti tutti i byte.
- **Numero massimo di byte per sessione**: numero totale di byte acquisiti, dopo l'arresto dell'acquisizione di pacchetti.
- **Limite di tempo (secondi)**: imposta un limite di tempo per l'arresto dell'acquisizione di pacchetti. Il valore predefinito è 1800 secondi.

> [!NOTE]
> Gli account di archiviazione Premium non sono attualmente supportati per l'archiviazione delle acquisizioni di pacchetti.

**Filtro (facoltativo)**

- **Protocollo**: protocollo per filtrare l'acquisizione di pacchetti. I valori disponibili sono TCP, UDP e Qualsiasi.
- **Indirizzo IP locale**: questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui l'indirizzo IP locale corrisponde a questo valore del filtro.
- **Porta locale**: questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui la porta locale corrisponde a questo valore del filtro.
- **Indirizzo IP remoto**: questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui l'IP remoto corrisponde a questo valore del filtro.
- **Porta remota**: questo valore filtra l'acquisizione di pacchetti per ottenere i pacchetti in cui la porta remota corrisponde a questo valore del filtro.

> [!NOTE]
> I valori di indirizzo IP e porta possono essere un valore singolo, un intervallo di valori o un set, ovvero 80-1024 per la porta. È possibile definire tutti i filtri desiderati.

Dopo che tutti i valori sono stati immessi, fare clic su **OK** per creare l'acquisizione di pacchetti.

![Creare un'acquisizione di pacchetti][2]

Alla scadenza del limite di tempo impostato, l'acquisizione di pacchetti viene arrestata e può essere esaminata. È anche possibile arrestare manualmente le sessioni delle acquisizioni di pacchetti.

## <a name="delete-a-packet-capture"></a>Eliminare un'acquisizione di pacchetti

Nella visualizzazione dell'acquisizione di pacchetti fare clic sul **menu contestuale** (...) o fare clic con il pulsante destro del mouse e scegliere **Elimina** per arrestare l'acquisizione di pacchetti

![Eliminare un'acquisizione di pacchetti][3]

> [!NOTE]
> L'eliminazione di un'acquisizione di pacchetti non elimina il file nell'account di archiviazione.

Viene chiesto di confermare se si vuole eliminare l'acquisizione del pacchetto. Fare clic su **Sì**

![Conferma][4]

## <a name="stop-a-packet-capture"></a>Interrompere un'acquisizione di pacchetti

Nella visualizzazione dell'acquisizione di pacchetti fare clic sul **menu contestuale** (...) o fare clic con il pulsante destro del mouse e scegliere **Arresta** per arrestare l'acquisizione di pacchetti

## <a name="download-a-packet-capture"></a>Scaricare un'acquisizione di pacchetti

Dopo aver completato la sessione di acquisizione di pacchetti, il file di acquisizione viene caricato nell'archivio BLOB o in un file locale nella macchina virtuale. La posizione di archiviazione dell'acquisizione di pacchetti viene definita al momento della creazione della sessione. Uno strumento utile per accedere ai file di acquisizione salvati in un account di archiviazione è Esplora archivi di Microsoft Azure, disponibile qui: http://storageexplorer.com/

Se viene specificato un account di archiviazione, i file di acquisizione di pacchetti vengono salvati in un account di archiviazione nel percorso seguente:
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come automatizzare le acquisizioni di pacchetti tramite gli avvisi della macchina virtuale, leggere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md).

Per stabilire se un traffico specificato è consentito all'interno o all'esterno di una macchina virtuale, vedere [Check IP flow verify](network-watcher-check-ip-flow-verify-portal.md) (Controllare la verifica del flusso IP).

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png














