---
title: Novità di Azure NetApp Files | Microsoft Docs
description: Viene fornito un riepilogo delle nuove funzionalità e dei miglioramenti più recenti di Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: beb1cc9957f7b43bc6ad9b9b6ee6c7707eb0c2d6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571548"
---
# <a name="whats-new-in-azure-netapp-files"></a>Novità di Azure NetApp Files

Il servizio Azure NetApp Files viene aggiornato regolarmente. Questo articolo fornisce un riepilogo delle nuove funzionalità e dei miglioramenti più recenti. 

## <a name="september-2020"></a>Settembre 2020

* [Replica tra più aree di Azure NetApp Files](cross-region-replication-introduction.md) (anteprima pubblica)

  Azure NetApp Files supporta ora la replica tra più aree. Con questa nuova funzionalità di ripristino di emergenza, è possibile replicare i volumi di Azure NetApp Files da un'area di Azure a un'altra in modo rapido e conveniente, proteggendo i dati da errori imprevedibili a livello di area. La replica tra più aree di Azure NetApp Files sfrutta la tecnologia NetApp SnapMirror® e vengono inviati in rete solo i blocchi modificati, in un formato compresso ed efficiente. Questa tecnologia proprietaria riduce al minimo la quantità di dati che è necessario replicare tra le aree, consentendo così di risparmiare sui costi di trasferimento dei dati. Viene inoltre ridotto il tempo necessario per la replica, quindi è possibile ottenere un obiettivo del punto di ripristino (RPO) più piccolo.

* [Pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md) (anteprima)  

    In un pool di capacità QoS manuale è possibile assegnare la capacità e la velocità effettiva per un volume in modo indipendente. La velocità effettiva totale di tutti i volumi creati con un pool di capacità QoS manuale è limitata dalla velocità effettiva totale del pool. Il suo valore è determinato dalla combinazione della velocità effettiva del livello di servizio e delle dimensioni del pool. In alternativa, il [tipo QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) di un pool di capacità può essere automatico, che è l'impostazione predefinita. In un pool di capacità QoS automatico la velocità effettiva viene assegnata automaticamente ai volumi nel pool, proporzionalmente alla quota di dimensioni assegnata ai volumi.

* [Firma LDAP](azure-netapp-files-create-volumes-smb.md) (anteprima)   

    Azure NetApp Files supporta ora la firma LDAP per le ricerche LDAP sicure tra il servizio Azure NetApp Files e i controller di dominio di Active Directory Domain Services specificati dall'utente. Questa funzionalità è attualmente in anteprima.

* [Crittografia AES per l'autenticazione di AD](azure-netapp-files-create-volumes-smb.md) (anteprima)

    Azure NetApp Files supporta ora la crittografia AES sulla connessione LDAP al controller di dominio per abilitare la crittografia AES per un volume SMB. Questa funzionalità è attualmente in anteprima. 

* Nuove [metriche](azure-netapp-files-metrics.md):   

    * Nuove metriche per i volumi: 
        * *Dimensioni allocate del volume*: dimensioni di un volume di cui è stato effettuato il provisioning
    * Nuove metriche per i pool: 
        * *Dimensioni allocate del pool*: dimensioni del pool di cui è stato effettuato il provisioning 
        * *Dimensioni totali degli snapshot per il pool*: somma delle dimensioni degli snapshot di tutti i volumi nel pool

## <a name="july-2020"></a>Luglio 2020

* [Volume con doppio protocollo (NFSv3 e SMB)](create-volumes-dual-protocol.md)

    È ora possibile creare un volume Azure NetApp Files che consente l'accesso simultaneo a un doppio protocollo (NFS v3 e SMB) con supporto per il mapping utente LDAP. Questa funzionalità consente casi d'uso in cui è possibile avere un carico di lavoro basato su Linux che genera e archivia i dati in un volume Azure NetApp Files. Contemporaneamente, il personale deve usare client e software basati su Windows per analizzare i nuovi dati generati dallo stesso volume Azure NetApp Files. La funzionalità di accesso simultaneo al doppio protocollo elimina la necessità di copiare i dati generati dal carico di lavoro in un volume separato con un protocollo diverso per la post-analisi, consentendo di risparmiare sui costi di archiviazione e sul tempo operativo. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è disponibile a livello generale. Per altre informazioni, vedere la [documentazione sull'accesso simultaneo al doppio protocollo](create-volumes-dual-protocol.MD).

* [Crittografia Kerberos NFS v4.1 in transito](configure-kerberos-encryption.MD)

    Azure NetApp Files supporta ora la crittografia client NFS nelle modalità Kerberos (krb5, krb5i e krb5p) con la crittografia AES-256, per una maggiore sicurezza dei dati. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è disponibile a livello generale. Per altre informazioni, vedere la [documentazione sulla crittografia Kerberos NFS v4.1](configure-kerberos-encryption.MD).

* [Modifica del livello di servizio del volume dinamico](dynamic-change-volume-service-level.MD)

    Il cloud offre flessibilità nella spesa IT. È ora possibile modificare il livello di servizio di un volume Azure NetApp Files esistente spostando il volume in un altro pool di capacità che usa il livello di servizio desiderato. Questa modifica sul posto del livello di servizio per il volume non richiede la migrazione dei dati. Non ha inoltre alcun impatto sull'accesso del piano dati al volume. È possibile modificare un volume esistente per usare un livello di servizio superiore per ottenere prestazioni migliori oppure per usare un livello di servizio inferiore per l'ottimizzazione dei costi. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è attualmente disponibile in anteprima pubblica. È possibile eseguire la registrazione per l'anteprima della funzionalità seguendo quanto indicato nella [documentazione sulla modifica del livello di servizio del volume dinamico](dynamic-change-volume-service-level.md).

* [Criteri snapshot del volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (anteprima) 

    Azure NetApp Files consente di creare snapshot temporizzati dei volumi. È ora possibile creare criteri snapshot per fare in modo che Azure NetApp Files crei automaticamente snapshot del volume in base a una frequenza definita. È possibile pianificare l'esecuzione degli snapshot in cicli orari, giornalieri, settimanali o mensili. È anche possibile specificare il numero massimo di snapshot da conservare nell'ambito dei criteri snapshot. Questa funzionalità è gratuita (si applica il normale [costo di archiviazione di Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/)) ed è attualmente disponibile in anteprima. È possibile eseguire la registrazione per l'anteprima della funzionalità seguendo quanto indicato nella [documentazione sui criteri snapshot del volume](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

* [Criteri di esportazione dell'accesso radice NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files consente ora di specificare se l'account radice può accedere al volume. 

* [Opzione Nascondi percorso snapshot](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files consente ora di specificare se un utente può visualizzare la directory `.snapshot` (client NFS) o la cartella `~snapshot` (client SMB) in un volume montato e accedervi.

## <a name="may-2020"></a>Maggio 2020

* [Utenti dei criteri di backup](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (anteprima)

    Azure NetApp Files consente di includere account aggiuntivi che richiedono privilegi elevati per l'account computer creato per l'uso con Azure NetApp Files. Agli account specificati sarà consentito modificare le autorizzazioni NTFS a livello di file o di cartella. È ad esempio possibile specificare un account del servizio senza privilegi usato per la migrazione dei dati a una condivisione file SMB in Azure NetApp Files. La funzionalità Utenti dei criteri di backup è attualmente in anteprima.

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su Azure NetApp Files](azure-netapp-files-introduction.md)
* [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
