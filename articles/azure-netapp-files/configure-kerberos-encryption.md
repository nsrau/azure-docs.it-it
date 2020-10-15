---
title: Configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files | Microsoft Docs
description: Viene descritto come configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files e l'effetto sulle prestazioni.
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
ms.topic: how-to
ms.date: 9/29/2020
ms.author: b-juche
ms.openlocfilehash: b683719fa2d0c1e7b5333c2ddf9c93f2797ade9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461479"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Configurare la crittografia Kerberos NFSv 4.1 per Azure NetApp Files

Azure NetApp Files supporta la crittografia client NFS in modalità Kerberos (krb5, Krb5i e krb5p) con crittografia AES-256. Questo articolo descrive le configurazioni necessarie per l'uso di un volume NFSv 4.1 con la crittografia Kerberos.

## <a name="requirements"></a>Requisiti

I requisiti seguenti si applicano alla crittografia client NFSv 4.1: 

* Connessione Active Directory Domain Services (AD DS) per facilitare la emissione di ticket Kerberos 
* Creazione di record DNS A/PTR per gli indirizzi IP del server client e Azure NetApp Files NFS
* Un client Linux  
    Questo articolo fornisce informazioni aggiuntive per i client RHEL e Ubuntu.  Altri client funzioneranno con passaggi di configurazione analoghi. 
* Accesso al server NTP  
    È possibile utilizzare uno dei controller di dominio di Dominio di Active Directory controller (DC) usati comunemente.

## <a name="create-an-nfs-kerberos-volume"></a>Creare un volume NFS Kerberos

