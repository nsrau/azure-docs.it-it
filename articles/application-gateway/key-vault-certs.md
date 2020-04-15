---
title: Terminazione TLS con certificati di Azure Key Vault
description: Informazioni su come integrare il gateway applicazione di Azure con l'insieme delle chiavi per i certificati server collegati ai listener abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 26093d051da8f2182a40f80837acbd9ef7dd008f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312080"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminazione TLS con certificati Key Vault

[L'insieme](../key-vault/key-vault-overview.md) di credenziali delle chiavi di Azure è un archivio segreto gestito dalla piattaforma che è possibile usare per proteggere segreti, chiavi e certificati TLS/SSL. Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server collegati a listener abilitati per HTTPS. Questo supporto è limitato allo SKU v2 del gateway applicazione.

L'integrazione di Key Vault offre due modelli per la terminazione TLS:

- È possibile fornire in modo esplicito i certificati TLS/SSL collegati al listener. Questo modello è il modo tradizionale per passare certificati TLS/SSL al gateway applicazione per la terminazione TLS.
- Facoltativamente, è possibile fornire un riferimento a un certificato o segreto dell'insieme di credenziali delle chiavi esistente quando si crea un listener abilitato per HTTPS.

L'integrazione del gateway applicazione con Key Vault offre molti vantaggi, tra cui:

- Maggiore sicurezza, poiché i certificati TLS/SSL non vengono gestiti direttamente dal team di sviluppo dell'applicazione. L'integrazione consente a un team di sicurezza separato di:
  * Configurare i gateway applicazione.
  * Controlla i cicli di vita dei gateway applicativi.
  * Concedere le autorizzazioni ai gateway applicazione selezionati per accedere ai certificati archiviati nell'insieme di credenziali delle chiavi.
- Supporto per l'importazione di certificati esistenti nell'insieme di credenziali delle chiavi. In alternativa, utilizzare le API dell'insieme di credenziali delle chiavi per creare e gestire nuovi certificati con uno dei partner trusted dell'insieme di credenziali delle chiavi.
- Supporto per il rinnovo automatico dei certificati archiviati nell'insieme di credenziali delle chiavi.

Il gateway applicazione supporta attualmente solo certificati convalidati dal software. I certificati convalidati dal modulo di sicurezza hardware (HSM) non sono supportati. Dopo aver configurato il gateway applicazione per l'utilizzo dei certificati dell'insieme di credenziali delle chiavi, le relative istanze recuperano il certificato dall'insieme di credenziali delle chiavi e li installano localmente per la terminazione TLS. Le istanze eseguono inoltre il polling dell'insieme delle chiavi a intervalli di 24 ore per recuperare una versione rinnovata del certificato, se esistente. Se viene trovato un certificato aggiornato, il certificato TLS/SSL attualmente associato al listener HTTPS viene ruotato automaticamente.

> [!NOTE]
> Il portale di Azure supporta solo i certificati KeyVault, non i segreti. Il gateway applicazione supporta ancora i segreti di riferimento da KeyVault, ma solo tramite risorse non portale come PowerShell, CLI, API, modelli ARM e così via. 

## <a name="how-integration-works"></a>Funzionamento dell'integrazione

L'integrazione del gateway applicazione con Key Vault richiede un processo di configurazione in tre passaggi:Application Gateway integration with Key Vault requires a three-step configuration process:

1. **Creare un'identità gestita assegnata dall'utenteCreate a user-assigned managed identity**

   Si crea o si riutilizza un'identità gestita assegnata dall'utente esistente, utilizzata dal gateway applicazione per recuperare i certificati dall'insieme di credenziali delle chiavi per conto dell'utente. Per altre informazioni, vedere [Che cos'è l'identità gestita per le risorse](../active-directory/managed-identities-azure-resources/overview.md)di Azure? . Questo passaggio consente di creare una nuova identità nel tenant di Azure Active Directory.This step creates a new identity in the Azure Active Directory tenant. L'identità è considerata attendibile dalla sottoscrizione usata per creare l'identità.

1. **Configurare l'insieme di credenziali delle chiavi**

   È quindi possibile importare un certificato esistente o crearne uno nuovo nell'insieme di credenziali delle chiavi. Il certificato verrà utilizzato dalle applicazioni eseguite tramite il gateway applicazione. In questo passaggio è anche possibile usare un segreto dell'insieme di credenziali delle chiavi archiviato come file PFX con codifica 64 senza password. È consigliabile usare un tipo di certificato a causa della funzionalità di rinnovo automatico disponibile con gli oggetti di tipo di certificato nell'insieme di credenziali delle chiavi. Dopo aver creato un certificato o un segreto, definire i criteri di accesso nell'insieme di credenziali delle chiavi per consentire all'identità di *ottenere* l'accesso al segreto.

1. **Configurare il gateway applicazione**

   Dopo aver completato i due passaggi precedenti, è possibile configurare o modificare un gateway applicazione esistente per l'utilizzo dell'identità gestita assegnata dall'utente. È inoltre possibile configurare il certificato TLS/SSL del listener HTTP in modo che punti all'URI completo del certificato del Vault chiave o dell'ID segreto.

   ![Certificati dell'insieme di credenziali delle chiaviKey vault certificates](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare la terminazione TLS con i certificati dell'insieme di chiavi tramite Azure PowerShellConfigure TLS termination with Key Vault certificates by using Azure PowerShell](configure-keyvault-ps.md)
