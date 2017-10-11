---
title: Preparare le risorse VMware locali per la replica in Azure con Azure Site Recovery | Microsoft Docs
description: Riepilogo dei passaggi necessari per replicare in Archiviazione di Azure i carichi di lavoro in esecuzione in VM VMware
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>Passaggio 6: Preparare la replica dell'ambiente VMware locale in Azure

Usare le istruzioni riportate in questo articolo per preparare i server VMware locali per l'interazione con Azure Site Recovery e le VM VMware per l'installazione del servizio Mobility. L'agente del servizio Mobility deve essere installato in tutte le VM locali di cui si vuole eseguire la replica in Azure.

## <a name="prepare-for-automatic-discovery"></a>Eseguire la preparazione per l'individuazione automatica

Site Recovery individua automaticamente le macchine virtuali eseguite in host vSphere ESXi (con o senza server vCenter). Per eseguire l'individuazione automatica, Site Recovery richiede un account per accedere agli host e ai server:

1. Per usare un account dedicato, creare un ruolo a livello di vCenter con le autorizzazioni descritte nella tabella seguente. Assegnare un nome all'account, ad esempio **Azure_Site_Recovery**.
2. Creare quindi un utente nell'host vSphere o nel server vCenter e assegnare il ruolo all'utente. Questo account utente viene specificato durante la distribuzione di Site Recovery.


### <a name="vmware-account-permissions"></a>Autorizzazioni dell'account VMware

Site Recovery deve avere accesso a VMware per l'individuazione automatica delle VM da parte del server di elaborazione e per il failover e il failback delle VM.

- **Migrazione**: se si vuole eseguire solo la migrazione di VM VMware in Azure, senza failback, è possibile usare un account VMware con un ruolo di sola lettura. Un ruolo di questo tipo può eseguire il failover, ma non arrestare i computer di origine protetti. Ciò non è necessario per la migrazione.
- **Replica/ripristino**: se si vuole distribuire una replica completa (replica, failover e failback), l'account deve poter eseguire operazioni come la creazione e la rimozione di dischi, l'accensione di VM e così via.
- **Individuazione automatica**: è necessario almeno un account di sola lettura.


**Attività** | **Account/ruolo necessario** | **Autorizzazioni** | **Dettagli**
--- | --- | --- | ---
**Individuazione automatica delle VM VMware da parte del server di elaborazione** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).
**Failover** | È necessario almeno un utente di sola lettura | Data Center object (Oggetto data center)–> Propagate to Child Object (Propaga a oggetto figlio), role=Read-only (ruolo=Sola lettura) | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).<br/><br/> È utile ai fini della migrazione, ma non per la replica completa, il failover e il failback.
**Failover e failback** | Suggeriamo di creare un ruolo (Azure_Site_Recovery) con le necessarie autorizzazioni e poi assegnare il ruolo a un utente o gruppo VMware | Data Center object (Oggetto data center) –> Propagate to Child Object (Propaga a oggetto figlio), role=Azure_Site_Recovery (ruolo=Azure_Site_Recovery)<br/><br/> Datastore (Archivio dati) -> Allocate space (Alloca spazio), Browse datastore (Sfoglia archivio dati), Low level file operations (Operazioni file di livello basso), Remove file (Rimuovi file), Update virtual machine files (Aggiorna file macchina virtuale)<br/><br/> Network (Rete) -> Network assign (Assegnazione rete)<br/><br/> Risorsa -> Assegnare VM al pool di risorse, migrare la VM spenta, migrare la VM accesa<br/><br/> Tasks (Attività) -> Create task (Crea attività), Update task (Aggiorna attività)<br/><br/> Virtual machine (Macchina virtuale) -> Configuration (Configurazione)<br/><br/> Virtual machine (Macchina virtuale) -> Interact (Interagisci) -> Answer question (Rispondi alla domanda), Device connection (Connessione dispositivo), Configure CD media (Configura supporto CD), Configure floppy media (Configura supporto floppy), Power off (Spegni), Power on (Accendi), VMware tools install (Installazione strumenti VMware)<br/><br/> Virtual machine (Macchina virtuale) -> Inventory (Inventario) -> Create (Crea), Register (Registra), Unregister (Annulla registrazione)<br/><br/> Virtual machine (Macchina virtuale) -> Provisioning -> Allow virtual machine download (Consenti download macchina virtuale), Allow virtual machine files upload (Consenti upload file macchina virtuale)<br/><br/> Macchina virtuale -> Snapshots -> Remove snapshots | L'utente viene assegnato a livello di data center e ha accesso a tutti gli oggetti nel data center.<br/><br/> Per limitare l'accesso, assegnare il ruolo **No access** (Nessun accesso) con **Propagate to child object** (Propaga a oggetto figlio) agli oggetti figlio (host vSphere, archivi dati, VM e reti).


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>Eseguire la preparazione per l'installazione push del servizio Mobility

Il servizio Mobility deve essere installato in tutte le VM da replicare. Ci sono diversi modi per installare il servizio, tra cui installazione manuale, installazione push dal server di elaborazione di Site Recovery e installazione con metodi come System Center Configuration Manager.

Se si vuole usare l'installazione push, è necessario preparare un account che Site Recovery possa usare per accedere alla VM.

- È possibile usare un account di dominio o locale
- Per Windows, se non si usa un account di dominio è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con un valore di 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
- Per aggiungere la voce del Registro di sistema per Windows da un'interfaccia della riga di comando, digitare:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Per Linux, l'account deve essere radice nel server Linux di origine.



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 7: Creare un insieme di credenziali](vmware-walkthrough-create-vault.md)
