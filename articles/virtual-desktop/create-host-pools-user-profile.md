---
title: Condivisione del contenitore di profili FSLogix di Windows Virtual Desktop - Azure
description: Come configurare un contenitore di profili FSLogix per un pool host di Windows Virtual Desktop usando una condivisione file basata su macchina virtuale.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250920"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Creare un contenitore di profili per un pool di host con una condivisione file

Il servizio Desktop virtuale di Windows offre i contenitori di profili FSLogix come soluzione di profilo utente consigliata. Non è consigliabile utilizzare la soluzione User Profile Disk (UPD), che sarà deprecata nelle versioni future di Windows Virtual Desktop.

Questo articolo illustra come configurare una condivisione del contenitore di profili FSLogix per un pool host usando una condivisione file basata su macchina virtuale. Per ulteriori informazioni sulla documentazione di FSLogix, vedere il [sito FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Per informazioni sul confronto tra le diverse opzioni di archiviazione del contenitore di profili FSLogix in Azure, vedere Opzioni di archiviazione per i [contenitori di profili FSLogix.](store-fslogix-profile.md)

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Creare una nuova macchina virtuale che fungerà da condivisione fileCreate a new virtual machine that will act as a file share

Quando si crea la macchina virtuale, assicurarsi di inserirla nella stessa rete virtuale delle macchine virtuali del pool host o in una rete virtuale con connettività alle macchine virtuali del pool host. È possibile creare una macchina virtuale in diversi modi:You can create a virtual machine in multiple ways:

- [Creare una macchina virtuale da un'immagine della raccolta di AzureCreate a virtual machine from an Azure Gallery image](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creare una macchina virtuale da un'immagine gestitaCreate a virtual machine from a managed image](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Creare una macchina virtuale da un'immagine non gestitaCreate a virtual machine from an unmanaged image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Dopo aver creato la macchina virtuale, aggiungerla al dominio eseguendo le operazioni seguenti:After creating the virtual machine, join it to the domain by doing the following things:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Nella macchina virtuale avviare **il Pannello di controllo** e selezionare **Sistema**.
3. Selezionare **Nome computer**, **Modifica impostazioni**e quindi **Cambia...**
4. Selezionare **Dominio** e quindi immettere il dominio di Active Directory nella rete virtuale.
5. Eseguire l'autenticazione con un account di dominio con privilegi per i computer di aggiunta al dominio.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Preparare la macchina virtuale per fungere da condivisione file per i profili utentePrepare the virtual machine to act as a file share for user profiles

Di seguito sono riportate le istruzioni generali su come preparare una macchina virtuale che funga da condivisione file per i profili utente:The following are general instructions about how to prepare a virtual machine to act as a file share for user profiles:

1. Aggiungere gli utenti di Active Directory di Windows Virtual Desktop a un gruppo di protezione di [Active Directory.](/windows/security/identity-protection/access-control/active-directory-security-groups/) Questo gruppo di sicurezza verrà utilizzato per autenticare gli utenti di Windows Virtual Desktop nella macchina virtuale di condivisione file appena creata.
2. [Connettersi alla macchina virtuale di condivisione file.](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)
3. Nella macchina virtuale di condivisione file creare una cartella **nell'unità C** che verrà utilizzata come condivisione del profilo.
4. Fare clic con il pulsante destro del mouse sulla nuova cartella, selezionare **Proprietà**, **Condivisione**, quindi selezionare **Condivisione avanzata...**.
5. Selezionare **Condividi questa cartella**, selezionare **Autorizzazioni...**, quindi **Aggiungi...**.
6. Cercare il gruppo di sicurezza a cui sono stati aggiunti gli utenti di Windows Virtual Desktop , quindi assicurarsi che il gruppo disponga del **controllo completo**.
7. Dopo aver aggiunto il gruppo di sicurezza, fare clic con il pulsante destro del mouse sulla cartella, scegliere **Proprietà**, **Condivisione**, quindi copiare il percorso di **rete** da utilizzare in un secondo momento.

Per ulteriori informazioni sulle autorizzazioni, vedere la documentazione di [FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Configurare il contenitore di profili FSLogixConfigure the FSLogix profile container

Per configurare le macchine virtuali con il software FSLogix, eseguire le operazioni seguenti in ogni computer registrato nel pool host:

1. [Connettersi alla macchina virtuale](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) con le credenziali fornite durante la creazione della macchina virtuale.
2. Avviare un browser Internet e passare a [questo collegamento](https://go.microsoft.com/fwlink/?linkid=2084562) per scaricare l'agente FSLogix.
3. Passare a \\ \\Win32\\ \\ \\Release o\\X64 Release nel file .zip ed eseguire **FSLogixAppsSetup** per installare l'agente FSLogix.  Per ulteriori informazioni su come installare FSLogix, vedere [Scaricare e installare FSLogix](/fslogix/install-ht/).
4. Passare a **Programmi** > **FSLogix** > **Apps** per confermare l'agente installato.
5. Dal menu Start, eseguire **RegEdit** come amministratore. Passare a **Software HKEY_LOCAL_MACHINE\\\\\\computer FSLogix**.
6. Creare una chiave denominata **Profili**.
7. Creare i seguenti valori per la chiave Profili:

| Nome                | Type               | Dati/Valore                        |
|---------------------|--------------------|-----------------------------------|
| Attivato             | DWORD              | 1                                 |
| VHDLocations        | Valore multistringa | "Percorso di rete per condivisione file"     |

>[!IMPORTANT]
>Per proteggere l'ambiente di Desktop virtuale Windows in Azure, è consigliabile non aprire la porta 3389 in ingresso nelle macchine virtuali. Desktop virtuale Windows non richiede una porta in ingresso 3389 per permettere agli utenti di accedere alle macchine virtuali del pool di host. Se è necessario aprire la porta 3389 per la risoluzione dei problemi, è consigliabile usare l'[accesso Just-In-Time alla VM](../security-center/security-center-just-in-time.md).
