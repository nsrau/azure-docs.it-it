---
title: Creare un contenitore di profili FSLogix per un pool host usando una condivisione file basata su macchina virtuale-Azure
description: Come configurare un contenitore del profilo FSLogix per un pool host di desktop virtuali Windows usando una condivisione file basata su macchina virtuale.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: 9b187696524e96bc13254a24fd8f39d5aeb89e7d
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676690"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Creare un contenitore di profili per un pool di host con una condivisione file

Il servizio desktop virtuale di Windows offre i contenitori del profilo FSLogix come soluzione di profilo utente consigliata. Non è consigliabile usare la soluzione disco profilo utente (UPD), che verrà deprecata nelle versioni future del desktop virtuale di Windows.

Questo articolo illustra come configurare una condivisione del contenitore del profilo FSLogix per un pool host usando una condivisione file basata su macchina virtuale. Per ulteriori informazioni sulla documentazione di FSLogix, vedere il [sito di FSLogix](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Creare una nuova macchina virtuale che fungerà da condivisione file

Quando si crea la macchina virtuale, assicurarsi di inserirla nella stessa rete virtuale delle macchine virtuali del pool host o in una rete virtuale con connettività alle macchine virtuali del pool host. È possibile creare una macchina virtuale in diversi modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Dopo aver creato la macchina virtuale, aggiungerla al dominio effettuando le operazioni seguenti:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Nella macchina virtuale avviare il **Pannello di controllo** e selezionare **sistema**.
3. Selezionare **nome computer**, fare clic su **Modifica impostazioni**e quindi selezionare **modifica...**
4. Selezionare **dominio** e quindi immettere il dominio Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che disponga dei privilegi per i computer aggiunti al dominio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparare la macchina virtuale in modo che funga da condivisione file per i profili utente

Di seguito sono riportate le istruzioni generali su come preparare una macchina virtuale da utilizzare come condivisione file per i profili utente:

1. Aggiungere il desktop virtuale Windows Active Directory utenti a un [gruppo di sicurezza Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Questo gruppo di sicurezza verrà usato per autenticare gli utenti di desktop virtuali Windows nella macchina virtuale di condivisione file appena creata.
2. [Connettersi alla macchina virtuale di condivisione file](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Nella macchina virtuale di condivisione file creare una cartella nell' **unità C** che verrà usata come condivisione del profilo.
4. Fare clic con il pulsante destro del mouse sulla nuova cartella, scegliere **Proprietà**, selezionare **condivisione**, quindi scegliere **condivisione avanzata.**
5. Selezionare **Condividi questa cartella**, selezionare **autorizzazioni...** , quindi selezionare **Aggiungi...** .
6. Cercare il gruppo di sicurezza a cui sono stati aggiunti gli utenti del desktop virtuale di Windows, quindi verificare che il gruppo disponga del **controllo completo**.
7. Dopo aver aggiunto il gruppo di sicurezza, fare clic con il pulsante destro del mouse sulla cartella, scegliere **Proprietà**, selezionare **condivisione**, quindi copiare il **percorso di rete** da utilizzare per un momento successivo.

Per ulteriori informazioni sulle autorizzazioni, vedere la [documentazione di FSLogix](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht).

## <a name="configure-the-fslogix-profile-container"></a>Configurare il contenitore del profilo FSLogix

Per configurare le macchine virtuali con il software FSLogix, eseguire le operazioni seguenti in ogni computer registrato nel pool host:

1. [Connettersi alla macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Avviare un browser Internet e passare a [questo collegamento](https://go.microsoft.com/fwlink/?linkid=2084562) per scaricare l'agente FSLogix.
3. Passare a \\ @ no__t-1Win32 @ no__t-2Release o \\ @ no__t-4X64 @ no__t-5Data nel file con estensione zip ed eseguire **FSLogixAppsSetup** per installare l'agente FSLogix.  Per ulteriori informazioni su come installare FSLogix, vedere [scaricare e installare FSLogix](https://docs.microsoft.com/fslogix/install-ht).
4. Passare a **programmi** > **FSLogix** > **app** per verificare che l'agente sia installato.
5. Dal menu Start eseguire **Regedit** come amministratore. Passare al **computer @ no__t-1HKEY_LOCAL_MACHINE @ no__t-2Software @ no__t-3FSLogix**.
6. Creare una chiave denominata **profili**.
7. Creare i valori seguenti per la chiave dei profili:

| Attività                | Type               | Dati/valore                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Valore multistringa | "Percorso di rete per la condivisione file"     |

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).