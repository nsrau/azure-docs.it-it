---
title: Problemi di utilizzo delle estensioni VM in Python 3-sistemi di macchine virtuali di Azure abilitati per Linux
description: Informazioni sull'uso delle estensioni VM nei sistemi Linux abilitati per Python 3
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82120782"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problemi di utilizzo delle estensioni VM in Python 3-sistemi di macchine virtuali di Azure abilitati per Linux

> [!NOTE]
> Microsoft consiglia agli utenti di adottare **Python 3. x** nei propri sistemi, a meno che il carico di lavoro non richieda il supporto per **Python 2. x** . Esempi di questo requisito possono includere gli script di amministrazione legacy o le estensioni, ad esempio **crittografia dischi di Azure** e **monitoraggio di Azure**.
>
> Prima di installare **Python 2. x** in produzione, si consideri la questione del supporto a lungo termine di Python 2. x, in particolare la possibilità di ricevere aggiornamenti della sicurezza. Come prodotti, incluse alcune delle estensioni indicate, aggiornamento con il supporto di **python 3,8** , è necessario sospendere l'uso di Python 2. x.

Alcune distribuzioni di Linux sono passate a Python 3,8 e hanno rimosso il `/usr/bin/python` EntryPoint legacy per Python. Questa transizione influisca sulla distribuzione automatica predefinita di determinate estensioni di macchina virtuale (VM) con le condizioni seguenti:

- Estensioni ancora in fase di transizione al supporto per Python 3. x
- Estensioni che usano il EntryPoint `/usr/bin/python` legacy

Gli utenti della distribuzione Linux che hanno eseguito la transizione a **Python 3. x** devono `/usr/bin/python` assicurarsi che il EntryPoint legacy esista prima di provare a distribuire tali estensioni alle macchine virtuali. In caso contrario, la distribuzione dell'estensione potrebbe non riuscire. 

- Le distribuzioni di Linux approvate interessate includono **Ubuntu Server 20,04 LTS** e **Ubuntu Pro 20,04 LTS**.

- Le estensioni di VM interessate includono **crittografia dischi di Azure**, **log Analytics**, **l'accesso alla macchina virtuale** (usato per la reimpostazione della password) e la **diagnostica Guest** (usata per contatori di prestazioni aggiuntivi).

Gli aggiornamenti sul posto, ad esempio l'aggiornamento da **ubuntu 18,04 LTS** a **Ubuntu 20,04 LTS**, devono mantenere `/usr/bin/python` il collegamento simbolico e rimanere inalterati.

## <a name="resolution"></a>Soluzione

Prima di distribuire le estensioni negli scenari di interesse noto descritti in precedenza nel riepilogo, prendere in considerazione i consigli generali seguenti:

1.  Prima di distribuire l'estensione, ripristinare `/usr/bin/python` il collegamento simbolico usando il metodo fornito dal fornitore di distribuzioni Linux.

    - Per **Python 2,7**, ad esempio, usare:`sudo apt update && sudo apt install python-is-python2`

2.  Se è già stata distribuita un'istanza che presenta questo problema, usare la funzionalità **Esegui comando** nel pannello della **macchina virtuale** per eseguire i comandi menzionati in precedenza. L'estensione del comando Run non è interessata dalla transizione a Python 3,8.

3.  Se si distribuisce una nuova istanza ed è necessario impostare un'estensione in fase di provisioning, usare i dati utente di **cloud-init** per installare i pacchetti menzionati in precedenza.

    Ad esempio, per Python 2,7:

    ```
    # create cloud-init config
    cat > cloudinitConfig.json <<EOF
    #cloud-config
    package_update: true
    
    runcmd:
    - sudo apt update
    - sudo apt install python-is-python2 
    EOF
    
    # create VM
    az vm create \
        --resource-group <resourceGroupName> \
        --name <vmName> \
        --image <Ubuntu 20.04 Image URN> \
        --admin-username azadmin \
        --ssh-key-value "<sshPubKey>" \
        --custom-data ./cloudinitConfig.json
    ```

4.  Se gli amministratori dei criteri dell'organizzazione stabiliscono che le estensioni non devono essere distribuite nelle macchine virtuali, è possibile disabilitare il supporto dell'estensione in fase di provisioning:

    - API REST

      Per disabilitare e abilitare le estensioni quando è possibile distribuire una macchina virtuale con questa proprietà:

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, fare riferimento ad [altre modifiche del sistema di base dal 18,04 LTS-Python 3 per impostazione predefinita](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) .
