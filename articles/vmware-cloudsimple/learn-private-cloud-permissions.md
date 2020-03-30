---
title: Azure VMware Soluzione di CloudSimple - Modello di autorizzazione del cloud privatoAzure VMware Solution by CloudSimple - Private Cloud permission model
description: Descrive il modello di autorizzazione, i gruppi e le categorie CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014947"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Modello di autorizzazione CloudSimple Private Cloud di VMware vCenter

CloudSimple mantiene l'accesso amministrativo completo all'ambiente Private Cloud. A ogni cliente CloudSimple vengono concessi privilegi amministrativi sufficienti per poter distribuire e gestire le macchine virtuali nel proprio ambiente.  Se necessario, è possibile riassegnare temporaneamente i privilegi per eseguire funzioni amministrative.

## <a name="cloud-owner"></a>Proprietario cloud

Quando si crea un cloud privato, viene creato un utente **CloudOwner** nel dominio Single Sign-On di vCenter, con accesso **Cloud-Owner-Role** per gestire gli oggetti nel cloud privato. Questo utente può anche configurare altre [origini di identità vCenter](set-vcenter-identity.md)e altri utenti nel centro di gestione del cloud privato.

> [!NOTE]
> L'utente predefinito per il cloud cloudowner@cloudsimple.local privato Cloud semplice vCenter è quando viene creato un cloud privato.

## <a name="user-groups"></a>Gruppi di utenti

Durante la distribuzione di un cloud privato viene creato un gruppo denominato **Cloud-Owner-Group.** Gli utenti di questo gruppo possono amministrare varie parti dell'ambiente vSphere nel cloud privato. A questo gruppo vengono assegnati automaticamente i privilegi **Cloud-Owner-Role** e l'utente **CloudOwner** viene aggiunto come membro di questo gruppo.  CloudSimple crea gruppi aggiuntivi con privilegi limitati per la facilità di gestione.  È possibile aggiungere qualsiasi utente a questi gruppi creati in precedena e i privilegi definiti di seguito vengono assegnati automaticamente agli utenti dei gruppi.

### <a name="pre-created-groups"></a>Gruppi creati in precedena

