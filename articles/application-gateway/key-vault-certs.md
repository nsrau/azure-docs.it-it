---
title: Terminazione SSL con certificati di Azure Key Vault
description: Informazioni su come è possibile integrare il Gateway applicazione di Azure Key Vault per i certificati server collegati di listener di traccia abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827635"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminazione SSL con certificati Key Vault

[Azure Key Vault](../key-vault/key-vault-whatis.md) è archiviare un segreto di piattaforma gestita che è possibile usare per proteggere i segreti, chiavi e certificati SSL. Gateway applicazione di Azure supporta l'integrazione con Key Vault (in anteprima pubblica) per i certificati server collegati di listener di traccia abilitati per HTTPS. Questo supporto è limitato a v2 SKU del Gateway applicazione.

> [!IMPORTANT]
> Integrazione del Gateway applicazione con Key Vault è attualmente in anteprima pubblica. Questa versione di anteprima viene fornito senza un contratto di servizio (SLA) e non è consigliato per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa versione di anteprima pubblica offre due modelli per la terminazione SSL:

- È possibile fornire in modo esplicito i certificati SSL collegati al listener. Questo modello è il metodo tradizionale per passare i certificati SSL per Gateway applicazione per la terminazione SSL.
- Quando si crea un listener abilitato per HTTPS, è possibile fornire facoltativamente un riferimento a un certificato di Key Vault esistente o un segreto.

Integrazione del Gateway applicazione con Key Vault offre molti vantaggi, tra cui:

- Una maggiore sicurezza, perché i certificati SSL non sono gestiti direttamente dal team di sviluppo dell'applicazione. L'integrazione consente a un team di sicurezza separati per:
  * Configurare i gateway applicazione.
  * Controllare i cicli di vita del gateway applicazione.
  * Concedere le autorizzazioni per i gateway applicazione selezionati per accedere ai certificati archiviati nell'insieme di credenziali delle chiavi.
- Supporto per l'importazione dei certificati esistenti in insieme di credenziali delle chiavi. Oppure usare le API dell'insieme di credenziali chiave per creare e gestire nuovi certificati con uno qualsiasi dei partner di Key Vault attendibile.
- Supporto per il rinnovo automatico dei certificati archiviati nell'insieme di credenziali delle chiavi.

Il Gateway applicazione supporta attualmente solo certificati software con convalida. Modulo di protezione hardware (HSM)-convalidati i certificati non sono supportati. Dopo aver configurato il Gateway applicazione per usare i certificati di Key Vault, le relative istanze recupero il certificato di Key Vault e installarli localmente per la terminazione SSL. Le istanze inoltre il polling Key Vault a intervalli di 24 ore per recuperare una versione di rinnovata del certificato, se presente. Se viene trovato un certificato aggiornato, il certificato SSL attualmente associato al listener HTTPS automaticamente viene ruotato.

## <a name="how-integration-works"></a>Funzionamento dell'integrazione

Integrazione del Gateway applicazione con Key Vault richiede un processo di configurazione in tre passaggi:

1. **Creare un'identità gestita assegnata dall'utente**

   Si crea o riutilizza un'esistente identità assegnata dall'utente gestita, che usa il Gateway applicazione per recuperare i certificati da Key Vault per tuo conto. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Questo passaggio Crea una nuova identità nel tenant di Azure Active Directory. L'identità è considerata attendibile dalla sottoscrizione che viene usata per creare l'identità.

1. **Configurare l'insieme di credenziali delle chiavi**

   Quindi importare un certificato esistente o crearne uno nuovo in insieme di credenziali delle chiavi. Il certificato verrà utilizzato dalle applicazioni eseguite tramite il gateway applicazione. In questo passaggio, è anche possibile usare un segreto dell'insieme di credenziali chiave che viene archiviato come un senza password file, in base 64 codificata in formato PFX. È consigliabile usare un tipo di certificato a causa la funzionalità di rinnovo automatico è disponibile con gli oggetti di tipo certificato in key vault. Dopo aver creato un certificato o una chiave privata, si definiscono i criteri di accesso in key vault per consentire l'identità a cui concedere *ottenere* accesso alla chiave privata.

1. **Configurare il gateway applicazione**

   Dopo aver completato i due passaggi precedenti, è possibile impostare o modificare un gateway applicazione esistente per usare l'identità gestito assegnata dall'utente. È anche possibile configurare il certificato SSL del listener HTTP in modo da puntare all'URI completo del certificato di Key Vault o ID del segreto.

   ![Certificati dell'insieme di credenziali chiave](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configura la terminazione SSL con certificati Key Vault con Azure PowerShell](configure-keyvault-ps.md)
