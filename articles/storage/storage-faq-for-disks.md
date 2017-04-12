---
title: Domande frequenti sui dischi delle macchine virtuali IaaS di Azure | Microsoft Docs
description: Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 1a462b8d557ad23bda912ddf9431195a8cfe909e
ms.lasthandoff: 03/31/2017


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Domande frequenti sui dischi e sui dischi Premium delle macchine virtuali IaaS di Azure (gestiti e non gestiti)

In questo articolo vengono illustrate alcune delle domande frequenti su Managed Disks e Archiviazione Premium.

## <a name="managed-disks"></a>Managed Disks

**Che cos'è Azure Managed Disks?**

Managed Disks è una funzionalità che semplifica la gestione dei dischi per le macchine virtuali IaaS di Azure, gestendo l'account di archiviazione per conto dell'utente. Per altre informazioni, vedere [Panoramica di Managed Disks](storage-managed-disks-overview.md).

**Se si crea un disco gestito Standard da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco gestito Standard creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco S10. Il costo addebitato corrisponderà al prezzo del disco S10. Per altri dettagli, vedere la pagina [relativa ai prezzi](https://azure.microsoft.com/pricing/details/storage).

**Sono previsti costi di transazione per i dischi gestiti Standard?**

Sì, ogni transazione prevede un addebito. Controllare la [pagina relativa ai prezzi] (https://azure.microsoft.com/pricing/details/storage) per informazioni dettagliate.

**Per un disco gestito Standard, si riceverà un addebito per le dimensioni effettive dei dati su disco o per la capacità con provisioning del disco?**

L'addebito viene effettuato in base alla capacità con provisioning del disco. Per altri dettagli, vedere la pagina [relativa ai prezzi](https://azure.microsoft.com/pricing/details/storage).

**In che modo il prezzo della versione Premium dei dischi gestiti è diverso da quello dei dischi non gestiti?**

Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti.

**È possibile modificare il tipo di account di archiviazione (Standard o Premium) dei dischi gestiti?**

Sì. È possibile modificare il tipo di account di archiviazione dei dischi gestiti tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

Sì, è possibile esportare i dischi gestiti tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in una sottoscrizione diversa?**

No.

**È possibile usare un file di disco rigido virtuale in un account di archiviazione di Azure per creare un disco gestito in un'area geografica diversa?**

No.

**Ci saranno le limitazioni di scalabilità per i clienti che usano dischi gestiti?**

Managed Disks elimina i limiti legati agli account di archiviazione. Tuttavia, il numero di dischi gestiti per ogni sottoscrizione è limitato a 2000 per impostazione predefinita. Il limite può essere aumentato contattando il supporto tecnico.

**È possibile fare uno snapshot incrementale di un disco gestito?**

No. La funzionalità Snapshot corrente crea una copia completa di un disco gestito. Tuttavia è previsto un supporto futuro per gli snapshot incrementali.

**È possibile disporre di macchine virtuali in un set di disponibilità composte da dischi gestiti e non gestiti?**

No, i dischi nelle macchine virtuali in un set di disponibilità devono essere o tutti gestiti o tutti non gestiti. Quando si crea un set di disponibilità, è possibile scegliere il tipo di dischi da utilizzare.

**Managed Disks è l'opzione predefinita nel portale di Azure?**

Non attualmente, ma diventerà l'impostazione predefinita in futuro.

**È possibile creare un disco vuoto gestito?**

Sì, è possibile creare un disco vuoto. Un disco gestito può essere creato indipendentemente da una macchina virtuale, ad esempio non collegandolo a una macchina virtuale.

**Qual è il numero di domini di errore supportati per i set di disponibilità con Managed Disks?**

Il numero di domini di errore supportato è di 2 o 3 per i set di disponibilità con Managed Disks a seconda dell'area in cui si trovano.

**Come viene configurato l'account di archiviazione Standard per l'impostazione della diagnostica?**

Si imposta un account di archiviazione privato per la diagnostica della macchina virtuale. In futuro, si intende estendere la diagnostica anche a Managed Disks.

**Quale tipologia di supporto RBAC è disponibile per Managed Disks?**

Managed Disks supporta tre ruoli predefiniti principali:

1.  Proprietario: può gestire tutto, compresi gli accessi.

2.  Collaboratore: può gestire tutto ad eccezione degli accessi.

3.  Lettore: può visualizzare tutto, ma non apportare modifiche.

**È possibile copiare o esportare un disco gestito in un account di archiviazione privato?**

È possibile ottenere un URI di firma di accesso condiviso in sola lettura per il disco gestito e usarla per copiare i contenuti in un account di archiviazione privato o in un archivio locale.

**È possibile creare una copia del proprio disco gestito?**

I clienti possono eseguire uno snapshot dei relativi dischi gestiti e usarlo per creare un altro disco gestito.

**I dischi non gestiti sono ancora supportati?**

Sì, sono supportati sia i dischi gestiti che quelli non gestiti. È tuttavia consigliabile iniziare a usare i dischi gestiti per i nuovi carichi di lavoro ed eseguire la migrazione dei carichi di lavoro correnti a Managed Disks.

**Se si crea un disco di dimensioni pari a 128 GB e si aumentano tali dimensioni a 130 GB, verranno addebitati i costi relativi al livello di dimensioni successivo (512 GB)?**

Sì.

**È possibile creare Managed Disks con LRS, GRS e ZRS?**

Azure Managed Disks supporta attualmente solo l'archiviazione con ridondanza locale.

## <a name="managed-disks-and-port-8443"></a>Managed Disks e porta 8443

**Perché i clienti devono sbloccare il traffico in uscita sulla porta 8443 per le macchine virtuali usando Managed Disks di Azure?**

L'agente di macchine virtuali di Azure usa la porta 8443 per segnalare lo stato di ogni estensione della macchina virtuale nella piattaforma di Azure. Se questa porta non viene sbloccata, l'agente di macchine virtuali non sarà in grado di segnalare lo stato di un'estensione della macchina virtuale. Per altre informazioni sull'agente di VM, vedere [Panoramica dell'agente di macchine virtuali di Azure](../virtual-machines/windows/agent-user-guide.md).

**Cosa accade se una macchina virtuale viene distribuita con le estensioni e la porta non viene sbloccata?**

La distribuzione provocherà un errore. 

**Cosa accade se una macchina virtuale viene distribuita senza estensioni e la porta non viene sbloccata?**

Questo non influenzerà la distribuzione. 

**Cosa accade se un'estensione viene installata in una macchina virtuale su cui è già stato eseguito il provisioning ed è in esecuzione e per cui la porta 8443 è bloccata?**

L'estensione non verrà distribuita correttamente. Lo stato dell'estensione sarà sconosciuto. 

**Cosa accade se viene usato un modello ARM per eseguire il provisioning su più macchine virtuali con la porta 8443 bloccata, una macchina virtuale con le estensioni e una seconda macchina virtuale che dipende dalla prima macchina virtuale?**

Per la prima macchina virtuale la distribuzione risulterà non riuscita perché le estensioni non vengono distribuite correttamente. La seconda macchina virtuale non verrà distribuita. 

**Il requisito di sblocco della porta verrà applicato a tutte le estensioni della macchina virtuale?**

Sì.

**Le connessioni in ingresso e in uscita sulla porta 8443 devono entrambe essere sbloccate?**

No. Solo le connessioni in uscita sulla porta 8443 devono essere sbloccate. 

**Lo sblocco della porta 8443 per le connessioni in uscita è necessario per l'intera durata della macchina virtuale?**

Sì.

**Lo sblocco di questa porta influisce sulle prestazioni della macchina virtuale?**

No.

**Esiste una data entro la quale è necessario correggere questo problema così da non dover più sbloccare la porta 8443?**

Sì, entro la fine di maggio 2017.

## <a name="premium-disks--both-managed-and-unmanaged"></a>Dischi Premium, sia gestiti che non

**Se una macchina virtuale usa una serie di dimensioni che supporta Archiviazione Premium, ad esempio DSv2, è possibile collegare dischi dati sia Premium che Standard?** 

Sì.

**È possibile collegare dischi sia Premium che Standard a una serie di dimensioni che non supporta l'archiviazione Premium, come le serie D, Dv2, G o F?**

No. È possibile collegare solo dischi dati Standard alle macchine virtuali che non usano una serie di dimensioni con supporto all'Archiviazione Premium.

**Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponderà al prezzo del disco P10.

**Sono previsti costi per transazione quando si usa Archiviazione Premium?**

È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per altri dettagli, vedere la pagina [relativa ai prezzi](https://azure.microsoft.com/pricing/details/storage).

**Quali sono i limiti per IOPS e velocità effettiva che è possibile ottenere dalla cache del disco?**

I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4000 IOPS per core e 33 MB al secondo per core. La serie GS offre 5000 IOPS per memoria centrale e 50 MB al secondo per memoria centrale.

**Sono supportate le unità SSD locali per le macchine virtuali di Managed Disks?**

L'unità SSD locale è un archivio temporaneo che è incluso in una macchina virtuale di Managed Disks. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa unità SSD locale per archiviare dati dell'applicazione che non sono salvati in modo permanente nell'archivio BLOB di Azure.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?

Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto. È possibile pubblicare una domanda nei commenti alla fine di questo articolo o nel [forum MSDN di Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) per mettersi in contattato con il team di Archiviazione di Azure e altri membri della community.

Per eseguire una richiesta di funzionalità, inviare richieste e idee al [forum dei commenti su Archiviazione di Azure](https://feedback.azure.com/forums/217298-storage).
