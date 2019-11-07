---
title: Terminazione SSL con certificati Azure Key Vault
description: Informazioni su come integrare applicazione Azure gateway con Key Vault per i certificati del server collegati ai listener abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 76807c8ed10e30c554b6aa06ec096c830a86e36e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571989"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminazione SSL con certificati Key Vault

[Azure Key Vault](../key-vault/key-vault-overview.md) è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere i segreti, le chiavi e i certificati SSL. Applicazione Azure gateway supporta l'integrazione con Key Vault per i certificati del server collegati ai listener abilitati per HTTPS. Questo supporto è limitato allo SKU V2 del gateway applicazione.

L'integrazione di Key Vault offre due modelli per la terminazione SSL:

- È possibile specificare in modo esplicito i certificati SSL collegati al listener. Questo modello rappresenta il modo tradizionale per passare i certificati SSL al gateway applicazione per la terminazione SSL.
- È possibile specificare facoltativamente un riferimento a un certificato o un segreto Key Vault esistente quando si crea un listener abilitato per HTTPS.

L'integrazione del gateway applicazione con Key Vault offre molti vantaggi, tra cui:

- Maggiore sicurezza, perché i certificati SSL non sono gestiti direttamente dal team di sviluppo di applicazioni. L'integrazione consente a un team di sicurezza separato di:
  * Configurare i gateway applicazione.
  * Controllare i cicli di vita del gateway applicazione.
  * Concedere le autorizzazioni ai gateway applicazione selezionati per accedere ai certificati archiviati nell'insieme di credenziali delle chiavi.
- Supporto per l'importazione di certificati esistenti nell'insieme di credenziali delle chiavi. In alternativa, è possibile usare Key Vault API per creare e gestire nuovi certificati con i partner Key Vault attendibili.
- Supporto per il rinnovo automatico dei certificati archiviati nell'insieme di credenziali delle chiavi.

Il gateway applicazione supporta attualmente solo certificati convalidati per il software. Modulo di protezione hardware (HSM): i certificati convalidati non sono supportati. Dopo la configurazione del gateway applicazione per l'uso di certificati Key Vault, le relative istanze recuperano il certificato da Key Vault e le installano localmente per la terminazione SSL. Anche le istanze eseguono il polling Key Vault a intervalli di 24 ore per recuperare una versione rinnovata del certificato, se esistente. Se viene trovato un certificato aggiornato, il certificato SSL attualmente associato al listener HTTPS viene ruotato automaticamente.

## <a name="how-integration-works"></a>Funzionamento dell'integrazione

L'integrazione del gateway applicazione con Key Vault richiede un processo di configurazione in tre passaggi:

1. **Creare un'identità gestita assegnata dall'utente**

   Si crea o si riutilizza un'identità gestita assegnata dall'utente esistente, che il gateway applicazione usa per recuperare i certificati da Key Vault per conto dell'utente. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Questo passaggio consente di creare una nuova identità nel tenant Azure Active Directory. L'identità è considerata attendibile dalla sottoscrizione usata per creare l'identità.

1. **Configurare l'insieme di credenziali delle chiavi**

   È quindi possibile importare un certificato esistente o crearne uno nuovo nell'insieme di credenziali delle chiavi. Il certificato verrà usato dalle applicazioni che vengono eseguite tramite il gateway applicazione. In questo passaggio, è anche possibile usare un segreto dell'insieme di credenziali delle chiavi archiviato come file PFX codificato in base 64 senza password. È consigliabile usare un tipo di certificato a causa della funzionalità di rinnovo automatico disponibile con gli oggetti tipo di certificato nell'insieme di credenziali delle chiavi. Dopo aver creato un certificato o un segreto, è necessario definire i criteri di accesso nell'insieme di credenziali delle chiavi per consentire all'identità di concedere *l'accesso al* segreto.

1. **Configurare il gateway applicazione**

   Dopo aver completato i due passaggi precedenti, è possibile impostare o modificare un gateway applicazione esistente per usare l'identità gestita assegnata dall'utente. È anche possibile configurare il certificato SSL del listener HTTP in modo che punti all'URI completo del certificato Key Vault o dell'ID segreto.

   ![Certificati di Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare la terminazione SSL con Key Vault certificati utilizzando Azure PowerShell](configure-keyvault-ps.md)
