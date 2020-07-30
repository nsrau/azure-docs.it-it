---
title: 'Esercitazione: Creare una raccolta contenuto per distribuire macchine virtuali nella soluzione Azure VMware'
description: In questa esercitazione della soluzione Azure VMware viene creata una raccolta contenuto per distribuire una macchina virtuale in un cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: d5f0f26293af9eb146cfb4b2f3adf47fe8cce94f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079384"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution-avs"></a>Esercitazione: Creare una raccolta contenuto per distribuire macchine virtuali nella soluzione Azure VMware

Una raccolta contenuto archivia e gestisce il contenuto sotto forma di elementi della raccolta. Un singolo elemento della raccolta è costituito da uno o più file usati per distribuire macchine virtuali. 
 
In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare una raccolta contenuto
> * Caricare un'immagine ISO nella raccolta contenuto
> * Distribuire una macchina virtuale usando un file ISO nella raccolta contenuto

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari un segmento di commutatore logico NSX-T e un servizio DHCP gestito.  Per informazioni dettagliate, vedere [Come gestire DHCP nella soluzione Azure VMware (anteprima)](manage-dhcp.md).

## <a name="create-a-content-library"></a>Creare una raccolta contenuto

1. Dal client vSphere locale selezionare **Menu > Content Libraries** (Menu > Raccolte contenuto).

   ![Selezionare Menu -> Content Libraries](./media/content-library/vsphere-menu-content-libraries.png)

1. Fare clic sul pulsante **Add** (Aggiungi) per creare una nuova raccolta contenuto.

   ![Fare clic sul pulsante Add per creare una nuova raccolta contenuto.](./media/content-library/create-new-content-library.png)

1. Specificare un nome e confermare l'indirizzo IP del server vCenter e selezionare **Next** (Avanti).

   ![Specificare un nome, aggiungere eventuali note e quindi fare clic su Next.](./media/content-library/new-content-library-step1.png)

1. Selezionare **Local content library** (Raccolta contenuto locale) e quindi **Next** (Avanti).

   ![Per questo esempio verrà creata una raccolta contenuto locale. Selezionare Next.](./media/content-library/new-content-library-step2.png)

1. Selezionare l'archivio dati in cui verrà archiviata la raccolta contenuto e quindi selezionare **Next** (Avanti).

   ![Selezionare l'archivio dati in cui si vuole ospitare la raccolta contenuto e selezionare Next.](./media/content-library/new-content-library-step3.png)

1. Rivedere e verificare le impostazioni della raccolta contenuto e quindi selezionare **Finish** (Fine).

   ![Verificare le impostazioni e fare clic su Finish.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Caricare un'immagine ISO nella raccolta contenuto

Ora che la raccolta contenuto è stata creata, è possibile aggiungere un'immagine ISO per distribuire una macchina virtuale in un cluster di cloud privato. 

1. Dal client vSphere selezionare **Menu > Content Libraries** (Menu > Raccolte contenuto).

1. Fare clic con il pulsante destro del mouse sulla raccolta contenuto da usare per la nuova immagine ISO e selezionare **Import element** (Importa elemento).

1. Importare un elemento della raccolta per l'origine eseguendo una delle operazioni seguenti e quindi selezionare **Import** (Importa):
   1. Selezionare URL e specificare un URL per scaricare un'immagine ISO.

   1. Selezionare **Local File** (File locale) per scaricare dal sistema locale.

   > [!TIP]
   > Facoltativamente, è possibile definire un nome di elemento personalizzato e aggiungere le note per la destinazione.

1. Aprire la raccolta e selezionare la scheda **Other Types** (Altri tipi) per verificare che l'immagine ISO sia stata caricata correttamente.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Distribuire una macchina virtuale in un cluster di cloud privato

1. Dal client vSphere selezionare **Menu > Hosts and Clusters** (Menu > Host e cluster).

1. Nel pannello di sinistra espandere l'albero e selezionare un cluster.

1. Selezionare **Actions > New Virtual Machine** (Azioni > Nuova macchina virtuale).

1. Eseguire la procedura guidata e modificare le impostazioni desiderate.

1. Selezionare **New CD/DVD Drive > Client Device > Content Library ISO File** (Nuova unità CD/DVD > Dispositivo client > File ISO raccolta contenuto).

1. Selezionare l'immagine ISO caricata nella sezione precedente e quindi selezionare **OK**.

1. Selezionare la casella di controllo **Connect** (Connetti) in modo che l'immagine ISO venga montata in fase di accensione.

1. Selezionare **New Network > Select dropdown > Browse** (Nuova rete > Elenco a discesa Seleziona > Sfoglia).

1. Selezionare il **commutatore logico (segmento)** e quindi **OK**.

1. Modificare eventuali altre impostazioni hardware e selezionare **Next** (Avanti).

1. Rivedere le impostazioni e selezionare **Finish** (Fine).


## <a name="next-steps"></a>Passaggi successivi

Se si prevede di usare Hybrid Cloud Extension (HCX) per eseguire la migrazione dei carichi di lavoro delle macchine virtuali nel cloud privato, usare la procedura [Installare HCX per la soluzione Azure VMware](hybrid-cloud-extension-installation.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->