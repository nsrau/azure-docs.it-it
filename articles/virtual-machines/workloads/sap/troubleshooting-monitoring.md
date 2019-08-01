---
title: Monitoraggio di SAP HANA in Azure (istanze di grandi dimensioni) | Microsoft Docs
description: Monitorare SAP HANA in Azure (istanze di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de7066004c4baa6e3086f2909d9d5150b50d8e41
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570547"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure

SAP HANA in Azure (istanze di grandi dimensioni) non è diverso da qualsiasi altra distribuzione IaaS: è necessario monitorare le attività del sistema operativo e dell'applicazione e il modo in cui questi usano le risorse seguenti:

- CPU
- Memoria
- Larghezza di banda della rete
- Spazio su disco

Con le macchine virtuali di Azure, è necessario stabilire se le classi di risorse indicate sopra sono sufficienti o meno. Ecco ulteriori dettagli su ciascuna delle diverse classi:

**Consumo di risorse CPU:** Il rapporto definito da SAP per un determinato carico di lavoro rispetto a HANA viene applicato per assicurarsi che siano disponibili risorse di CPU sufficienti per l'uso dei dati archiviati in memoria. Tuttavia potrebbero esserci casi in cui HANA usa molte query eseguite in CPU a causa di indici mancanti o problemi simili. Ciò significa che è necessario monitorare il consumo delle risorse di CPU dell'unità dell'istanza HANA di grandi dimensioni nonché le risorse di CPU utilizzate dai servizi HANA specifici.

**Utilizzo memoria:** È importante monitorare dall'interno di HANA, nonché all'esterno di HANA sull'unità. All'interno di HANA monitorare il modo in cui i dati usano la memoria allocata di HANA per rispettare le linee guida sulle dimensioni di SAP. È utile inoltre monitorare il consumo di memoria a livello dell'istanza di grandi dimensioni per assicurarsi che il software aggiuntivo non HANA installato non usi troppa memoria e pertanto non entri in competizione con HANA per la memoria.

**Larghezza di banda di rete:** Il gateway Azure VNet è limitato alla larghezza di banda dei dati spostati nella VNet di Azure. è quindi utile monitorare i dati ricevuti da tutte le macchine virtuali di Azure all'interno di un VNet per capire quanto sia vicino ai limiti dello SKU del gateway di Azure selezionato. Nell'unità dell'istanza HANA di grandi dimensioni ha senso monitorare anche il traffico di rete in ingresso e in uscita nonché monitorare e tenere traccia dei volumi che vengono gestiti nel tempo.

**Spazio su disco:** Il consumo di spazio su disco in genere aumenta nel tempo. Le cause più comuni sono: l'aumento di volume dei dati, l'esecuzione di backup del log delle transazioni, l'archiviazione dei file di traccia e l'esecuzione di snapshot di archiviazione. Pertanto è importante monitorare l'utilizzo dello spazio su disco e gestire lo spazio su disco associato all'unità dell'istanza HANA di grandi dimensioni.

Per gli **SKU di tipo II** delle istanze di grandi dimensioni di HANA, il server viene fornito con gli strumenti di diagnostica di sistema precaricati. È possibile usare questi strumenti di diagnostica per eseguire il controllo di integrità del sistema. Eseguire il comando seguente per generare il file di log di controllo integrità in /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando si lavora con il team di supporto tecnico Microsoft per risolvere un problema, potrebbe anche essere richiesto di fornire i file di log usando questi strumenti di diagnostica. È possibile comprimere il file seguendo questo comando:
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Passaggi successivi**

- Vedere [Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
