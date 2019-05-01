---
title: 'Gestire le acquisizioni di pacchetti con Azure Network Watcher: portale di Azure | Microsoft Docs'
description: Altre informazioni su come gestire la funzionalità di acquisizione di pacchetti di Network Watcher usando il portale di Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 50092db9e2e3670168cbb3440b8cb99eb0c2ac20
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714725"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Gestire le acquisizioni di pacchetti con Azure Network Watcher usando il portale

Il servizio di acquisizione di pacchetti di Network Watcher consente di creare sessioni di acquisizione per registrare il traffico da e verso una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri usi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione su una macchina virtuale desiderata, consentendo un notevole risparmio di tempo.

Questo articolo descrive come avviare, arrestare, scaricare ed eliminare un'acquisizione di pacchetti. 

## <a name="before-you-begin"></a>Prima di iniziare

L'acquisizione di pacchetti richiede la connettività seguente:
* Connettività in uscita a un account di archiviazione tramite la porta 443.
* Connettività in ingresso e in uscita a 169.254.169.254
* Connettività in ingresso e in uscita a 168.63.129.16

Se un gruppo di sicurezza di rete è associato all'interfaccia di rete o alla subnet in cui si trova l'interfaccia di rete, garantire l’esistenza di regole che autorizzino le porte precedenti. 

## <a name="start-a-packet-capture"></a>Avviare un'acquisizione di pacchetti

1. Nel browser, passare al [portale di Azure](https://portal.azure.com) e selezionare **Tutti i servizi**e quindi selezionare **Network Watcher** nella **sezione rete**.
2. Selezionare **Acquisizione di pacchetti** sotto **Strumenti di diagnostica di rete**. Sono elencate tutte le acquisizioni di pacchetti esistenti, indipendentemente dal loro stato.
3. Selezionare **Aggiungi** per creare un'acquisizione di pacchetti. Selezionare i valori per gli elementi seguenti:
   - **Sottoscrizione** La sottoscrizione che consentono di acquisire la macchina virtuale che si desidera creare il pacchetto per le novità.
   - **Gruppo di risorse**: Il gruppo di risorse della macchina virtuale.
   - **Macchina virtuale di destinazione**: La macchina virtuale che si desidera creare per l'acquisizione di pacchetti.
   - **Nome acquisizione pacchetti**: Un nome per l'acquisizione di pacchetti.
   - **Account di archiviazione o file**: Selezionare **account di archiviazione**, **File**, o entrambi. Se si seleziona **File**, l'acquisizione viene scritta in un percorso all'interno della macchina virtuale.
   - **Percorso file locale**: Il percorso locale nella macchina virtuale in cui verrà salvata l'acquisizione di pacchetti (valido solo quando *File* è selezionata). Il percorso deve essere valido. Se si usa una macchina virtuale Linux, il percorso deve iniziare con *var/captures*.
   - **Account di archiviazione**: Selezionare un account di archiviazione esistente, se è stato selezionato *account di archiviazione*. Questa opzione è disponibile solo se è stato selezionato **Archiviazione**.
   
     > [!NOTE]
     > Gli account di archiviazione Premium non sono attualmente supportati per l'archiviazione delle acquisizioni di pacchetti.

   - **Numero massimo di byte per pacchetto**: Il numero di byte che sono stati acquisiti da ogni pacchetto. Se lasciato vuoto, vengono acquisiti tutti i byte.
   - **Numero massimo di byte per sessione**: Numero totale di byte acquisiti. Una volta raggiunto il valore, l'acquisizione pacchetti si arresta.
   - **Il limite di tempo (secondi)**: Il limite di tempo prima che l'acquisizione di pacchetti viene arrestata. Il valore predefinito è 18.000 secondi.
   - Filtro (facoltativo). Selezionare **+ Aggiungere filtro**
     - **Protocollo**: Protocollo per filtrare l'acquisizione di pacchetti. I valori disponibili sono TCP, UDP e Qualsiasi.
     - **Indirizzo IP locale**: Filtra l'acquisizione di pacchetti per i pacchetti in cui l'indirizzo IP locale corrisponde a questo valore.
     - **Porta locale**: Filtra l'acquisizione di pacchetti per i pacchetti in cui la porta locale corrisponde a questo valore.
     - **Indirizzo IP remoto**: Filtra l'acquisizione di pacchetti per i pacchetti in cui l'indirizzo IP remoto corrisponde a questo valore.
     - **Porta remota**: Filtra l'acquisizione di pacchetti per i pacchetti in cui la porta remota corrisponde a questo valore.
    
     > [!NOTE]
     > I valori di indirizzo IP e porta possono essere un valore singolo, intervallo di valori o un intervallo, ad esempio 80-1024, per porta. È possibile definire tutti i filtri desiderati.

4. Selezionare **OK**.

Una volta scaduto il limite di tempo impostato per l'acquisizione di pacchetti, l'acquisizione di pacchetti viene arrestata e può essere rivista. È anche possibile arrestare manualmente le sessioni delle acquisizioni di pacchetti.

> [!NOTE]
> Il portale automaticamente:
>  * Creare un'istanza di network watcher nella stessa area dell'area in cui è presente la macchina virtuale selezionata, se l'area non dispone ancora di un network watcher.
>  * Aggiungere l'estensione della macchina virtuale*AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) oppure [Windows](../virtual-machines/windows/extensions-nwa.md) alla macchina virtuale, se non ancora installata.

## <a name="delete-a-packet-capture"></a>Eliminare un'acquisizione di pacchetti

1. Nella visualizzazione dell'acquisizione di pacchetti selezionare **...** sul lato destro dell'acquisizione di pacchetti, oppure fare clic su un'acquisizione di pacchetti esistente e selezionare **Elimina**.
2. Viene chiesto di confermare se si desidera eliminare l'acquisizione di pacchetti. Selezionare **Sì**.

> [!NOTE]
> L'eliminazione di un'acquisizione di pacchetti non elimina il file di acquisizione nell'account di archiviazione o nella macchina virtuale.

## <a name="stop-a-packet-capture"></a>Interrompere un'acquisizione di pacchetti

Nella visualizzazione dell'acquisizione di pacchetti selezionare **...** sul lato destro del pacchetto di acquisizione, o fare clic su un'acquisizione di pacchetti esistente e selezionare **Arresta**.

## <a name="download-a-packet-capture"></a>Scaricare un'acquisizione di pacchetti

Al termine della sessione di acquisizione di pacchetti, il file di acquisizione viene caricato nell'archiviazione blob o in un file locale nella macchina virtuale. La posizione di archiviazione dell'acquisizione di pacchetti viene definita durante la creazione dell'acquisizione di pacchetti. Uno strumento utile per accedere ai file di acquisizione salvati in un account di archiviazione è Microsoft Azure Storage Explorer, che è possibile [scaricare](https://storageexplorer.com/).

Se viene specificato un account di archiviazione, i file di acquisizione di pacchetti vengono salvati in un account di archiviazione nel percorso seguente:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Se è stato selezionato **File** quando è stata creata l'acquisizione, è possibile visualizzare o scaricare il file dal percorso configurato nella macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come automatizzare le acquisizioni di pacchetti con gli avvisi della macchina virtuale, consultare [Creare un avviso di un'acquisizione di pacchetti attivati](network-watcher-alert-triggered-packet-capture.md).
- Per determinare se è consentito il traffico specifico all’interno o all'esterno di una macchina virtuale, consultare [Diagnosticare un problema di filtro del traffico di rete di una macchina virtuale](diagnose-vm-network-traffic-filtering-problem.md).
