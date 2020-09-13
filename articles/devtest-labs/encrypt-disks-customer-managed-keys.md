---
title: Crittografare i dischi del sistema operativo usando chiavi gestite dal cliente in Azure DevTest Labs
description: Informazioni su come crittografare i dischi del sistema operativo usando chiavi gestite dal cliente in Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 257894c6318c9ca083c72daf3c888f7d509ae683
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489833"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Crittografare i dischi del sistema operativo usando chiavi gestite dal cliente in Azure DevTest Labs
La crittografia lato server (SSE) protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. SSE crittografa automaticamente i dati archiviati nei dischi gestiti in Azure (dischi del sistema operativo e dati) per impostazione predefinita quando vengono salvati in modo permanente nel cloud. Scopri di più sulla [crittografia del disco](../virtual-machines/windows/disk-encryption.md) in Azure. 

All'interno di DevTest Labs tutti i dischi del sistema operativo e i dischi dati creati come parte di un Lab vengono crittografati usando chiavi gestite dalla piattaforma. Tuttavia, in qualità di proprietario del Lab, è possibile scegliere di crittografare i dischi del sistema operativo della macchina virtuale del Lab usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una **chiave gestita dal cliente** da usare per la crittografia dei dati nei dischi del sistema operativo Lab. Per altre informazioni sulla crittografia lato server (SSE) con chiavi gestite dal cliente e altri tipi di crittografia dei dischi gestiti, vedere [chiavi gestite dal cliente](../virtual-machines/windows/disk-encryption.md#customer-managed-keys). Vedere anche [restrizioni relative all'uso delle chiavi gestite dal cliente](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Attualmente la crittografia del disco con una chiave gestita dal cliente è supportata solo per i dischi del sistema operativo in DevTest Labs. 
> 
> - L'impostazione si applica ai dischi del sistema operativo appena creati nel Lab. Se si sceglie di modificare il set di crittografia del disco in un determinato momento, i dischi meno recenti nel Lab continueranno a rimanere crittografati con il precedente set di crittografia del disco. 

La sezione seguente illustra come un proprietario del Lab può configurare la crittografia usando una chiave gestita dal cliente.

## <a name="pre-requisites"></a>Prerequisiti

1. Se non è stata impostata una crittografia del disco, seguire questo articolo per [configurare un Key Vault e un set di crittografia del disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Tenere presente i requisiti seguenti per il set di crittografia del disco: 

    - Il set di crittografia del disco deve trovarsi **nella stessa area e nella stessa sottoscrizione del Lab**. 
    - Assicurarsi che (proprietario del Lab) disponga almeno di un **accesso a livello di lettura** al set di crittografia del disco che verrà usato per crittografare i dischi del sistema operativo del Lab. 
1. Per i laboratori creati prima del 8/1/2020, il proprietario del Lab dovrà assicurarsi che l'identità assegnata dal sistema Lab sia abilitata. A tale scopo, il proprietario del Lab può accedere al proprio Lab, fare clic su **configurazione e criteri**, fare clic sul pannello **identità (anteprima)** , impostare **lo stato** di identità assegnato al sistema **su** Sì e fare clic su **Salva**. Per i nuovi Lab creati dopo l'identità assegnata dal sistema di 8/1/2020 Lab verrà abilitato per impostazione predefinita. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Chiavi gestite":::
1. Per consentire al Lab di gestire la crittografia per tutti i dischi del sistema operativo Lab, il proprietario del lab deve concedere in modo esplicito il ruolo lettore di **identità assegnato dal sistema** del Lab sul set di crittografia del disco e il ruolo Collaboratore macchina virtuale nella sottoscrizione di Azure sottostante. Il proprietario del Lab può eseguire questa operazione completando i passaggi seguenti:

   
    1. Assicurarsi di essere un membro del [ruolo amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) a livello di sottoscrizione di Azure, in modo da poter gestire l'accesso utente alle risorse di Azure. 
    1. Nella pagina **Set Encryption disk** selezionare controllo di **accesso (IAM)** nel menu a sinistra. 
    1. Selezionare **+ Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi un'assegnazione di ruolo**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Aggiungi gestione ruoli-menu":::
    1. Nella pagina **Aggiungi assegnazione ruolo** selezionare il ruolo **lettore** o un ruolo che consente un maggiore accesso. 
    1. Digitare il nome del Lab per il quale verrà usato il set di crittografia del disco e selezionare il nome del Lab (identità assegnata dal sistema per il Lab) nell'elenco a discesa. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Selezionare l'identità gestita dal sistema del Lab":::        
    1. Sulla barra degli strumenti selezionare **Salva**. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Salva assegnazione ruolo":::
3. Aggiungere l' **identità assegnata dal sistema** Lab al ruolo **collaboratore macchina virtuale** utilizzando la pagina controllo di accesso della **sottoscrizione**  ->  **(IAM)** . I passaggi sono simili a quelli descritti nei passaggi precedenti. 

    
    1. Passare alla pagina **sottoscrizione** nel portale di Azure. 
    1. Selezionare **Controllo di accesso (IAM)** . 
    1. Selezionare **+ Aggiungi** sulla barra degli strumenti e selezionare **Aggiungi un'assegnazione di ruolo**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Sottoscrizione-pagina controllo di accesso > (IAM)":::
    1. Nella pagina **Aggiungi assegnazione ruolo** selezionare **collaboratore macchina virtuale** per il ruolo.
    1. Digitare il nome del Lab e selezionare il **nome del Lab** (identità assegnata dal sistema per il Lab) nell'elenco a discesa. 
    1. Sulla barra degli strumenti selezionare **Salva**. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Crittografare i dischi del sistema operativo Lab con una chiave gestita dal cliente 

1. Nella portale di Azure home page per il Lab selezionare **configurazione e criteri** dal menu a sinistra. 
1. Nella pagina **configurazione e criteri** selezionare **dischi (anteprima)** nella sezione **crittografia** . Per impostazione predefinita, il **tipo di crittografia** è impostato su crittografia dei componenti inattivi **con una chiave gestita dalla piattaforma**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Scheda dischi della pagina configurazione e criteri":::
1. Per **tipo di crittografia**selezionare **crittografia dei componenti inattivi con una chiave gestita dal cliente** dall'elenco a discesa. 
1. Per la **crittografia del disco impostata**selezionare il set di crittografia del disco creato in precedenza. Si tratta dello stesso set di crittografia del disco a cui può accedere l'identità assegnata dal sistema del Lab.
1. Sulla barra degli strumenti selezionare **Salva**. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Abilitare la crittografia con la chiave gestita dal cliente":::
1. Nella finestra di messaggio con il testo seguente: *questa impostazione verrà applicata ai computer appena creati nel Lab. Il disco del sistema operativo precedente resterà crittografato con il vecchio set di crittografia del disco*, selezionare **OK**. 

    Una volta configurata, i dischi del sistema operativo Lab verranno crittografati con la chiave gestita dal cliente fornita tramite il set di crittografia del disco. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Come verificare se i dischi vengono crittografati

1. Passare a una macchina virtuale Lab creata dopo aver abilitato la crittografia del disco con una chiave gestita dal cliente nel Lab.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="VM con crittografia del disco abilitata":::
1. Fare clic sul gruppo di risorse della macchina virtuale e fare clic sul disco del sistema operativo.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Gruppo di risorse VM":::
1. Passare a crittografia e verificare se la crittografia è impostata su chiave gestita dal cliente con il set di crittografia del disco selezionato.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Convalida crittografia":::
  
## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti: 

- [Crittografia dischi di Azure](../virtual-machines/windows/disk-encryption.md). 
- [Chiavi gestite dal cliente](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 
