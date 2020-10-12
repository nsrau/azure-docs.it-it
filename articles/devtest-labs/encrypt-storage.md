---
title: Crittografare un account di archiviazione di Azure usato da un Lab in Azure DevTest Labs
description: Informazioni su come configurare la crittografia di una risorsa di archiviazione di Azure usata da un Lab in Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 3c1d1531084deeabbe9a8d261e93554a2c691eb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87433563"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Crittografare l'archiviazione di Azure usata da un Lab in Azure DevTest Labs
Ogni Lab creato in Azure DevTest Labs viene creato con un account di archiviazione di Azure associato. L'account di archiviazione viene usato per gli scopi seguenti: 

- Archiviazione dei documenti delle [formule](devtest-lab-manage-formulas.md) che possono essere utilizzati per creare macchine virtuali.
- Archiviazione dei risultati degli artefatti che includono log di distribuzione e di estensione generati dall'applicazione di elementi. 
- [Caricamento di dischi rigidi virtuali (VHD) per la creazione di immagini personalizzate nel Lab.](devtest-lab-create-template.md)
- Memorizzazione nella cache di [elementi](add-artifact-vm.md) utilizzati di frequente e [modelli di Azure Resource Manager](devtest-lab-create-environment-from-arm.md) per un recupero più veloce durante la creazione della macchina virtuale/ambiente.

> [!NOTE]
> Le informazioni riportate sopra sono essenziali per il funzionamento del Lab. Viene archiviato per la durata del Lab (e delle risorse Lab), a meno che non venga eliminato esplicitamente. L'eliminazione manuale di queste risorse può causare errori durante la creazione di macchine virtuali Lab e/o formule danneggiate per un uso futuro. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Individuare l'account di archiviazione e visualizzarne il contenuto

1. Nella home page per il Lab selezionare il gruppo di **risorse** nella pagina **Panoramica** . Verrà visualizzata la pagina del **gruppo di risorse** per il gruppo di risorse che contiene il Lab. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Selezionare il gruppo di risorse nella pagina Panoramica":::
1. Selezionare l'account di archiviazione di Azure del Lab. La convenzione di denominazione per l'account di archiviazione Lab è: `a<labNameWithoutInvalidCharacters><4-digit number>` . Ad esempio, se il nome del Lab è `contosolab` , il nome dell'account di archiviazione potrebbe essere `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Selezionare il gruppo di risorse nella pagina Panoramica":::
3. Nella pagina **account di archiviazione** selezionare **Storage Explorer (anteprima)** nel menu a sinistra e quindi selezionare **contenitori BLOB** per trovare il contenuto correlato al Lab pertinente. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Selezionare il gruppo di risorse nella pagina Panoramica" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Crittografare l'account di archiviazione Lab
Archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia di archiviazione di Azure protegge i tuoi dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per altre informazioni, vedere [Crittografia di Archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).

I dati nell'account di archiviazione Lab vengono crittografati con una **chiave gestita da Microsoft**. È possibile utilizzare chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con chiavi personalizzate. Se si sceglie di gestire la crittografia con le proprie chiavi per l'account di archiviazione del Lab, è possibile specificare una **chiave gestita dal cliente** con Azure Key Vault da usare per crittografare/decrittografare i dati nell'archivio BLOB e in file di Azure. Per altre informazioni sulle chiavi gestite dal cliente, vedere [usare chiavi gestite dal cliente con Azure Key Vault per gestire la crittografia di archiviazione di Azure](../storage/common/encryption-customer-managed-keys.md).

Per informazioni su come configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure, vedere gli articoli seguenti: 

- [Azure portal](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Interfaccia della riga di comando di Azure](../storage/common/storage-encryption-keys-cli.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Gestire il ciclo di vita dell'archiviazione BLOB di Azure
Come indicato in precedenza, le informazioni archiviate nell'account di archiviazione del Lab sono fondamentali per il funzionamento del Lab senza errori. A meno che non vengano eliminati in modo esplicito, questi dati continueranno a rimanere nell'account di archiviazione del Lab per tutta la durata del Lab o la durata di macchine virtuali Lab specifiche, a seconda del tipo di dati.

### <a name="uploaded-vhds"></a>Dischi rigidi virtuali caricati
Questi dischi rigidi virtuali vengono usati per creare immagini personalizzate. Rimuovendo tali immagini non sarà più possibile creare immagini personalizzate da questi VHD.

### <a name="artifacts-cache"></a>Cache artefatti
Queste cache verranno ricreate ogni volta che vengono applicati gli artefatti. Verranno aggiornati con il contenuto più recente dei rispettivi repository a cui si fa riferimento. Quindi, se si eliminano queste informazioni per salvare le spese relative all'archiviazione, il sollievo sarà temporaneo.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager cache modelli
Queste cache verranno ricreate ogni volta che i repository di modelli basati su Azure Resource Manager vengono connessi e attivati nel Lab. Verranno aggiornati con il contenuto più recente dei rispettivi repository a cui si fa riferimento. Quindi, se si eliminano queste informazioni per salvare le spese relative all'archiviazione, il sollievo sarà temporaneo.

### <a name="formulas"></a>Formule
Questi documenti vengono usati per supportare l'opzione per la creazione di formule da macchine virtuali esistenti e per la creazione di macchine virtuali da formule. L'eliminazione dei documenti delle formule può causare errori durante le operazioni seguenti:

- Creazione di una formula da una macchina virtuale lab esistente
- Creazione o aggiornamento di formule 
- Creazione di una macchina virtuale da una formula.

### <a name="artifact-results"></a>Risultati artefatto
Quando vengono applicati gli artefatti, le dimensioni dei rispettivi risultati degli artefatti possono aumentare nel tempo a seconda del numero e del tipo di artefatti eseguiti nelle macchine virtuali del Lab. Quindi, in qualità di proprietario del Lab, potrebbe essere necessario controllare il ciclo di vita di tali documenti. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Si consiglia di eseguire questo passaggio per ridurre le spese associate all'account di archiviazione di Azure. 

La regola seguente, ad esempio, viene usata per impostare una regola di scadenza di 90 giorni in modo specifico per i risultati dell'artefatto. Garantisce che i risultati degli artefatti meno recenti vengano riciclati dall'account di archiviazione a cadenza regolare.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure, vedere gli articoli seguenti: 

- [Azure portal](../storage/common/storage-encryption-keys-portal.md)
- [Azure PowerShell](../storage/common/storage-encryption-keys-powershell.md)
- [Interfaccia della riga di comando di Azure](../storage/common/storage-encryption-keys-cli.md)


