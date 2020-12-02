---
title: Usare il portale di Azure per risolvere gli errori di attivazione correlati a Azure Stack Edge Pro con GPU | Microsoft Docs
description: Viene descritto come risolvere i problemi relativi all'attivazione della GPU di Azure Stack Edge Pro e all'insieme di credenziali delle chiavi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: alkohli
ms.openlocfilehash: e93a7fd7aec5463a3d77bd9d6bb17d7072097870
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447636"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Risolvere i problemi di attivazione nel dispositivo GPU Pro Azure Stack Edge 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come risolvere i problemi di attivazione sul dispositivo GPU Azure Stack Edge Pro. 


## <a name="activation-errors"></a>Errori di attivazione

La tabella seguente riepiloga gli errori correlati all'attivazione del dispositivo e alla risoluzione consigliata corrispondente.

| Messaggio di errore| Risoluzione consigliata |
|------------------------------------------------------|--------------------------------------|
| Se il Azure Key Vault usato per l'attivazione viene eliminato prima che il dispositivo venga attivato con il codice di attivazione, viene visualizzato questo errore. <br> ![Errore di Key Vault 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Se l'insieme di credenziali delle chiavi è stato eliminato, è possibile recuperare l'insieme di credenziali delle chiavi se l'insieme di credenziali è in durata di ripulitura. Seguire i passaggi in [ripristinare un](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)insieme di credenziali delle chiavi. <br>Se la durata della ripulitura è trascorsa, l'insieme di credenziali delle chiavi non può essere recuperato. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| Se il Azure Key Vault viene eliminato dopo l'attivazione del dispositivo e si tenta di eseguire qualsiasi operazione che prevede la crittografia, ad esempio: **Aggiungi utente**, **Aggiungi condivisione**, **Configura calcolo**, viene visualizzato questo errore. <br> ![Errore di Key Vault 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Se l'insieme di credenziali delle chiavi è stato eliminato, è possibile recuperare l'insieme di credenziali delle chiavi se l'insieme di credenziali è in durata di ripulitura. Seguire i passaggi in ripristinare un insieme di credenziali delle chiavi. <br>Se la durata della ripulitura è trascorsa, l'insieme di credenziali delle chiavi non può essere recuperato. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| Se la chiave di integrità del canale nel Azure Key Vault viene eliminata e si tenta di eseguire qualsiasi operazione che comporti la crittografia, ad esempio: **Aggiungi utente**, **Aggiungi condivisione**, **Configura calcolo** , verrà visualizzato questo errore. <br> ![Errore di Key Vault 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Se la chiave di integrità del canale nell'insieme di credenziali delle chiavi viene eliminata, ma è ancora entro la durata dell'eliminazione, seguire la procedura descritta in [annullare la rimozione della chiave di Key Vault](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Se è trascorsa la durata della protezione da ripulitura e se è stato eseguito il backup della chiave, è possibile eseguire il ripristino dal backup altrimenti non è possibile ripristinare la chiave. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| Se la generazione della chiave di attivazione non riesce a causa di un errore, viene visualizzato questo errore. Nella notifica sono presenti ulteriori dettagli. <br> ![Errore di Key Vault 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Attendere alcuni minuti e ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| Se l'utente dispone di autorizzazioni di sola lettura, l'utente non è autorizzato a generare una chiave di attivazione e viene visualizzato questo errore. <br> ![Errore di Key Vault 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Il problema potrebbe essere dovuto al fatto che non si dispone dell'accesso corretto o  *Microsoft.* l'insieme di credenziali non è registrato.<li>Assicurarsi di disporre dell'accesso come proprietario o collaboratore a livello di gruppo di risorse usato per la risorsa Azure Stack Edge.</li><li>Verificare che il provider di risorse Microsoft. Vault sia registrato. Per registrare un provider di risorse, passare alla sottoscrizione usata per la risorsa Azure Stack Edge. Passare a **provider di risorse**, cercare *Microsoft. Vault* e selezionare e **registrare**.</li> |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [risolvere i problemi](azure-stack-edge-gpu-troubleshoot.md)relativi ai dispositivi.