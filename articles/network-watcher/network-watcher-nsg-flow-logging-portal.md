---
title: Registrare il flusso del traffico di rete da e verso una macchina virtuale - Esercitazione - Portale di Azure | Microsoft Docs
description: Imparare a registrare il flusso del traffico di rete da e verso una macchina virtuale usando la funzionalità di log del flusso di NSG di Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Esercitazione: Registrare il traffico di rete da e verso una macchina virtuale tramite il portale di Azure

Un gruppo di sicurezza di rete (NSG) consente di filtrare il traffico in ingresso verso e quello in uscita da una macchina virtuale. È possibile registrare il traffico di rete che scorre attraverso una NSG con la funzionalità di log del flusso di NSG di Network Watcher. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale con un gruppo di sicurezza di rete
> * Abilitare Network Watcher e registrare il provider Microsoft.Insights
> * Abilitare un log del flusso di traffico per un NSG usando la funzionalità di log del flusso di NSG di Network Watcher
> * Scaricare i dati registrati
> * Visualizzare i dati registrati

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Selezionare **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter** o **Ubuntu Server 17.10 VM**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    |Impostazione|Valore|
    |---|---|
    |NOME|myVm|
    |Nome utente| Immettere un nome utente a scelta.|
    |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Sottoscrizione| Selezionare la propria sottoscrizione.|
    |Gruppo di risorse| Selezionare **Crea nuovo** e immettere **myResourceGroup**.|
    |Località| Selezionare **Stati Uniti orientali**.|

4. Selezionare una dimensione per la VM e quindi selezionare **Seleziona**.
5. In **Impostazioni**  accettare tutte le impostazioni predefinite e scegliere **OK**.
6. In **Crea** in **Riepilogo** selezionare **Crea** per avviare la distribuzione della VM. La distribuzione della VM richiede alcuni minuti. Attendere che la macchina virtuale completi la distribuzione prima di continuare con i passaggi rimanenti.

La creazione della VM richiede alcuni minuti. Non proseguire con i passaggi rimanenti finché non è completata la creazione della macchina virtuale. Mentre il portale crea la macchina virtuale, crea anche un gruppo di sicurezza di rete denominato **myVm-nsg**, che associa all'interfaccia di rete per la macchina virtuale.

## <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si dispone già di Network Watcher abilitato nell'area Stati Uniti orientali, passare al paragrafo [Registrare il provider Insights](#register-insights-provider).

