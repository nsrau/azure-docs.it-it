---
title: Monitoraggio di SAP HANA in Azure (istanze di grandi dimensioni) | Microsoft Docs
description: Monitorare SAP HANA in Azure (istanze di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f046304121e0aed8efa1bbc2535d34186eba3496
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713708"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure

SAP HANA in Azure (istanze di grandi dimensioni) non è diverso da qualsiasi altra distribuzione IaaS: è necessario monitorare le attività del sistema operativo e dell'applicazione e il modo in cui questi usano le risorse seguenti:

- CPU
- Memoria
- Larghezza di banda della rete
- Spazio su disco

Con le macchine virtuali di Azure, è necessario stabilire se le classi di risorse indicate sopra sono sufficienti o meno. Ecco ulteriori dettagli su ciascuna delle diverse classi:

**Utilizzo delle risorse della CPU:** Il rapporto che SAP definiti per alcuni carichi di lavoro su HANA viene usato per assicurarsi che ci siano sufficienti risorse di CPU disponibile per i dati archiviati in memoria. Tuttavia potrebbero esserci casi in cui HANA usa molte query eseguite in CPU a causa di indici mancanti o problemi simili. Ciò significa che è necessario monitorare il consumo delle risorse di CPU dell'unità dell'istanza HANA di grandi dimensioni nonché le risorse di CPU utilizzate dai servizi HANA specifici.

**Utilizzo della memoria:** È importante monitorare dall'interno di HANA nonché dall'esterno di HANA nell'unità. All'interno di HANA monitorare il modo in cui i dati usano la memoria allocata di HANA per rispettare le linee guida sulle dimensioni di SAP. È utile inoltre monitorare il consumo di memoria a livello dell'istanza di grandi dimensioni per assicurarsi che il software aggiuntivo non HANA installato non usi troppa memoria e pertanto non entri in competizione con HANA per la memoria.

**Larghezza di banda di rete:** Il gateway di rete virtuale di Azure è limitato nella larghezza di banda dei dati trasferiti in rete virtuale di Azure, pertanto è utile monitorare i dati ricevuti da tutte le macchine virtuali di Azure all'interno di una rete virtuale per scoprire quanto si è vicini ai limiti di Azure nello SKU del gateway selezionato. Nell'unità dell'istanza HANA di grandi dimensioni ha senso monitorare anche il traffico di rete in ingresso e in uscita nonché monitorare e tenere traccia dei volumi che vengono gestiti nel tempo.

**Spazio su disco:** Utilizzo dello spazio su disco è in genere cresce nel tempo. Le cause più comuni sono: l'aumento di volume dei dati, l'esecuzione di backup del log delle transazioni, l'archiviazione dei file di traccia e l'esecuzione di snapshot di archiviazione. Pertanto è importante monitorare l'utilizzo dello spazio su disco e gestire lo spazio su disco associato all'unità dell'istanza HANA di grandi dimensioni.

Per gli **SKU di tipo II** delle istanze di grandi dimensioni di HANA, il server viene fornito con gli strumenti di diagnostica di sistema precaricati. È possibile usare questi strumenti di diagnostica per eseguire il controllo di integrità del sistema. Eseguire il comando seguente per generare il file di log di controllo integrità in /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando si lavora con il team di supporto tecnico Microsoft per risolvere un problema, potrebbe anche essere richiesto di fornire i file di log usando questi strumenti di diagnostica. È possibile comprimere il file seguendo questo comando:
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Passaggi successivi**

- Vedere [Come monitorare SAP HANA (istanze di grandi dimensioni) in Azure](troubleshooting-monitoring.md).