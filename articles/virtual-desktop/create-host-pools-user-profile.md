---
title: Condivisione contenitore del profilo FSLogix per desktop virtuale Windows-Azure
description: Come configurare un contenitore del profilo FSLogix per un pool host di desktop virtuali Windows usando una condivisione file basata su macchina virtuale.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002595"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Creare un contenitore di profili per un pool di host con una condivisione file

Il servizio desktop virtuale di Windows offre i contenitori del profilo FSLogix come soluzione di profilo utente consigliata. Non è consigliabile usare la soluzione disco profilo utente (UPD), che verrà deprecata nelle versioni future del desktop virtuale di Windows.

Questo articolo illustra come configurare una condivisione del contenitore del profilo FSLogix per un pool host usando una condivisione file basata su macchina virtuale. È consigliabile usare File di Azure anziché le condivisioni file. Per ulteriori informazioni sulla documentazione di FSLogix, vedere il [sito di FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Se si sta cercando materiale di confronto sulle diverse opzioni di archiviazione del contenitore del profilo FSLogix in Azure, vedere [Opzioni di archiviazione per i contenitori di profili FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Creare una nuova macchina virtuale che fungerà da condivisione file

Quando si crea la macchina virtuale, assicurarsi di inserirla nella stessa rete virtuale delle macchine virtuali del pool host o in una rete virtuale con connettività alle macchine virtuali del pool host. È possibile creare una macchina virtuale in diversi modi:

- [Creare una macchina virtuale da un'immagine della raccolta di Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestita](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Creare una macchina virtuale da un'immagine non gestita](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Dopo aver creato la macchina virtuale, aggiungerla al dominio effettuando le operazioni seguenti:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Nella macchina virtuale aprire il **Pannello di controllo** e selezionare **Sistema**.
3. Selezionare **Nome computer**, selezionare **Modifica impostazioni** e quindi selezionare **Modifica**
4. Selezionare **Dominio** e quindi immettere il dominio di Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio che dispone dei privilegi per l'aggiunta di computer al dominio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparare la macchina virtuale in modo che funga da condivisione file per i profili utente

Di seguito sono riportate le istruzioni generali su come preparare una macchina virtuale da utilizzare come condivisione file per i profili utente:

1. Aggiungere il desktop virtuale Windows Active Directory utenti a un [gruppo di sicurezza Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Questo gruppo di sicurezza verrà usato per autenticare gli utenti di desktop virtuali Windows nella macchina virtuale di condivisione file appena creata.
2. [Connettersi alla macchina virtuale di condivisione file](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Nella macchina virtuale di condivisione file creare una cartella nell' **unità C** che verrà usata come condivisione del profilo.
4. Fare clic con il pulsante destro del mouse sulla nuova cartella, scegliere **Proprietà**, selezionare **condivisione**, quindi scegliere **condivisione avanzata.**
5. Selezionare **Condividi questa cartella**, selezionare **autorizzazioni...**, quindi selezionare **Aggiungi...**.
6. Cercare il gruppo di sicurezza a cui sono stati aggiunti gli utenti del desktop virtuale di Windows, quindi verificare che il gruppo disponga del **controllo completo**.
7. Dopo aver aggiunto il gruppo di sicurezza, fare clic con il pulsante destro del mouse sulla cartella, scegliere **Proprietà**, selezionare **condivisione**, quindi copiare il **percorso di rete** da utilizzare per un momento successivo.

Per ulteriori informazioni sulle autorizzazioni, vedere la [documentazione di FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Configurare il contenitore del profilo FSLogix

Per configurare le macchine virtuali con il software FSLogix, eseguire le operazioni seguenti in ogni computer registrato nel pool host:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali specificate durante la creazione della macchina virtuale.
2. Avviare un browser Internet e passare a [questo collegamento](https://go.microsoft.com/fwlink/?linkid=2084562) per scaricare l'agente FSLogix.
3. Passare a \\ \\ versione Win32 \\ o \\ \\ versione x64 \\ nel file con estensione zip ed eseguire **FSLogixAppsSetup** per installare l'agente FSLogix.  Per ulteriori informazioni su come installare FSLogix, vedere [scaricare e installare FSLogix](/fslogix/install-ht/).
4. Passare a **programmi**  >  **FSLogix**  >  **app** per verificare che l'agente sia installato.
5. Dal menu Start eseguire **Regedit** come amministratore. Passare a **Computer \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.
6. Creare una chiave denominata **profili**.
7. Creare i valori seguenti per la chiave dei profili:

| Nome                | Type               | Dati/valore                        |
|---------------------|--------------------|-----------------------------------|
| Attivato             | DWORD              | 1                                 |
| VHDLocations        | Valore multistringa | "Percorso di rete per la condivisione file"     |

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md).