1. Nel portale selezionare **Tutti i servizi**. Nella **casella del filtro** immettere *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati.
2. Selezionare **Area** per espandere e quindi selezionare **...** a destra di **Stati Uniti orientali**, come mostrato nell'immagine seguente:

    ![Abilitare Network Watcher](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Selezionare **Abilita Network Watcher**.

## <a name="register-insights-provider"></a>Registrare il provider Insights

La registrazione del flusso di NSG richiede il provider **Microsoft.Insights**. Per registrare il provider completare i passaggi seguenti:

1. Nell'angolo in alto a sinistra del portale selezionare **Tutti i servizi**. Nella casella di filtro immettere *Sottoscrizioni*. Selezionare **Sottoscrizioni** quando viene visualizzato nei risultati della ricerca.
2. Dall'elenco selezionare la sottoscrizione per cui si desidera abilitare il provider.
3. Selezionare **Provider di risorse** in **IMPOSTAZIONI**.
4. Verificare che la colonna **STATO** per il provider **microsoft.insights** indichi **Registrazione completata**, come mostrato nell'immagine seguente. Se lo stato è **Non registrato**, selezionare **Registra** a destra del provider.

    ![Registrare il provider](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Abilitare il log del flusso di NSG

1. I dati del log del flusso di NSG vengono scritti in un account di archiviazione di Azure. Per creare un account di archiviazione di Azure, selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale.
2. Selezionare **Storage** (Archiviazione) e quindi **Storage account - blob, file, table, queue** (Account di archiviazione: BLOB, File, Tabelle, Code).
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea**.

    | Impostazione        | Valore                                                        |
    | ---            | ---   |
    | NOME           | Può essere di lunghezza compresa tra 3 e 24 caratteri, può contenere solo lettere minuscole e numeri e deve essere univoco in tutti gli account di archiviazione di Azure.                                                               |
    | Località       | Selezionare **Stati Uniti orientali**.                                           |
    | Gruppo di risorse | Selezionare **Usa esistente** e quindi **myResourceGroup** |

    La creazione dell'account di archiviazione può richiedere all'incirca un minuto. Non proseguire con i passaggi rimanenti finché non è stato creato l'account di archiviazione. Se si usa un account di archiviazione esistente invece di crearne uno, assicurarsi di selezionarne uno con l'impostazione predefinita **Tutte le reti** selezionata in **Firewall e reti virtuali** nelle **IMPOSTAZIONI** dell'account di archiviazione.
4. Nell'angolo in alto a sinistra del portale selezionare **Tutti i servizi**. Nella **casella del filtro** digitare *Network Watcher*. Selezionare **Network Watcher** quando viene visualizzato tra i risultati della ricerca.
5. In **LOGS** (LOG) selezionare **Log del flusso del NSG**, come illustrato nell'immagine seguente:

    ![Gruppi di sicurezza di rete](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Dall'elenco di NSG selezionare l'NSG denominato **myVm-nsg**.
7. In **Impostazioni dei log dei flussi** selezionare **On**.
8. Selezionare l'account di archiviazione creato al passaggio 3.
9. Impostare **Conservazione (giorni)** su 5 e poi selezionare **Salva**.

## <a name="download-flow-log"></a>Scaricare il log del flusso

1. Da Network Watcher nel portale selezionare **Log del flusso del NSG** in **LOGS**.
2. Selezionare **È possibile scaricare i log dei flussi dagli account di archiviazione configurati**, come mostrato nell'immagine seguente:

  ![Scaricare i log di flusso](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Selezionare l'account di archiviazione configurato al passaggio 2 di [Abilitare il log del flusso di NSG](#enable-nsg-flow-log).
4. Selezionare **Contenitori** in **SERVIZIO BLOB**, quindi selezionare il contenitore **insights-logs-networksecuritygroupflowevent**, come mostrato nell'immagine seguente:

    ![Selezionare un contenitore](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Esplorare la gerarchia di cartelle e proseguire finché non si arriva a un file PT1H.json, come mostrato nell'immagine seguente:

    ![File di log](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    I file di log vengono scritti in una gerarchia di cartelle che segue la convenzione di denominazione seguente: https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

6. Selezionare **...**  a destra del file PT1H.json e pois selezionare **Download** (Scarica).

## <a name="view-flow-log"></a>Visualizzare il log del flusso

Il codice json seguente è un esempio di ciò che verrà visualizzato nel file PT1H.json per ogni flusso per cui i dati vengono registrati:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

Il valore per **mac** negli output precedenti è l'indirizzo MAC dell'interfaccia di rete che è stato creato quando è stata creata la macchina virtuale. Le informazioni separate da virgola per **flowTuples** sono quelle riportate di seguito:

| Dati di esempio | Cosa rappresentano i dati   | Spiegazione                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Timestamp             | Il timestamp di quando si è verificato il flusso in formato UNIX EPOCH. Nell'esempio precedente, la data viene convertita in 1° maggio 2018, ore 14:59:05 GMT.                                                                                    |
| 192.168.1.4  | Indirizzo IP di origine      | L'indirizzo IP di origine del flusso.
| 10.0.0.4     | Indirizzo IP di destinazione | L'indirizzo IP di destinazione del flusso. 10.0.0.4 è l'indirizzo IP privato della macchina virtuale creata in [Creare una macchina virtuale](#create-a-vm).                                                                                 |
| 55960        | Porta di origine            | La porta di origine del flusso.                                           |
| 3389         | Porta di destinazione       | La porta di destinazione del flusso. Poiché il traffico è destinato alla porta 3389, il flusso è stato elaborato dalla regola denominata **UserRule_default-allow-rdp** nel file di log.                                                |
| T            | Protocollo               | Indica se il protocollo del flusso era TCP (T) o UDP (U).                                  |
| I            | Direzione              | Indica se il traffico era in ingresso (I) o in uscita (O).                                     |
| Una             | Azione                 | Indica se il traffico è stato consentito (A) o negato (D).                                           |

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare la registrazione del flusso per un NSG. Si è anche imparato a scaricare e visualizzare i dati registrati in un file. I dati non elaborati nel file json possono essere difficili da interpretare. Per visualizzare i dati, è possibile usare l'[analisi del traffico](traffic-analytics.md) Network Watcher, Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)e altri strumenti.