1.  Seguire i passaggi in [creare un volume NFS per Azure NetApp files](azure-netapp-files-create-volumes.md) per creare il volume NFSv 4.1.   

    Nella pagina Crea volume impostare la versione NFS su **NFSv 4.1**e impostare Kerberos su **abilitato**.

    > [!IMPORTANT] 
    > Non è possibile modificare la selezione dell'abilitazione Kerberos dopo la creazione del volume.

    ![Crea volume Kerberos NFSv 4.1](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Selezionare **Esporta criteri** in modo che corrisponda al livello di accesso e all'opzione di sicurezza desiderati (Kerberos 5, Kerberos 5i o Kerberos 5p) per il volume.   

    Per l'effetto sulle prestazioni di Kerberos, vedere l' [effetto sulle prestazioni di Kerberos in NFSv 4.1](#kerberos_performance).  

    È anche possibile modificare i metodi di sicurezza Kerberos per il volume facendo clic su Esporta criterio nel riquadro di spostamento Azure NetApp Files.

3.  Fare clic su **Verifica + crea** per creare il volume NFSv 4.1.

## <a name="configure-the-azure-portal"></a>Verrà configurato il portale di Azure 

1.  Seguire le istruzioni riportate in [creare una Active Directory connessione](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Per Kerberos è necessario creare almeno un account del computer in Active Directory. Le informazioni sull'account fornite vengono usate per creare gli account per i volumi Kerberos SMB *e* NFSv 4.1. Questo computer viene creato automaticamente durante la creazione del volume.

2.  In **area di autenticazione Kerberos**immettere il **nome del server ad** e l'indirizzo **IP KDC** .

    Il server AD e l'IP KDC possono essere lo stesso server. Queste informazioni vengono utilizzate per creare l'account del computer SPN utilizzato da Azure NetApp Files. Dopo aver creato l'account del computer, Azure NetApp Files utilizzerà i record del server DNS per individuare i server KDC aggiuntivi in base alle esigenze. 

    ![Area di autenticazione Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Fare clic su **Aggiungi** per salvare la configurazione.

## <a name="configure-active-directory-connection"></a>Configurare la connessione Active Directory 

La configurazione di NFSv 4.1 Kerberos crea due account computer in Active Directory:
* Un account computer per le condivisioni SMB
* Un account computer per NFSv 4.1: è possibile identificare questo account tramite il prefisso `NFS-` . 

Dopo aver creato il primo volume Kerberos NFSv 4.1, impostare il tipo di crittografia o l'account del computer usando il comando PowerShell seguente:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Configurare il client NFS 

Per configurare il client NFS, seguire le istruzioni riportate in [configurare un client NFS per Azure NetApp files](configure-nfs-clients.md) .  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Montare il volume Kerberos NFS

1. Nella pagina **volumi** selezionare il volume NFS che si desidera montare.

2. Selezionare **istruzioni di montaggio** dal volume per visualizzare le istruzioni.

    Ad esempio: 

    ![Istruzioni di montaggio per i volumi Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Creare la directory (punto di montaggio) per il nuovo volume.  

4. Impostare il tipo di crittografia predefinito su AES 256 per l'account computer:  
    `Set-ADComputer $COMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * È necessario eseguire questo comando solo una volta per ogni account computer.
    * È possibile eseguire questo comando da un controller di dominio o da un PC in cui è installato strumenti di [amministrazione remota](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) del server. 
    * La `$COMPUTERACCOUNT` variabile è l'account computer creato in Active Directory quando si distribuisce il volume Kerberos. Si tratta dell'account con prefisso `NFS-` . 
    * La `$ANFSERVICEACCOUNT` variabile è un account utente Active Directory senza privilegi con controlli delegati sull'unità organizzativa in cui è stato creato l'account computer. 

5. Montare il volume nell'host: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * La `$ANFEXPORT` variabile è il `host:/export` percorso trovato nelle istruzioni di montaggio.
    * La `$ANFMOUNTPOINT` variabile è la cartella creata dall'utente nell'host Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Conseguenze sulle prestazioni di Kerberos in NFSv 4.1 

Questa sezione consente di comprendere l'effetto sulle prestazioni di Kerberos in NFSv 4.1.

### <a name="available-security-options"></a>Opzioni di sicurezza disponibili 

Le opzioni di sicurezza attualmente disponibili per i volumi NFSv 4.1 sono le seguenti: 

* **sec = sys** USA UID UNIX locali e gid usando AUTH_SYS per autenticare le operazioni NFS.
* **sec = krb5** USA Kerberos V5 anziché gli UID UNIX locali e GID per autenticare gli utenti.
* **sec = krb5i** USA Kerberos V5 per l'autenticazione utente ed esegue il controllo dell'integrità delle operazioni NFS usando i checksum protetti per impedire la manomissione dei dati.
* **sec = krb5p** USA Kerberos V5 per l'autenticazione degli utenti e il controllo dell'integrità. Crittografa il traffico NFS per impedire lo sniffing del traffico. Questa opzione è l'impostazione più sicura, ma comporta anche il sovraccarico delle prestazioni.

### <a name="performance-vectors-tested"></a>Vettori di prestazioni testati

In questa sezione viene descritto il singolo effetto sulle prestazioni lato client delle varie `sec=*` Opzioni.

* L'effetto sulle prestazioni è stato testato a due livelli: concorrenza bassa (carico basso) e concorrenza elevata (limiti massimi di I/O e velocità effettiva).  
* Sono stati testati tre tipi di carichi di lavoro:  
    * Operazioni di lettura/scrittura casuali di piccole dimensioni (usando FIO)
    * Operazioni di lettura/scrittura sequenziali di grandi dimensioni (con FIO)
    * Carichi di lavoro pesanti dei metadati generati da applicazioni come Git

### <a name="expected-performance-impact"></a>Conseguenze sulle prestazioni previste 

Ci sono due aree di interesse: caricamento leggero e limite superiore. Gli elenchi seguenti descrivono l'impostazione di sicurezza relativa all'effetto sulle prestazioni in base alle impostazioni di sicurezza e allo scenario. Tutti i confronti vengono eseguiti in base al `sec=sys` parametro di sicurezza. Il test è stato eseguito su un singolo volume, usando un singolo client. 

Conseguenze sulle prestazioni di krb5:

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,3 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * La velocità effettiva massima sequenziale non è stata influenzata da krb5.
    * Il numero massimo di I/O casuali diminuisce del 30% per i carichi di lavoro di lettura puri con l'effetto complessivo che si sposta su zero quando il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

Conseguenze sulle prestazioni di Krb5i: 

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,5 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * Il numero massimo di velocità effettiva sequenziale è diminuito del 70% a prescindere dalla combinazione del carico di lavoro.
    * Il numero massimo di I/O casuali diminuisce del 50% per i carichi di lavoro di lettura puri con l'effetto complessivo che diminuisce fino al 25% mentre il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

Conseguenze sulle prestazioni di krb5p:

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,8 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * Il numero massimo di velocità effettiva sequenziale è diminuito del 85% a prescindere dalla combinazione del carico di lavoro. 
    * Il numero massimo di I/O casuali diminuisce del 65% per i carichi di lavoro di lettura puri con l'effetto complessivo che diminuisce fino al 43% mentre il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

## <a name="next-steps"></a>Passaggi successivi  

* [Domande frequenti sulla Azure NetApp Files](azure-netapp-files-faqs.md)
* [Creare un volume NFS per Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Creare una connessione Active Directory](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Configurare un client NFS per Azure NetApp Files](configure-nfs-clients.md) 
