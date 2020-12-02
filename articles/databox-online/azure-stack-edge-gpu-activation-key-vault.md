---
title: Integrazione di Azure Key Vault con la risorsa Azure Stack Edge e l'attivazione del dispositivo
description: Descrive il modo in cui Azure Key Vault è associata alla gestione dei segreti durante l'attivazione del dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: ec7a32739940d53d976e73a7e170df96a0acc245
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449523"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integrazione di Azure Key Vault con Azure Stack Edge 

Azure Key Vault è integrato con la risorsa Azure Stack Edge per la gestione dei segreti. Questo articolo fornisce informazioni dettagliate sul modo in cui viene creata una Azure Key Vault per la risorsa Azure Stack Edge durante l'attivazione del dispositivo e viene quindi usata per la gestione dei segreti. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Informazioni su Key Vault e Azure Stack Edge

Azure Key Vault servizio cloud viene usato per archiviare e controllare in modo sicuro l'accesso a token, password, certificati, chiavi API e altri segreti. Key Vault facilita inoltre la creazione e il controllo delle chiavi di crittografia utilizzate per crittografare i dati. Per ulteriori informazioni sulle transazioni consentite e sugli addebiti corrispondenti, vedere [prezzi per Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

Per Azure Stack servizio Edge, uno dei segreti usati è la chiave di integrità del canale (CIK). Questa chiave consente di crittografare i segreti. Con l'integrazione di Key Vault, la CIK viene archiviata in modo sicuro nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [archiviare in modo sicuro segreti e chiavi](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Creazione dell'insieme di credenziali delle chiavi

Viene creato un insieme di credenziali delle chiavi per la risorsa Azure Stack Edge durante il processo di generazione della chiave di attivazione. 

- Quando si crea Azure Stack risorsa perimetrale, è necessario registrare il provider di risorse *Microsoft.* l'insieme di credenziali. Il provider di risorse viene registrato automaticamente se si dispone dell'accesso come proprietario o collaboratore alla sottoscrizione. L'insieme di credenziali delle chiavi viene creato nella stessa sottoscrizione e nel gruppo di risorse della risorsa Azure Stack Edge. 

- Quando si crea una risorsa di Azure Stack Edge, viene creato anche un identità del servizio gestita (MSI) che viene mantenuto per la durata della risorsa e comunica con il provider di risorse nel cloud. 

    Quando il file MSI è abilitato, Azure crea un'identità attendibile per la risorsa Azure Stack Edge.

- Dopo aver creato la risorsa Azure Stack Edge e generato una chiave di attivazione dalla portale di Azure, viene creato un insieme di credenziali delle chiavi. Questo insieme di credenziali delle chiavi viene usato per la gestione dei segreti e persiste fino a quando la risorsa Azure Stack Edge esiste. 

    ![Key Vault creato durante la generazione della chiave di attivazione](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- È possibile scegliere di accettare il nome predefinito della chiave o specificare un nome personalizzato per l'insieme di credenziali delle chiavi. Il nome dell'insieme di credenziali delle chiavi deve avere una lunghezza compreso tra 3 e 24 caratteri. Non è possibile usare un insieme di credenziali delle chiavi già in uso. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![MSI creato durante la creazione della risorsa Azure Stack Edge](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Per passare all'insieme di credenziali delle chiavi di Azure, passare alle **Proprietà** nella risorsa di Azure stack Edge e selezionare il nome dell'insieme di credenziali delle chiavi. 

- Per evitare l'eliminazione accidentale, un blocco di risorsa è abilitato nell'insieme di credenziali delle chiavi. Nell'insieme di credenziali delle chiavi è abilitata anche un'eliminazione temporanea che consente il ripristino dell'insieme di credenziali delle chiavi entro 90 giorni, se si verifica un'eliminazione accidentale. Per altre informazioni, vedere [Panoramica di Azure Key Vault soft-delete](../key-vault/general/soft-delete-overview.md)

    Se l'insieme di credenziali delle chiavi viene eliminato accidentalmente e la durata di ripulitura di 90 giorni non è trascorsa, seguire questa procedura per [ripristinare l'](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)insieme di credenziali delle chiavi. 

- Se si dispone di una risorsa Azure Stack Edge esistente prima che la Azure Key Vault sia stata integrata con la risorsa Azure Stack Edge, non si avrà alcun effetto. È possibile continuare a usare la risorsa Azure Stack Edge esistente. 

- Quando viene eliminata la risorsa Azure Stack Edge, viene eliminata anche la Azure Key Vault con la risorsa. Viene chiesto di confermare l'operazione. Se non si intende eliminare questo insieme di credenziali delle chiavi, è possibile scegliere di non fornire il consenso. Solo la risorsa Azure Stack Edge viene eliminata lasciando intatto l'insieme di credenziali delle chiavi. 

- Se questo insieme di credenziali delle chiavi è stato usato per archiviare altre chiavi, è comunque possibile ripristinarlo entro 90 giorni dall'eliminazione. Durante l'eliminazione del periodo di protezione, non è possibile usare il nome dell'insieme di credenziali delle chiavi per creare un nuovo insieme di credenziali delle chiavi.

Se si verificano problemi relativi all'attivazione del dispositivo e dell'insieme di credenziali delle chiavi, vedere [risolvere i problemi di attivazione del dispositivo](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [generare la chiave di attivazione](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).