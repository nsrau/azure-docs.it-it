---
title: Panoramica del provisioning di Linux
description: Panoramica della procedura per importare le immagini di VM Linux o crearne di nuove da usare in Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a7d9aa7de8bb75a22acc85c77924765eaa1b6b3b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87080149"
---
# <a name="azure-linux-vm-provisioning"></a>Provisioning di macchine virtuali Linux in Azure
Quando si crea una VM da un'immagine generalizzata (raccolta immagini condivise o immagine gestita), il piano di controllo consentirà di creare una VM e di passarvi parametri e impostazioni. Questo processo è detto *provisioning* della VM. Durante il provisioning, la piattaforma rende disponibili alla macchina virtuale i valori dei parametri necessari per la creazione (nome host, nome utente, password, chiavi SSH, dati personalizzati) all'avvio. 

Un agente di provisioning inserito all'interno dell'immagine si interfaccia con la piattaforma, connettendosi a più interfacce di provisioning indipendenti, imposta le proprietà e segnala il completamento alla piattaforma. 

Gli agenti di provisioning possono essere l'[agente Linux di Azure](../extensions/agent-linux.md) o [cloud-init](./using-cloud-init.md). Sono [prerequisiti](create-upload-generic.md) per la creazione di immagini generalizzate.

Gli agenti di provisioning forniscono il supporto per tutte le [distribuzioni Linux in Azure](./endorsed-distros.md) approvate. In molti casi le immagini delle distribuzioni approvate vengono fornite sia con cloud-init che con l'agente Linux. In questo modo è possibile scegliere di affidare a cloud-init la gestione del provisioning, quindi l'agente Linux fornirà il supporto per gestire le [estensioni di Azure](../extensions/features-windows.md). Il supporto per le estensioni significa che la VM sarà quindi idonea per supportare altri servizi di Azure, come la reimpostazione della password delle VM, Monitoraggio di Azure, Backup di Azure, Crittografia dischi di Azure e così via.

Al termine del provisioning, cloud-init viene eseguito a ogni avvio. Cloud-init monitorerà le modifiche apportate alla macchina virtuale, ad esempio le modifiche di rete, il montaggio, la formattazione del disco temporaneo e l'avvio dell'agente Linux. L'agente Linux viene continuamente eseguito sul server, alla ricerca di un 'stato obiettivo' (nuova configurazione) della piattaforma Azure, quindi, ogni volta che si installano le estensioni, l'agente sarà in grado di elaborarle.

Anche se attualmente esistono due agenti di provisioning, è consigliabile scegliere cloud-init come agente e installare l'agente Linux per il supporto delle estensioni. In questo modo è possibile sfruttare le ottimizzazioni della piattaforma e disabilitare/rimuovere l'agente Linux. Per altre informazioni su come creare immagini senza l'agente e su come rimuoverlo, vedere questa [documentazione](disable-provisioning.md).

Se un kernel Linux non supporta l'esecuzione di uno dei due agenti, ma si vogliono impostare alcune proprietà di creazione della VM, come nome host, dati personalizzati, nome utente, password, chiavi SSH e così via, questo documento descrive come [creare immagini generalizzate senza un agente](no-agent.md) e soddisfare i requisiti della piattaforma.


## <a name="provisioning-agent-responsibilities"></a>Responsabilità degli agenti di provisioning

**Provisioning di immagini**
  
- Creazione di un account utente
- Configurazione dei tipi di autenticazione SSH
- Distribuzione di coppie di chiavi e chiavi pubbliche SSH
- Impostazione del nome host
- Pubblicazione del nome host nel DNS della piattaforma
- Segnalazione dell'ID digitale della chiave dell'host SSH alla piattaforma
- Gestione del disco risorse
- Formattazione e montaggio del disco risorse
- Utilizzo ed elaborazione di `customData`
 
**Rete**
  
- Gestisce i percorsi per migliorare la compatibilità con i server DHCP della piattaforma.
- Garantisce la stabilità del nome dell'interfaccia di rete

**Kernel**
  
- Configura un NUMA virtuale (disabilitare per kernel <`2.6.37`)
- Utilizza l'entropia Hyper-V per `/dev/random`
- Configura i timeout SCSI per il dispositivo radice (che può essere remoto)

**Diagnostica**
  
- Reindirizzamento della console alla porta seriale

## <a name="communication"></a>Comunicazione
Il flusso di informazioni dalla piattaforma all'agente avviene tramite due canali:

- Un DVD collegato in fase di avvio per le distribuzioni IaaS. Nel DVD è incluso un file di configurazione conforme a OVF che contiene tutte le informazioni di provisioning, ad eccezione delle coppie di chiavi SSH effettive.
- Un endpoint TCP che espone un'API REST usata per ottenere la configurazione della distribuzione e della topologia.


## <a name="azure-provisioning-agent-requirements"></a>Requisiti degli agenti di provisioning di Azure
Per il corretto funzionamento dell'agente Linux e di cloud-init sono necessari alcuni pacchetti di sistema:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Utilità di file system `sfdisk`, `fdisk`, `mkfs`, `parted`
- Strumenti password: chpasswd, sudo
- Strumenti di elaborazione testo: sed, grep
- Strumenti di rete: ip-route
- Supporto di kernel per l'installazione di file system UDF.

## <a name="next-steps"></a>Passaggi successivi

Se necessario, è possibile [disabilitare il provisioning e rimuovere l'agente Linux](disable-provisioning.md).