| Nome gruppo | Scopo | Ruolo |
| -------- | ------- | ------ |
| Gruppo proprietario cloud | I membri di questo gruppo dispongono di privilegi amministrativi per il private cloud vCenter | [Ruolo proprietario cloud](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | I membri di questo gruppo dispongono di privilegi amministrativi nel cluster vCenter del cloud privato | [Ruolo di amministrazione del cluster cloud](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | I membri di questo gruppo possono gestire lo spazio di archiviazione sul canale vCenter del cloud privato | [Ruolo di amministrazione cloud](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | I membri di questo gruppo possono gestire gruppi di porte di rete e distribuite nel vCenter del cloud privato | [Ruolo di amministrazione della rete cloud](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | I membri di questo gruppo possono gestire le macchine virtuali nel server vCenter del cloud privato | [Ruolo di amministrazione cloudCloud-VM-Admin-Role](#cloud-vm-admin-role) |

Per concedere ai singoli utenti le autorizzazioni per gestire il cloud privato, creare account utente aggiungere ai gruppi appropriati.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* o *Cloud-Global-VM-Admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account di servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Elenco dei privilegi di vCenter per i ruoli predefiniti

### <a name="cloud-owner-role"></a>Ruolo proprietario cloud

| **Category** | **Privilegio** |
|----------|-----------|
| **Avvisi** | Conferma avviso <br> Crea allarme <br> Disattivare l'azione di allarme <br> Modifica allarme <br> Rimuovere l'allarme <br> Impostare lo stato dell'allarme |
| **Autorizzazioni** | Autorizzazione di modifica |
| **Raccolta contenuto** | Aggiungi elemento di libreria <br> Creare una libreria locale <br> Creare una libreria sottoscrittaCreate subscribed library <br> Eliminare la voce di libreria <br> Eliminare una libreria locale <br> Eliminare la libreria sottoscritta <br> Scaricare i file <br> Evict (elemento della libreria) <br> Libreria sottoscritta Evict <br> Importare l'archiviazione <br> Informazioni sull'abbonamento sonda <br> Leggere l'archiviazioneRead storage <br> Elemento della libreria di sincronizzazione <br> Sincronizzare la raccolta sottoscritta <br> Introspezione del tipo <br> Aggiornare le impostazioni di configurazione <br> Aggiornare i file <br> Aggiorna libreria <br> Aggiorna elemento libreria <br> Aggiorna libreria locale <br> Aggiornare la libreria sottoscritta <br> Visualizzare le impostazioni di configurazione |
| **Operazioni crittografiche** | Aggiungi disco <br> Clone <br> Decrypt <br> DirectAccess <br> Encrypt <br> Crittografa nuovo <br> Gestire il Servizio di gestione delle chiavi <br> Gestire i criteri di crittografia <br> Gestire le chiavi <br> Migrazione <br> Recrypt <br> Registra macchina virtuale <br> Registra host |
| **dvPort** | Create <br> Delete <br> Modifica <br> Funzionamento della politica <br> Operazione di ambito |
| **Datastore** | Allocare spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configurare l'archivio dati <br> Operazioni sui file di basso livello <br> Spostare l'archivio dati <br> Rimuovi archivio dati <br> Rimuovi file <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtualeUpdate virtual machine files <br> Aggiornare i metadati della macchina virtualeUpdate virtual machine metadata |
| **Responsabile agente ESX** | File di configurazione <br> Modifica <br> Visualizza |
| **Estensione** | Registra estensione <br> Annullare la registrazione estensione <br> Aggiorna estensione |
| **Fornitore di statistiche esterne**| Register <br> Unregister  <br> Aggiornamento |
| **Cartella** | Creazione cartella <br> Elimina cartella <br> Sposta cartella <br> Rinomina cartella |
| **Globale** | Annulla attività <br> Pianificazione della capacità <br> Diagnostica <br> Metodi Disable <br> Metodi di abilitazione <br> Tag globale <br> Integrità <br> Licenze <br> Registra evento <br> Gestire gli attributi personalizzati <br> Proxy <br> Azione script <br> Responsabili dei servizi <br> Impostare un attributo personalizzato <br> Tag di sistema |
| **Provider di aggiornamento dell'integrità** | Register <br> Unregister  <br> Aggiornamento |
| **Configurazione > host** | Configurazione della partizione di archiviazione |
| **Inventario > host** | Modificare il cluster |
| **Applicazione di tag vSphere** | Assegnazione o annullamento dell'assegnazione del tag vSphere <br> Crea tag vSphere <br> Crea categoria di tag vSphere <br> Elimina tag vSphere <br> Elimina categoria di tag vSphere <br> Modifica tag vSphere <br> Modifica categoria tag vSphere <br> Modifica campo UsedBy per categoria <br> Modifica del campo UsedBy per tag |
| **Rete** | Assign network <br> Configurare <br> Sposta rete <br> Rimuovere |
| **Prestazioni** | Modificare gli intervalli |
| **Profilo host** | Visualizza |
| **Risorsa** | Applica suggerimento <br> Assegnare vApp al pool di risorseAssign vApp to resource pool <br> Assign virtual machine to resource pool <br> Creare un pool di risorseCreate resource pool <br> Eseguire la migrazione della macchina virtuale spentaMigrate powered off virtual machine <br> Eseguire la migrazione della macchina virtuale <br> Modificare il pool di risorseModify resource pool <br> Spostare il pool di risorse <br> Query vMotion <br> Rimuovere il pool di risorseRemove resource pool <br> Rinominare il pool di risorse |
| **Attività pianificata** | Creare attività <br> Modifica attività <br> Rimuovi attività <br> Esegui attività |
| **Sessioni** | Rappresentare l'utente <br> Message <br> Convalida sessione <br> Visualizzare e interrompere le sessioni |
| **Cluster di datastore** | Configurare un cluster di archivio datiConfigure a datastore cluster |
| **Archiviazione basata su profili** | Aggiornamento dello spazio di archiviazione basato sul profilo <br> Visualizzazione di archiviazione basata su profili |
| **Visualizzazioni di archiviazione** | Configurazione del servizio <br> Visualizza |
| **Attività** | Creare un'attività <br> Attività di aggiornamento |
| **Servizio di trasferimento**| Gestione <br> Monitorare |
| **Vapp** | Aggiungi macchina virtuale <br> Assegnare un pool di risorseAssign resource pool <br> Assegna vApp <br> Clone <br> Create <br> Delete <br> Esportazione <br> Importa <br> Spostamento <br> Spegnimento <br> Accendere <br> Rinominare <br> Sospendi <br> Unregister  <br> Visualizza ambiente OVF <br> Configurazione dell'applicazione vApp <br> Configurazione dell'istanza vApp <br> Configurazione di vApp managedBy <br> Configurazione delle risorse vApp |
| **VrMPolicy (Criteri)** | Query VRMPolicy <br> Aggiornare VRMPolicy |
| **Configurazione del > della macchina virtualeVirtual machine > Configuration** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungere o rimuovere un dispositivo <br> Avanzate <br> Modificare il numero di CPU <br> Modifica risorsa <br> Configurare managedBy <br> Rilevamento delle modifiche del disco <br> Lease del disco <br> Impostazioni di connessione dello schermo <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memoria <br> Modificare le impostazioni del dispositivo <br> Compatibilità con la tolleranza di errore delle queryQuery Fault Tolerance compatibility <br> Eseguire query su file non di clavamente <br> Dispositivo grezzo <br> Ricarica dal percorso <br> Rimuovi disco <br> Rinominare <br> Reimpostare le informazioni sull'ospite <br> Imposta annotazione <br> Impostazioni <br> Posizionamento dei file di scambio <br> Attiva/disattiva padre fork <br> Sbloccare la macchina virtuale <br> Aggiornare la compatibilità delle macchine virtualiUpgrade virtual machine compatibility |
| **Macchine virtuali > operazioni guestVirtual machine > Guest operations** | Modifica dell'alias dell'operazione guest <br> Query alias operazione guest <br> Modifiche alle operazioni guest <br> Esecuzione del programma operativo guest <br> Query sulle operazioni guest |
| **Interazione > macchina virtualeVirtual machine > Interaction** | Rispondere alla domanda <br> Operazione di backup nella macchina virtuale <br> Configurare il supporto CD <br> Configurare i supporti floppy <br> Interazione con la console <br> Crea screenshot <br> Deframmentare tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest tramite L'API VIX <br> Inserimento di codici di scansione HID USB <br> Pausa o Riattiva <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Sessione di riproduzione nella macchina virtualeReplay session on virtual machine <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospensione della tolleranza di errore <br> Failover di test <br> Testriavvio macchina virtuale secondariaTest restart Secondary VM <br> Disattivare la tolleranza di errore <br> Attivare la tolleranza di errore <br> Installazione di VMware Tools |
| **Inventario > macchine virtuali** | Crea da esistente <br> Creare un nuovo gruppo di risorse <br> Spostamento <br> Register <br> Rimuovere <br> Unregister  |
| **Provisioning di > di macchine virtualiVirtual machine > Provisioning** | Consenti accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti download macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Modello di clonazione <br> Clona macchina virtuale <br> Creare un modello da una macchina virtualeCreate template from virtual machine <br> Personalizza <br> Distribuire il modello <br> Segna come modello <br> Contrassegna come macchina virtuale <br> Modificare le specifiche di personalizzazione <br> Alzare di livello i dischi <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio > della macchina virtualeVirtual machine > Service configuration** | Allow notifications <br> Consentire il polling delle notifiche di eventi globali <br> Gestire le configurazioni dei servizi <br> Modificare la configurazione del servizio <br> Configurazioni del servizio di query <br> Leggere la configurazione del servizioRead service configuration |
| **Gestione snapshot > macchina virtualeVirtual machine > Snapshot management** | Creare snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristinare l'istantanea |
| **Replica vSphere > macchina virtuale** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **VService (servizio)** | Crea dipendenza <br> Distruggi la dipendenza <br> Riconfigurare la configurazione delle dipendenze <br> Aggiorna dipendenza |

### <a name="cloud-cluster-admin-role"></a>Ruolo di amministrazione del cluster cloud

| **Category** | **Privilegio** |
|----------|-----------|
| **Datastore** | Allocare spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configurare l'archivio dati <br> Operazioni sui file di basso livello <br> Rimuovi archivio dati <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtualeUpdate virtual machine files <br> Aggiornare i metadati della macchina virtualeUpdate virtual machine metadata |
| **Cartella** | Creazione cartella <br> Elimina cartella <br> Sposta cartella <br> Rinomina cartella |
| **Configurazione > host**  | Configurazione della partizione di archiviazione |
| **Applicazione di tag vSphere** | Assegnazione o annullamento dell'assegnazione del tag vSphere <br> Crea tag vSphere <br> Crea categoria di tag vSphere <br> Elimina tag vSphere <br> Elimina categoria di tag vSphere <br> Modifica tag vSphere <br> Modifica categoria tag vSphere <br> Modifica campo UsedBy per categoria <br> Modifica del campo UsedBy per tag |
| **Rete** | Assign network |
| **Risorsa** | Applica suggerimento <br> Assegnare vApp al pool di risorseAssign vApp to resource pool <br> Assign virtual machine to resource pool <br> Creare un pool di risorseCreate resource pool <br> Eseguire la migrazione della macchina virtuale spentaMigrate powered off virtual machine <br> Eseguire la migrazione della macchina virtuale <br> Modificare il pool di risorseModify resource pool <br> Spostare il pool di risorse <br> Query vMotion <br> Rimuovere il pool di risorseRemove resource pool <br> Rinominare il pool di risorse |
| **Vapp** | Aggiungi macchina virtuale <br> Assegnare un pool di risorseAssign resource pool <br> Assegna vApp <br> Clone <br> Create <br> Delete <br> Esportazione <br> Importa <br> Spostamento <br> Spegnimento <br> Accendere <br> Rinominare <br> Sospendi <br> Unregister  <br> Visualizza ambiente OVF <br> Configurazione dell'applicazione vApp <br> Configurazione dell'istanza vApp <br> Configurazione di vApp managedBy <br> Configurazione delle risorse vApp |
| **VrMPolicy (Criteri)** | Query VRMPolicy <br> Aggiornare VRMPolicy |
| **Configurazione del > della macchina virtualeVirtual machine > Configuration** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungere o rimuovere un dispositivo <br> Avanzate <br> Modificare il numero di CPU <br> Modifica risorsa <br> Configurare managedBy <br> Rilevamento delle modifiche del disco <br> Lease del disco <br> Impostazioni di connessione dello schermo <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memoria <br> Modificare le impostazioni del dispositivo <br> Compatibilità con la tolleranza di errore delle queryQuery Fault Tolerance compatibility <br> Eseguire query su file non di clavamente <br> Dispositivo grezzo <br> Ricarica dal percorso <br> Rimuovi disco <br> Rinominare <br> Reimpostare le informazioni sull'ospite <br> Imposta annotazione <br> Impostazioni <br> Posizionamento dei file di scambio <br> Attiva/disattiva padre fork <br> Sbloccare la macchina virtuale <br> Aggiornare la compatibilità delle macchine virtualiUpgrade virtual machine compatibility |
| **Macchine virtuali > operazioni guestVirtual machine > Guest operations** | Modifica dell'alias dell'operazione guest <br> Query alias operazione guest <br> Modifiche alle operazioni guest <br> Esecuzione del programma operativo guest <br> Query sulle operazioni guest |
| **Interazione > macchina virtualeVirtual machine > Interaction** | Rispondere alla domanda <br> Operazione di backup nella macchina virtuale <br> Configurare il supporto CD <br> Configurare i supporti floppy <br> Interazione con la console <br> Crea screenshot <br> Deframmentare tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest tramite L'API VIX <br> Inserimento di codici di scansione HID USB <br> Pausa o Riattiva <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Sessione di riproduzione nella macchina virtualeReplay session on virtual machine <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospensione della tolleranza di errore <br> Failover di test <br> Testriavvio macchina virtuale secondariaTest restart Secondary VM <br> Disattivare la tolleranza di errore <br> Attivare la tolleranza di errore <br> Installazione di VMware Tools
| **Inventario > macchine virtuali** | Crea da esistente <br> Creare un nuovo gruppo di risorse <br> Spostamento <br> Register <br> Rimuovere <br> Unregister  |
| **Provisioning di > di macchine virtualiVirtual machine > Provisioning** | Consenti accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti download macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Modello di clonazione <br> Clona macchina virtuale <br> Creare un modello da una macchina virtualeCreate template from virtual machine <br> Personalizza <br> Distribuire il modello <br> Segna come modello <br> Contrassegna come macchina virtuale <br> Modificare le specifiche di personalizzazione <br> Alzare di livello i dischi  <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio > della macchina virtualeVirtual machine > Service configuration** | Allow notifications <br> Consentire il polling delle notifiche di eventi globali <br> Gestire le configurazioni dei servizi <br> Modificare la configurazione del servizio <br> Configurazioni del servizio di query <br> Leggere la configurazione del servizioRead service configuration
| **Gestione snapshot > macchina virtualeVirtual machine > Snapshot management** | Creare snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristinare l'istantanea |
| **Replica vSphere > macchina virtuale** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **VService (servizio)** | Crea dipendenza <br> Distruggi la dipendenza <br> Riconfigurare la configurazione delle dipendenze <br> Aggiorna dipendenza |

### <a name="cloud-storage-admin-role"></a>Ruolo di amministrazione cloud

| **Category** | **Privilegio** |
|----------|-----------|
| **Datastore** | Allocare spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configurare l'archivio dati <br> Operazioni sui file di basso livello <br> Rimuovi archivio dati <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtualeUpdate virtual machine files <br> Aggiornare i metadati della macchina virtualeUpdate virtual machine metadata |
| **Configurazione > host** | Configurazione della partizione di archiviazione |
| **Cluster di datastore** | Configurare un cluster di archivio datiConfigure a datastore cluster |
| **Archiviazione basata su profili** | Aggiornamento dello spazio di archiviazione basato sul profilo <br> Visualizzazione di archiviazione basata su profili |
| **Visualizzazioni di archiviazione** | Configurazione del servizio <br> Visualizza |

### <a name="cloud-network-admin-role"></a>Ruolo di amministrazione della rete cloud

| **Category** | **Privilegio** |
|----------|-----------|
| **dvPort** | Create <br> Delete <br> Modifica <br> Funzionamento della politica <br> Operazione di ambito |
| **Rete** | Assign network <br> Configurare <br> Sposta rete <br> Rimuovere |
| **Configurazione del > della macchina virtualeVirtual machine > Configuration** | Modificare le impostazioni del dispositivo |

### <a name="cloud-vm-admin-role"></a>Ruolo di amministrazione cloudCloud-VM-Admin-Role

| **Category** | **Privilegio** |
|----------|-----------|
| **Datastore** | Allocare spazio <br> Browse Datastore (Sfoglia archivio dati) |
| **Rete** | Assign network |
| **Risorsa** | Assign virtual machine to resource pool <br> Eseguire la migrazione della macchina virtuale spentaMigrate powered off virtual machine <br> Eseguire la migrazione della macchina virtuale
| **Vapp** | Esportazione <br> Importa |
| **Configurazione del > della macchina virtualeVirtual machine > Configuration** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungere o rimuovere un dispositivo <br> Avanzate <br> Modificare il numero di CPU <br> Modifica risorsa <br> Configurare managedBy <br> Rilevamento delle modifiche del disco <br> Lease del disco <br> Impostazioni di connessione dello schermo <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memoria <br> Modificare le impostazioni del dispositivo <br> Compatibilità con la tolleranza di errore delle queryQuery Fault Tolerance compatibility <br> Eseguire query su file non di clavamente <br> Dispositivo grezzo <br> Ricarica dal percorso <br> Rimuovi disco <br> Rinominare <br> Reimpostare le informazioni sull'ospite <br> Imposta annotazione <br> Impostazioni <br> Posizionamento dei file di scambio <br> Attiva/disattiva padre fork <br> Sbloccare la macchina virtuale <br> Aggiornare la compatibilità delle macchine virtualiUpgrade virtual machine compatibility |
| **Macchine virtuali >operazioni guestVirtual machine >Guest operations** | Modifica dell'alias dell'operazione guest <br> Query alias operazione guest <br> Modifiche alle operazioni guest <br> Esecuzione del programma operativo guest <br> Query sulle operazioni guest    |
| **Interazione >macchina virtualeVirtual machine >Interaction** | Rispondere alla domanda <br> Operazione di backup nella macchina virtuale <br> Configurare il supporto CD <br> Configurare i supporti floppy <br> Interazione con la console <br> Crea screenshot <br> Deframmentare tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest tramite L'API VIX <br> Inserimento di codici di scansione HID USB <br> Pausa o Riattiva <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Sessione di riproduzione nella macchina virtualeReplay session on virtual machine <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospensione della tolleranza di errore <br> Failover di test <br> Testriavvio macchina virtuale secondariaTest restart Secondary VM <br> Disattivare la tolleranza di errore <br> Attivare la tolleranza di errore <br> Installazione di VMware Tools |
| **Inventario >macchine virtuali** | Crea da esistente <br> Creare un nuovo gruppo di risorse <br> Spostamento <br> Register <br> Rimuovere <br> Unregister  |
| **Provisioning di >di macchine virtualiVirtual machine >Provisioning** | Consenti accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti download macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Modello di clonazione <br> Clona macchina virtuale <br> Creare un modello da una macchina virtualeCreate template from virtual machine <br> Personalizza <br> Distribuire il modello <br> Segna come modello <br> Contrassegna come macchina virtuale <br> Modificare le specifiche di personalizzazione <br> Alzare di livello i dischi <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio >della macchina virtualeVirtual machine >Service configuration** | Allow notifications <br> Consentire il polling delle notifiche di eventi globali <br> Gestire le configurazioni dei servizi <br> Modificare la configurazione del servizio <br> Configurazioni del servizio di query <br> Leggere la configurazione del servizioRead service configuration
| **Gestione snapshot della macchina virtuale >** | Creare snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristinare l'istantanea |
| **Replica vSphere >macchina virtualeVirtual machine >vSphere Replication** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **VService (servizio)** | Crea dipendenza <br> Distruggi la dipendenza <br> Riconfigurare la configurazione delle dipendenze <br> Aggiorna dipendenza |
