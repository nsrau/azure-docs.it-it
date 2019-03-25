---
title: Configurare una condivisione di profilo utente per un pool di host di anteprima di Desktop virtuale Windows - Azure
description: Come configurare un contenitore di profilo FSLogix per un pool di host Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c9c2ca2cc27c5fa757b8ff6846e0a6a8f7087875
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403715"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Configurare una condivisione di profilo utente per un pool di host

Il servizio di Windows Virtual Desktop Preview offre contenitori profilo FSLogix come soluzione consigliata per l'utente del profilo. Non è consigliabile usare la soluzione disco profili utente (UPD) e nelle versioni future di Windows Desktop virtuale verrà deprecato.

In questa sezione indicano come configurare una condivisione di contenitore FSLogix profilo per un pool di host.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Creare una nuova macchina virtuale che verrà utilizzato come una condivisione file

Quando si crea la macchina virtuale, assicurarsi di inserirlo in entrambi la stessa rete virtuale le macchine virtuali del pool di host o in una rete virtuale con connettività per le macchine virtuali del pool di host. È possibile creare una macchina virtuale in più modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Dopo aver creato la macchina virtuale, aggiungerla al dominio effettuando le operazioni seguenti:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Nella macchina virtuale, avviare **Pannello di controllo** e selezionare **sistema**.
3. Selezionare **nome Computer**, selezionare **modificare le impostazioni**, quindi selezionare **modifica...**
4. Selezionare **dominio** e quindi immettere il dominio di Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che disponga dei privilegi per le macchine di aggiunta al dominio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparare la macchina virtuale da usare come una condivisione file per i profili utente

Ecco le istruzioni generali su come preparare una macchina virtuale da usare come una condivisione file per i profili utente:

1. Aggiungere le macchine virtuali host sessione a un [gruppo di sicurezza di Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Questo gruppo di sicurezza da utilizzare per autenticare le macchine virtuali host sessione per la macchina virtuale di condivisione file che appena creato.
2. [Connettersi alla macchina virtuale di condivisione file](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Nella macchina virtuale di condivisione file, creare una cartella nel **unità C** che verrà usato come la condivisione dei profili.
4. Fare clic su nuova cartella, selezionare **delle proprietà**, selezionare **Sharing**, quindi selezionare **di condivisione avanzate...** .
5. Selezionare **Condividi questa cartella**, selezionare **autorizzazioni...** , quindi selezionare **Aggiungi...** .
6. Cercare il gruppo di sicurezza a cui è stato aggiunto macchine virtuali host sessione e quindi verificare che tale gruppo abbia **controllo completo**.
7. Dopo aver aggiunto il gruppo di sicurezza, fare clic su nella cartella, selezionare **delle proprietà**, selezionare **condivisione**, quindi copiare il **percorso di rete** da utilizzare per un uso successivo.

Per le procedure consigliate sulle autorizzazioni, vedere gli argomenti seguenti [FSLogix documentazione](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Configurare il contenitore di profilo FSLogix

Per configurare le macchine virtuali con il software FSLogix, eseguire le operazioni seguenti in ogni computer registrato per il pool di host:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Avviare un browser internet e passare al seguente [collegamento](https://go.microsoft.com/fwlink/?linkid=2084562) per scaricare l'agente FSLogix. Durante l'anteprima pubblica di Desktop virtuale Windows, si otterrà un codice di licenza per attivare il software FSLogix. La chiave è il file LicenseKey.txt incluso nel file con estensione zip FSLogix dell'agente.
3. Installare l'agente FSLogix.
4. Passare a **Program Files** > **FSLogix** > **app** per confermare l'agente installato.
5. Dal menu start, eseguire **RegEdit** come amministratore. Passare a **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix\\profili**
6. Creare i seguenti valori:

| NOME                | Type               | / Valore dei dati                        |
|---------------------|--------------------|-----------------------------------|
| Attivato             | DWORD              | 1                                 |
| VHDLocations        | Valore multistringa | "Percorso di rete per la condivisione file" |
| VolumeType          | string             |  VHDX                              |
| SizeInMBs           | DWORD              | "numero intero per le dimensioni del profilo"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
