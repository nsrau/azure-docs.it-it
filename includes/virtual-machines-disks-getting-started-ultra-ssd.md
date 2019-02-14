---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e55058d6b1f76b4afcb847b946df85d5ab69971b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985442"
---
# <a name="enabling-azure-ultra-ssds"></a>Abilitazione di unità Azure Ultra SSD

Azure Ultra SSD offre una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. I vantaggi aggiuntivi delle unità SSD Ultra includono la possibilità di modificare dinamicamente le prestazioni del disco in base ai carichi di lavoro, senza dover riavviare le macchine virtuali. Le unità Ultra SSD sono adatte per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni.

Le unità Ultra SSD sono attualmente in anteprima ed è necessario [eseguire la registrazione](https://aka.ms/UltraSSDPreviewSignUp) all'anteprima per accedervi.

Dopo l'approvazione, eseguire uno dei comandi seguenti per determinare in quale zona degli Stati Uniti orientali 2 distribuire l'unità Ultra SSD:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

Interfaccia della riga di comando: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

Il formato della risposta sarà simile al seguente, dove X è la zona da usare per la distribuzione negli Stati Uniti orientali 2. X può essere 1, 2 o 3.

|ResourceType  |NOME  |Località  |Zone  |Restrizione  |Funzionalità  |Valore  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Se il comando non restituisce risposte, significa che la registrazione alla funzionalità è ancora in sospeso o non è ancora stata approvata.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire la procedura illustrata in questo articolo per distribuire le prime macchine virtuali con i dischi Ultra SSD.

## <a name="deploying-an-ultra-ssd"></a>Distribuzione di un'unità Ultra SSD

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. In questa anteprima sono supportate solo le famiglie di macchine virtuali DsV3 ed EsV3. Per altri dettagli su queste dimensioni di macchine virtuali, vedere la seconda tabella in questo [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).
Vedere anche l'esempio [Create a VM with multiple Ultra SSD disks](https://aka.ms/UltraSSDTemplate) (Creare una macchina virtuale con più dischi Ultra SSD), che illustra come creare una macchina virtuale con più dischi Ultra SSD.

Di seguito vengono elencati i cambiamenti apportati a un modello di Resource Manager nuovo/modificato: **apiVersion** per `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` deve essere impostato come `2018-06-01` (o versione successiva).

Specificare UltraSSD_LRS, come SKU del disco, la capacità del disco, le operazioni di I/O al secondo e la velocità effettiva in MBps per creare un disco Ultra SSD. Il seguente è un esempio che crea un disco con 1.024 GiB (GiB = 2^30 byte), 80.000 operazioni di I/O al secondo e 1.200 MBps  (MBps = 10^6 byte al secondo):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Aggiungere un'ulteriore funzionalità nelle proprietà della macchina virtuale per indicare che l'unità Ultra SSD è abilitata. Vedere l'[esempio](https://aka.ms/UltraSSDTemplate) per il modello di Resource Manager completo:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.

## <a name="additional-ultra-ssd-scenarios"></a>Altri scenari delle unità Ultra SSD

- Durante la creazione della macchina virtuale, possono essere create implicitamente anche unità Ultra SSD. Questi dischi riceveranno tuttavia un valore predefinito per le operazioni di I/O al secondo (500) e la velocità effettiva (8 MiB/s).
- Unità Ultra SSD aggiuntive possono essere collegate alle macchine virtuali compatibili con Ultra SSD.
- Ultra SSD supporta la regolazione degli attributi delle prestazioni del disco (operazioni di I/O al secondo e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto.
- Per aumentare la capacità del disco, è necessario deallocare una macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per provare il nuovo tipo di disco, se non è ancora stata effettuata l'iscrizione all'anteprima, [richiedere l'accesso tramite questo sondaggio](https://aka.ms/UltraSSDPreviewSignUp).
